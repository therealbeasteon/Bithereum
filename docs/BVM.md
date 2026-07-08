# Bithereum Virtual Machine

This document defines the first planning target for the Bithereum Virtual Machine, or BVM.

## Goal

BVM is a Bitcoin-native smart-contract execution layer for Bithereum.

It should add programmable UTXOs, contract validation, tokens, assets, NFTs, DeFi-style applications, DAOs, and smart wallets while preserving a UTXO-first architecture.

BVM is not an EVM clone and should not introduce an Ethereum-style global account model in the first phase.

## Design Principles

- Bitcoin-native first
- UTXO-first state model
- Deterministic execution
- Explicit resource metering
- Consensus-safe validation
- Small activation steps
- No mainnet activation until regtest/test networks prove the model

## Contract-Bearing UTXOs

A BVM contract is represented by one or more UTXOs with additional committed data.

A contract-bearing UTXO should commit to:

- Contract program identifier
- Contract state hash
- Optional contract metadata
- Locking or authorization rules
- Asset/token balances when applicable

A transaction that spends a contract-bearing UTXO must provide enough witness data for nodes to verify the contract transition.

## Execution Model

A BVM transaction consumes zero or more contract-bearing UTXOs and creates zero or more new contract-bearing UTXOs.

Validation checks that:

1. Input contract states are committed by the spent UTXOs.
2. The transaction provides invocation data.
3. BVM executes deterministically with bounded gas.
4. Output contract states match the VM result.
5. Fees cover normal transaction relay/mining and BVM execution cost.

## Program Model

The first BVM design should specify a minimal bytecode or instruction format before implementation.

The program format must be:

- Deterministic
- Versioned
- Easy to meter
- Safe to validate during block connection
- Independent from host machine architecture

## State Model

BVM state should be UTXO-local first.

The first phase should avoid global mutable account state. Shared state can be modeled through contract UTXOs that must be explicitly spent and recreated.

This keeps concurrency and validation closer to Bitcoin's existing transaction model.

## Validation Flow

A future consensus implementation should fit into transaction and block validation without bypassing existing Bitcoin safety boundaries.

Expected flow:

1. Parse transaction.
2. Identify BVM inputs and outputs.
3. Load committed input state from UTXO set.
4. Execute BVM with gas limit.
5. Verify output state commitments.
6. Apply standard fee and consensus checks.
7. Accept or reject transaction/block.

## Mempool Policy

Mempool policy may reject BVM transactions that are valid by consensus but too expensive or risky to relay.

Policy rules may include:

- Maximum gas per transaction
- Maximum witness size
- Minimum fee rate for BVM gas
- Limits on state size growth
- Limits on contract creation rate

## Activation Strategy

BVM should not be activated directly on mainnet.

Recommended order:

1. Write BVM and gas specs.
2. Add non-consensus skeleton types and tests.
3. Add regtest-only experimental activation.
4. Add functional tests for contract-bearing UTXOs.
5. Add testnet/signet activation only after regtest is stable.
6. Decide mainnet activation separately.

## Open Questions

- What is the first bytecode format?
- Should BVM programs live directly in witnesses, outputs, or referenced code UTXOs?
- How should state commitments be encoded?
- Should token balances be first-class BVM state or library-level state?
- What is the minimum viable opcode set?
- How are contract upgrades represented?
- How are read-only calls handled outside consensus?
- How should wallets discover and display contract UTXOs?
