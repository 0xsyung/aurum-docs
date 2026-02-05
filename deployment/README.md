# Aurum Labs Deployment Documentation

This directory documents deployment steps that match the **current** codebase. The backend is not implemented and the contracts repo only contains `ConditionalTokens`.

## Quick Links

- [Repository Overview](./repository-overview.md)
- [Base Sepolia Notes](./base-sepolia.md)
- [Cloudflare Pages Setup](./cloudflare-setup.md)
- [Render Setup](./render-setup.md)
- [Frontend Deployment](./frontend-deployment.md)
- [Backend Deployment](./backend-deployment.md)
- [URL Configuration](./url-configuration.md)

## Current Deployment Status

| Component | Status | Notes |
|-----------|--------|-------|
| aurum-contracts | Prototype | Only ConditionalTokens exists; no tracked deployments |
| aurum-app | Prototype | UI only; mock data |
| aurum-website | Active | Static landing page |
| aurum-backend | Placeholder | Not implemented |

## Deployment Order (Current)

1. Deploy **aurum-website** (static site)
2. Deploy **aurum-app** (static site)
3. Optionally deploy **ConditionalTokens** to a testnet and record the address

## Environment Variables (Current Reality)

- `aurum-app` does **not** yet read contract addresses from env vars.
- `aurum-website` uses `VITE_DAPP_URL` to link the “Launch App” button.

Once the app and backend start consuming env vars, update these docs with concrete templates.
