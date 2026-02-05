# AURUM Token Economics (Planned)

## Status

The AURUM token is **not implemented** in `aurum-contracts` yet. This document describes a proposed design and should be treated as a plan, not current behavior.

## Overview

AURUM is the native governance and utility token of the Aurum Labs prediction market platform. It serves multiple purposes within the ecosystem.

## Token Details

| Property | Value |
|----------|-------|
| Name | Aurum Token |
| Symbol | AURUM |
| Decimals | 18 |
| Total Supply | 1,000,000,000 (1 billion) |
| Network | Base (Ethereum L2) |
| Standard | ERC-20 |

## Token Utility

### 1. Oracle Staking
- **Propose Outcomes**: Stake AURUM to propose market resolutions
- **Dispute Outcomes**: Stake AURUM to challenge incorrect proposals
- **Minimum Stake**: 100 AURUM

### 2. Governance Voting
- **Dispute Resolution**: Vote on contested market outcomes
- **Protocol Governance**: Vote on protocol upgrades and parameters
- **Voting Power**: 1 AURUM = 1 vote

### 3. Fee Sharing (Future)
- **Staking Rewards**: Earn portion of protocol fees
- **Redemption Fees**: 2% of winning redemptions distributed to stakers

## Token Distribution

### Recommended Distribution

Based on research of successful governance tokens (UMA, AAVE, Compound), here is the recommended distribution:

| Allocation | Percentage | Amount | Vesting |
|------------|------------|--------|---------|
| Treasury | 40% | 400,000,000 | Controlled by governance |
| Community & Ecosystem | 25% | 250,000,000 | Various programs |
| Team & Advisors | 15% | 150,000,000 | 4-year vesting, 1-year cliff |
| Investors | 10% | 100,000,000 | 2-year vesting, 6-month cliff |
| Initial Liquidity | 5% | 50,000,000 | Unlocked at launch |
| Airdrop | 5% | 50,000,000 | Immediate unlock |

### Distribution Rationale

**Treasury (40%)**
- Largest allocation for long-term sustainability
- Controlled by governance for future initiatives
- Can fund: development, partnerships, grants, buybacks

**Community & Ecosystem (25%)**
- Liquidity mining rewards
- Oracle participation rewards
- Bug bounties
- Developer grants
- Marketing campaigns

**Team & Advisors (15%)**
- Standard allocation for crypto projects
- 4-year vesting aligns long-term incentives
- 1-year cliff prevents immediate selling

**Investors (10%)**
- Seed and strategic investors
- 2-year vesting with 6-month cliff
- Shorter than team to reward early risk

**Initial Liquidity (5%)**
- DEX liquidity pools (Uniswap, Aerodrome)
- Ensures tradability at launch

**Airdrop (5%)**
- Early users and testers
- Community building
- Creates initial token distribution

### Comparison with Industry Standards

| Project | Team | Treasury | Community | Investors |
|---------|------|----------|-----------|-----------|
| UMA | 33.5% | 35% | 16.5% | 15% |
| AAVE | 13% | 30% | 40% | 17% |
| Compound | 26% | 43% | 23% | 8% |
| **Aurum** | **15%** | **40%** | **30%** | **10%** |

## Vesting Schedule

### Team & Advisors (15%)
```
Year 0: 0% (cliff)
Year 1: 25% unlocked
Year 2: 50% unlocked
Year 3: 75% unlocked
Year 4: 100% unlocked
```

### Investors (10%)
```
Month 0-6: 0% (cliff)
Month 6: 25% unlocked
Month 12: 50% unlocked
Month 18: 75% unlocked
Month 24: 100% unlocked
```

### Community & Ecosystem (25%)
```
Released over 4 years through:
- Liquidity mining: 10%
- Oracle rewards: 5%
- Grants & bounties: 5%
- Marketing: 5%
```

## Token Release Schedule

```
Year 1: ~25% of supply in circulation
Year 2: ~50% of supply in circulation
Year 3: ~75% of supply in circulation
Year 4: ~90% of supply in circulation
Year 5+: 100% (treasury releases via governance)
```

## Economic Model

### Value Accrual

AURUM accrues value through:

1. **Utility Demand**: Required for oracle participation
2. **Governance Rights**: Control over protocol parameters
3. **Fee Sharing**: Future staking rewards from protocol fees
4. **Scarcity**: Fixed supply with potential burns

### Fee Structure

| Fee Type | Rate | Distribution |
|----------|------|--------------|
| Redemption Fee | 2% | Treasury (100%) |
| Trading Fee | 0% | N/A (initially) |

**Future Fee Distribution** (after governance vote):
- 50% to AURUM stakers
- 30% to Treasury
- 20% to Liquidity providers

## Testnet Configuration

For Base Sepolia testnet:

| Parameter | Value |
|-----------|-------|
| Initial Mint | 1,000,000,000 AURUM |
| Mint To | Deployer wallet |
| Faucet Amount | 1,000 AURUM per request |

## Security Considerations

1. **No Infinite Mint**: Total supply is capped
2. **Vesting Contracts**: Team tokens locked in smart contracts
3. **Multisig Treasury**: Treasury controlled by multisig
4. **Governance Timelock**: 48-hour delay on governance actions
