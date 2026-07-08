# BVM Gas And Metering

This document defines the first planning target for Bithereum Virtual Machine gas and execution metering.

## Goal

Gas protects Bithereum nodes from unbounded BVM execution cost.

BVM gas should meter CPU, memory, validation complexity, and state growth while remaining compatible with a Bitcoin-native UTXO fee model.

## Design Principles

- Deterministic metering
- No unbounded loops without gas cost
- No free state growth
- Conservative defaults
- Mempool policy stricter than consensus where needed
- UTXO-compatible fee payment
- No Ethereum account-balance dependency in the first phase

## Gas Unit

A gas unit is an abstract measure of BVM execution work.

The first gas model should account for:

- Instruction execution
- Memory allocation
- State reads
- State writes
- Contract creation
- Contract invocation
- Hashing and cryptographic checks
- Witness decoding

## Transaction Gas Limit

Each BVM transaction must declare or imply a maximum gas limit.

A transaction is invalid if BVM execution exceeds its gas limit.

Open question: whether the gas limit is encoded in a new transaction field, witness field, contract invocation envelope, or policy-only structure during early regtest experiments.

## Block Gas Limit

Each block should have a maximum total BVM gas budget.

This protects block validation time independently from raw block weight.

The first implementation should use conservative regtest-only constants before any public network activation.

## Fee Payment

BVM fees should remain UTXO-native.

Initial direction:

- Normal transaction fees continue to be paid by input minus output value.
- BVM gas cost affects required minimum fee.
- Consensus may enforce maximum gas usage.
- Policy may enforce minimum fee per gas.

Open question: whether gas pricing is consensus-enforced, policy-enforced, or split between both.

## Out Of Gas

If execution exceeds the gas limit:

- The transaction is invalid for consensus if the gas limit is consensus-visible.
- No partial state transition is accepted.
- Outputs must not be considered valid unless the full transition succeeds.

## Instruction Cost Table

The first BVM spec should define a versioned instruction cost table.

Cost categories:

| Category | Examples | Notes |
| --- | --- | --- |
| Constant cost | arithmetic, comparison | Simple deterministic operations |
| Linear cost | copying, hashing, serialization | Cost grows with byte length |
| Memory cost | allocation, expansion | Prevents memory abuse |
| State read cost | reading committed state | May depend on state size/proof size |
| State write cost | creating new state commitment | Must discourage state bloat |
| Crypto cost | signature/hash verification | Explicit high-cost operations |

## State Growth Cost

State growth is more dangerous than pure computation because it affects long-term node cost.

The first gas model should charge for:

- New contract UTXO creation
- Larger state commitments
- Token/asset metadata growth
- Persistent storage expansion

Open question: whether long-lived state requires rent, higher one-time fees, pruning rules, or all of the above.

## Mempool Policy

Mempool policy should be allowed to reject expensive BVM transactions even if they would be consensus-valid.

Possible policy limits:

- Max BVM gas per transaction
- Max BVM gas per package
- Max BVM witness bytes
- Max contract creations per transaction
- Minimum satoshis per gas

## Consensus Rules

Consensus should enforce only the minimum necessary rules for deterministic safety:

- Deterministic VM result
- Gas exhaustion behavior
- Valid state transition
- Block-level gas cap if adopted
- Versioned rules for future upgrades

## Open Questions

- Where is the gas limit encoded?
- Is gas price consensus or policy?
- Should unused gas have refunds?
- How should BVM gas interact with Bitcoin transaction weight?
- What is the first regtest gas schedule?
- Should state growth have a separate fee multiplier?
- How do packages and CPFP interact with BVM gas?
