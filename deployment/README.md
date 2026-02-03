# Aurum Labs Deployment Documentation

This directory contains comprehensive deployment guides for the Aurum Labs prediction market platform.

## Quick Links

### Getting Started
- [Repository Overview](./repository-overview.md) - Understanding all Aurum Labs repositories

### Smart Contracts
- [Base Sepolia Deployment](./base-sepolia.md) - Testnet contract addresses and configuration

### Hosting Setup
- [Cloudflare Setup Guide](./cloudflare-setup.md) - Frontend hosting on Cloudflare Pages
- [Render Setup Guide](./render-setup.md) - Backend hosting on Render

### Application Deployment
- [Frontend Deployment](./frontend-deployment.md) - Deploy the React frontend application
- [Backend Deployment](./backend-deployment.md) - Deploy the Node.js backend API

### Configuration
- [URL Configuration Guide](./url-configuration.md) - Update application URLs after deployment

## Repository Summary

| Repository | Purpose | Status | Deployment Target |
|------------|---------|--------|-------------------|
| **aurum-app** | Trading interface | Active | Cloudflare Pages |
| **aurum-website** | Landing page | Active | Render or Cloudflare |
| **aurum-backend** | API server | Not implemented | Render |
| **aurum-contracts** | Smart contracts | Deployed | Base Sepolia |

For detailed information about each repository, see the [Repository Overview](./repository-overview.md).

## Deployment Order

For a complete deployment, follow this order:

1. **Deploy Smart Contracts** → Already deployed on [Base Sepolia](./base-sepolia.md)
2. **Set Up Hosting Accounts**
   - [Cloudflare Pages](./cloudflare-setup.md) for aurum-app
   - [Render](./render-setup.md) for aurum-website (if using server features)
3. **Deploy aurum-app** → [Cloudflare Setup Guide](./cloudflare-setup.md)
4. **Deploy aurum-website** → [Render Setup Guide](./render-setup.md) or Cloudflare
5. **Configure URLs** → [URL Configuration](./url-configuration.md)

## Current Deployment Status

### Base Sepolia Testnet

| Component | Status | Address/URL |
|-----------|--------|-------------|
| ConditionalTokens | ✅ Deployed | `0x9A7A037469204604C29a44901b69B0bBB1d45B13` |
| AurumToken | ✅ Deployed | `0xdBfa3D8516C49581e2A6cBbD75F02F24c59811c1` |
| FeeCollector | ✅ Deployed | `0xBCC8aC562085E207460B9a0342d62a480DD9caAC` |
| AurumOracle | ✅ Deployed | `0x4EC0295F0344ac264EB83bd7bDb0069015702297` |
| MarketFactory | ✅ Deployed | `0xaF8ddE93C551ce4f6A21db07508858Fb15E4bbC9` |
| aurum-app | ⏳ Pending | - |
| aurum-website | ⏳ Pending | - |
| aurum-backend | ❌ Not implemented | - |

### Base Mainnet

| Component | Status |
|-----------|--------|
| Smart Contracts | ⏳ Not deployed |
| Frontend | ⏳ Not deployed |
| Backend | ⏳ Not deployed |

## Environment Variables Template

Copy these environment variables to your deployment configuration:

```env
# Network Configuration
VITE_CHAIN_ID=84532
VITE_RPC_URL=https://sepolia.base.org
VITE_BLOCK_EXPLORER=https://sepolia.basescan.org

# Contract Addresses (Base Sepolia)
VITE_CONDITIONAL_TOKENS_ADDRESS=0x9A7A037469204604C29a44901b69B0bBB1d45B13
VITE_AURUM_TOKEN_ADDRESS=0xdBfa3D8516C49581e2A6cBbD75F02F24c59811c1
VITE_FEE_COLLECTOR_ADDRESS=0xBCC8aC562085E207460B9a0342d62a480DD9caAC
VITE_AURUM_ORACLE_ADDRESS=0x4EC0295F0344ac264EB83bd7bDb0069015702297
VITE_MARKET_FACTORY_ADDRESS=0xaF8ddE93C551ce4f6A21db07508858Fb15E4bbC9
VITE_USDC_ADDRESS=0x036CbD53842c5426634e7929541eC2318f3dCF7e

# Application Configuration
VITE_API_URL=https://api.aurumlabs.io
VITE_WALLETCONNECT_PROJECT_ID=your-project-id
VITE_TRANSACTION_DEADLINE_MINUTES=60
VITE_SLIPPAGE_TOLERANCE_PERCENT=1
```

## Support

For deployment issues, please open an issue in the respective repository:

| Repository | GitHub Link |
|------------|-------------|
| Smart Contracts | [aurum-contracts](https://github.com/0xsyung/aurum-contracts) |
| Trading App | [aurum-app](https://github.com/0xsyung/aurum-app) |
| Landing Page | [aurum-website](https://github.com/0xsyung/aurum-website) |
| Backend API | [aurum-backend](https://github.com/0xsyung/aurum-backend) |
| Documentation | [aurum-docs](https://github.com/0xsyung/aurum-docs) |
