# Deterministic Devnet Harness

## Introduction

A lightweight harness for running Ethereum devnets where **the chain is deterministic**: identical genesis + identical inputs always produce identical blocks, state roots, and fork-choice decisions, slot for slot. The goal is reproducible devnet runs, replayable incidents, and chaos testing with rewind — without the cost or complexity of a bit-deterministic hypervisor.

Bit-deterministic execution at the hypervisor level — every syscall, every thread interleaving, every malloc — is overkill for what we actually need. Ethereum's state transition function is already deterministic; what introduces non-determinism in a running devnet is wall-clock time, network ordering, and a few sources of OS entropy. Control those, and the chain is reproducible.

Built on top of [ethpandaops/ethereum-package](https://github.com/ethpandaops/ethereum-package) so devnets remain spun up the way the team already runs them.

## Goals

- Two runs of the same devnet config produce byte-identical block roots at every slot
- Any captured run can be **replayed** offline against the same client set
- Any captured run can be **forked at slot N** with mutations injected and re-run forward
- Divergence between clients on identical inputs surfaces immediately and is bisectable

## Non-goals

- Bit-deterministic execution at the OS/syscall level
- Replacing Kurtosis or the existing ethereum-package
- A hypervisor or custom kernel
