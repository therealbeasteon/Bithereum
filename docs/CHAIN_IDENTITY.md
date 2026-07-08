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

## Completed Regtest Separation

Regtest identity was separated first because it is the safest local-development network.

Implemented regtest values:

| Area | Bithereum regtest value |
| --- | --- |
| Message start | `ce e2 c4 16` |
| P2P port | `19444` |
| RPC port | `19443` |
| Bech32 HRP | `bthrt` |

Regtest intentionally keeps consensus behavior unchanged for now. This step only prevents inherited Bitcoin regtest identity values from being used by Bithereum local development.

## Testnet And Signet Planning

After regtest, the next separation step is testnet and signet. These networks should be split before mainnet is touched.

### Testnet3

Provisional values:

| Area | Current inherited value | Proposed Bithereum value |
| --- | --- | --- |
| Message start | `0b 11 09 07` | `ce e2 c4 13` |
| P2P port | `18333` | `19334` |
| RPC port | `18332` | `19342` |
| Bech32 HRP | `tb` | `tbth` |
| DNS seeds | Bitcoin testnet seeds | Remove until Bithereum testnet seeds exist |
| Fixed seeds | Bitcoin testnet seed data | Clear until regenerated |
| Minimum chain work | Bitcoin testnet chain work | Reset or replace |
| AssumeValid | Bitcoin testnet block hash | Reset or replace |
| AssumeUTXO | Bitcoin testnet snapshots | Remove or regenerate |

Recommended implementation: use a dedicated testnet3-only PR after tests are updated for regtest.

### Testnet4

Provisional values:

| Area | Current inherited value | Proposed Bithereum value |
| --- | --- | --- |
| Message start | `1c 16 3f 28` | `ce e2 c4 14` |
| P2P port | `48333` | `19335` |
| RPC port | `48332` | `19343` |
| Bech32 HRP | `tb` | `tbth` |
| DNS seeds | Bitcoin testnet4 seeds | Remove until Bithereum testnet4 seeds exist |
| Fixed seeds | Bitcoin testnet4 seed data | Clear until regenerated |
| Minimum chain work | Bitcoin testnet4 chain work | Reset or replace |
| AssumeValid | Bitcoin testnet4 block hash | Reset or replace |
| AssumeUTXO | Bitcoin testnet4 snapshots | Remove or regenerate |

Recommended implementation: combine with testnet3 only if the PR remains small and tests are easy to review.

### Signet

Signet is different because its message-start bytes are derived from the signet challenge. The safer first step is to define a Bithereum signet challenge, then review the derived network magic.

Provisional values:

| Area | Current inherited value | Proposed Bithereum value |
| --- | --- | --- |
| Challenge | Bitcoin default signet challenge | New Bithereum signet challenge |
| Message start | Challenge-derived | Derived from Bithereum challenge |
| P2P port | `38333` | `19336` |
| RPC port | `38332` | `19344` |
| Bech32 HRP | `tb` | `tbth` |
| DNS seeds | Bitcoin signet seeds | Remove until Bithereum signet seeds exist |
| Fixed seeds | Bitcoin signet seed data | Clear until regenerated |
| Minimum chain work | Bitcoin signet chain work | Reset or replace |
| AssumeValid | Bitcoin signet block hash | Reset or replace |
| AssumeUTXO | Bitcoin signet snapshots | Remove or regenerate |

Recommended implementation: use a signet-only PR after choosing the challenge.

## Test Network Implementation Order

1. Verify regtest build and test assumptions.
2. Document final testnet3/testnet4 values.
3. Implement testnet3 and testnet4 identity separation.
4. Choose a Bithereum signet challenge.
5. Implement signet identity separation.
6. Remove inherited Bitcoin seeds and snapshot assumptions from test networks.
7. Only after test networks are separated, revisit mainnet planning.

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
