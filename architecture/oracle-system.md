# Aurum Oracle System Design

## Overview

The Aurum Oracle is an optimistic oracle system inspired by UMA's design. It allows anyone with sufficient AURUM tokens to propose market outcomes, with a challenge mechanism to ensure accuracy.

## Design Philosophy

The oracle operates on the principle of **optimistic verification**:
1. Proposers are assumed to be honest
2. Outcomes are accepted unless disputed
3. Economic incentives ensure honest behavior
4. Disputes are resolved by token holder voting

## Oracle Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           AURUM ORACLE FLOW                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌──────────────┐                                                          │
│  │   Market     │                                                          │
│  │   Ends       │                                                          │
│  └──────┬───────┘                                                          │
│         │                                                                   │
│         ▼                                                                   │
│  ┌──────────────┐     Stake 100 AURUM                                      │
│  │   Proposer   │────────────────────▶ Propose Outcome                     │
│  │   Stakes     │                                                          │
│  └──────┬───────┘                                                          │
│         │                                                                   │
│         ▼                                                                   │
│  ┌──────────────┐                                                          │
│  │  Challenge   │     Binary: 2 hours                                      │
│  │   Period     │     Categorical: 24 hours                                │
│  └──────┬───────┘                                                          │
│         │                                                                   │
│    ┌────┴────┐                                                             │
│    │         │                                                             │
│    ▼         ▼                                                             │
│  ┌─────┐  ┌─────────┐                                                      │
│  │ No  │  │Disputed │     Disputer stakes 100 AURUM                        │
│  │Disp.│  │         │                                                      │
│  └──┬──┘  └────┬────┘                                                      │
│     │          │                                                            │
│     ▼          ▼                                                            │
│  ┌──────┐  ┌──────────┐                                                    │
│  │Finali│  │  Voting  │     48-hour voting period                          │
│  │  ze  │  │  Period  │     AURUM holders vote                             │
│  └──┬───┘  └────┬─────┘                                                    │
│     │           │                                                           │
│     │      ┌────┴────┐                                                     │
│     │      │         │                                                     │
│     │      ▼         ▼                                                     │
│     │  ┌───────┐ ┌───────┐                                                 │
│     │  │Proposer│ │Disputer│                                               │
│     │  │ Wins  │ │ Wins   │                                                │
│     │  └───┬───┘ └───┬───┘                                                 │
│     │      │         │                                                     │
│     │      ▼         ▼                                                     │
│     │  Disputer   Proposer                                                 │
│     │  Slashed    Slashed                                                  │
│     │                                                                       │
│     ▼                                                                       │
│  ┌──────────────┐                                                          │
│  │   Market     │                                                          │
│  │  Resolved    │                                                          │
│  └──────────────┘                                                          │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

## Parameters

### Staking Requirements

| Parameter | Value | Rationale |
|-----------|-------|-----------|
| Minimum Proposal Stake | 100 AURUM | Low enough to encourage participation, high enough to deter spam |
| Minimum Dispute Stake | 100 AURUM | Equal to proposal stake for fairness |
| Slashing Rate | 100% | Full stake lost on incorrect proposal/dispute |

### Challenge Periods

| Market Type | Challenge Period | Rationale |
|-------------|------------------|-----------|
| Binary (2 outcomes) | 2 hours | Simple yes/no questions are easy to verify |
| Categorical (3+ outcomes) | 24 hours | Complex outcomes need more time for verification |

**Research Note**: Polymarket uses a 2-hour challenge period for all markets. However, for categorical markets with more complexity, a longer period is recommended to allow thorough verification.

### Voting Period

| Parameter | Value | Rationale |
|-----------|-------|-----------|
| Voting Duration | 48 hours | Allows global participation across time zones |
| Quorum | 10% of staked AURUM | Ensures sufficient participation |
| Passing Threshold | 51% majority | Simple majority for clear outcomes |

## Economic Security

### Cost of Attack Analysis

For the oracle to be secure, the cost of corrupting it must exceed the potential profit:

```
Cost to Corrupt = (51% of voting AURUM) × (AURUM price)
Profit from Corruption = Market liquidity at risk

Security Condition: Cost to Corrupt > Profit from Corruption
```

**Example**:
- Total AURUM supply: 1,000,000,000
- Circulating supply: 200,000,000 (20%)
- Staked for voting: 50,000,000 (25% of circulating)
- Required to corrupt: 25,500,000 AURUM (51%)
- At $0.01/AURUM: $255,000 to corrupt

Markets should be limited to liquidity below the cost of corruption.

### Incentive Structure

| Role | Correct Behavior | Incorrect Behavior |
|------|------------------|-------------------|
| Proposer | Receives stake back + small reward | Loses entire stake |
| Disputer | Receives stake back + proposer's stake | Loses entire stake |
| Voter | Receives voting rewards | No penalty (but no reward if wrong) |

## Implementation Details

### Proposal Structure

```solidity
struct Proposal {
    bytes32 marketId;           // Market being resolved
    address proposer;           // Address that proposed
    uint256[] payouts;          // Payout numerators per outcome
    uint256 proposedAt;         // Timestamp of proposal
    uint256 stake;              // Amount staked
    bool disputed;              // Whether disputed
    address disputer;           // Address that disputed (if any)
    uint256 disputeStake;       // Disputer's stake
    bool finalized;             // Whether finalized
    uint256 finalizedAt;        // Timestamp of finalization
}
```

### Voting Structure

```solidity
struct Vote {
    bytes32 marketId;           // Market being voted on
    address voter;              // Voter address
    uint256[] payouts;          // Voted payout distribution
    uint256 weight;             // Voting weight (AURUM balance)
    bytes32 commitHash;         // Hash of vote (for commit-reveal)
    bool revealed;              // Whether vote revealed
}
```

### Commit-Reveal Voting

To prevent vote copying, we use commit-reveal:

1. **Commit Phase** (24 hours): Voters submit hash of their vote
   ```
   commitHash = keccak256(abi.encodePacked(marketId, payouts, salt))
   ```

2. **Reveal Phase** (24 hours): Voters reveal their actual vote
   ```
   reveal(marketId, payouts, salt)
   ```

3. **Tally**: Votes are counted after reveal phase ends

## Comparison with UMA

| Feature | UMA | Aurum Oracle |
|---------|-----|--------------|
| Token | UMA | AURUM |
| Default Challenge Period | 2 hours | 2 hours (binary), 24 hours (categorical) |
| Voting Period | 48-96 hours | 48 hours |
| Minimum Stake | Variable | 100 AURUM |
| Dispute Mechanism | DVM voting | AURUM holder voting |
| Commit-Reveal | Yes | Yes |

## Edge Cases

### 1. No Proposal After Market End
- Grace period: 7 days for someone to propose
- After grace period: Market can be voided, collateral returned

### 2. Tie in Voting
- If exactly 50-50: Proposal stands (proposer wins)
- Rationale: Benefit of doubt to original proposer

### 3. Insufficient Quorum
- If quorum not reached: Voting period extended by 24 hours
- Maximum 2 extensions, then proposal stands

### 4. Market Voided
- If outcome cannot be determined (e.g., event cancelled)
- Admin can void market
- All positions redeemed at equal value

## Future Improvements

1. **Reputation System**: Track proposer accuracy for reduced stake requirements
2. **Delegated Voting**: Allow AURUM holders to delegate voting power
3. **Multiple Oracles**: Support for Chainlink price feeds for certain markets
4. **Insurance Fund**: Protocol insurance for oracle failures
