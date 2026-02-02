# Development Roadmap

## Overview

This document outlines the development plan for Aurum Labs prediction market platform. The development is divided into phases, with each phase building on the previous one.

## Phase 1: Foundation (Current)

**Duration**: 2-3 weeks
**Status**: In Progress

### Smart Contracts (aurum-contracts)

| Task | Priority | Status |
|------|----------|--------|
| AurumToken.sol - ERC20 governance token | High | Not Started |
| AurumOracle.sol - Optimistic oracle | High | Not Started |
| MarketFactory.sol - Market creation | High | Not Started |
| MarketAMM.sol - Trading AMM | High | Not Started |
| FeeCollector.sol - Fee management | Medium | Not Started |
| Unit tests for all contracts | High | Not Started |
| Integration tests | High | Not Started |
| Deploy to Base Sepolia | High | Not Started |

### Documentation (aurum-docs)

| Task | Priority | Status |
|------|----------|--------|
| Architecture overview | High | ✅ Complete |
| Smart contract specs | High | ✅ Complete |
| Oracle system design | High | ✅ Complete |
| Tokenomics | High | ✅ Complete |
| Development roadmap | High | ✅ Complete |

---

## Phase 2: Core dApp (Next)

**Duration**: 3-4 weeks
**Status**: Not Started

### Frontend (aurum-app)

| Task | Priority | Status |
|------|----------|--------|
| Project setup (React + Vite + Tailwind) | High | Not Started |
| Wallet connection (RainbowKit/wagmi) | High | Not Started |
| Market listing page | High | Not Started |
| Market detail page | High | Not Started |
| Trading interface | High | Not Started |
| Portfolio page | High | Not Started |
| Transaction history | Medium | Not Started |
| Mobile responsive design | Medium | Not Started |

### Backend (aurum-backend)

| Task | Priority | Status |
|------|----------|--------|
| Project setup (Node.js + TypeScript) | High | Not Started |
| Event indexer service | High | Not Started |
| REST API endpoints | High | Not Started |
| Price calculation service | High | Not Started |
| Database schema (PostgreSQL) | High | Not Started |
| WebSocket for real-time updates | Medium | Not Started |

---

## Phase 3: Integration & Testing

**Duration**: 2 weeks
**Status**: Not Started

### Integration Tasks

| Task | Priority | Status |
|------|----------|--------|
| Connect frontend to smart contracts | High | Not Started |
| Connect frontend to backend API | High | Not Started |
| End-to-end testing | High | Not Started |
| Performance optimization | Medium | Not Started |
| Security review | High | Not Started |

### Testing Checklist

- [ ] Create market flow
- [ ] Buy outcome tokens flow
- [ ] Sell outcome tokens flow
- [ ] Propose outcome flow
- [ ] Dispute outcome flow
- [ ] Vote on dispute flow
- [ ] Redeem winnings flow
- [ ] Add/remove liquidity flow

---

## Phase 4: Website Update

**Duration**: 1 week
**Status**: Not Started

### aurum-website Updates

| Task | Priority | Status |
|------|----------|--------|
| Add "Launch App" button | High | Not Started |
| Link to dApp | High | Not Started |
| Update product description | Medium | Not Started |
| Add documentation links | Low | Not Started |

---

## Phase 5: Beta Launch

**Duration**: 2 weeks
**Status**: Not Started

### Beta Tasks

| Task | Priority | Status |
|------|----------|--------|
| Deploy to Base Sepolia (final) | High | Not Started |
| Create test markets | High | Not Started |
| Distribute testnet AURUM | High | Not Started |
| Bug bounty program | Medium | Not Started |
| Community testing | High | Not Started |
| Collect feedback | High | Not Started |

---

## Phase 6: Mainnet Preparation (Future)

**Duration**: 4+ weeks
**Status**: Not Started

### Mainnet Tasks

| Task | Priority | Status |
|------|----------|--------|
| Smart contract audit | Critical | Not Started |
| Security fixes | Critical | Not Started |
| Mainnet deployment | Critical | Not Started |
| Liquidity bootstrapping | High | Not Started |
| Token distribution | High | Not Started |
| Marketing launch | Medium | Not Started |

---

## GitHub Issues Summary

### aurum-contracts Repository

1. **[HIGH]** Implement AurumToken.sol
2. **[HIGH]** Implement AurumOracle.sol
3. **[HIGH]** Implement MarketFactory.sol
4. **[HIGH]** Implement MarketAMM.sol
5. **[MEDIUM]** Implement FeeCollector.sol
6. **[HIGH]** Write unit tests
7. **[HIGH]** Deploy to Base Sepolia

### aurum-app Repository

1. **[HIGH]** Initialize React project
2. **[HIGH]** Implement wallet connection
3. **[HIGH]** Build market listing page
4. **[HIGH]** Build trading interface
5. **[HIGH]** Build portfolio page
6. **[MEDIUM]** Add Web3Auth login

### aurum-backend Repository

1. **[HIGH]** Initialize Node.js project
2. **[HIGH]** Build event indexer
3. **[HIGH]** Create REST API
4. **[MEDIUM]** Add WebSocket support

### aurum-website Repository

1. **[HIGH]** Add "Launch App" button

---

## Timeline Summary

```
Week 1-3:   Phase 1 - Smart Contracts & Documentation
Week 4-7:   Phase 2 - Frontend & Backend Development
Week 8-9:   Phase 3 - Integration & Testing
Week 10:    Phase 4 - Website Update
Week 11-12: Phase 5 - Beta Launch
Week 13+:   Phase 6 - Mainnet Preparation
```

## Success Metrics

### Phase 1 Success
- [ ] All smart contracts deployed to Base Sepolia
- [ ] All contracts verified on Basescan
- [ ] Unit test coverage > 80%

### Phase 2 Success
- [ ] Users can connect wallet
- [ ] Users can browse markets
- [ ] Users can buy/sell tokens

### Phase 3 Success
- [ ] All flows work end-to-end
- [ ] No critical bugs
- [ ] Performance < 3s page load

### Beta Success
- [ ] 100+ test users
- [ ] 10+ test markets
- [ ] < 5 critical bugs reported
