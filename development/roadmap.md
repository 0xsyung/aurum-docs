# Development Roadmap

## Overview

This roadmap reflects the **current code state** and the planned next steps.

## Phase 1: Foundations (Current)

### Smart Contracts (aurum-contracts)

| Task | Status |
|------|--------|
| ConditionalTokens (ERC6909) implementation | ✅ Complete |
| ERC6909 helpers + tests | ✅ Complete |
| Deploy ConditionalTokens script | ✅ Complete |
| AurumToken / Oracle / MarketFactory / AMM / FeeCollector | ⏳ Not Started |

### Frontend (aurum-app)

| Task | Status |
|------|--------|
| Project setup (React + Vite + Tailwind) | ✅ Complete |
| Wallet UI (Coinbase connector) | ✅ Complete |
| Market list/detail UI (mock data) | ✅ Complete |
| Create market UI (placeholder) | ✅ Complete |
| Trading and on-chain integration | ⏳ Not Started |

### Website (aurum-website)

| Task | Status |
|------|--------|
| Landing page | ✅ Complete |
| Launch App link via `VITE_DAPP_URL` | ✅ Complete |

### Backend (aurum-backend)

| Task | Status |
|------|--------|
| Project setup | ⏳ Not Started |
| API / Indexer | ⏳ Not Started |

## Phase 2: Core Integrations (Next)

1. Define on-chain contract scope (MarketFactory/AMM/Oracle)
2. Implement missing contracts in `aurum-contracts`
3. Wire `aurum-app` to contracts (reads + transactions)
4. Decide whether a backend indexer is required

## Phase 3: Deployment & Testing (Future)

1. Deploy contracts to Base Sepolia
2. Configure app for testnet addresses
3. End-to-end testing on testnet
4. Prepare mainnet deployment plan
