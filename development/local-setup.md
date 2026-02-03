# Local Development Setup Guide

This guide walks you through setting up the complete Aurum Labs platform locally for end-to-end testing. By the end, you'll have smart contracts deployed on a local blockchain, the trading app running, and be able to test the full prediction market flow.

## Prerequisites

Before starting, ensure you have the following installed on your machine:

| Tool | Version | Purpose | Installation |
|------|---------|---------|--------------|
| **Node.js** | 18+ | JavaScript runtime | [nodejs.org](https://nodejs.org/) |
| **pnpm** | 8+ | Package manager | `npm install -g pnpm` |
| **Foundry** | Latest | Smart contract development | See below |
| **Git** | 2.30+ | Version control | [git-scm.com](https://git-scm.com/) |

### Install Foundry

Foundry is required for smart contract development and local blockchain testing:

```bash
# Install Foundry
curl -L https://foundry.paradigm.xyz | bash

# Reload your shell or run
source ~/.bashrc  # or ~/.zshrc

# Install Foundry tools
foundryup
```

Verify installation:

```bash
forge --version
anvil --version
cast --version
```

---

## Step 1: Clone All Repositories

Create a workspace directory and clone all Aurum Labs repositories:

```bash
# Create workspace
mkdir -p ~/aurum-labs && cd ~/aurum-labs

# Clone repositories
git clone https://github.com/0xsyung/aurum-contracts.git
git clone https://github.com/0xsyung/aurum-app.git
git clone https://github.com/0xsyung/aurum-website.git
git clone https://github.com/0xsyung/aurum-docs.git
```

Your directory structure should look like:

```
~/aurum-labs/
├── aurum-contracts/    # Smart contracts (Foundry)
├── aurum-app/          # Trading interface (React + Vite)
├── aurum-website/      # Landing page (React + Vite + Express)
└── aurum-docs/         # Documentation
```

---

## Step 2: Set Up Smart Contracts

### 2.1 Install Dependencies

```bash
cd ~/aurum-labs/aurum-contracts

# Install Foundry dependencies
forge install
```

### 2.2 Build Contracts

```bash
# Build all contracts
forge build

# Verify build succeeded
forge build --sizes
```

You should see all contracts compiled with sizes under the 24KB limit.

### 2.3 Run Tests

```bash
# Run all tests
forge test

# Run tests with verbose output
forge test -vvv

# Run specific test file
forge test --match-contract MarketFactoryTest
```

Expected output: All 283+ tests should pass.

### 2.4 Start Local Blockchain (Anvil)

Open a new terminal and start Anvil (local Ethereum node):

```bash
cd ~/aurum-labs/aurum-contracts

# Start Anvil with a deterministic mnemonic for consistent addresses
anvil --mnemonic "test test test test test test test test test test test junk"
```

Anvil will display:

```
Available Accounts
==================
(0) 0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266 (10000 ETH)
(1) 0x70997970C51812dc3A010C7d01b50e0d17dc79C8 (10000 ETH)
...

Private Keys
==================
(0) 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80
(1) 0x59c6995e998f97a5a0044966f0945389dc9e86dae88c7a8412f4603b6b78690d
...

Listening on 127.0.0.1:8545
```

**Keep this terminal running** - it's your local blockchain.

### 2.5 Deploy Contracts to Local Blockchain

Open another terminal and deploy contracts:

```bash
cd ~/aurum-labs/aurum-contracts

# Set environment variables
export PRIVATE_KEY=ac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80
export RPC_URL=http://127.0.0.1:8545

# Deploy all contracts
forge script script/DeployAll.s.sol:DeployAll --rpc-url $RPC_URL --private-key $PRIVATE_KEY --broadcast
```

**Save the deployed contract addresses** from the output. You'll need them for the frontend configuration.

Example output:

```
== Logs ==
  Deploying Aurum Labs contracts...
  ConditionalTokens deployed at: 0x5FbDB2315678afecb367f032d93F642f64180aa3
  AurumToken deployed at: 0xe7f1725E7734CE288F8367e1Bb143E90bb3F0512
  FeeCollector deployed at: 0x9fE46736679d2D9a65F0992F2272dE9f3c7fa6e0
  AurumOracle deployed at: 0xCf7Ed3AccA5a467e9e704C703E8D87F634fB0Fc9
  MarketFactory deployed at: 0xDc64a140Aa3E981100a9becA4E685f962f0cF6C9
```

---

## Step 3: Set Up Trading App (aurum-app)

### 3.1 Install Dependencies

```bash
cd ~/aurum-labs/aurum-app

# Install dependencies
npm install
```

### 3.2 Configure Environment

Create a `.env` file with your local contract addresses:

```bash
cat > .env << 'EOF'
# Local Development Configuration
VITE_CHAIN_ID=31337
VITE_RPC_URL=http://127.0.0.1:8545

# Contract Addresses (update with your deployed addresses)
VITE_CONDITIONAL_TOKENS_ADDRESS=0x5FbDB2315678afecb367f032d93F642f64180aa3
VITE_AURUM_TOKEN_ADDRESS=0xe7f1725E7734CE288F8367e1Bb143E90bb3F0512
VITE_FEE_COLLECTOR_ADDRESS=0x9fE46736679d2D9a65F0992F2272dE9f3c7fa6e0
VITE_AURUM_ORACLE_ADDRESS=0xCf7Ed3AccA5a467e9e704C703E8D87F634fB0Fc9
VITE_MARKET_FACTORY_ADDRESS=0xDc64a140Aa3E981100a9becA4E685f962f0cF6C9

# Optional: WalletConnect (not needed for local testing with Anvil)
VITE_WALLETCONNECT_PROJECT_ID=
EOF
```

**Important**: Update the contract addresses with the actual addresses from your deployment output.

### 3.3 Configure Wagmi for Local Chain

You may need to add the local Anvil chain to your wagmi configuration. Check `src/wagmi.ts` or similar configuration file and ensure it includes:

```typescript
import { defineChain } from 'viem'

export const anvil = defineChain({
  id: 31337,
  name: 'Anvil',
  nativeCurrency: {
    decimals: 18,
    name: 'Ether',
    symbol: 'ETH',
  },
  rpcUrls: {
    default: {
      http: ['http://127.0.0.1:8545'],
    },
  },
})
```

### 3.4 Start Development Server

```bash
# Start the development server
npm run dev
```

The app will be available at `http://localhost:5173` (or similar port shown in terminal).

---

## Step 4: Set Up Landing Page (aurum-website)

### 4.1 Install Dependencies

```bash
cd ~/aurum-labs/aurum-website

# Install dependencies with pnpm
pnpm install
```

### 4.2 Configure Environment

Create a `.env` file to configure the Launch App button to point to your local trading app:

```bash
cat > .env << 'EOF'
# dApp URL - Points to the local trading app
VITE_DAPP_URL=http://localhost:5173
EOF
```

**Note**: The `VITE_DAPP_URL` environment variable controls where the "Launch App" button navigates. For local testing, this should point to your local aurum-app instance. For production, set it to your deployed dApp URL (e.g., `https://app.aurumlabs.io`).

### 4.3 Start Development Server

```bash
# Start the development server (runs on a different port than aurum-app)
pnpm run dev
```

The website will be available at `http://localhost:5174` (or the port shown). Note that if aurum-app is already running on port 5173, the landing page will automatically use the next available port.

### 4.4 Test the Launch App Button

1. Ensure aurum-app is running on `http://localhost:5173`
2. Open the landing page at `http://localhost:5174`
3. Click the "Launch App" button in the navigation or hero section
4. The trading app should open in a new tab

---

## Step 5: Configure MetaMask for Local Testing

### 5.1 Add Anvil Network to MetaMask

1. Open MetaMask
2. Click the network dropdown → **Add Network** → **Add a network manually**
3. Enter the following details:

| Field | Value |
|-------|-------|
| Network Name | Anvil Local |
| RPC URL | http://127.0.0.1:8545 |
| Chain ID | 31337 |
| Currency Symbol | ETH |

4. Click **Save**

### 5.2 Import Test Account

Import one of the Anvil test accounts into MetaMask:

1. Click your account icon → **Import Account**
2. Select **Private Key**
3. Paste a private key from Anvil output, e.g.:
   ```
   0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80
   ```
4. Click **Import**

You should now have an account with 10,000 ETH for testing.

---

## Step 6: End-to-End Testing Flow

Now you can test the complete prediction market flow:

### 6.1 Grant Market Creator Role

Before creating markets, grant the market creator role to your test account:

```bash
cd ~/aurum-labs/aurum-contracts

# Grant market creator role (replace with your account address)
cast send $MARKET_FACTORY_ADDRESS "grantMarketCreator(address)" 0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266 \
  --rpc-url http://127.0.0.1:8545 \
  --private-key 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80
```

### 6.2 Create a Test Market

Using cast (command line):

```bash
# Create a market (example)
cast send $MARKET_FACTORY_ADDRESS "createMarket(bytes32,uint256,uint256,uint256)" \
  0x1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef \
  2 \
  $(date -d "+7 days" +%s) \
  1000000000000000000 \
  --rpc-url http://127.0.0.1:8545 \
  --private-key 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80
```

Or use the trading app UI to create markets.

### 6.3 Test Trading

1. Open the trading app at `http://localhost:5173`
2. Connect your MetaMask wallet
3. Select a market
4. Buy outcome tokens
5. Sell outcome tokens
6. Add/remove liquidity

### 6.4 Test Market Resolution

```bash
# Propose an outcome (as oracle)
cast send $AURUM_ORACLE_ADDRESS "proposeOutcome(bytes32,uint256[])" \
  $QUESTION_ID \
  "[1000000000000000000,0]" \
  --rpc-url http://127.0.0.1:8545 \
  --private-key 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80

# Wait for dispute period, then finalize
# ... (after dispute period)

# Redeem winning tokens
cast send $CONDITIONAL_TOKENS_ADDRESS "redeemPositions(address,bytes32,bytes32,uint256[])" \
  $COLLATERAL_ADDRESS \
  $PARENT_COLLECTION_ID \
  $CONDITION_ID \
  "[1]" \
  --rpc-url http://127.0.0.1:8545 \
  --private-key 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80
```

---

## Quick Reference: Running Services

Open multiple terminal windows/tabs:

| Terminal | Directory | Command | URL |
|----------|-----------|---------|-----|
| 1 | aurum-contracts | `anvil --mnemonic "test test..."` | http://127.0.0.1:8545 |
| 2 | aurum-app | `npm run dev` | http://localhost:5173 |
| 3 | aurum-website | `pnpm run dev` | http://localhost:5174 |

---

## Environment Variables Reference

### aurum-app (.env)

```env
# Network
VITE_CHAIN_ID=31337
VITE_RPC_URL=http://127.0.0.1:8545

# Contracts (update with your deployed addresses)
VITE_CONDITIONAL_TOKENS_ADDRESS=0x...
VITE_AURUM_TOKEN_ADDRESS=0x...
VITE_FEE_COLLECTOR_ADDRESS=0x...
VITE_AURUM_ORACLE_ADDRESS=0x...
VITE_MARKET_FACTORY_ADDRESS=0x...

# Optional
VITE_WALLETCONNECT_PROJECT_ID=
VITE_TRANSACTION_DEADLINE_MINUTES=60
VITE_SLIPPAGE_TOLERANCE_PERCENT=1
```

### aurum-website (.env)

```env
# dApp URL - Where the Launch App button navigates
# For local development, point to local aurum-app
VITE_DAPP_URL=http://localhost:5173
```

### Shell Environment (for cast commands)

```bash
export RPC_URL=http://127.0.0.1:8545
export PRIVATE_KEY=ac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80
export CONDITIONAL_TOKENS_ADDRESS=0x5FbDB2315678afecb367f032d93F642f64180aa3
export AURUM_TOKEN_ADDRESS=0xe7f1725E7734CE288F8367e1Bb143E90bb3F0512
export FEE_COLLECTOR_ADDRESS=0x9fE46736679d2D9a65F0992F2272dE9f3c7fa6e0
export AURUM_ORACLE_ADDRESS=0xCf7Ed3AccA5a467e9e704C703E8D87F634fB0Fc9
export MARKET_FACTORY_ADDRESS=0xDc64a140Aa3E981100a9becA4E685f962f0cF6C9
```

---

## Troubleshooting

### Anvil Not Starting

**Error**: `anvil: command not found`

**Solution**: Run `foundryup` to install/update Foundry tools, then restart your terminal.

### Contract Deployment Fails

**Error**: `Failed to send transaction`

**Solution**: Ensure Anvil is running in another terminal and the RPC_URL is correct.

### MetaMask Not Connecting

**Error**: MetaMask shows wrong network or won't connect

**Solution**:
1. Ensure you've added the Anvil network with Chain ID 31337
2. Reset MetaMask account: Settings → Advanced → Reset Account
3. Restart the browser

### Transaction Reverts

**Error**: `execution reverted`

**Solution**:
1. Check if you have the required role (market creator, resolver)
2. Verify contract addresses are correct
3. Check function parameters match expected types
4. Run with `-vvvv` flag for detailed trace: `cast call ... -vvvv`

### Frontend Not Loading Contracts

**Error**: Contract calls fail or return undefined

**Solution**:
1. Verify `.env` file has correct contract addresses
2. Restart the dev server after changing `.env`
3. Check browser console for RPC errors
4. Ensure wagmi is configured for chain ID 31337

### Port Already in Use

**Error**: `Port 5173 is already in use`

**Solution**:
```bash
# Find and kill the process
lsof -i :5173
kill -9 <PID>

# Or use a different port
npm run dev -- --port 3000
```

---

## Useful Cast Commands

```bash
# Check account balance
cast balance 0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266 --rpc-url $RPC_URL

# Get current block number
cast block-number --rpc-url $RPC_URL

# Read contract state
cast call $MARKET_FACTORY_ADDRESS "owner()" --rpc-url $RPC_URL

# Decode transaction data
cast 4byte-decode <calldata>

# Get transaction receipt
cast receipt <tx-hash> --rpc-url $RPC_URL
```

---

## Next Steps

After completing local testing:

1. **Deploy to Base Sepolia** - See [Base Sepolia Deployment Guide](../deployment/base-sepolia.md)
2. **Set up CI/CD** - Configure GitHub Actions for automated testing
3. **Deploy frontend** - See [Cloudflare Setup Guide](../deployment/cloudflare-setup.md)
4. **Deploy backend** - See [Render Setup Guide](../deployment/render-setup.md)
