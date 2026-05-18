# Deterministic Devnet Harness

## Introduction

A lightweight harness for running Ethereum devnets where **the chain is deterministic**: identical genesis + identical inputs always produce identical blocks, state roots, and fork-choice decisions, slot for slot. The goal is reproducible devnet runs, replayable incidents, and chaos testing with rewind — without the cost or complexity of a bit-deterministic hypervisor.

Bit-deterministic execution at the hypervisor level — every syscall, every thread interleaving, every malloc — is overkill for what we actually need. Ethereum's state transition function is already deterministic; what introduces non-determinism in a running devnet is wall-clock time, network ordering, and a few sources of OS entropy. Control those, and the chain is reproducible.

Built on top of [ethpandaops/ethereum-package](https://github.com/ethpandaops/ethereum-package) so devnets remain spun up the way the team already runs them.

## Goals

- Two runs of the same devnet config produce byte-identical block roots at every slot
- Any captured run can be **replayed** offline against the same client set
- Any captured run can be **forked at slot N** with mutations injected (drop messages, delay blocks, swap proposers) and re-run forward
- Divergence between clients on an identical input trace surfaces immediately and is bisectable

Explicit non-goals:

- Bit-deterministic execution at the OS/syscall level
- Replacing Kurtosis or the existing ethereum-package
- A hypervisor or custom kernel

## Sources of non-determinism to control

| Source | How it's controlled |
|---|---|
| Wall-clock time (slot starts, gossip score timers, attestation deadlines) | Virtual clock injected into every client container |
| Network message ordering and timing | In-the-middle libp2p + discv5 relay routes all p2p traffic |
| OS entropy (peer IDs, ephemeral keys, nonce selection) | Seeded `/dev/urandom` shim or `LD_PRELOAD` of `getrandom`/`getentropy` |
| Engine API timing between EL and CL | Routed through the same mediator, virtual-clock timestamped |
| Validator client aggregator/builder selection | Deterministic by design once clock and peer set are pinned |

Goroutine / async-task interleaving is **not** controlled and does not need to be — at the chain level, if all inputs are pinned, the output chain is the same.

## Architecture

### 1. Virtual clock

A single time oracle that every container reads from. Each client is launched with an `LD_PRELOAD` shim (`libfaketime`-style) that intercepts `clock_gettime`, `gettimeofday`, `time`, and the monotonic clocks. A small daemon ticks the virtual clock at a configurable rate (`1x`, `100x`, paused, single-stepped per slot).

Per-client investigation is needed:

- **Go clients** (Geth, Prysm, etc.): `time.Now()` resolves through vDSO — `LD_PRELOAD` covers most cases but the Go runtime caches some timers
- **Rust clients** (Lighthouse, Reth): `Instant::now()` uses `CLOCK_MONOTONIC` which must be slewed alongside `CLOCK_REALTIME` or some timers will drift
- **JVM clients** (Teku, Besu): `System.currentTimeMillis()` / `System.nanoTime()` need JVM-level interception or a patched JNI shim
- **Nim** (Nimbus): chronos has its own clock abstraction — likely needs an upstream PR exposing an override
- **JS clients** (Lodestar): `Date.now()` and `performance.now()` need to be patched at the Node level

Some clients will need small upstream PRs to read from an env-overridable clock source. This is the largest chunk of cross-client work in the project.

### 2. Network mediator

Replace direct peer connections with an in-the-middle relay that proxies all libp2p gossip, libp2p req/resp, and discv5. Every message is logged with `(from, to, topic, payload-hash, virtual-time-ns, sequence)`. The mediator has two modes:

- **Record**: clients talk normally, every message captured into a trace file
- **Replay**: trace file is the source of truth; the mediator re-injects messages at the recorded virtual times and refuses to forward anything not in the trace

The mediator handles **L7 protocol-level** concerns the disruptor layer (see below) can't reach:

- Pin message order deterministically by seed
- Delay specific gossip topics by N slots
- Drop messages matching a content predicate
- Inject duplicate or malformed messages
- Swap proposers or replay attester votes from the trace

This component is the centerpiece. It must look like a well-behaved peer to all five CL clients simultaneously so it doesn't get score-down'd out of the gossip mesh.

### 2a. Network disruption (delegated to disruptoor)

L3/L4 chaos — partitions, latency, jitter, packet loss, bandwidth caps — is **not** reimplemented here. [`disruptoor`](https://github.com/ethpandaops/disruptoor) already does this and is wired into ethereum-package via [PR #1398](https://github.com/ethpandaops/ethereum-package/pull/1398) as an `additional_services` option.

What the harness adds on top of disruptoor:

- **Virtual-time scheduling** — disruptoor commands are issued at virtual times, not wall times, so a partition at virtual slot 1000 lands at the same chain state on every replay
- **Trace recording** — every disruptoor state mutation (apply partition, apply shaping, clear) is captured into the same trace as p2p messages
- **Replay** — on replay, the harness re-applies recorded disruptoor commands at the recorded virtual times

Net effect: disruptoor owns the chaos *primitives*, the harness owns *when and in what order* those primitives are applied so they survive a replay.

### 3. State observer

At every slot boundary, snapshot from each client:

- CL: `eth/v2/debug/beacon/states/head`, fork-choice store, attestation pool, justification/finalization
- EL: state root, `debug_dumpBlock`, txpool state

Persisted to a sqlite/duckdb file keyed by `(run_id, slot, client_id)`. Cheap, easy, and gives downstream tooling something concrete to bisect against.

### 4. Replay & mutate

Given `(trace, genesis, virtual-clock-recording)`:

- **Replay**: re-run the devnet from the trace. If block roots diverge from the original run, either there's an uncontrolled source of non-determinism or there's a real bug.
- **Fork**: branch at slot N, modify the trace (drop these attestations, delay this block proposal by 4s, simulate validator X going offline), run forward, diff the resulting chain against baseline.
- **Bisect**: between two runs that diverged, binary-search the input trace to find the minimal differing input.

## Use cases

- **Reproducible CI**: a devnet run that fails once in a thousand is no longer a shrug — capture the trace, replay until you understand it
- **Incident replay**: capture the trace from a public testnet incident, replay locally as many times as needed with full mutability
- **Spec-ambiguity finder**: identical inputs across five CL clients diverge → either a client bug or a spec under-specification, both worth filing
- **Chaos with rewind**: drop 30% of attestations between slot 1000–1010, replay with 40%, compare — without rebooting anything
- **Researcher tool**: "what if proposer-boost were 50?" — fork a captured Pectra trace, change the config, see the alternate timeline
- **Pre-fork validation**: capture a baseline trace, apply a candidate client patch, replay, prove the patch is a no-op (or isn't) at the chain level

## Configuration

```yaml
harness:
  virtual_clock:
    rate: "1x"          # or "100x", "paused", "step"
    start_unix: 1700000000
  entropy:
    seed: "0xdeadbeef"
  mediator:
    mode: "record"      # or "replay"
    trace_file: "./run-001.trace"
    policies: []        # chaos policies, see below
  observer:
    db_path: "./run-001.duckdb"
    snapshot_every_slot: true

# Example mediator policy (L7 — protocol-aware)
mediator_policies:
  - kind: delay
    topic: "beacon_block"
    selector: { slot_mod: 32 }
    delay_ms: 3000

# Example disruptoor schedule (L3/L4 — network primitives, virtual-time scheduled)
disruptoor_schedule:
  - at_virtual_slot: 1000
    apply:
      partitions:
        - groups:
            - participants: [1, 2]
            - participants: [3, 4]
          components: [el, cl]
  - at_virtual_slot: 1005
    clear: partitions
```

## Scope notes

This is honestly 2–3 months of focused work, and the scope creep risks are real. Suggested phasing:

**Phase 1 (month 1):** Virtual clock working across all five CL clients + their EL pair. Just getting two identical runs to produce identical block roots through the existing devnet plumbing is itself a milestone.

**Phase 2 (month 2):** Network mediator in record-only mode. State observer. Confirm a recorded run replays deterministically.

**Phase 3 (month 3):** Replay mode, fork/mutate, mediator policies, virtual-time scheduling of disruptoor commands with trace recording, bisection. CLI + Jupyter for analysis.

What's explicitly cut from v1:

- No GUI — CLI and notebooks only
- No smart input fuzzing — record/replay/mutate is the surface area
- No syscall-level recording
- No multi-host distribution — single-host devnets only

## Depends on / related work

- [ethpandaops/ethereum-package](https://github.com/ethpandaops/ethereum-package) — the substrate this plugs into
- [ethpandaops/disruptoor](https://github.com/ethpandaops/disruptoor) — L3/L4 chaos primitives (partitions, shaping). Already wired into ethereum-package via [PR #1398](https://github.com/ethpandaops/ethereum-package/pull/1398). The harness layers virtual-time scheduling and trace recording on top of it.
- [ethpandaops/hermes](https://github.com/ethpandaops/hermes) — already a libp2p listener/tracer, likely the seed for the network mediator
- [ethpandaops/assertoor](https://github.com/ethpandaops/assertoor) — natural consumer for the replay engine
- [ethpandaops/forky](https://github.com/ethpandaops/forky), [ethpandaops/tracoor](https://github.com/ethpandaops/tracoor) — complementary observability
- [Attacknet](https://ethpandaops.io/posts/attacknet-introduction) — chaos testing today, but without rewind
- [`libfaketime`](https://github.com/wolfcw/libfaketime) — prior art for clock interposition
- FoundationDB's [Joshua](https://apple.github.io/foundationdb/testing.html) — the deterministic simulation approach this borrows from in spirit

## Prior art notes

A few client teams have experimented with deterministic test modes internally (Lighthouse's `sim` testing, Prysm's e2e harness, Nimbus's testnet sim). None of them are cross-client and none of them give you replay/mutate against a captured trace from a real network. This harness aims to be that piece.
