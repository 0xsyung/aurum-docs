# Contract Interaction Guide

## Status

The only on-chain contract currently implemented is `ConditionalTokens`. This guide documents **only** those functions. MarketFactory/AMM/Oracle docs were removed because they are not implemented in `aurum-contracts` yet.

## ConditionalTokens

**Source**: `/Users/bob/codex-workspace/aurum-contracts/src/ConditionalTokens.sol`

### Prepare a Condition

**Signature**:
```solidity
function prepareCondition(
    address oracle,
    bytes32 questionId,
    uint256 outcomeSlotCount
) external
```

**Notes**:
- `outcomeSlotCount` must be > 1 and <= 256
- The oracle address is used when computing the `conditionId`

### Report Payouts (Resolve)

**Signature**:
```solidity
function reportPayouts(
    bytes32 questionId,
    uint256[] calldata payouts
) external
```

**Notes**:
- The caller must be the same `oracle` used in `prepareCondition`
- `payouts.length` defines the outcome slot count
- Sum of payouts must be > 0

### Split Positions

**Signature**:
```solidity
function splitPosition(
    IERC20 collateralToken,
    bytes32 parentCollectionId,
    bytes32 conditionId,
    uint256[] calldata partition,
    uint256 amount
) external
```

**Notes**:
- `partition` is an array of bitmask index sets that must cover the full set
- If `parentCollectionId` is zero, collateral is transferred in

### Merge Positions

**Signature**:
```solidity
function mergePositions(
    IERC20 collateralToken,
    bytes32 parentCollectionId,
    bytes32 conditionId,
    uint256[] calldata partition,
    uint256 amount
) external
```

**Notes**:
- Inverse of `splitPosition`
- If `parentCollectionId` is zero, collateral is transferred out

### Redeem Positions

**Signature**:
```solidity
function redeemPositions(
    IERC20 collateralToken,
    bytes32 parentCollectionId,
    bytes32 conditionId,
    uint256[] calldata indexSets
) external
```

**Notes**:
- Condition must be resolved (payout denominator > 0)
- Burns winning positions and transfers collateral or parent positions

### Helper Functions

```solidity
function getConditionId(address oracle, bytes32 questionId, uint256 outcomeSlotCount)
    public pure returns (bytes32)

function getCollectionId(bytes32 parentCollectionId, bytes32 conditionId, uint256 indexSet)
    public pure returns (bytes32)

function getPositionId(IERC20 collateralToken, bytes32 collectionId)
    public pure returns (uint256)

function getOutcomeSlotCount(bytes32 conditionId)
    external view returns (uint256)
```

## ERC6909 Transfers

`ConditionalTokens` inherits ERC6909, so positions are ERC6909 token IDs. Standard ERC6909 transfer/approval patterns apply.

## Not Implemented

These components are planned but not present in the current codebase:

- MarketFactory
- MarketAMM
- AurumToken
- AurumOracle
- FeeCollector

Update this guide when those contracts exist in `aurum-contracts`.
