# System Architecture Overview

## Status (Current vs Planned)

The codebase currently provides:

- A **landing page** (`aurum-website`) with static content and a simple Express server.
- A **dApp UI prototype** (`aurum-app`) that renders mock markets and wallet UI but does not yet interact with contracts.
- A **contracts repo** (`aurum-contracts`) containing only a minimal `ConditionalTokens` implementation (ERC6909-based) plus helpers.
- A **backend repo** (`aurum-backend`) that is a placeholder.

Everything else described below is either planned or partially stubbed.

## High-Level Architecture (Current)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              AURUM LABS (CURRENT)                           │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────┐    ┌─────────────────┐                                 │
│  │   aurum-website │    │    aurum-app    │                                 │
│  │   (Landing)     │───▶│   (UI Prototype)│                                 │
│  └─────────────────┘    └────────┬────────┘                                 │
│                                  │                                          │
│                                  ▼                                          │
│  ┌──────────────────────────────────────────────────────────────────┐      │
│  │                        BASE BLOCKCHAIN                            │      │
│  │  ┌──────────────────┐                                             │      │
│  │  │ ConditionalTokens │  (ERC6909-based, minimal)                  │      │
│  │  └──────────────────┘                                             │      │
│  └──────────────────────────────────────────────────────────────────┘      │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

## Components

### 1. Frontend Layer

#### aurum-website (Company Website)
- **Purpose**: Marketing and information about Aurum Labs
- **Technology**: React + Vite + Tailwind CSS + Express (static server)
- **Current Features**:
  - Landing page content
  - Launch App link (configured via `VITE_DAPP_URL`)
- **Repository**: [aurum-website](https://github.com/0xsyung/aurum-website)

#### aurum-app (Prediction Market UI)
- **Purpose**: UI prototype for trading workflows
- **Technology**: React + Vite + Tailwind CSS + wagmi
- **Current Features**:
  - Wallet connection UI (Coinbase connector)
  - Market browsing with mock data
  - Create/trade/portfolio screens with placeholder logic
- **Not Implemented Yet**:
  - Contract reads/writes
  - Live market data
  - Backend integration
- **Repository**: [aurum-app](https://github.com/0xsyung/aurum-app)

### 2. Backend Layer

#### aurum-backend (API & Indexer)
- **Status**: Placeholder (README only)
- **Repository**: [aurum-backend](https://github.com/0xsyung/aurum-backend)

### 3. Smart Contract Layer

#### aurum-contracts
- **Purpose**: On-chain logic and state
- **Technology**: Solidity + Foundry
- **Current Contracts**:
  - `ConditionalTokens.sol` - Gnosis-style conditional tokens using ERC6909
  - `ERC6909.sol` + `ERC6909Claims.sol` - minimal ERC6909 implementation
- **Repository**: [aurum-contracts](https://github.com/0xsyung/aurum-contracts)

## Planned Architecture (Not Implemented)

The following components are still design goals and are not implemented in code:

- `AurumToken` (ERC20 governance token)
- `AurumOracle` (optimistic oracle)
- `MarketFactory` and `MarketAMM`
- `FeeCollector`
- Backend indexer, APIs, and real-time updates

## Network Configuration

| Network | Chain ID | RPC URL | Explorer |
|---------|----------|---------|----------|
| Base Sepolia (Testnet) | 84532 | https://sepolia.base.org | https://sepolia.basescan.org |
| Base (Mainnet) | 8453 | https://mainnet.base.org | https://basescan.org |

## Next Steps

See [Development Roadmap](../development/roadmap.md) for the updated implementation plan.
