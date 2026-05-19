# Beacon API Spec Tester

## Introduction

A spec-compliance tester for the consensus layer [Beacon API](https://github.com/ethereum/beacon-APIs). For each endpoint, it verifies that every consensus client (Lighthouse, Lodestar, Nimbus, Prysm, Teku, Grandine) returns the **correct fields, in the correct order, with the correct types**, against a pinned commit of the spec.

Existing Hive simulators cover EL JSON-RPC well but the CL beacon API has no equivalent that locks responses to the wire format the spec describes. Field reordering, missing optional fields, and type drift between clients are the kind of bugs that only surface when a downstream consumer (explorer, validator client, MEV pipeline) breaks — this tool aims to catch them at the source.

Results should land in a clearly digestible frontend, in the style of [hive.ethpandaops.io](https://hive.ethpandaops.io) — a per-endpoint × per-client matrix with green/yellow/red and drill-down into the diff against the expected response.

## Goals

- Pin a commit of [ethereum/beacon-APIs](https://github.com/ethereum/beacon-APIs), fetch the OpenAPI spec at that commit, and treat it as the source of truth
- Hit every endpoint on every CL client and compare the response against the spec schema — field presence, field order, types, and enum values
- Report per-endpoint × per-client results in a Hive-style frontend with pass / fail / partial and a diff view
- Run on demand against a fresh devnet and on a schedule against long-running networks

## Non-goals

- Behavioral / consensus correctness — that is what Assertoor and Hive consensus simulators are for
- Performance benchmarking of the beacon API — out of scope
- Patching client responses; this tool only observes and reports

## Features

### Spec ingestion
- Accept an arbitrary commit / tag / branch of [ethereum/beacon-APIs](https://github.com/ethereum/beacon-APIs) as input
- Parse the OpenAPI definitions and derive the expected schema per endpoint, including ordered field lists for SSZ-adjacent responses
- Track which endpoints are mandatory vs. optional per fork (phase0 / altair / bellatrix / capella / deneb / electra / fusaka …)

### Client coverage
- Run against all major CL clients: Lighthouse, Lodestar, Nimbus, Prysm, Teku, Grandine
- Support both JSON and SSZ encodings where the spec defines both
- Cover validator, beacon, config, debug, events, and node endpoint groups

### Devnet integration
- Use [Kurtosis](https://github.com/kurtosis-tech/kurtosis) + [ethpandaops/ethereum-package](https://github.com/ethpandaops/ethereum-package) to spin up a fresh devnet with all target clients
- Optionally point at an existing network (long-running devnet, public testnet) instead of spawning one
- Reach a known chain state (post-genesis, post-fork) before running the test matrix so responses are non-trivial

### Reporting frontend
- Hive-style results UI: endpoints on one axis, clients on the other, status per cell
- Per-cell drill-down showing expected vs. actual response with a structural diff (missing field / extra field / wrong order / wrong type)
- Stable URLs per run so failures can be linked in client issues
- JSON output for CI integration

### CI / automation
- Single command to run the full matrix against a given spec commit and devnet config
- Exit codes suitable for CI gating
- Scheduled runs against `dev` of beacon-APIs to catch regressions early

## Depends on

- [ethereum/beacon-APIs](https://github.com/ethereum/beacon-APIs): source of truth for endpoint shapes
- [ethpandaops/ethereum-package](https://github.com/ethpandaops/ethereum-package): devnet spinner for the client matrix
- [kurtosis-tech/kurtosis](https://github.com/kurtosis-tech/kurtosis): orchestration layer

## Prior art / related work

- [hive.ethpandaops.io](https://hive.ethpandaops.io) — the results-UI shape we want to mirror
- Hive's existing `eth2-beacon-api` simulator — narrower scope, less spec-pinned
- [Assertoor](https://github.com/ethpandaops/assertoor) — complementary, focuses on chain behavior rather than API shape

## Use cases

- Catch field-order and type drift across CL clients before downstream tools (explorers, MEV relays, validator clients) hit it
- Validate a client's beacon API implementation against an in-flight beacon-APIs PR before it merges
- Regression-gate client releases by running the matrix on every tag
- Surface gaps in optional-endpoint coverage per client
