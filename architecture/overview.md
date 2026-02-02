# System Architecture Overview

## Introduction

Aurum Labs is a decentralized prediction market platform that enables users to trade on the outcomes of future events. The platform is built on Base (Ethereum L2) and uses the AURUM token for governance and oracle operations.

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              AURUM LABS PLATFORM                            │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐         │
│  │   aurum-website │    │    aurum-app    │    │  aurum-backend  │         │
│  │   (Landing)     │───▶│   (dApp)        │◀──▶│   (API/Indexer) │         │
│  └─────────────────┘    └────────┬────────┘    └────────┬────────┘         │
│                                  │                      │                   │
│                                  ▼                      ▼                   │
│  ┌──────────────────────────────────────────────────────────────────┐      │
│  │                        BASE BLOCKCHAIN                            │      │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐            │      │
│  │  │ Conditional  │  │    Aurum     │  │   Market     │            │      │
│  │  │   Tokens     │  │   Oracle     │  │   Factory    │            │      │
│  │  │  (ERC6909)   │  │              │  │              │            │      │
│  │  └──────────────┘  └──────────────┘  └──────────────┘            │      │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐            │      │
│  │  │    Aurum     │  │   Market     │  │     Fee      │            │      │
│  │  │    Token     │  │    AMM       │  │  Collector   │            │      │
│  │  │   (ERC20)    │  │              │  │              │            │      │
│  │  └──────────────┘  └──────────────┘  └──────────────┘            │      │
│  └──────────────────────────────────────────────────────────────────┘      │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

## Components

### 1. Frontend Layer

#### aurum-website (Company Website)
- **Purpose**: Marketing and information about Aurum Labs
- **Technology**: React + Tailwind CSS
- **Features**:
  - Company information
  - Product overview
  - "Launch App" button to access dApp
- **Repository**: [aurum-website](https://github.com/0xsyung/aurum-website)

#### aurum-app (Prediction Market dApp)
- **Purpose**: Main user interface for trading
- **Technology**: React + Vite + Tailwind CSS
- **Features**:
  - Wallet connection (MetaMask, WalletConnect)
  - Market browsing and search
  - Trading interface (buy/sell outcome tokens)
  - Portfolio management
  - Market creation (admin)
- **Repository**: [aurum-app](https://github.com/0xsyung/aurum-app)

### 2. Backend Layer

#### aurum-backend (API & Indexer)
- **Purpose**: Off-chain services for performance
- **Technology**: Node.js / TypeScript
- **Features**:
  - Event indexer (index blockchain events)
  - REST/GraphQL API
  - Price calculation service
  - Notification service
- **Repository**: [aurum-backend](https://github.com/0xsyung/aurum-backend)

### 3. Smart Contract Layer

#### aurum-contracts
- **Purpose**: On-chain logic and state
- **Technology**: Solidity + Foundry
- **Contracts**:
  - `ConditionalTokens.sol` - ERC6909-based outcome tokens
  - `AurumToken.sol` - ERC20 governance token
  - `AurumOracle.sol` - Optimistic oracle system
  - `MarketFactory.sol` - Market creation and management
  - `MarketAMM.sol` - Automated market maker
  - `FeeCollector.sol` - Fee collection and distribution
- **Repository**: [aurum-contracts](https://github.com/0xsyung/aurum-contracts)

## Data Flow

### Market Creation Flow
```
1. Admin creates market via aurum-app
2. MarketFactory.createMarket() called on-chain
3. ConditionalTokens.prepareCondition() initializes outcome tokens
4. MarketAMM pool created with initial liquidity
5. Backend indexes MarketCreated event
6. Market appears in aurum-app
```

### Trading Flow
```
1. User connects wallet to aurum-app
2. User selects market and outcome
3. User approves USDC spending
4. MarketAMM.buy() or sell() called
5. Outcome tokens minted/burned
6. Backend updates market prices
7. UI reflects new position
```

### Resolution Flow
```
1. Market end time reached
2. Proposer calls AurumOracle.proposeOutcome()
3. Challenge period begins (2 hours for binary, 24 hours for complex)
4. If no dispute: outcome finalized
5. If disputed: AURUM holders vote
6. ConditionalTokens.reportPayouts() called
7. Users can redeem winning positions
```

## Network Configuration

| Network | Chain ID | RPC URL | Explorer |
|---------|----------|---------|----------|
| Base Sepolia (Testnet) | 84532 | https://sepolia.base.org | https://sepolia.basescan.org |
| Base (Mainnet) | 8453 | https://mainnet.base.org | https://basescan.org |

## Security Considerations

1. **Smart Contract Security**
   - All contracts audited before mainnet
   - Upgradeable proxy pattern for critical contracts
   - Time-locked admin functions

2. **Oracle Security**
   - Economic guarantees via staking
   - Dispute mechanism prevents manipulation
   - Multi-day voting for contested outcomes

3. **Frontend Security**
   - No private key storage
   - Transaction simulation before signing
   - Rate limiting on API

## Next Steps

See [Development Roadmap](../development/roadmap.md) for implementation timeline.
