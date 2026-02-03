# URL Configuration Guide

This guide explains how to update the Aurum Labs application with correct URLs after deployment.

## Overview

When deploying Aurum Labs, you need to configure URLs in multiple places:

1. **Frontend** - API URL, RPC URL, contract addresses
2. **Backend** - CORS origins, RPC URL, contract addresses
3. **Smart Contracts** - Already deployed, addresses need to be recorded

## URL Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Production URLs                          │
├─────────────────────────────────────────────────────────────┤
│  Frontend:     https://app.aurumlabs.io                     │
│  Backend API:  https://api.aurumlabs.io                     │
│  WebSocket:    wss://api.aurumlabs.io/ws                    │
│  RPC (Base):   https://mainnet.base.org                     │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                      Testnet URLs                            │
├─────────────────────────────────────────────────────────────┤
│  Frontend:     https://testnet.aurumlabs.io                 │
│  Backend API:  https://api-testnet.aurumlabs.io             │
│  WebSocket:    wss://api-testnet.aurumlabs.io/ws            │
│  RPC (Sepolia): https://sepolia.base.org                    │
└─────────────────────────────────────────────────────────────┘
```

## Step 1: Gather Your URLs

After deploying your services, collect the following URLs:

### Hosting URLs

| Service | Provider | URL Pattern |
|---------|----------|-------------|
| Frontend | Cloudflare Pages | `your-project.pages.dev` |
| Backend | Render | `your-service.onrender.com` |
| Custom Domain | Your DNS | `app.yourdomain.com` |

### Blockchain URLs

| Network | RPC URL |
|---------|---------|
| Base Mainnet | `https://mainnet.base.org` |
| Base Sepolia | `https://sepolia.base.org` |

### Contract Addresses

Collect addresses from your deployment:

| Contract | Testnet Address | Mainnet Address |
|----------|-----------------|-----------------|
| ConditionalTokens | `0x...` | `0x...` |
| MarketFactory | `0x...` | `0x...` |
| AurumToken | `0x...` | `0x...` |
| AurumOracle | `0x...` | `0x...` |
| FeeCollector | `0x...` | `0x...` |

## Step 2: Update Frontend Configuration

### Environment Variables

Update your frontend environment variables:

#### Cloudflare Pages

1. Go to your Pages project
2. Navigate to **Settings** > **Environment variables**
3. Update the following variables:

```env
# API Configuration
VITE_API_URL=https://api.aurumlabs.io

# Blockchain Configuration
VITE_CHAIN_ID=84532
VITE_RPC_URL=https://sepolia.base.org

# Contract Addresses
VITE_CONDITIONAL_TOKENS_ADDRESS=0x...
VITE_MARKET_FACTORY_ADDRESS=0x...
VITE_AURUM_TOKEN_ADDRESS=0x...
VITE_AURUM_ORACLE_ADDRESS=0x...
VITE_FEE_COLLECTOR_ADDRESS=0x...

# WalletConnect
VITE_WALLETCONNECT_PROJECT_ID=your-project-id
```

4. Click **Save**
5. Trigger a new deployment

#### Local Development

Update `.env.local`:

```env
VITE_API_URL=http://localhost:3001
VITE_CHAIN_ID=84532
VITE_RPC_URL=https://sepolia.base.org
VITE_CONDITIONAL_TOKENS_ADDRESS=0x...
VITE_MARKET_FACTORY_ADDRESS=0x...
VITE_AURUM_TOKEN_ADDRESS=0x...
VITE_AURUM_ORACLE_ADDRESS=0x...
VITE_FEE_COLLECTOR_ADDRESS=0x...
```

### Code Updates (if hardcoded)

If any URLs are hardcoded in the code, update them:

```typescript
// src/config/constants.ts
export const CONFIG = {
  API_URL: import.meta.env.VITE_API_URL || 'https://api.aurumlabs.io',
  CHAIN_ID: Number(import.meta.env.VITE_CHAIN_ID) || 84532,
  RPC_URL: import.meta.env.VITE_RPC_URL || 'https://sepolia.base.org',
};

export const CONTRACTS = {
  CONDITIONAL_TOKENS: import.meta.env.VITE_CONDITIONAL_TOKENS_ADDRESS,
  MARKET_FACTORY: import.meta.env.VITE_MARKET_FACTORY_ADDRESS,
  AURUM_TOKEN: import.meta.env.VITE_AURUM_TOKEN_ADDRESS,
  AURUM_ORACLE: import.meta.env.VITE_AURUM_ORACLE_ADDRESS,
  FEE_COLLECTOR: import.meta.env.VITE_FEE_COLLECTOR_ADDRESS,
};
```

## Step 3: Update Backend Configuration

### Environment Variables

Update your backend environment variables:

#### Render

1. Go to your Web Service
2. Navigate to **Environment**
3. Update the following variables:

```env
# Server Configuration
NODE_ENV=production
PORT=10000

# Database
DATABASE_URL=postgresql://...

# CORS - Update with your frontend URL
CORS_ORIGIN=https://app.aurumlabs.io

# Blockchain Configuration
RPC_URL=https://sepolia.base.org
CHAIN_ID=84532
PRIVATE_KEY=0x...

# Contract Addresses
CONDITIONAL_TOKENS_ADDRESS=0x...
MARKET_FACTORY_ADDRESS=0x...
AURUM_TOKEN_ADDRESS=0x...
AURUM_ORACLE_ADDRESS=0x...
FEE_COLLECTOR_ADDRESS=0x...
```

4. Click **Save Changes**
5. The service will automatically redeploy

### CORS Configuration

Ensure CORS is properly configured to allow your frontend domain:

```typescript
// src/middleware/cors.ts
const corsOptions = {
  origin: process.env.CORS_ORIGIN?.split(',') || ['https://app.aurumlabs.io'],
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'DELETE', 'OPTIONS'],
  allowedHeaders: ['Content-Type', 'Authorization'],
};
```

For multiple origins (e.g., staging + production):

```env
CORS_ORIGIN=https://app.aurumlabs.io,https://staging.aurumlabs.io
```

## Step 4: Update Contract Addresses

After deploying smart contracts, update addresses in all repositories.

### Frontend Repository

1. Update environment variables (see Step 2)
2. Update any hardcoded addresses in config files
3. Commit and push changes

### Backend Repository

1. Update environment variables (see Step 3)
2. Update any hardcoded addresses in config files
3. Commit and push changes

### Documentation Repository

Update `deployment/base-sepolia.md` with new addresses:

```markdown
## Deployed Contract Addresses

| Contract | Address | Verified |
|----------|---------|----------|
| ConditionalTokens | `0x...` | ✅ |
| MarketFactory | `0x...` | ✅ |
| AurumToken | `0x...` | ✅ |
| AurumOracle | `0x...` | ✅ |
| FeeCollector | `0x...` | ✅ |
```

## Step 5: Verify Configuration

### Frontend Verification

1. Open browser developer tools
2. Check Network tab for API calls
3. Verify requests go to correct API URL
4. Test wallet connection
5. Verify contract interactions work

### Backend Verification

1. Check health endpoint:
   ```bash
   curl https://api.aurumlabs.io/health
   ```

2. Verify CORS headers:
   ```bash
   curl -I -X OPTIONS https://api.aurumlabs.io/api/markets \
     -H "Origin: https://app.aurumlabs.io"
   ```

3. Test API endpoints:
   ```bash
   curl https://api.aurumlabs.io/api/markets
   ```

### Contract Verification

1. Check contract on block explorer
2. Verify contract is verified
3. Test read functions
4. Test write functions with test wallet

## Common URL Patterns

### Development

```
Frontend:  http://localhost:5173
Backend:   http://localhost:3001
RPC:       https://sepolia.base.org
```

### Staging

```
Frontend:  https://staging.aurumlabs.io
Backend:   https://api-staging.aurumlabs.io
RPC:       https://sepolia.base.org
```

### Production (Testnet)

```
Frontend:  https://testnet.aurumlabs.io
Backend:   https://api-testnet.aurumlabs.io
RPC:       https://sepolia.base.org
```

### Production (Mainnet)

```
Frontend:  https://app.aurumlabs.io
Backend:   https://api.aurumlabs.io
RPC:       https://mainnet.base.org
```

## Troubleshooting

### CORS Errors

**Symptom**: Browser console shows CORS errors

**Solution**:
1. Verify `CORS_ORIGIN` includes your frontend URL
2. Check for trailing slashes (should not have)
3. Ensure protocol matches (https vs http)
4. Redeploy backend after changes

### Contract Not Found

**Symptom**: "Contract not deployed" errors

**Solution**:
1. Verify contract address is correct
2. Check you're on the correct network
3. Verify contract is deployed on that network
4. Check RPC URL matches network

### API Connection Failed

**Symptom**: Frontend can't reach backend

**Solution**:
1. Verify API URL is correct
2. Check backend is running
3. Verify no firewall blocking
4. Check SSL certificate is valid

### Wrong Network

**Symptom**: Transactions fail or show wrong data

**Solution**:
1. Verify `CHAIN_ID` is correct
2. Check `RPC_URL` matches chain ID
3. Ensure wallet is on correct network
4. Update contract addresses for correct network

## URL Update Checklist

Use this checklist when updating URLs:

### Frontend
- [ ] Update `VITE_API_URL`
- [ ] Update `VITE_RPC_URL`
- [ ] Update `VITE_CHAIN_ID`
- [ ] Update all contract addresses
- [ ] Redeploy frontend
- [ ] Verify API calls work
- [ ] Test wallet connection

### Backend
- [ ] Update `CORS_ORIGIN`
- [ ] Update `RPC_URL`
- [ ] Update `CHAIN_ID`
- [ ] Update all contract addresses
- [ ] Update `DATABASE_URL` if changed
- [ ] Redeploy backend
- [ ] Verify health check
- [ ] Test API endpoints

### Documentation
- [ ] Update contract addresses in docs
- [ ] Update URL examples
- [ ] Commit and push changes

## Next Steps

- [Deploy smart contracts to testnet](./base-sepolia.md)
- [Set up Cloudflare Pages](./cloudflare-setup.md)
- [Set up Render backend](./render-setup.md)
