# Architecture Notes

This document records the initial architecture direction for the fork.

## Starting Point

The repository begins from the Bitcoin Core codebase. The first engineering priority is to separate local development and future network behavior from the inherited Bitcoin network identity.

## Layering

```text
Imported Bitcoin Core codebase
├── Chain parameters
├── P2P networking
├── Mempool policy
├── Block validation
├── Transaction validation
├── Wallet and RPC surfaces
└── Future programmable-output research
```

## First Principle

Do not implement new execution behavior until the fork has a reviewed network-identity plan.

## Chain Identity Work

The following values must be reviewed before any public network is attempted:

- Network message-start bytes
- Default P2P port
- Default RPC port
- Address prefixes
- Bech32 human-readable prefix
- Seed-node behavior
- Checkpoint and assume-valid behavior
- Replay-protection policy
- Regtest/testnet/signet configuration

## Programmable Output Research

The future execution layer should be specified before implementation. Open questions include:

- Whether to extend script or add a separate execution engine
- How outputs carry contract rules
- How state transitions are represented
- How full nodes meter validation cost
- How block-level execution limits are enforced
- How mempool policy rejects expensive or invalid transitions early

## Regtest First

All experimental execution behavior should land behind regtest-only code paths before any broader network configuration is introduced.
