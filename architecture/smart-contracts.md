# Smart Contract Architecture

## Overview

The Aurum Labs smart contract system is built on Foundry and deployed on Base. It consists of six main contracts that work together to provide prediction market functionality.

## Contract Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         SMART CONTRACT ARCHITECTURE                          │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                          GOVERNANCE LAYER                            │   │
│  │  ┌──────────────────┐                                               │   │
│  │  │   AurumToken     │  ERC20 governance token                       │   │
│  │  │   (AURUM)        │  - Staking for oracle                         │   │
│  │  │                  │  - Voting on disputes                         │   │
│  │  └──────────────────┘                                               │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                    │                                        │
│                                    ▼                                        │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                           ORACLE LAYER                               │   │
│  │  ┌──────────────────┐                                               │   │
│  │  │   AurumOracle    │  Optimistic oracle system                     │   │
│  │  │                  │  - Propose outcomes                           │   │
│  │  │                  │  - Challenge period                           │   │
│  │  │                  │  - Dispute voting                             │   │
│  │  └──────────────────┘                                               │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                    │                                        │
│                                    ▼                                        │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                           MARKET LAYER                               │   │
│  │  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐  │   │
│  │  │  MarketFactory   │  │    MarketAMM     │  │  FeeCollector    │  │   │
│  │  │                  │  │                  │  │                  │  │   │
│  │  │  - Create market │  │  - Buy/sell      │  │  - Collect fees  │  │   │
│  │  │  - Manage state  │  │  - Liquidity     │  │  - Distribute    │  │   │
│  │  └──────────────────┘  └──────────────────┘  └──────────────────┘  │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                    │                                        │
│                                    ▼                                        │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                           TOKEN LAYER                                │   │
│  │  ┌──────────────────┐                                               │   │
│  │  │ ConditionalTokens│  ERC6909-based outcome tokens                 │   │
│  │  │                  │  - Split/merge positions                      │   │
│  │  │                  │  - Redeem winnings                            │   │
│  │  └──────────────────┘                                               │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

## Contract Specifications

### 1. AurumToken.sol

**Purpose**: ERC20 governance token for the Aurum ecosystem.

**Key Features**:
- Standard ERC20 functionality
- Minting controlled by owner (initially)
- Used for oracle staking and dispute voting

**Interface**:
```solidity
interface IAurumToken {
    function mint(address to, uint256 amount) external;
    function burn(uint256 amount) external;
    function transfer(address to, uint256 amount) external returns (bool);
    function approve(address spender, uint256 amount) external returns (bool);
    function transferFrom(address from, address to, uint256 amount) external returns (bool);
}
```

**Token Details**:
- Name: Aurum Token
- Symbol: AURUM
- Decimals: 18
- Total Supply: 1,000,000,000 (1 billion)

---

### 2. AurumOracle.sol

**Purpose**: Optimistic oracle for market resolution.

**Key Features**:
- Proposers stake AURUM to propose outcomes
- Challenge period allows disputes
- AURUM holders vote on disputed outcomes
- Slashing for incorrect proposals/disputes

**Interface**:
```solidity
interface IAurumOracle {
    // Propose an outcome for a market
    function proposeOutcome(
        bytes32 marketId,
        uint256[] calldata payouts
    ) external;
    
    // Dispute a proposed outcome
    function disputeOutcome(bytes32 marketId) external;
    
    // Vote on a disputed outcome
    function vote(bytes32 marketId, uint256[] calldata payouts) external;
    
    // Finalize outcome after challenge period
    function finalizeOutcome(bytes32 marketId) external;
    
    // Get current proposal for a market
    function getProposal(bytes32 marketId) external view returns (Proposal memory);
}

struct Proposal {
    address proposer;
    uint256[] payouts;
    uint256 proposedAt;
    uint256 stake;
    bool disputed;
    bool finalized;
}
```

**Parameters**:
- Minimum stake: 100 AURUM
- Challenge period (binary): 2 hours
- Challenge period (categorical): 24 hours
- Voting period: 48 hours

---

### 3. MarketFactory.sol

**Purpose**: Factory contract for creating and managing markets.

**Key Features**:
- Create new prediction markets
- Track all markets
- Admin controls for market parameters

**Interface**:
```solidity
interface IMarketFactory {
    // Create a new market
    function createMarket(
        string calldata question,
        string[] calldata outcomes,
        uint256 endTime,
        address collateralToken,
        uint256 initialLiquidity
    ) external returns (bytes32 marketId);
    
    // Get market details
    function getMarket(bytes32 marketId) external view returns (Market memory);
    
    // Get all active markets
    function getActiveMarkets() external view returns (bytes32[] memory);
    
    // Pause/unpause market
    function pauseMarket(bytes32 marketId) external;
}

struct Market {
    bytes32 id;
    string question;
    string[] outcomes;
    uint256 endTime;
    address collateralToken;
    address ammPool;
    bytes32 conditionId;
    MarketStatus status;
}

enum MarketStatus {
    Active,
    Paused,
    Resolving,
    Resolved
}
```

---

### 4. MarketAMM.sol

**Purpose**: Automated Market Maker for trading outcome tokens.

**Key Features**:
- Constant product market maker (x * y = k)
- Liquidity provision
- Buy/sell outcome tokens
- Fee collection on trades

**Interface**:
```solidity
interface IMarketAMM {
    // Buy outcome tokens
    function buy(
        bytes32 marketId,
        uint256 outcomeIndex,
        uint256 amount,
        uint256 maxCost
    ) external returns (uint256 cost);
    
    // Sell outcome tokens
    function sell(
        bytes32 marketId,
        uint256 outcomeIndex,
        uint256 amount,
        uint256 minReturn
    ) external returns (uint256 returnAmount);
    
    // Add liquidity
    function addLiquidity(
        bytes32 marketId,
        uint256 amount
    ) external returns (uint256 lpTokens);
    
    // Remove liquidity
    function removeLiquidity(
        bytes32 marketId,
        uint256 lpTokens
    ) external returns (uint256 amount);
    
    // Get current prices
    function getPrices(bytes32 marketId) external view returns (uint256[] memory);
}
```

**AMM Formula**:
```
For binary market with outcomes A and B:
- Reserve A * Reserve B = k (constant)
- Price A = Reserve B / (Reserve A + Reserve B)
- Price B = Reserve A / (Reserve A + Reserve B)
```

---

### 5. FeeCollector.sol

**Purpose**: Collect and distribute protocol fees.

**Key Features**:
- Collect redemption fees (2%)
- Distribute to treasury
- Future: distribute to AURUM stakers

**Interface**:
```solidity
interface IFeeCollector {
    // Collect fee from redemption
    function collectFee(
        address token,
        uint256 amount
    ) external returns (uint256 feeAmount);
    
    // Withdraw fees to treasury
    function withdrawToTreasury(address token) external;
    
    // Get accumulated fees
    function getAccumulatedFees(address token) external view returns (uint256);
}
```

**Fee Structure**:
- Redemption fee: 2% of winnings
- Trading fee: 0% (initially)

---

### 6. ConditionalTokens.sol (Existing)

**Purpose**: ERC6909-based outcome token management.

**Key Features**:
- Prepare conditions (markets)
- Split collateral into outcome tokens
- Merge outcome tokens back to collateral
- Redeem winning positions

**Already Implemented** - See [aurum-contracts repository](https://github.com/0xsyung/aurum-contracts)

---

## Deployment Order

1. **AurumToken** - No dependencies
2. **ConditionalTokens** - No dependencies (already exists)
3. **AurumOracle** - Depends on AurumToken
4. **FeeCollector** - No dependencies
5. **MarketFactory** - Depends on ConditionalTokens, AurumOracle
6. **MarketAMM** - Depends on MarketFactory, ConditionalTokens, FeeCollector

## Collateral Token

**USDC on Base Sepolia**:
- Address: `0x036CbD53842c5426634e7929541eC2318f3dCF7e`
- Decimals: 6

Users will need to acquire testnet USDC from a faucet or bridge.

## Gas Estimates

| Function | Estimated Gas |
|----------|---------------|
| createMarket | ~500,000 |
| buy | ~150,000 |
| sell | ~150,000 |
| proposeOutcome | ~100,000 |
| disputeOutcome | ~100,000 |
| redeemPositions | ~200,000 |

## Security Considerations

1. **Reentrancy Protection**: All state changes before external calls
2. **Access Control**: OpenZeppelin Ownable for admin functions
3. **Integer Overflow**: Solidity 0.8.x built-in checks
4. **Oracle Manipulation**: Economic guarantees via staking
