# Aurum Labs Documentation

Welcome to the official documentation repository for **Aurum Labs** - a decentralized prediction market platform built on Base.

## Overview

Aurum Labs is building a prediction market platform similar to Polymarket, but with our own governance token (AURUM) and oracle system. The platform allows users to trade on the outcomes of future events using outcome tokens backed by stablecoin collateral.

## Quick Start

- **[Local Development Setup](./development/local-setup.md)** - Set up the complete platform locally for end-to-end testing
- **[Deployment Guide](./deployment/README.md)** - Deploy to production (Cloudflare, Render, Base)

## Repository Structure

```
aurum-docs/
├── README.md                    # This file
├── architecture/                # System architecture documentation
│   ├── overview.md              # High-level system overview
│   ├── smart-contracts.md       # Smart contract architecture
│   ├── oracle-system.md         # Aurum Oracle design
│   └── frontend-backend.md      # Frontend and backend architecture
├── tokenomics/                  # Token economics documentation
│   ├── aurum-token.md           # AURUM token details
│   └── distribution.md          # Token distribution plan
├── development/                 # Development documentation
│   ├── local-setup.md           # Local development setup guide
│   ├── roadmap.md               # Development roadmap
│   └── issues/                  # GitHub issues tracking
├── deployment/                  # Deployment guides
│   ├── README.md                # Deployment overview
│   ├── cloudflare-setup.md      # Cloudflare Pages setup
│   ├── render-setup.md          # Render backend setup
│   ├── base-sepolia.md          # Testnet deployment
│   ├── repository-overview.md   # Repository summary
│   └── alternatives.md          # Alternative hosting options
├── api/                         # API documentation
│   └── contract-interactions.md # Smart contract API reference
├── research/                    # Research and references
│   ├── polymarket-analysis.md   # Polymarket architecture analysis
│   ├── uma-oracle.md            # UMA Oracle research
│   └── prediction-markets.md    # General prediction market research
└── changelog/                   # Development changelog
    └── YYYY-MM-DD.md            # Daily progress logs
```

## Related Repositories

| Repository | Description | Status |
|------------|-------------|--------|
| [aurum-contracts](https://github.com/0xsyung/aurum-contracts) | Smart contracts (Foundry) | ✅ Deployed (Base Sepolia) |
| [aurum-app](https://github.com/0xsyung/aurum-app) | Trading interface (React) | 🔄 In Development |
| [aurum-website](https://github.com/0xsyung/aurum-website) | Landing page | ✅ Deployed |
| [aurum-backend](https://github.com/0xsyung/aurum-backend) | Backend services | ⏳ Not Started |

## Deployed Contracts (Base Sepolia)

| Contract | Address |
|----------|---------|
| ConditionalTokens | `0x9A7A037469204604C29a44901b69B0bBB1d45B13` |
| AurumToken | `0xdBfa3D8516C49581e2A6cBbD75F02F24c59811c1` |
| FeeCollector | `0xBCC8aC562085E207460B9a0342d62a480DD9caAC` |
| AurumOracle | `0x4EC0295F0344ac264EB83bd7bDb0069015702297` |
| MarketFactory | `0xaF8ddE93C551ce4f6A21db07508858Fb15E4bbC9` |

## Quick Links

### Getting Started
- [Local Development Setup](./development/local-setup.md)
- [Repository Overview](./deployment/repository-overview.md)

### Architecture
- [System Architecture](./architecture/overview.md)
- [Smart Contracts](./architecture/smart-contracts.md)
- [Oracle System](./architecture/oracle-system.md)

### Deployment
- [Deployment Overview](./deployment/README.md)
- [Cloudflare Setup](./deployment/cloudflare-setup.md)
- [Render Setup](./deployment/render-setup.md)
- [Base Sepolia Contracts](./deployment/base-sepolia.md)

### Development
- [Development Roadmap](./development/roadmap.md)
- [Contract API Reference](./api/contract-interactions.md)

### Tokenomics
- [AURUM Token](./tokenomics/aurum-token.md)
- [Token Distribution](./tokenomics/distribution.md)

## Target Network

- **Testnet**: Base Sepolia (Chain ID: 84532)
- **Mainnet**: Base (Chain ID: 8453) - Future

## License

MIT License - See LICENSE file for details.
