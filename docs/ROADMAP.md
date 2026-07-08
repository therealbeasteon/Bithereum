# Roadmap

This document tracks the early engineering sequence for converting the imported Bitcoin Core codebase into an independent experimental protocol fork.

## Phase 1: Repository Bootstrap

- Replace inherited project overview text with fork-specific engineering notes.
- Add architecture documentation.
- Add development roadmap documentation.
- Keep changes documentation-only until the network identity plan is reviewed.

## Phase 2: Independent Chain Identity

- Define unique network message-start bytes.
- Define unique default P2P and RPC ports.
- Define unique address and bech32 prefixes.
- Remove inherited seed-node assumptions.
- Define replay-protection strategy.
- Confirm local regtest isolation.

## Phase 3: Programmable Output Research

- Specify programmable output structure.
- Specify deterministic execution rules.
- Specify execution-cost accounting.
- Specify state-transition validation.
- Specify mempool and block-validation integration.

## Phase 4: Regtest Prototype

- Implement only on regtest first.
- Add unit tests and functional tests.
- Benchmark validation cost.
- Document every consensus-adjacent change before expanding scope.
