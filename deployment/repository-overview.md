# Aurum Labs Repository Overview

This document provides a comprehensive overview of all Aurum Labs repositories, their purposes, and deployment recommendations.

## Repository Summary

| Repository | Purpose | Tech Stack | Status | Deployment |
|------------|---------|------------|--------|------------|
| **aurum-app** | Prediction market trading interface | React, Vite, wagmi, viem | Active | Cloudflare Pages |
| **aurum-website** | Landing page and waitlist | React, Vite, Node.js | Active | Render or Cloudflare |
| **aurum-backend** | API server | TBD | Not implemented | Render |
| **aurum-contracts** | Smart contracts | Solidity, Foundry | Deployed | On-chain (Base Sepolia) |
| **aurum-docs** | Documentation | Markdown | Active | GitHub Pages (optional) |

---

## aurum-app

The aurum-app repository contains the main prediction market trading interface. This is a pure frontend application that allows users to connect their wallets, browse markets, and trade outcome tokens.

### Technical Details

The application is built with React 19 and Vite 7, using TypeScript for type safety. It integrates with the blockchain using wagmi and viem libraries, and includes Coinbase's OnchainKit for enhanced wallet connectivity. The UI is styled with Tailwind CSS 4.

### Key Dependencies

| Package | Purpose |
|---------|---------|
| wagmi | React hooks for Ethereum |
| viem | TypeScript Ethereum library |
| @coinbase/onchainkit | Coinbase wallet integration |
| react-router-dom | Client-side routing |
| @tanstack/react-query | Data fetching and caching |

### Build Configuration

| Setting | Value |
|---------|-------|
| Build Command | `npm run build` (runs `tsc && vite build`) |
| Output Directory | `dist` |
| Node Version | 18+ recommended |
| Package Manager | npm |

### Deployment Recommendation

Deploy to **Cloudflare Pages** as a static site. The application has no server-side requirements and all blockchain interactions happen client-side through the user's wallet.

---

## aurum-website

The aurum-website repository contains the marketing landing page with a waitlist signup feature. Unlike aurum-app, this repository includes a Node.js server component.

### Technical Details

The frontend is built with React and Vite, similar to aurum-app. However, it also includes a server directory with Express-like functionality for handling form submissions and potentially other server-side operations. The project uses pnpm as its package manager.

### Build Configuration

| Setting | Value |
|---------|-------|
| Build Command | `pnpm run build` (runs `vite build && esbuild server/index.ts...`) |
| Start Command | `pnpm run start` (runs `node dist/index.js`) |
| Output Directory | `dist` |
| Package Manager | pnpm |

### Deployment Options

**Option 1: Full Deployment (Render)** - Deploy the complete application with server functionality to Render. This is recommended if you need the waitlist form to submit to a backend.

**Option 2: Static Deployment (Cloudflare Pages)** - Deploy only the static frontend to Cloudflare Pages. The waitlist form would need to submit to an external service (e.g., Mailchimp, ConvertKit, or your own API).

---

## aurum-backend

The aurum-backend repository is intended to house the API server for the prediction market platform. As of the current review, this repository contains only a README file and has not been implemented.

### Planned Functionality

When implemented, the backend should provide market data aggregation and caching, user portfolio tracking, market creation and management APIs, oracle integration for market resolution, and event indexing from blockchain.

### Recommended Tech Stack

For the backend implementation, consider using Node.js with Express or Fastify for the API server, PostgreSQL for the database, Redis for caching, and either Prisma or Drizzle as the ORM.

### Deployment Recommendation

When implemented, deploy to **Render** as a Web Service with a PostgreSQL database.

---

## aurum-contracts

The aurum-contracts repository contains all Solidity smart contracts for the prediction market platform. The contracts are developed using the Foundry framework.

### Deployed Contracts (Base Sepolia)

| Contract | Address | Purpose |
|----------|---------|---------|
| ConditionalTokens | `0x9A7A037469204604C29a44901b69B0bBB1d45B13` | ERC6909 outcome tokens |
| AurumToken | `0xdBfa3D8516C49581e2A6cBbD75F02F24c59811c1` | Governance token |
| FeeCollector | `0xBCC8aC562085E207460B9a0342d62a480DD9caAC` | Fee management |
| AurumOracle | `0x4EC0295F0344ac264EB83bd7bDb0069015702297` | Market resolution |
| MarketFactory | `0xaF8ddE93C551ce4f6A21db07508858Fb15E4bbC9` | Market creation |

### Security Features

The contracts include deadline protection on all trading operations, oracle staleness validation with a 7-day threshold, role-based access control for market creation and resolution, price bounds validation for oracle outcomes, reentrancy protection on all state-changing functions, and slippage protection on trades.

### Build Configuration

| Setting | Value |
|---------|-------|
| Framework | Foundry |
| Solidity Version | 0.8.33 |
| Optimizer | Enabled (200 runs + via_ir) |
| Test Coverage | 283 tests, 64.04% branch coverage |

---

## aurum-docs

The aurum-docs repository contains all project documentation, including architecture guides, deployment instructions, and API references.

### Documentation Structure

| Directory | Contents |
|-----------|----------|
| `/architecture` | System design and smart contract documentation |
| `/deployment` | Hosting setup guides and configuration |
| `/api` | Contract interaction guides and API references |

### Deployment Recommendation

Documentation can be hosted on **GitHub Pages** for free, or simply accessed directly from the GitHub repository.

---

## Deployment Architecture

The recommended deployment architecture for Aurum Labs consists of three main components.

### Frontend Layer

The aurum-app should be deployed to Cloudflare Pages, providing global CDN distribution, automatic HTTPS, and zero-cost hosting. Users access the trading interface through their web browsers and connect their wallets to interact with the blockchain.

### Marketing Layer

The aurum-website can be deployed to either Cloudflare Pages (static only) or Render (full functionality). The choice depends on whether server-side waitlist processing is required.

### Backend Layer (Future)

When aurum-backend is implemented, it should be deployed to Render with a PostgreSQL database. The backend will provide APIs for market data, user portfolios, and other off-chain functionality.

### Blockchain Layer

Smart contracts are deployed on Base Sepolia (testnet) and will be deployed to Base mainnet for production. All contract interactions happen through the frontend using the user's wallet.

---

## Environment Configuration

### Frontend Environment Variables

Both aurum-app and aurum-website require the following environment variables for blockchain connectivity:

| Variable | Description | Base Sepolia Value |
|----------|-------------|-------------------|
| `VITE_CHAIN_ID` | Chain ID | `84532` |
| `VITE_RPC_URL` | RPC endpoint | `https://sepolia.base.org` |
| `VITE_MARKET_FACTORY_ADDRESS` | MarketFactory contract | `0xaF8ddE93C551ce4f6A21db07508858Fb15E4bbC9` |
| `VITE_AURUM_ORACLE_ADDRESS` | AurumOracle contract | `0x4EC0295F0344ac264EB83bd7bDb0069015702297` |
| `VITE_AURUM_TOKEN_ADDRESS` | AurumToken contract | `0xdBfa3D8516C49581e2A6cBbD75F02F24c59811c1` |
| `VITE_CONDITIONAL_TOKENS_ADDRESS` | ConditionalTokens contract | `0x9A7A037469204604C29a44901b69B0bBB1d45B13` |
| `VITE_FEE_COLLECTOR_ADDRESS` | FeeCollector contract | `0xBCC8aC562085E207460B9a0342d62a480DD9caAC` |

### Backend Environment Variables (Template)

When aurum-backend is implemented, it will require database connection strings, JWT secrets, and blockchain configuration. See the [Render Setup Guide](./render-setup.md) for details.

---

## Next Steps

1. Deploy **aurum-app** to Cloudflare Pages following the [Cloudflare Setup Guide](./cloudflare-setup.md)
2. Deploy **aurum-website** to Render or Cloudflare following the appropriate guide
3. Implement **aurum-backend** when ready for production features
4. Deploy contracts to Base mainnet when ready for production
