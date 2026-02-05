# Frontend Deployment Guide (aurum-app)

This guide covers deploying the **aurum-app** UI prototype.

## Status Notes

- The app currently uses **mock market data**.
- Contract addresses in `.env.example` are **not yet wired into the code**.

## Prerequisites

- Node.js 18+
- Access to the `aurum-app` repository

## Build

```bash
cd ~/aurum-labs/aurum-app
npm install
npm run build
```

Build output is in `dist/`.

## Deployment Options

### Cloudflare Pages (Recommended)

1. Connect the repo to Cloudflare Pages
2. Build command: `npm run build`
3. Output directory: `dist`

### Vercel / Netlify

Use standard Vite static deployment settings.

## Optional Environment Variables

When the app starts consuming contract addresses, update these in your hosting provider:

```env
VITE_CHAIN_ID=84532
VITE_RPC_URL=https://sepolia.base.org
VITE_CONDITIONAL_TOKENS_ADDRESS=0x...
```
