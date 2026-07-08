# Chain Identity Plan

This document tracks the first separation work needed before changing consensus or adding programmable transaction execution.

## Goal

The fork must not accidentally behave like a Bitcoin mainnet, testnet, signet, or regtest node. Network identity must be separated before any execution-layer work begins.

## Current Inherited Values To Replace

The imported codebase currently inherits Bitcoin network identity values from Bitcoin Core.

| Area | Current inherited value | Required action |
| --- | --- | --- |
| Mainnet message start | `f9 be b4 d9` | Replace before any public network use |
| Mainnet P2P port | `8333` | Replace |
| Mainnet RPC port | `8332` | Replace |
| Mainnet bech32 HRP | `bc` | Replace |
| Mainnet DNS seeds | Bitcoin seed nodes | Remove or replace |
| Fixed seeds | Bitcoin seed data | Remove or regenerate |
| AssumeValid | Bitcoin chain hash | Remove or replace |
| Minimum chain work | Bitcoin chain work | Reset or replace |
| AssumeUTXO data | Bitcoin snapshots | Remove or regenerate |

## Proposed Provisional Values

These values are placeholders for early development. They should be reviewed before public testnet or mainnet use.

| Network | Message start | P2P port | RPC port | Bech32 HRP |
| --- | --- | ---: | ---: | --- |
| main | `bf c0 b7 48` | `19333` | `19332` | `bth` |
| testnet3 | `ce e2 c4 13` | `19334` | `19342` | `tbth` |
| testnet4 | `ce e2 c4 14` | `19335` | `19343` | `tbth` |
| signet | custom challenge-derived | `19336` | `19344` | `tbth` |
| regtest | `ce e2 c4 16` | `19444` | `19443` | `bthrt` |

## Proposed Address Prefixes

These are provisional and should be validated against wallet compatibility and collision risk.

| Prefix type | Main | Test/regtest |
| --- | ---: | ---: |
| P2PKH | `25` | `111` initially, then replace |
| P2SH | `85` | `196` initially, then replace |
| WIF secret | `153` | `239` initially, then replace |
| Bech32 | `bth` | `tbth` / `bthrt` |

## Implementation Order

1. Add documentation for inherited Bitcoin values.
2. Change regtest identity first.
3. Verify local regtest startup.
4. Change signet/testnet identity.
5. Remove inherited Bitcoin DNS seeds and fixed seeds.
6. Reset inherited Bitcoin assume-valid, assume-UTXO, and minimum-chain-work data.
7. Decide whether public chains use a new genesis block or a fork-height split.
8. Only then modify mainnet identity values.

## Replay Protection Decision

Replay protection depends on launch style.

### New genesis launch

If the fork launches with a new genesis block, replay protection comes primarily from the separate chain history. Bitcoin transactions will not be valid on the new chain unless the new rules intentionally import Bitcoin UTXOs.

### Fork-height launch

If the fork launches from a Bitcoin snapshot at a specific fork height, replay protection must be explicit. Options include:

- New transaction version rule after fork activation
- Mandatory fork-specific sighash behavior
- Mandatory fork marker in transactions after activation
- Fork-specific script rule after activation

This decision must be made before any public network or balance migration plan.

## First Code PR Scope

The first code PR should be small and regtest-only:

- Change regtest message-start bytes
- Change regtest default P2P port
- Change regtest default RPC port
- Change regtest bech32 HRP
- Keep consensus behavior unchanged
- Add or update tests that assert the new values

Mainnet values should not be changed in the same first PR unless tests are already passing.
