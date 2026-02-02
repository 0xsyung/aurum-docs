# Base Sepolia Deployment

**Network**: Base Sepolia Testnet  
**Chain ID**: 84532  
**Deployment Date**: February 2, 2026  
**Deployer**: 0x095b95ea2aD7e05c4c7104F1f0743Dc046a108b6

## Deployed Contract Addresses

| Contract | Address | Description |
|----------|---------|-------------|
| **AurumToken** | `0x9A7A037469204604C29a44901b69B0bBB1d45B13` | ERC20 governance token (1B supply) |
| **ConditionalTokens** | `0xdBfa3D8516C49581e2A6cBbD75F02F24c59811c1` | ERC6909 outcome tokens |
| **AurumOracle** | `0xBCC8aC562085E207460B9a0342d62a480DD9caAC` | Optimistic oracle for resolution |
| **FeeCollector** | `0x4EC0295F0344ac264EB83bd7bDb0069015702297` | Protocol fee management |
| **MarketFactory** | `0xaF8ddE93C551ce4f6A21db07508858Fb15E4bbC9` | Market creation factory |

## Block Explorer Links

- [AurumToken](https://sepolia.basescan.org/address/0x9A7A037469204604C29a44901b69B0bBB1d45B13)
- [ConditionalTokens](https://sepolia.basescan.org/address/0xdBfa3D8516C49581e2A6cBbD75F02F24c59811c1)
- [AurumOracle](https://sepolia.basescan.org/address/0xBCC8aC562085E207460B9a0342d62a480DD9caAC)
- [FeeCollector](https://sepolia.basescan.org/address/0x4EC0295F0344ac264EB83bd7bDb0069015702297)
- [MarketFactory](https://sepolia.basescan.org/address/0xaF8ddE93C551ce4f6A21db07508858Fb15E4bbC9)

## Configuration

### AurumToken
- **Total Supply**: 1,000,000,000 AURUM (1 billion)
- **Decimals**: 18
- **Owner**: Deployer address

### AurumOracle
- **Minimum Bond**: 100 AURUM
- **Binary Challenge Period**: 2 hours
- **Categorical Challenge Period**: 24 hours
- **Treasury**: Deployer address

### FeeCollector
- **Treasury Share**: 50%
- **Stakers Share**: 40%
- **Burn Share**: 10%

## Verification Status

Contracts are deployed but not yet verified on Basescan. To verify:

```bash
forge verify-contract <address> <contract> --chain base-sepolia
```

## Notes

- This is a testnet deployment for development and testing purposes
- Do not use real funds on testnet
- Contract sizes should be optimized before mainnet deployment
