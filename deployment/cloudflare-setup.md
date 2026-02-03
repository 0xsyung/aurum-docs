# Cloudflare Pages Setup Guide

This guide walks you through setting up Cloudflare Pages for hosting the Aurum Labs frontend applications. Aurum Labs has two frontend projects that can be deployed to Cloudflare Pages.

## Repository Overview

| Repository | Purpose | Type | Recommended Hosting |
|------------|---------|------|---------------------|
| **aurum-app** | Prediction market trading interface | Static React app | Cloudflare Pages |
| **aurum-website** | Landing page with waitlist | React + Node server | Cloudflare Pages (static) or Render (full) |

This guide focuses on deploying **aurum-app** to Cloudflare Pages, as it is a pure static application. For aurum-website, see the notes at the end of this document.

## Prerequisites

Before starting, ensure you have the following:

1. A Cloudflare account (free tier is sufficient)
2. Access to the [aurum-app repository](https://github.com/0xsyung/aurum-app)
3. A custom domain (optional)

## Step 1: Create a Cloudflare Account

Navigate to [cloudflare.com](https://www.cloudflare.com/) and click **Sign Up** in the top right corner. Enter your email address and create a password, then verify your email address by clicking the link sent to your inbox. Complete the account setup wizard to finish registration.

## Step 2: Access Cloudflare Pages

Log in to your Cloudflare dashboard and click **Workers & Pages** in the left sidebar. Click **Create application**, select the **Pages** tab, and then click **Connect to Git**.

## Step 3: Connect Your GitHub Repository

Click **Connect GitHub** and authorize Cloudflare to access your repositories. Select the **aurum-app** repository from the list and click **Begin setup**.

## Step 4: Configure Build Settings

Configure the following build settings for the aurum-app project:

| Setting | Value |
|---------|-------|
| **Project name** | `aurum-app` |
| **Production branch** | `main` |
| **Framework preset** | `Vite` |
| **Build command** | `npm run build` |
| **Build output directory** | `dist` |
| **Root directory** | `/` |

The build command `npm run build` executes `tsc && vite build`, which compiles TypeScript and builds the production bundle.

## Step 5: Set Environment Variables

Click **Environment variables** and add the following variables. These are required for the application to connect to the blockchain and smart contracts.

### Network Configuration

| Variable Name | Description | Value (Base Sepolia) |
|---------------|-------------|----------------------|
| `VITE_CHAIN_ID` | Target blockchain chain ID | `84532` |
| `VITE_RPC_URL` | RPC endpoint URL | `https://sepolia.base.org` |
| `VITE_BLOCK_EXPLORER` | Block explorer URL | `https://sepolia.basescan.org` |

### Smart Contract Addresses

| Variable Name | Description | Value |
|---------------|-------------|-------|
| `VITE_CONDITIONAL_TOKENS_ADDRESS` | ConditionalTokens contract | `0x9A7A037469204604C29a44901b69B0bBB1d45B13` |
| `VITE_MARKET_FACTORY_ADDRESS` | MarketFactory contract | `0xaF8ddE93C551ce4f6A21db07508858Fb15E4bbC9` |
| `VITE_AURUM_TOKEN_ADDRESS` | AurumToken contract | `0xdBfa3D8516C49581e2A6cBbD75F02F24c59811c1` |
| `VITE_AURUM_ORACLE_ADDRESS` | AurumOracle contract | `0x4EC0295F0344ac264EB83bd7bDb0069015702297` |
| `VITE_FEE_COLLECTOR_ADDRESS` | FeeCollector contract | `0xBCC8aC562085E207460B9a0342d62a480DD9caAC` |
| `VITE_USDC_ADDRESS` | USDC collateral token | `0x036CbD53842c5426634e7929541eC2318f3dCF7e` |

### Application Configuration

| Variable Name | Description | Example Value |
|---------------|-------------|---------------|
| `VITE_API_URL` | Backend API URL (when available) | `https://api.aurumlabs.io` |
| `VITE_WALLETCONNECT_PROJECT_ID` | WalletConnect project ID | `your-project-id` |
| `VITE_TRANSACTION_DEADLINE_MINUTES` | Default transaction deadline | `60` |
| `VITE_SLIPPAGE_TOLERANCE_PERCENT` | Default slippage tolerance | `1` |

For testnet deployments, use the Base Sepolia contract addresses from the [deployment documentation](./base-sepolia.md).

## Step 6: Deploy

Click **Save and Deploy** and wait for the build to complete (typically 2-5 minutes). Once deployed, you will receive a `*.pages.dev` URL where your application is accessible.

## Step 7: Configure Custom Domain (Optional)

To use a custom domain, go to your Pages project settings and click **Custom domains**. Click **Set up a custom domain** and enter your domain (e.g., `app.aurumlabs.io`). Follow the DNS configuration instructions provided by Cloudflare.

### DNS Configuration Example

Add a CNAME record to your DNS provider:

| Type | Name | Target | Proxy Status |
|------|------|--------|--------------|
| CNAME | app | aurum-app.pages.dev | Proxied |

If your domain is already on Cloudflare, the configuration will be automatic. Otherwise, you may need to wait up to 24 hours for DNS propagation.

## Step 8: Enable Preview Deployments

Cloudflare Pages automatically creates preview deployments for pull requests. To configure this feature, go to **Settings** > **Builds & deployments** and enable **Preview deployments**. Configure branch patterns if you want to limit which branches trigger preview builds.

## Continuous Deployment

After initial setup, Cloudflare Pages will automatically deploy to production when you push to `main`, create preview deployments for pull requests, and allow rollback to previous deployments if needed.

## Deploying aurum-website

The aurum-website repository contains both a React frontend and a Node.js server component. You have two options for deployment:

### Option A: Static Deployment (Cloudflare Pages)

If you only need the static landing page without server-side functionality:

| Setting | Value |
|---------|-------|
| **Project name** | `aurum-website` |
| **Production branch** | `main` |
| **Framework preset** | `Vite` |
| **Build command** | `pnpm install && pnpm run build` |
| **Build output directory** | `dist/public` |
| **Root directory** | `/` |

Note that this approach will not include the server-side functionality. The waitlist form submissions would need to be handled by a separate backend service.

### Option B: Full Deployment (Render)

If you need the full application with server-side functionality, deploy to Render instead. See the [Render Setup Guide](./render-setup.md) for instructions on deploying Node.js applications.

## Troubleshooting

### Build Failures

If your build fails, check the build logs in the Cloudflare dashboard for specific error messages. Common issues include missing environment variables, incorrect build commands, and TypeScript compilation errors. Ensure all required environment variables are set and verify that the build command works locally by running `npm run build` in your project directory.

### Environment Variables Not Working

Environment variables in Vite must be prefixed with `VITE_` to be accessible in the client-side code. After adding or changing environment variables, you must trigger a new deployment for the changes to take effect. Double-check for typos in variable names, as they are case-sensitive.

### Custom Domain Issues

DNS changes can take up to 24 hours to propagate globally. Use [dnschecker.org](https://dnschecker.org) to verify DNS propagation status. Ensure your Cloudflare SSL/TLS settings are configured to "Full" or "Full (strict)" for proper HTTPS functionality.

### Web3 Connection Issues

If the application cannot connect to the blockchain, verify that the `VITE_RPC_URL` is correct and accessible. Check that all contract addresses are valid and deployed on the target network. Ensure the `VITE_CHAIN_ID` matches the network where contracts are deployed.

## Security Best Practices

Never commit secrets or private keys to the repository. Use environment variables for all sensitive configuration. Enable Web Application Firewall (WAF) for production deployments to protect against common attacks. Configure rate limiting to prevent abuse. HTTPS is automatically enabled with Cloudflare.

## Cost Considerations

Cloudflare Pages free tier includes unlimited sites, unlimited requests, unlimited bandwidth, 500 builds per month, and 1 concurrent build. For most projects, the free tier is sufficient. If you need more concurrent builds or advanced features, paid plans start at $20/month.

## Next Steps

After deploying the frontend, proceed with the following:

1. [Configure Render for backend hosting](./render-setup.md) (when aurum-backend is implemented)
2. [Review deployed smart contracts](./base-sepolia.md)
3. [Update application URLs](./url-configuration.md)
