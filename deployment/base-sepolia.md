# Base Sepolia Deployment

**Network**: Base Sepolia Testnet  
**Chain ID**: 84532  
**Deployment Date**: February 3, 2026  
**Deployer**: 0x095b95ea2aD7e05c4c7104F1f0743Dc046a108b6

## Deployed Contract Addresses

| Contract | Address | Description |
|----------|---------|-------------|
| **ConditionalTokens** | `0x9A7A037469204604C29a44901b69B0bBB1d45B13` | ERC6909 outcome tokens |
| **AurumToken** | `0xdBfa3D8516C49581e2A6cBbD75F02F24c59811c1` | ERC20 governance token (1B supply) |
| **FeeCollector** | `0xBCC8aC562085E207460B9a0342d62a480DD9caAC` | Protocol fee management |
| **AurumOracle** | `0x4EC0295F0344ac264EB83bd7bDb0069015702297` | Optimistic oracle for resolution |
| **MarketFactory** | `0xaF8ddE93C551ce4f6A21db07508858Fb15E4bbC9` | Market creation factory |

## Block Explorer Links

- [ConditionalTokens](https://sepolia.basescan.org/address/0x9A7A037469204604C29a44901b69B0bBB1d45B13)
- [AurumToken](https://sepolia.basescan.org/address/0xdBfa3D8516C49581e2A6cBbD75F02F24c59811c1)
- [FeeCollector](https://sepolia.basescan.org/address/0xBCC8aC562085E207460B9a0342d62a480DD9caAC)
- [AurumOracle](https://sepolia.basescan.org/address/0x4EC0295F0344ac264EB83bd7bDb0069015702297)
- [MarketFactory](https://sepolia.basescan.org/address/0xaF8ddE93C551ce4f6A21db07508858Fb15E4bbC9)

## Deployment Transactions

| Contract | Transaction Hash |
|----------|-----------------|
| ConditionalTokens | `0xae4a9aa04598ccdad490f0d292a9b192d181c2d1bae4b5361eb9f0d876274f6b` |
| AurumToken | `0x9751cc2cb37701330862a1d638b8b9e70a4addf5dc80f2788076d9e4dfdcff57` |
| FeeCollector | `0x17f8b9a27518b696f71e972c88390686b7391b505d37cbb433fa9bf74f32573e` |
| AurumOracle | `0xf40976592c59aafff83466c9913103506a933e62030666c6c95d725a50d22e0b` |
| MarketFactory | `0xb79a5593cda73a685195d1635f7f5495d9ec0e6f100ed0f711f0e115b8703db4` |
| Add Collateral | `0x1455b5c832cd9ead8c2b9b7706257d947108c060814b262cafe20721a79b7f1f` |

## Environment Variables

Copy these to your `.env` file:

```env
# Base Sepolia Configuration
VITE_CHAIN_ID=84532
VITE_RPC_URL=https://sepolia.base.org

# Contract Addresses
VITE_CONDITIONAL_TOKENS_ADDRESS=0x9A7A037469204604C29a44901b69B0bBB1d45B13
VITE_AURUM_TOKEN_ADDRESS=0xdBfa3D8516C49581e2A6cBbD75F02F24c59811c1
VITE_FEE_COLLECTOR_ADDRESS=0xBCC8aC562085E207460B9a0342d62a480DD9caAC
VITE_AURUM_ORACLE_ADDRESS=0x4EC0295F0344ac264EB83bd7bDb0069015702297
VITE_MARKET_FACTORY_ADDRESS=0xaF8ddE93C551ce4f6A21db07508858Fb15E4bbC9
VITE_USDC_ADDRESS=0x036CbD53842c5426634e7929541eC2318f3dCF7e
```

## Configuration

### AurumToken
- **Total Supply**: 1,000,000,000 AURUM (1 billion)
- **Decimals**: 18
- **Owner**: Deployer address
- **Features**: Snapshots for voting, standard ERC20 functions

### AurumOracle
- **Treasury**: Deployer address
- **Staleness Threshold**: 7 days (configurable)
- **Features**: 
  - Optimistic oracle with dispute mechanism
  - Outcome sum validation (0 < sum <= 100%)
  - Proposal staleness checks
  - Challenge period: 2 hours (binary), 24 hours (categorical)

### FeeCollector
- **Treasury**: Deployer address
- **Staking Pool**: Deployer address
- **Features**: Collects fees from market trades and redemptions

### MarketFactory
- **Conditional Tokens**: `0x9A7A037469204604C29a44901b69B0bBB1d45B13`
- **Oracle**: `0x4EC0295F0344ac264EB83bd7bDb0069015702297`
- **Fee Collector**: `0xBCC8aC562085E207460B9a0342d62a480DD9caAC`
- **Allowed Collateral**: USDC (0x036CbD53842c5426634e7929541eC2318f3dCF7e)
- **Features**:
  - Role-based access control
  - Market creator and resolver roles
  - Admin-controlled collateral management

## Role Setup

After deployment, configure roles for market operations:

```bash
# Grant market creator role to an address
cast send 0xaF8ddE93C551ce4f6A21db07508858Fb15E4bbC9 \
  "grantMarketCreator(address)" \
  0x<MARKET_CREATOR_ADDRESS> \
  --rpc-url https://sepolia.base.org \
  --private-key $PRIVATE_KEY

# Grant market resolver role to an address
cast send 0xaF8ddE93C551ce4f6A21db07508858Fb15E4bbC9 \
  "grantMarketResolver(address)" \
  0x<MARKET_RESOLVER_ADDRESS> \
  --rpc-url https://sepolia.base.org \
  --private-key $PRIVATE_KEY
```

## Security Features

### Implemented Protections

1. **Deadline Protection**: All trading operations require deadline parameter
   - Prevents old transactions from executing at unfavorable prices
   - Recommended deadline: block.timestamp + 1 hour

2. **Oracle Staleness Validation**: 
   - Proposals older than 7 days are considered stale
   - Prevents use of outdated market data
   - Configurable via setStalenessThreshold()

3. **Price Bounds Validation**:
   - Outcome sum must be > 0 and <= 100%
   - Prevents invalid probability distributions

4. **Role-Based Access Control**:
   - Market creation restricted to authorized creators
   - Market resolution restricted to authorized resolvers
   - Admin-controlled role management

5. **Reentrancy Protection**:
   - All state-changing functions protected with ReentrancyGuard
   - Prevents reentrancy attacks

6. **Slippage Protection**:
   - All trading functions have minTokensOut and minCollateralOut parameters
   - Protects against sandwich attacks and price manipulation

## Frontend Configuration

Add these environment variables to your frontend `.env` file:

```env
# Network Configuration
VITE_CHAIN_ID=84532
VITE_RPC_URL=https://sepolia.base.org
VITE_BLOCK_EXPLORER=https://sepolia.basescan.org

# Contract Addresses
VITE_CONDITIONAL_TOKENS_ADDRESS=0x9A7A037469204604C29a44901b69B0bBB1d45B13
VITE_AURUM_TOKEN_ADDRESS=0xdBfa3D8516C49581e2A6cBbD75F02F24c59811c1
VITE_FEE_COLLECTOR_ADDRESS=0xBCC8aC562085E207460B9a0342d62a480DD9caAC
VITE_AURUM_ORACLE_ADDRESS=0x4EC0295F0344ac264EB83bd7bDb0069015702297
VITE_MARKET_FACTORY_ADDRESS=0xaF8ddE93C551ce4f6A21db07508858Fb15E4bbC9
VITE_USDC_ADDRESS=0x036CbD53842c5426634e7929541eC2318f3dCF7e

# Transaction Settings
VITE_TRANSACTION_DEADLINE_MINUTES=60
VITE_SLIPPAGE_TOLERANCE_PERCENT=1
```

## Verification Status

Contracts are deployed but not yet verified on Basescan. To verify:

```bash
# Verify ConditionalTokens
forge verify-contract \
  0x9A7A037469204604C29a44901b69B0bBB1d45B13 \
  src/ConditionalTokens.sol:ConditionalTokens \
  --chain-id 84532 \
  --etherscan-api-key YOUR_API_KEY

# Verify AurumToken
forge verify-contract \
  0xdBfa3D8516C49581e2A6cBbD75F02F24c59811c1 \
  src/tokens/AurumToken.sol:AurumToken \
  --chain-id 84532 \
  --constructor-args $(cast abi-encode "constructor(uint256)" 1000000000000000000000000000) \
  --etherscan-api-key YOUR_API_KEY
```

## Testing the Deployment

### Get Test ETH

1. Visit the [Base Sepolia Faucet](https://www.coinbase.com/faucets/base-ethereum-goerli-faucet)
2. Connect your wallet
3. Request test ETH

### Get Test USDC

1. Visit Aave Faucet or similar
2. Request USDC on Base Sepolia
3. Or use the USDC contract directly if you have permission

### Verify Contracts are Working

```bash
# Check AurumToken balance
cast call 0xdBfa3D8516C49581e2A6cBbD75F02F24c59811c1 \
  "balanceOf(address)(uint256)" \
  0x095b95ea2aD7e05c4c7104F1f0743Dc046a108b6 \
  --rpc-url https://sepolia.base.org

# Check MarketFactory admin
cast call 0xaF8ddE93C551ce4f6A21db07508858Fb15E4bbC9 \
  "admin()(address)" \
  --rpc-url https://sepolia.base.org

# Check if USDC is allowed collateral
cast call 0xaF8ddE93C551ce4f6A21db07508858Fb15E4bbC9 \
  "allowedCollaterals(address)(bool)" \
  0x036CbD53842c5426634e7929541eC2318f3dCF7e \
  --rpc-url https://sepolia.base.org
```

## Notes

- This is a testnet deployment for development and testing purposes
- Do not use real funds on testnet
- Contract sizes have been optimized with Solidity optimizer (200 runs + via_ir)
- All contracts are under the EIP-170 size limit (24,576 bytes)
- All security fixes and enhancements are included in this deployment
- Role-based access control must be configured before market operations
