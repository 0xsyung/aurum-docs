# Aurum Labs Documentation

Documentation for the Aurum Labs project. This repo is intended to stay **in sync with the code** and clearly mark what is implemented vs planned.

## Current Implementation Snapshot

- **aurum-contracts**: minimal Gnosis-style `ConditionalTokens` + `ERC6909` helpers only.
- **aurum-app**: UI prototype with mock markets and no live contract interactions.
- **aurum-backend**: placeholder (minimal server scaffold only).
- **aurum-website**: static landing page served by a small Express server.

If anything below contradicts the code, treat the code as the source of truth and update the docs.

## Quick Start

- **[Local Development Setup](./development/local-setup.md)**
- **[Repository Overview](./deployment/repository-overview.md)**
- **[Web3 Login Research](./development/web3-login.md)**
- **[x402 Payments](./development/x402.md)**

## Repository Structure

```
aurum-docs/
├── README.md                    # This file
├── architecture/                # System architecture documentation
│   ├── overview.md              # High-level system overview (current vs planned)
│   ├── smart-contracts.md       # Current smart contracts and planned components
│   └── oracle-system.md         # Oracle design (planned, not implemented)
├── tokenomics/                  # Token economics documentation (planned)
│   └── aurum-token.md           # AURUM token design (planned)
├── development/                 # Development documentation
│   ├── local-setup.md           # Local development setup guide
│   └── roadmap.md               # Development roadmap (updated status)
├── deployment/                  # Deployment guides
│   ├── README.md                # Deployment overview
│   ├── cloudflare-setup.md      # Cloudflare Pages setup
│   ├── render-setup.md          # Render setup (website + backend template)
│   ├── base-sepolia.md          # Testnet deployment notes (ConditionalTokens only)
│   ├── repository-overview.md   # Repository summary
│   ├── frontend-deployment.md   # aurum-app deployment
│   ├── backend-deployment.md    # Backend placeholder
│   └── url-configuration.md     # URL/env var configuration
├── api/                         # API documentation
│   └── contract-interactions.md # ConditionalTokens interaction guide
└── changelog/                   # Development changelog
    └── YYYY-MM-DD.md            # Daily progress logs
```

## Features (Current)

- Architecture and deployment documentation
- Local setup guide
- Web3 login research and x402 notes
- Contract interaction guide for `ConditionalTokens`

## Features (Planned)

- Full contract suite docs (AurumToken, Oracle, MarketFactory, AMM)
- Backend API documentation
- Production deployment runbooks

## Related Repositories

| Repository | Description | Status |
|------------|-------------|--------|
| [aurum-contracts](https://github.com/0xsyung/aurum-contracts) | ConditionalTokens (ERC6909) contracts | Prototype |
| [aurum-app](https://github.com/0xsyung/aurum-app) | Trading UI | Prototype |
| [aurum-website](https://github.com/0xsyung/aurum-website) | Landing page | Active |
| [aurum-backend](https://github.com/0xsyung/aurum-backend) | Backend services | Placeholder |

## Deployed Contracts

This repository does not currently track on-chain deployments. If you deploy the current contracts, record addresses in `deployment/base-sepolia.md`.

## Target Network

- **Testnet**: Base Sepolia (Chain ID: 84532)
- **Mainnet**: Base (Chain ID: 8453) - Future

## License

MIT License - See LICENSE file for details.
