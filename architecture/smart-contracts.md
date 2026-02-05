# Smart Contract Architecture

## Status

The **current** `aurum-contracts` repo only implements a minimal Gnosis-style Conditional Tokens system based on ERC6909. Other components (token, oracle, market factory, AMM, fee collector) are **not implemented** yet.

## Current Contracts

### 1. `ConditionalTokens.sol`

**Purpose**: Implements Conditional Tokens using an ERC6909 position representation.

**Key Capabilities**:
- Prepare and resolve conditions
- Split/merge positions
- Redeem payouts after resolution

**Core Functions**:
- `prepareCondition(address oracle, bytes32 questionId, uint256 outcomeSlotCount)`
- `reportPayouts(bytes32 questionId, uint256[] payouts)`
- `splitPosition(IERC20 collateralToken, bytes32 parentCollectionId, bytes32 conditionId, uint256[] partition, uint256 amount)`
- `mergePositions(IERC20 collateralToken, bytes32 parentCollectionId, bytes32 conditionId, uint256[] partition, uint256 amount)`
- `redeemPositions(IERC20 collateralToken, bytes32 parentCollectionId, bytes32 conditionId, uint256[] indexSets)`
- Helpers: `getConditionId`, `getCollectionId`, `getPositionId`, `getOutcomeSlotCount`

**Events**:
- `ConditionPreparation`
- `ConditionResolution`
- `PositionSplit`
- `PositionsMerge`
- `PayoutRedemption`

**Files**:
- `/Users/bob/codex-workspace/aurum-contracts/src/ConditionalTokens.sol`

### 2. `ERC6909.sol` and `ERC6909Claims.sol`

Minimal ERC6909 implementation and helper extension used by `ConditionalTokens`.

**Files**:
- `/Users/bob/codex-workspace/aurum-contracts/src/ERC6909.sol`
- `/Users/bob/codex-workspace/aurum-contracts/src/ERC6909Claims.sol`

## Deployment Script

A simple deployment script exists for `ConditionalTokens` only:

- `/Users/bob/codex-workspace/aurum-contracts/script/ConditionalTokens.s.sol`

## Tests

Current tests cover ERC6909 and ConditionalTokens core behavior:

- `/Users/bob/codex-workspace/aurum-contracts/test/ConditionalTokens.t.sol`
- `/Users/bob/codex-workspace/aurum-contracts/test/ERC6909.t.sol`
- `/Users/bob/codex-workspace/aurum-contracts/test/ERC6909Claims.t.sol`

## Planned (Not Implemented)

The following components are described in older docs but are not present in the codebase:

- `AurumToken.sol` (ERC20 governance token)
- `AurumOracle.sol` (optimistic oracle)
- `MarketFactory.sol` (market creation and registry)
- `MarketAMM.sol` (trading AMM)
- `FeeCollector.sol` (fee accounting)

If/when these contracts are implemented, update this document with their real interfaces and deployment details.
