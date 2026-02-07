# Aurum Labs Documentation

Welcome to the documentation repository for **Aurum Labs**.

## Current Implementation Snapshot

This repo is meant to reflect the actual code state across the project. As of today:

- **aurum-contracts** contains only a minimal Gnosis-style `ConditionalTokens` implementation plus `ERC6909` helpers.
- **aurum-app** is a UI prototype that renders mock markets and has no live contract interactions yet.
- **aurum-backend** is a placeholder with only a README.
- **aurum-website** is a static landing page served by a small Express server.

If anything below contradicts the code, treat the code as the source of truth and update the docs.

## Quick Start

- **[Local Development Setup](./development/local-setup.md)** - Start the app/website locally and optionally deploy the current contract.
- **[Repository Overview](./deployment/repository-overview.md)** - What each repo contains today.
- **[Web3 Login Research](./development/web3-login.md)** - Recommended auth approach (Privy) and alternatives.
- **[x402 Payments](./development/x402.md)** - How to integrate HTTP 402 payments in the app.

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

## Related Repositories

| Repository | Description | Status |
|------------|-------------|--------|
| [aurum-contracts](https://github.com/0xsyung/aurum-contracts) | ConditionalTokens (ERC6909) contracts | Prototype (minimal set) |
| [aurum-app](https://github.com/0xsyung/aurum-app) | Trading UI | Prototype (mock data, no chain integration) |
| [aurum-website](https://github.com/0xsyung/aurum-website) | Landing page | Active |
| [aurum-backend](https://github.com/0xsyung/aurum-backend) | Backend services | Placeholder |

## Deployed Contracts

This repository does not currently track any on-chain deployments. If you deploy the current contracts, record addresses in `deployment/base-sepolia.md`.

## Target Network

- **Testnet**: Base Sepolia (Chain ID: 84532)
- **Mainnet**: Base (Chain ID: 8453) - Future

## License

MIT License - See LICENSE file for details.
