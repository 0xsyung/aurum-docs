# Cloudflare Pages Setup Guide

This guide walks you through setting up Cloudflare Pages for hosting the Aurum Labs frontend application.

## Prerequisites

- A Cloudflare account (free tier is sufficient)
- Access to the Aurum Labs frontend repository
- A custom domain (optional)

## Step 1: Create a Cloudflare Account

1. Navigate to [cloudflare.com](https://www.cloudflare.com/)
2. Click **Sign Up** in the top right corner
3. Enter your email address and create a password
4. Verify your email address by clicking the link sent to your inbox
5. Complete the account setup wizard

## Step 2: Access Cloudflare Pages

1. Log in to your Cloudflare dashboard
2. In the left sidebar, click **Workers & Pages**
3. Click **Create application**
4. Select **Pages** tab
5. Click **Connect to Git**

## Step 3: Connect Your GitHub Repository

1. Click **Connect GitHub** and authorize Cloudflare to access your repositories
2. Select the **aurum-frontend** repository from the list
3. Click **Begin setup**

## Step 4: Configure Build Settings

Configure the following build settings:

| Setting | Value |
|---------|-------|
| **Project name** | `aurum-labs` |
| **Production branch** | `main` |
| **Framework preset** | `Vite` |
| **Build command** | `npm run build` |
| **Build output directory** | `dist` |
| **Root directory** | `/` |

## Step 5: Set Environment Variables

Click **Environment variables** and add the following:

### Production Environment Variables

| Variable Name | Description | Example Value |
|---------------|-------------|---------------|
| `VITE_API_URL` | Backend API URL | `https://api.aurumlabs.io` |
| `VITE_CHAIN_ID` | Target blockchain chain ID | `84532` (Base Sepolia) |
| `VITE_CONDITIONAL_TOKENS_ADDRESS` | ConditionalTokens contract | `0x...` |
| `VITE_MARKET_FACTORY_ADDRESS` | MarketFactory contract | `0x...` |
| `VITE_AURUM_TOKEN_ADDRESS` | AurumToken contract | `0x...` |
| `VITE_AURUM_ORACLE_ADDRESS` | AurumOracle contract | `0x...` |
| `VITE_FEE_COLLECTOR_ADDRESS` | FeeCollector contract | `0x...` |
| `VITE_WALLETCONNECT_PROJECT_ID` | WalletConnect project ID | `your-project-id` |

> **Note**: For testnet deployments, use the Base Sepolia contract addresses from the [deployment documentation](./base-sepolia.md).

## Step 6: Deploy

1. Click **Save and Deploy**
2. Wait for the build to complete (typically 2-5 minutes)
3. Once deployed, you'll receive a `*.pages.dev` URL

## Step 7: Configure Custom Domain (Optional)

1. Go to your Pages project settings
2. Click **Custom domains**
3. Click **Set up a custom domain**
4. Enter your domain (e.g., `app.aurumlabs.io`)
5. Follow the DNS configuration instructions:
   - Add a CNAME record pointing to your `*.pages.dev` URL
   - Or transfer your domain to Cloudflare for automatic configuration

### DNS Configuration Example

```
Type: CNAME
Name: app
Target: aurum-labs.pages.dev
Proxy status: Proxied
```

## Step 8: Enable Preview Deployments

Cloudflare Pages automatically creates preview deployments for pull requests:

1. Go to **Settings** > **Builds & deployments**
2. Enable **Preview deployments**
3. Configure branch patterns if needed

## Continuous Deployment

After initial setup, Cloudflare Pages will automatically:

- Deploy to production when you push to `main`
- Create preview deployments for pull requests
- Roll back to previous deployments if needed

## Troubleshooting

### Build Failures

1. Check the build logs in the Cloudflare dashboard
2. Ensure all environment variables are set correctly
3. Verify the build command works locally

### Environment Variables Not Working

1. Ensure variables are prefixed with `VITE_` for Vite projects
2. Redeploy after adding/changing environment variables
3. Check for typos in variable names

### Custom Domain Issues

1. Verify DNS propagation using [dnschecker.org](https://dnschecker.org)
2. Wait up to 24 hours for DNS changes to propagate
3. Ensure SSL/TLS is set to "Full" or "Full (strict)" in Cloudflare

## Security Best Practices

1. **Never commit secrets** to the repository
2. **Use environment variables** for all sensitive configuration
3. **Enable Web Application Firewall (WAF)** for production
4. **Configure rate limiting** to prevent abuse
5. **Enable HTTPS** (automatic with Cloudflare)

## Cost Considerations

Cloudflare Pages free tier includes:

- Unlimited sites
- Unlimited requests
- Unlimited bandwidth
- 500 builds per month
- 1 concurrent build

For most projects, the free tier is sufficient.

## Next Steps

- [Configure Render for backend hosting](./render-setup.md)
- [Deploy smart contracts to testnet](./base-sepolia.md)
- [Update application URLs](./url-configuration.md)
