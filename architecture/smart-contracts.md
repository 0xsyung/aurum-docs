# Smart Contract Architecture

## Overview

The Aurum Labs smart contract system is built on Foundry and deployed on Base Sepolia. It consists of six main contracts that work together to provide prediction market functionality with enhanced security features.

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
│  │  │                  │  - Staleness validation                       │   │
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
│  │  │  - Role-based    │  │  - Liquidity     │  │  - Distribute    │  │   │
│  │  │    access        │  │  - Deadline      │  │                  │  │   │
│  │  │  - Manage state  │  │    protection    │  │                  │  │   │
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
- Snapshot functionality for voting

**Interface**:
```solidity
interface IAurumToken {
    function mint(address to, uint256 amount) external;
    function burn(uint256 amount) external;
    function transfer(address to, uint256 amount) external returns (bool);
    function approve(address spender, uint256 amount) external returns (bool);
    function transferFrom(address from, address to, uint256 amount) external returns (bool);
    function snapshot() external returns (uint256);
}
```

**Token Details**:
- Name: Aurum Token
- Symbol: AURUM
- Decimals: 18
- Total Supply: 1,000,000,000 (1 billion)

---

### 2. AurumOracle.sol

**Purpose**: Optimistic oracle for market resolution with enhanced security.

**Key Features**:
- Proposers stake AURUM to propose outcomes
- Challenge period allows disputes
- **NEW**: Staleness validation (7 days default)
- **NEW**: Price bounds validation (sum <= 100%)
- AURUM holders vote on disputed outcomes
- Slashing for incorrect proposals/disputes

**Security Enhancements**:
- Proposal staleness check prevents use of outdated data
- Outcome sum validation ensures valid probability distributions
- Configurable staleness threshold via `setStalenessThreshold()`

**Interface**:
```solidity
interface IAurumOracle {
    // Register a new question for resolution
    function registerQuestion(
        bytes32 questionId,
        uint256 outcomeSlotCount,
        bool isBinary
    ) external;
    
    // Propose an outcome for a market
    function proposeOutcome(
        bytes32 questionId,
        uint256[] calldata proposedOutcome
    ) external;
    
    // Dispute a proposed outcome
    function disputeProposal(
        bytes32 questionId,
        uint256[] calldata disputedOutcome
    ) external;
    
    // Finalize outcome after challenge period
    function finalizeProposal(bytes32 questionId) external;
    
    // Get current proposal for a market
    function getProposal(bytes32 questionId) external view returns (Proposal memory);
    
    // Check if proposal is stale
    function isProposalStale(bytes32 questionId) external view returns (bool);
    
    // Set staleness threshold (admin only)
    function setStalenessThreshold(uint256 newThreshold) external;
}

struct Proposal {
    address proposer;
    uint256[] proposedOutcome;
    uint256 proposalTime;
    uint256 expirationTime;
    uint256 stakeAmount;
    bool isDisputed;
    address disputer;
    uint256[] disputedOutcome;
}
```

**Parameters**:
- Minimum stake: 100 AURUM
- Challenge period (binary): 2 hours
- Challenge period (categorical): 24 hours
- Staleness threshold: 7 days (configurable)
- Maximum outcome sum: 1e18 (100%)

---

### 3. MarketFactory.sol

**Purpose**: Factory contract for creating and managing markets with role-based access control.

**Key Features**:
- Create new prediction markets
- **NEW**: Role-based access control (market creators and resolvers)
- **NEW**: Grant/revoke market creator and resolver roles
- Track all markets
- Admin controls for market parameters

**Security Enhancements**:
- Market creation restricted to authorized market creators
- Market resolution restricted to authorized resolvers
- Admin-controlled role management
- Configurable allowed collateral tokens

**Interface**:
```solidity
interface IMarketFactory {
    // Create a new market (requires market creator role)
    function createMarket(
        address collateralToken,
        string calldata question,
        string[] calldata outcomes,
        uint256 resolutionTime
    ) external returns (bytes32 marketId);
    
    // Deploy AMM for a market
    function deployAMM(
        bytes32 marketId,
        uint256 initialLiquidity,
        uint256 fee
    ) external returns (address ammAddress);
    
    // Resolve a market (requires market resolver role)
    function resolveMarket(bytes32 marketId) external;
    
    // Get market details
    function getMarket(bytes32 marketId) external view returns (Market memory);
    
    // Role management (admin only)
    function grantMarketCreator(address account) external;
    function revokeMarketCreator(address account) external;
    function grantMarketResolver(address account) external;
    function revokeMarketResolver(address account) external;
    
    // Collateral management (admin only)
    function addCollateral(address token) external;
    function removeCollateral(address token) external;
}

struct Market {
    address creator;
    address collateralToken;
    bytes32 questionId;
    bytes32 conditionId;
    uint256 outcomeSlotCount;
    uint256 resolutionTime;
    address ammAddress;
    bool resolved;
    uint256[] payouts;
}
```

---

### 4. MarketAMM.sol

**Purpose**: Automated Market Maker for trading outcome tokens with deadline protection.

**Key Features**:
- Constant product market maker (x * y = k)
- Liquidity provision
- Buy/sell outcome tokens
- **NEW**: Deadline protection on all operations
- Fee collection on trades
- Reentrancy protection

**Security Enhancements**:
- All trading operations require deadline parameter
- Prevents old transactions from executing at unfavorable prices
- Slippage protection via minTokensOut and minCollateralOut
- Reentrancy guards on all state-changing functions

**Interface**:
```solidity
interface IMarketAMM {
    // Add liquidity with deadline protection
    function addLiquidity(
        uint256 collateralAmount,
        address recipient,
        uint256 deadline
    ) external returns (uint256 lpTokensMinted);
    
    // Remove liquidity with deadline protection
    function removeLiquidity(
        uint256 lpTokenAmount,
        uint256 minCollateralOut,
        uint256 deadline
    ) external returns (uint256 collateralOut);
    
    // Buy outcome tokens with deadline protection
    function buy(
        uint256 outcomeIndex,
        uint256 collateralIn,
        uint256 minTokensOut,
        uint256 deadline
    ) external returns (uint256 tokensOut);
    
    // Sell outcome tokens with deadline protection
    function sell(
        uint256 outcomeIndex,
        uint256 tokensIn,
        uint256 minCollateralOut,
        uint256 deadline
    ) external returns (uint256 collateralOut);
    
    // Collect accumulated fees
    function collectFees() external;
    
    // Get current prices
    function getPrices() external view returns (uint256[] memory);
}
```

**AMM Formula**:
```
For binary market with outcomes A and B:
- Reserve A * Reserve B = k (constant)
- Price A = Reserve B / (Reserve A + Reserve B)
- Price B = Reserve A / (Reserve A + Reserve B)
```

**Deadline Parameter**:
- All trading functions require a `deadline` parameter (Unix timestamp)
- Transaction reverts if `block.timestamp > deadline`
- Recommended: Set deadline to `block.timestamp + 1 hours` in frontend

---

### 5. FeeCollector.sol

**Purpose**: Collect and distribute protocol fees.

**Key Features**:
- Collect trading fees from MarketAMM
- Distribute to treasury
- Future: distribute to AURUM stakers

**Interface**:
```solidity
interface IFeeCollector {
    // Collect fee from market
    function collectFee(uint256 amount) external;
    
    // Withdraw fees to treasury
    function withdrawFees() external;
    
    // Get accumulated fees
    function getAccumulatedFees() external view returns (uint256);
}
```

**Fee Structure**:
- Trading fee: 0.5% - 1% (configurable per market)
- Redemption fee: 2% of winnings

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
6. **MarketAMM** - Deployed per market by MarketFactory

## Base Sepolia Deployment

**Deployed Addresses**:
- **ConditionalTokens**: `0x9A7A037469204604C29a44901b69B0bBB1d45B13`
- **AurumToken**: `0xdBfa3D8516C49581e2A6cBbD75F02F24c59811c1`
- **FeeCollector**: `0xBCC8aC562085E207460B9a0342d62a480DD9caAC`
- **AurumOracle**: `0x4EC0295F0344ac264EB83bd7bDb0069015702297`
- **MarketFactory**: `0xaF8ddE93C551ce4f6A21db07508858Fb15E4bbC9`

**Collateral Token**:
- **USDC**: `0x036CbD53842c5426634e7929541eC2318f3dCF7e`
- Decimals: 6

**Network Details**:
- Chain ID: 84532
- RPC: https://sepolia.base.org
- Block Explorer: https://sepolia.basescan.org

## Gas Estimates

| Function | Estimated Gas |
|----------|---------------|
| createMarket | ~500,000 |
| deployAMM | ~1,200,000 |
| buy | ~150,000 |
| sell | ~150,000 |
| addLiquidity | ~200,000 |
| removeLiquidity | ~150,000 |
| proposeOutcome | ~100,000 |
| disputeProposal | ~100,000 |
| finalizeProposal | ~80,000 |
| redeemPositions | ~200,000 |

## Security Considerations

### Implemented Protections

1. **Reentrancy Protection**: ReentrancyGuard on all state-changing functions
2. **Access Control**: Role-based permissions for market creation and resolution
3. **Integer Overflow**: Solidity 0.8.33 built-in checks
4. **Oracle Manipulation**: 
   - Economic guarantees via staking
   - Staleness validation (7 days)
   - Price bounds validation (sum <= 100%)
5. **Deadline Protection**: All trading operations require deadline parameter
6. **Slippage Protection**: minTokensOut and minCollateralOut parameters
7. **Code Optimization**: Solidity optimizer enabled (200 runs) to reduce contract size

### Vulnerability Mitigations

- **Sandwich Attacks**: Deadline + slippage protection
- **Flash Loan Attacks**: Slippage checks on AMM operations
- **Oracle Staleness**: Configurable staleness threshold
- **Invalid Outcomes**: Sum validation (0 < sum <= 1e18)
- **Unauthorized Access**: Role-based access control

## Testing

**Test Coverage**:
- 283 tests passing
- Branch coverage: 64.04%
- All critical paths tested
- Security vulnerability scenarios covered

**Test Suites**:
- AurumToken: 52 tests
- ConditionalTokens: 22 tests
- AurumOracle: 22 tests
- MarketFactory: 18 tests
- MarketAMM: 20 tests
- Additional Coverage: 129 tests
- Vulnerability Tests: 20 tests

## Recent Updates (February 2026)

- ✅ Deadline protection added to all trading operations
- ✅ Oracle staleness validation implemented
- ✅ Role-based access control for market creation/resolution
- ✅ Price bounds validation for oracle outcomes
- ✅ Contract size optimization (EIP-170 compliant)
- ✅ Comprehensive security audit and testing
- ✅ Deployed to Base Sepolia testnet
