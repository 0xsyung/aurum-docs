# Contract Interaction Guide

## Overview

This guide provides detailed information on how to interact with Aurum Labs smart contracts, including function signatures, parameters, and usage examples.

## MarketAMM - Trading Functions

### Buy Outcome Tokens

**Function Signature**:
```solidity
function buy(
    uint256 outcomeIndex,
    uint256 collateralIn,
    uint256 minTokensOut,
    uint256 deadline
) external returns (uint256 tokensOut)
```

**Parameters**:
- `outcomeIndex` (uint256): Index of the outcome to buy (0 to outcomeCount-1)
- `collateralIn` (uint256): Amount of collateral to spend (in token decimals)
- `minTokensOut` (uint256): Minimum tokens to receive (slippage protection)
- `deadline` (uint256): Unix timestamp deadline for transaction execution

**Returns**:
- `tokensOut` (uint256): Amount of outcome tokens received

**Example**:
```javascript
// Buy 100 USDC worth of outcome tokens for outcome 0
// Set deadline to 1 hour from now
const collateralIn = ethers.parseUnits('100', 6); // 100 USDC (6 decimals)
const minTokensOut = ethers.parseUnits('90', 18); // Expect at least 90 tokens (18 decimals)
const deadline = Math.floor(Date.now() / 1000) + 3600; // 1 hour from now

const tx = await amm.buy(0, collateralIn, minTokensOut, deadline);
const receipt = await tx.wait();
```

**Errors**:
- `TransactionExpired()`: If `block.timestamp > deadline`
- `MarketResolved()`: If market has already been resolved
- `InvalidOutcome()`: If outcome index is invalid
- `SlippageExceeded()`: If actual output < minTokensOut
- `TransferFailed()`: If collateral transfer fails

---

### Sell Outcome Tokens

**Function Signature**:
```solidity
function sell(
    uint256 outcomeIndex,
    uint256 tokensIn,
    uint256 minCollateralOut,
    uint256 deadline
) external returns (uint256 collateralOut)
```

**Parameters**:
- `outcomeIndex` (uint256): Index of the outcome to sell
- `tokensIn` (uint256): Amount of outcome tokens to sell
- `minCollateralOut` (uint256): Minimum collateral to receive (slippage protection)
- `deadline` (uint256): Unix timestamp deadline for transaction execution

**Returns**:
- `collateralOut` (uint256): Amount of collateral received

**Example**:
```javascript
// Sell 50 outcome tokens for outcome 0
const tokensIn = ethers.parseUnits('50', 18);
const minCollateralOut = ethers.parseUnits('45', 6); // Expect at least 45 USDC
const deadline = Math.floor(Date.now() / 1000) + 3600;

const tx = await amm.sell(0, tokensIn, minCollateralOut, deadline);
const receipt = await tx.wait();
```

**Errors**:
- `TransactionExpired()`: If `block.timestamp > deadline`
- `MarketResolved()`: If market has already been resolved
- `InvalidOutcome()`: If outcome index is invalid
- `SlippageExceeded()`: If actual output < minCollateralOut

---

### Add Liquidity

**Function Signature**:
```solidity
function addLiquidity(
    uint256 collateralAmount,
    address recipient,
    uint256 deadline
) external returns (uint256 lpTokensMinted)
```

**Parameters**:
- `collateralAmount` (uint256): Amount of collateral to provide
- `recipient` (address): Address to receive LP tokens
- `deadline` (uint256): Unix timestamp deadline for transaction execution

**Returns**:
- `lpTokensMinted` (uint256): Amount of LP tokens minted

**Example**:
```javascript
// Add 1000 USDC liquidity
const collateralAmount = ethers.parseUnits('1000', 6);
const recipient = userAddress;
const deadline = Math.floor(Date.now() / 1000) + 3600;

const tx = await amm.addLiquidity(collateralAmount, recipient, deadline);
const receipt = await tx.wait();
```

**Errors**:
- `TransactionExpired()`: If `block.timestamp > deadline`
- `MarketResolved()`: If market has already been resolved
- `InvalidAmount()`: If collateralAmount is 0

---

### Remove Liquidity

**Function Signature**:
```solidity
function removeLiquidity(
    uint256 lpTokenAmount,
    uint256 minCollateralOut,
    uint256 deadline
) external returns (uint256 collateralOut)
```

**Parameters**:
- `lpTokenAmount` (uint256): Amount of LP tokens to burn
- `minCollateralOut` (uint256): Minimum collateral to receive
- `deadline` (uint256): Unix timestamp deadline for transaction execution

**Returns**:
- `collateralOut` (uint256): Amount of collateral returned

**Example**:
```javascript
// Remove 500 LP tokens
const lpTokenAmount = ethers.parseUnits('500', 18);
const minCollateralOut = ethers.parseUnits('450', 6);
const deadline = Math.floor(Date.now() / 1000) + 3600;

const tx = await amm.removeLiquidity(lpTokenAmount, minCollateralOut, deadline);
const receipt = await tx.wait();
```

**Errors**:
- `TransactionExpired()`: If `block.timestamp > deadline`
- `MarketResolved()`: If market has already been resolved
- `InvalidAmount()`: If lpTokenAmount is 0
- `SlippageExceeded()`: If actual output < minCollateralOut

---

## MarketFactory - Market Management

### Create Market

**Function Signature**:
```solidity
function createMarket(
    address collateralToken,
    string calldata question,
    string[] calldata outcomes,
    uint256 resolutionTime
) external returns (bytes32 marketId)
```

**Parameters**:
- `collateralToken` (address): Address of ERC20 collateral token
- `question` (string): Market question text
- `outcomes` (string[]): Array of outcome descriptions
- `resolutionTime` (uint256): Unix timestamp when market can be resolved

**Returns**:
- `marketId` (bytes32): Unique identifier for the market

**Access Control**:
- Requires market creator role (granted by admin)

**Example**:
```javascript
// Create a binary market
const collateralToken = USDC_ADDRESS;
const question = "Will BTC reach $100k by end of 2026?";
const outcomes = ["Yes", "No"];
const resolutionTime = Math.floor(Date.now() / 1000) + 30 * 24 * 3600; // 30 days

const tx = await factory.createMarket(
    collateralToken,
    question,
    outcomes,
    resolutionTime
);
const receipt = await tx.wait();
const marketId = receipt.events[0].args.marketId;
```

**Errors**:
- `Unauthorized()`: If caller doesn't have market creator role
- `InvalidCollateral()`: If collateral token is not allowed
- `InvalidOutcomeCount()`: If outcomes.length < 2 or > 256
- `InvalidResolutionTime()`: If resolutionTime <= block.timestamp

---

### Deploy AMM

**Function Signature**:
```solidity
function deployAMM(
    bytes32 marketId,
    uint256 initialLiquidity,
    uint256 fee
) external returns (address ammAddress)
```

**Parameters**:
- `marketId` (bytes32): ID of the market
- `initialLiquidity` (uint256): Initial liquidity amount
- `fee` (uint256): Trading fee in basis points (e.g., 100 = 1%)

**Returns**:
- `ammAddress` (address): Address of deployed AMM contract

**Example**:
```javascript
// Deploy AMM with 10,000 USDC initial liquidity and 1% fee
const marketId = "0x..."; // from createMarket
const initialLiquidity = ethers.parseUnits('10000', 6);
const fee = 100; // 1%

const tx = await factory.deployAMM(marketId, initialLiquidity, fee);
const receipt = await tx.wait();
const ammAddress = receipt.events[0].args.ammAddress;
```

---

### Resolve Market

**Function Signature**:
```solidity
function resolveMarket(bytes32 marketId) external
```

**Parameters**:
- `marketId` (bytes32): ID of the market to resolve

**Access Control**:
- Requires market resolver role (granted by admin)

**Requirements**:
- Oracle must have finalized the outcome
- Market must not already be resolved

**Example**:
```javascript
// Resolve a market
const marketId = "0x...";
const tx = await factory.resolveMarket(marketId);
const receipt = await tx.wait();
```

**Errors**:
- `Unauthorized()`: If caller doesn't have market resolver role
- `MarketNotFound()`: If market doesn't exist
- `MarketAlreadyResolved()`: If market is already resolved
- `MarketNotResolved()`: If oracle hasn't finalized outcome

---

## AurumOracle - Market Resolution

### Propose Outcome

**Function Signature**:
```solidity
function proposeOutcome(
    bytes32 questionId,
    uint256[] calldata proposedOutcome
) external
```

**Parameters**:
- `questionId` (bytes32): ID of the question
- `proposedOutcome` (uint256[]): Array of payout numerators (sum must be <= 1e18)

**Requirements**:
- Proposer must have AURUM tokens for staking
- No active proposal for this question
- Sum of outcomes must be > 0 and <= 1e18

**Example**:
```javascript
// Propose outcome for binary market (outcome 0 wins)
const questionId = "0x...";
const proposedOutcome = [
    ethers.parseUnits('1', 18),  // Outcome 0: 100%
    ethers.parseUnits('0', 18)   // Outcome 1: 0%
];

// Approve AURUM tokens for staking
await aurumToken.approve(oracle.address, stakeAmount);

const tx = await oracle.proposeOutcome(questionId, proposedOutcome);
const receipt = await tx.wait();
```

**Errors**:
- `InvalidQuestion()`: If question doesn't exist
- `QuestionAlreadyResolved()`: If question is already resolved
- `ProposalAlreadyExists()`: If proposal already exists
- `InvalidOutcome()`: If sum is 0 or > 1e18
- `TransferFailed()`: If stake transfer fails

---

### Dispute Proposal

**Function Signature**:
```solidity
function disputeProposal(
    bytes32 questionId,
    uint256[] calldata disputedOutcome
) external
```

**Parameters**:
- `questionId` (bytes32): ID of the question
- `disputedOutcome` (uint256[]): Alternative outcome array

**Requirements**:
- Active proposal must exist
- Proposal must not be stale (< 7 days old)
- Disputer must have AURUM tokens for staking
- Disputer cannot be the proposer

**Example**:
```javascript
// Dispute the proposal
const questionId = "0x...";
const disputedOutcome = [
    ethers.parseUnits('0', 18),  // Outcome 0: 0%
    ethers.parseUnits('1', 18)   // Outcome 1: 100%
];

// Approve AURUM tokens for staking
await aurumToken.approve(oracle.address, stakeAmount);

const tx = await oracle.disputeProposal(questionId, disputedOutcome);
const receipt = await tx.wait();
```

**Errors**:
- `InvalidQuestion()`: If question doesn't exist
- `QuestionAlreadyResolved()`: If question is already resolved
- `NoActiveProposal()`: If no proposal exists
- `ProposalAlreadyDisputed()`: If proposal is already disputed
- `ProposalStale()`: If proposal is older than 7 days
- `ProposalNotExpired()`: If challenge period hasn't ended
- `CannotDisputeOwnProposal()`: If disputer is the proposer
- `InvalidOutcome()`: If sum is 0 or > 1e18

---

## Role Management (Admin Only)

### Grant Market Creator Role

**Function Signature**:
```solidity
function grantMarketCreator(address account) external onlyAdmin
```

**Parameters**:
- `account` (address): Address to grant role to

**Example**:
```javascript
const creatorAddress = "0x...";
const tx = await factory.grantMarketCreator(creatorAddress);
await tx.wait();
```

---

### Grant Market Resolver Role

**Function Signature**:
```solidity
function grantMarketResolver(address account) external onlyAdmin
```

**Parameters**:
- `account` (address): Address to grant role to

**Example**:
```javascript
const resolverAddress = "0x...";
const tx = await factory.grantMarketResolver(resolverAddress);
await tx.wait();
```

---

## Best Practices

### 1. Deadline Handling

Always set a reasonable deadline for trading operations:

```javascript
// Set deadline to 1 hour from now
const deadline = Math.floor(Date.now() / 1000) + 3600;

// Or use ethers.js helper
const deadline = (await ethers.provider.getBlock('latest')).timestamp + 3600;
```

### 2. Slippage Protection

Always specify minimum output amounts:

```javascript
// Get expected output from view function
const expectedOutput = await amm.calculateBuyAmount(outcomeIndex, collateralIn);

// Apply 1% slippage tolerance
const minOutput = expectedOutput * 99n / 100n;

// Use in transaction
await amm.buy(outcomeIndex, collateralIn, minOutput, deadline);
```

### 3. Approval Pattern

Always approve tokens before transfers:

```javascript
// Approve collateral for AMM
await collateralToken.approve(amm.address, collateralAmount);

// Approve outcome tokens for selling
await outcomeToken.approve(amm.address, tokensToSell);

// Approve AURUM for oracle staking
await aurumToken.approve(oracle.address, stakeAmount);
```

### 4. Error Handling

Handle contract errors appropriately:

```javascript
try {
    const tx = await amm.buy(outcomeIndex, collateralIn, minTokensOut, deadline);
    await tx.wait();
} catch (error) {
    if (error.reason === 'TransactionExpired') {
        console.error('Transaction deadline passed');
    } else if (error.reason === 'SlippageExceeded') {
        console.error('Slippage exceeded - try again with higher slippage tolerance');
    } else {
        console.error('Transaction failed:', error);
    }
}
```

---

## View Functions

### Get Market Details

```solidity
function getMarket(bytes32 marketId) external view returns (Market memory)
```

Returns complete market information including creator, collateral, outcomes, and resolution status.

### Get Current Prices

```solidity
function getPrices() external view returns (uint256[] memory)
```

Returns current outcome token prices based on AMM reserves.

### Calculate Buy Amount

```solidity
function calculateBuyAmount(uint256 outcomeIndex, uint256 collateralIn) 
    external view returns (uint256)
```

Returns expected outcome tokens for given collateral amount (before fees).

### Calculate Sell Amount

```solidity
function calculateSellAmount(uint256 outcomeIndex, uint256 tokensIn) 
    external view returns (uint256)
```

Returns expected collateral for given outcome tokens (before fees).

### Check Proposal Staleness

```solidity
function isProposalStale(bytes32 questionId) external view returns (bool)
```

Returns true if proposal is older than staleness threshold (7 days).

---

## Gas Optimization Tips

1. **Batch Operations**: Group multiple trades together to save gas
2. **Liquidity Efficiency**: Add liquidity in larger amounts to reduce per-transaction overhead
3. **Fee Awareness**: Consider trading fees when calculating expected returns
4. **View Functions**: Use view functions to estimate gas before executing transactions

---

## Testnet Information

**Chain**: Base Sepolia
**Chain ID**: 84532
**RPC URL**: https://sepolia.base.org

**Contract Addresses**:
- MarketFactory: `0xaF8ddE93C551ce4f6A21db07508858Fb15E4bbC9`
- AurumOracle: `0x4EC0295F0344ac264EB83bd7bDb0069015702297`
- USDC: `0x036CbD53842c5426634e7929541eC2318f3dCF7e`
