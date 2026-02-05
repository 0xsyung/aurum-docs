# Local Development Setup Guide

This guide reflects the **current** state of the codebase. The dApp UI is a prototype with mock data, the backend is not implemented, and the contracts repo only contains `ConditionalTokens`.

## Prerequisites

| Tool | Version | Purpose |
|------|---------|---------|
| Node.js | 18+ | Frontend tooling |
| pnpm | 8+ | aurum-website package manager |
| Foundry | Latest | Contract development (optional) |
| Git | 2.30+ | Version control |

## Step 1: Clone Repositories

```bash
mkdir -p ~/aurum-labs && cd ~/aurum-labs

git clone https://github.com/0xsyung/aurum-contracts.git
git clone https://github.com/0xsyung/aurum-app.git
git clone https://github.com/0xsyung/aurum-website.git
git clone https://github.com/0xsyung/aurum-docs.git
```

## Step 2: Contracts (Optional)

The contracts repo currently includes **only** `ConditionalTokens` plus ERC6909 helpers.

### 2.1 Install + Test

```bash
cd ~/aurum-labs/aurum-contracts
forge install
forge test
```

### 2.2 Run a Local Chain

```bash
anvil --mnemonic "test test test test test test test test test test test junk"
```

### 2.3 Deploy ConditionalTokens

```bash
cd ~/aurum-labs/aurum-contracts
export RPC_URL=http://127.0.0.1:8545
export PRIVATE_KEY=0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80

forge script script/ConditionalTokens.s.sol:ConditionalTokensScript \
  --rpc-url $RPC_URL \
  --private-key $PRIVATE_KEY \
  --broadcast
```

Record the deployed address if you plan to wire the UI later.

## Step 3: Run aurum-app (UI Prototype)

```bash
cd ~/aurum-labs/aurum-app
npm install
npm run dev
```

Notes:
- The UI uses **mock markets** and does not yet read contract addresses from env vars.
- Wallet connection UI is present, but trading actions are placeholders.

## Step 4: Run aurum-website (Landing Page)

```bash
cd ~/aurum-labs/aurum-website
pnpm install
pnpm run dev
```

If you need to change where the “Launch App” button points, edit `VITE_DAPP_URL` in `.env`.
