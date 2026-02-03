# Aurum Labs Deployment Documentation

This directory contains comprehensive deployment guides for the Aurum Labs prediction market platform.

## Quick Links

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

## Deployment Order

For a complete deployment, follow this order:

1. **Deploy Smart Contracts** → [Base Sepolia Guide](./base-sepolia.md)
2. **Set Up Hosting Accounts**
   - [Cloudflare Pages](./cloudflare-setup.md) for frontend
   - [Render](./render-setup.md) for backend
3. **Deploy Backend** → [Backend Deployment](./backend-deployment.md)
4. **Deploy Frontend** → [Frontend Deployment](./frontend-deployment.md)
5. **Configure URLs** → [URL Configuration](./url-configuration.md)

## Current Deployment Status

### Base Sepolia Testnet

| Component | Status | URL/Address |
|-----------|--------|-------------|
| ConditionalTokens | ✅ Deployed | `0x9A7A037469204604C29a44901b69B0bBB1d45B13` |
| AurumToken | ✅ Deployed | `0xdBfa3D8516C49581e2A6cBbD75F02F24c59811c1` |
| FeeCollector | ✅ Deployed | `0xBCC8aC562085E207460B9a0342d62a480DD9caAC` |
| AurumOracle | ✅ Deployed | `0x4EC0295F0344ac264EB83bd7bDb0069015702297` |
| MarketFactory | ✅ Deployed | `0xaF8ddE93C551ce4f6A21db07508858Fb15E4bbC9` |
| Frontend | ⏳ Pending | - |
| Backend | ⏳ Pending | - |

### Base Mainnet

| Component | Status |
|-----------|--------|
| Smart Contracts | ⏳ Not deployed |
| Frontend | ⏳ Not deployed |
| Backend | ⏳ Not deployed |

## Environment Variables Template

```env
# Network Configuration
VITE_CHAIN_ID=84532
VITE_RPC_URL=https://sepolia.base.org

# Contract Addresses (Base Sepolia)
VITE_CONDITIONAL_TOKENS_ADDRESS=0x9A7A037469204604C29a44901b69B0bBB1d45B13
VITE_AURUM_TOKEN_ADDRESS=0xdBfa3D8516C49581e2A6cBbD75F02F24c59811c1
VITE_FEE_COLLECTOR_ADDRESS=0xBCC8aC562085E207460B9a0342d62a480DD9caAC
VITE_AURUM_ORACLE_ADDRESS=0x4EC0295F0344ac264EB83bd7bDb0069015702297
VITE_MARKET_FACTORY_ADDRESS=0xaF8ddE93C551ce4f6A21db07508858Fb15E4bbC9

# API Configuration
VITE_API_URL=https://api.aurumlabs.io
```

## Support

For deployment issues, please open an issue in the respective repository:
- Smart Contracts: [aurum-contracts](https://github.com/0xsyung/aurum-contracts)
- Documentation: [aurum-docs](https://github.com/0xsyung/aurum-docs)
