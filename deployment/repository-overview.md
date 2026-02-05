# Aurum Labs Repository Overview

This document reflects the current state of each repository.

## Repository Summary

| Repository | Purpose | Tech Stack | Status |
|------------|---------|------------|--------|
| **aurum-app** | Prediction market UI prototype | React, Vite, wagmi | Prototype (mock data) |
| **aurum-website** | Landing page | React, Vite, Express | Active |
| **aurum-backend** | API server | TBD | Placeholder |
| **aurum-contracts** | Smart contracts | Solidity, Foundry | Prototype (ConditionalTokens only) |
| **aurum-docs** | Documentation | Markdown | Active |

---

## aurum-app

- **Current behavior**: UI-only prototype with mock data. No on-chain reads/writes yet.
- **Wallets**: Coinbase connector configured in `src/config/wagmi.ts`.
- **Deployment**: static site (Cloudflare Pages recommended).

## aurum-website

- **Current behavior**: Landing page served via a small Express server.
- **Config**: `VITE_DAPP_URL` controls the “Launch App” link.
- **Deployment**: static (Cloudflare Pages) or server (Render).

## aurum-backend

- **Status**: Placeholder only. No code beyond a README.
- **Deployment**: N/A until implemented.

## aurum-contracts

- **Current contracts**:
  - `ConditionalTokens.sol`
  - `ERC6909.sol`
  - `ERC6909Claims.sol`
- **Deployment**: no tracked deployments. Use `script/ConditionalTokens.s.sol` if you need a testnet deployment.

---

## Environment Configuration

At the moment, only `aurum-website` consumes environment variables (`VITE_DAPP_URL`). The `aurum-app` repo includes `.env.example`, but those variables are **not yet wired into the code**.
