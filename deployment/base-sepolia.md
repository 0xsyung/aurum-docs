# Base Sepolia Deployment (ConditionalTokens Only)

## Status

The current contracts repo only includes `ConditionalTokens`. There are **no tracked Base Sepolia deployments** in this documentation.

## Deploy ConditionalTokens to Base Sepolia

### Prerequisites

- Foundry installed
- Base Sepolia RPC URL
- Funded deployer key (test ETH)

### Deploy

```bash
cd ~/aurum-labs/aurum-contracts

export RPC_URL=https://sepolia.base.org
export PRIVATE_KEY=0xYOUR_PRIVATE_KEY

forge script script/ConditionalTokens.s.sol:ConditionalTokensScript \
  --rpc-url $RPC_URL \
  --private-key $PRIVATE_KEY \
  --broadcast
```

Record the deployed address here once deployed.

## Environment Variables (When Wired)

`aurum-app` does not currently read contract addresses from env vars. When that changes, add:

```env
VITE_CHAIN_ID=84532
VITE_RPC_URL=https://sepolia.base.org
VITE_CONDITIONAL_TOKENS_ADDRESS=0x...
```
