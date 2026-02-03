# Frontend Deployment Guide

Complete guide for deploying the Aurum Labs frontend application to production.

## Overview

The Aurum Labs frontend is a React application built with Vite, designed to interact with the Aurum prediction market smart contracts on Base Sepolia (testnet) or Base Mainnet (production).

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Cloudflare Pages                         │
│  ┌─────────────────────────────────────────────────────┐   │
│  │              Aurum Labs Frontend                     │   │
│  │  ┌─────────┐  ┌─────────┐  ┌─────────────────────┐  │   │
│  │  │  React  │  │  Vite   │  │  WalletConnect/RPC  │  │   │
│  │  └─────────┘  └─────────┘  └─────────────────────┘  │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      Base Network                           │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐ │
│  │ Conditional │  │   Market    │  │      Aurum          │ │
│  │   Tokens    │  │   Factory   │  │      Oracle         │ │
│  └─────────────┘  └─────────────┘  └─────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

## Prerequisites

- Node.js 18+ installed locally
- Access to the `aurum-frontend` repository
- Cloudflare account (see [Cloudflare Setup Guide](./cloudflare-setup.md))
- Deployed smart contracts (see [Base Sepolia Deployment](./base-sepolia.md))

## Local Development Setup

### 1. Clone the Repository

```bash
git clone https://github.com/0xsyung/aurum-frontend.git
cd aurum-frontend
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Configure Environment Variables

Create a `.env.local` file:

```env
# API Configuration
VITE_API_URL=http://localhost:3001

# Blockchain Configuration
VITE_CHAIN_ID=84532
VITE_RPC_URL=https://sepolia.base.org

# Contract Addresses (Base Sepolia)
VITE_CONDITIONAL_TOKENS_ADDRESS=0x...
VITE_MARKET_FACTORY_ADDRESS=0x...
VITE_AURUM_TOKEN_ADDRESS=0x...
VITE_AURUM_ORACLE_ADDRESS=0x...
VITE_FEE_COLLECTOR_ADDRESS=0x...

# WalletConnect
VITE_WALLETCONNECT_PROJECT_ID=your-project-id
```

### 4. Start Development Server

```bash
npm run dev
```

The application will be available at `http://localhost:5173`.

## Build Process

### Production Build

```bash
npm run build
```

This creates an optimized production build in the `dist/` directory.

### Build Verification

```bash
npm run preview
```

Preview the production build locally before deploying.

## Deployment Options

### Option 1: Cloudflare Pages (Recommended)

See the [Cloudflare Setup Guide](./cloudflare-setup.md) for detailed instructions.

**Quick Deploy:**

1. Connect repository to Cloudflare Pages
2. Set build command: `npm run build`
3. Set output directory: `dist`
4. Add environment variables
5. Deploy

### Option 2: Vercel

1. Import project from GitHub
2. Configure build settings:
   - Framework: Vite
   - Build Command: `npm run build`
   - Output Directory: `dist`
3. Add environment variables
4. Deploy

### Option 3: Netlify

1. Connect repository
2. Configure build settings:
   - Build Command: `npm run build`
   - Publish Directory: `dist`
3. Add environment variables in Site Settings
4. Deploy

### Option 4: Self-Hosted (Nginx)

1. Build the application:
   ```bash
   npm run build
   ```

2. Configure Nginx:
   ```nginx
   server {
       listen 80;
       server_name app.aurumlabs.io;
       root /var/www/aurum-frontend/dist;
       index index.html;

       location / {
           try_files $uri $uri/ /index.html;
       }

       location /assets {
           expires 1y;
           add_header Cache-Control "public, immutable";
       }
   }
   ```

3. Enable SSL with Certbot:
   ```bash
   sudo certbot --nginx -d app.aurumlabs.io
   ```

## Environment Configuration

### Development Environment

```env
VITE_API_URL=http://localhost:3001
VITE_CHAIN_ID=84532
VITE_RPC_URL=https://sepolia.base.org
```

### Staging Environment

```env
VITE_API_URL=https://api-staging.aurumlabs.io
VITE_CHAIN_ID=84532
VITE_RPC_URL=https://sepolia.base.org
```

### Production Environment

```env
VITE_API_URL=https://api.aurumlabs.io
VITE_CHAIN_ID=8453
VITE_RPC_URL=https://mainnet.base.org
```

## Contract Address Configuration

### Base Sepolia (Testnet)

| Contract | Address |
|----------|---------|
| ConditionalTokens | `0x...` |
| MarketFactory | `0x...` |
| AurumToken | `0x...` |
| AurumOracle | `0x...` |
| FeeCollector | `0x...` |

> **Note**: See [Base Sepolia Deployment](./base-sepolia.md) for current addresses.

### Base Mainnet (Production)

| Contract | Address |
|----------|---------|
| ConditionalTokens | `TBD` |
| MarketFactory | `TBD` |
| AurumToken | `TBD` |
| AurumOracle | `TBD` |
| FeeCollector | `TBD` |

## WalletConnect Setup

1. Create a project at [WalletConnect Cloud](https://cloud.walletconnect.com/)
2. Copy the Project ID
3. Add to environment variables:
   ```env
   VITE_WALLETCONNECT_PROJECT_ID=your-project-id
   ```

## Performance Optimization

### Code Splitting

The application uses dynamic imports for route-based code splitting:

```typescript
const MarketPage = lazy(() => import('./pages/Market'));
const PortfolioPage = lazy(() => import('./pages/Portfolio'));
```

### Asset Optimization

- Images are optimized during build
- CSS is purged and minified
- JavaScript is tree-shaken and minified

### Caching Strategy

Configure caching headers for optimal performance:

```
/assets/*
  Cache-Control: public, max-age=31536000, immutable

/index.html
  Cache-Control: no-cache, no-store, must-revalidate
```

## Monitoring and Analytics

### Error Tracking (Sentry)

Add Sentry for error tracking:

```env
VITE_SENTRY_DSN=https://...@sentry.io/...
```

### Analytics

The application supports various analytics providers. Configure in environment:

```env
VITE_ANALYTICS_ID=your-analytics-id
```

## Troubleshooting

### Build Errors

**Issue**: TypeScript errors during build
```bash
# Check for type errors
npm run type-check

# Fix common issues
npm run lint:fix
```

**Issue**: Missing environment variables
```bash
# Verify all required variables are set
cat .env.local
```

### Runtime Errors

**Issue**: Contract interaction failures
- Verify contract addresses are correct
- Check network configuration matches deployed contracts
- Ensure wallet is connected to correct network

**Issue**: WalletConnect not working
- Verify Project ID is correct
- Check browser console for errors
- Try clearing local storage

### Deployment Errors

**Issue**: Build fails on Cloudflare
- Check Node.js version compatibility
- Verify build command is correct
- Review build logs for specific errors

**Issue**: Environment variables not working
- Ensure variables are prefixed with `VITE_`
- Redeploy after adding variables
- Check for typos

## Security Checklist

- [ ] All secrets stored in environment variables
- [ ] HTTPS enabled
- [ ] CSP headers configured
- [ ] No sensitive data in client-side code
- [ ] API endpoints validated
- [ ] Rate limiting configured on API

## Rollback Procedure

### Cloudflare Pages

1. Go to Deployments
2. Find the previous working deployment
3. Click "Rollback to this deployment"

### Manual Rollback

```bash
# Revert to previous commit
git revert HEAD
git push origin main
```

## Next Steps

- [Set up backend on Render](./render-setup.md)
- [Configure URL updates](./url-configuration.md)
- [Deploy smart contracts](./base-sepolia.md)
