# Render Setup Guide

This guide walks you through setting up Render for hosting the Aurum Labs backend services. Render provides managed hosting for web services, databases, and background workers with automatic deployments from Git.

## Repository Overview

| Repository | Purpose | Status | Recommended Hosting |
|------------|---------|--------|---------------------|
| **aurum-backend** | API server for prediction markets | Not yet implemented | Render Web Service |
| **aurum-website** | Landing page with server | Implemented | Render Web Service (full) or Cloudflare Pages (static) |

This guide covers deploying both **aurum-website** (which has a Node.js server) and provides a template for **aurum-backend** when it is implemented.

## Prerequisites

Before starting, ensure you have the following:

1. A Render account (free tier available)
2. Access to the Aurum Labs repositories
3. A PostgreSQL database (can be created on Render)

## Step 1: Create a Render Account

Navigate to [render.com](https://render.com/) and click **Get Started** or **Sign Up**. You can sign up with GitHub for easier repository access. Complete the account verification process to finish registration.

## Step 2: Connect Your GitHub Account

In the Render dashboard, go to **Account Settings** and click **Connect GitHub**. Authorize Render to access your repositories. This enables automatic deployments when you push code changes.

---

## Deploying aurum-website

The aurum-website repository includes a Node.js server that handles the landing page and waitlist functionality.

### Step 3a: Create Web Service for aurum-website

Click **New +** in the Render dashboard and select **Web Service**. Connect your GitHub repository and select **aurum-website**. Configure the following settings:

| Setting | Value |
|---------|-------|
| **Name** | `aurum-website` |
| **Region** | Choose closest to your users |
| **Branch** | `main` |
| **Root Directory** | Leave empty |
| **Runtime** | `Node` |
| **Build Command** | `pnpm install && pnpm run build` |
| **Start Command** | `pnpm run start` |
| **Instance Type** | `Free` (development) or `Starter` (production) |

### Environment Variables for aurum-website

Add the following environment variables in the **Environment** section:

| Variable | Description | Example |
|----------|-------------|---------|
| `NODE_ENV` | Environment mode | `production` |
| `PORT` | Server port (Render sets this automatically) | `10000` |

### Deploy aurum-website

Click **Create Web Service** and wait for the initial deployment (5-10 minutes). Once deployed, you will receive a `*.onrender.com` URL.

---

## Deploying aurum-backend (Template)

The aurum-backend repository is currently empty and awaiting implementation. This section provides a template for when the backend is built.

### Step 3b: Create Web Service for aurum-backend

When aurum-backend is implemented, click **New +** and select **Web Service**. Connect the **aurum-backend** repository and configure:

| Setting | Value |
|---------|-------|
| **Name** | `aurum-api` |
| **Region** | Choose closest to your users |
| **Branch** | `main` |
| **Root Directory** | Leave empty |
| **Runtime** | `Node` (or `Python` if using FastAPI/Flask) |
| **Build Command** | `npm install && npm run build` |
| **Start Command** | `npm run start` |
| **Instance Type** | `Starter` (production recommended) |

### Environment Variables for aurum-backend

When the backend is implemented, configure these environment variables:

#### Required Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `NODE_ENV` | Environment mode | `production` |
| `PORT` | Server port | `10000` |
| `DATABASE_URL` | PostgreSQL connection string | `postgresql://user:pass@host:5432/db` |
| `JWT_SECRET` | JWT signing secret (generate securely) | `your-secure-secret-min-32-chars` |
| `CORS_ORIGIN` | Frontend URL for CORS | `https://app.aurumlabs.io` |

#### Blockchain Configuration

| Variable | Description | Value (Base Sepolia) |
|----------|-------------|----------------------|
| `RPC_URL` | Base Sepolia RPC URL | `https://sepolia.base.org` |
| `CHAIN_ID` | Target chain ID | `84532` |
| `PRIVATE_KEY` | Backend wallet private key | `0x...` (keep secure!) |
| `CONDITIONAL_TOKENS_ADDRESS` | Contract address | `0x9A7A037469204604C29a44901b69B0bBB1d45B13` |
| `MARKET_FACTORY_ADDRESS` | Contract address | `0xaF8ddE93C551ce4f6A21db07508858Fb15E4bbC9` |
| `AURUM_TOKEN_ADDRESS` | Contract address | `0xdBfa3D8516C49581e2A6cBbD75F02F24c59811c1` |
| `AURUM_ORACLE_ADDRESS` | Contract address | `0x4EC0295F0344ac264EB83bd7bDb0069015702297` |
| `FEE_COLLECTOR_ADDRESS` | Contract address | `0xBCC8aC562085E207460B9a0342d62a480DD9caAC` |

#### Optional Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `LOG_LEVEL` | Logging verbosity | `info` |
| `RATE_LIMIT_WINDOW` | Rate limit window (ms) | `60000` |
| `RATE_LIMIT_MAX` | Max requests per window | `100` |

---

## Step 4: Create PostgreSQL Database

If your backend requires a database, click **New +** and select **PostgreSQL**. Configure the database:

| Setting | Value |
|---------|-------|
| **Name** | `aurum-db` |
| **Database** | `aurum` |
| **User** | `aurum_user` |
| **Region** | Same as your web service |
| **PostgreSQL Version** | `15` (latest stable) |
| **Instance Type** | `Free` (development) or `Starter` (production) |

After creation, copy the **Internal Database URL** and use it as the `DATABASE_URL` environment variable in your web service. The internal URL provides faster, more secure connections within Render's network.

## Step 5: Configure Health Check

In your web service settings, find **Health Check Path** and set it to `/health` or `/api/health`. This allows Render to monitor your service status and automatically restart it if it becomes unhealthy.

## Step 6: Configure Custom Domain (Optional)

To use a custom domain, go to your web service settings and click **Custom Domains**. Click **Add Custom Domain** and enter your domain (e.g., `api.aurumlabs.io`).

### DNS Configuration

Add a CNAME record to your DNS provider:

| Type | Name | Target |
|------|------|--------|
| CNAME | api | aurum-api.onrender.com |

Wait for SSL certificate provisioning, which happens automatically after DNS propagation.

## Step 7: Set Up Background Workers (Optional)

If your backend requires background job processing (e.g., market resolution, event indexing), create a background worker:

1. Click **New +** and select **Background Worker**
2. Connect the same repository
3. Configure with the same environment variables
4. Set the start command to your worker script (e.g., `npm run worker`)

## Database Migrations

### Running Migrations on Deploy

Add a pre-deploy command in your service settings or `render.yaml`:

```yaml
preDeployCommand: npm run migrate
```

### Manual Migration

To run migrations manually, go to your web service, click **Shell**, and run your migration command (e.g., `npm run migrate`).

## Monitoring and Logs

### Viewing Logs

Go to your service dashboard and click the **Logs** tab. Use filters to search for specific log entries. Logs are retained for 7 days on the free tier and 30 days on paid plans.

### Setting Up Alerts

Go to **Settings** > **Notifications** and configure alerts for deploy failures, service downtime, and high error rates.

## Continuous Deployment

Render automatically deploys when you push to the configured branch. Production deployments occur on push to `main`, and you can configure preview environments for pull requests.

### Disabling Auto-Deploy

If you prefer manual deployments, go to **Settings** > **Build & Deploy** and toggle off **Auto-Deploy**.

## Cost Considerations

### Free Tier Limitations

The free tier has several limitations to be aware of. Services spin down after 15 minutes of inactivity, which causes cold start delays. You get 750 hours per month of free usage, limited to 512 MB RAM. Custom domains are not available on the free tier for web services.

### Recommended Production Setup

| Service | Instance Type | Monthly Cost |
|---------|---------------|--------------|
| Web Service (aurum-website) | Starter | $7 |
| Web Service (aurum-api) | Starter | $7 |
| PostgreSQL | Starter | $7 |
| Background Worker | Starter | $7 |

Total estimated cost for production: $14-28/month depending on your needs.

## Troubleshooting

### Service Won't Start

Check the build logs for compilation errors. Verify all required environment variables are set. Ensure the start command is correct and matches your package.json scripts. Check for port binding issues by using the `PORT` environment variable.

### Database Connection Issues

Verify the `DATABASE_URL` is correct and uses the Internal URL for Render PostgreSQL. Check if the database is running and accessible. Ensure connection limits are not exceeded.

### Slow Cold Starts (Free Tier)

Free tier services spin down after 15 minutes of inactivity. To mitigate this, upgrade to a paid tier for always-on services, use a health check service to keep the service alive, or accept the cold start latency for development environments.

## Security Best Practices

Use environment variables for all secrets and never commit them to the repository. SSL is automatically enabled on Render. Configure CORS properly to only allow requests from your frontend domain. Use strong, randomly generated JWT secrets (minimum 32 characters). Rotate credentials regularly and enable IP allowlisting for database access if connecting from external services.

## Alternative: Railway

If Render doesn't meet your needs, consider [Railway](https://railway.app/) as an alternative. Railway offers similar features with a different pricing model and developer experience. Key differences include usage-based pricing instead of instance-based, built-in Redis and other databases, and a more generous free tier for hobbyist projects.

## Next Steps

After deploying the backend, proceed with the following:

1. [Configure Cloudflare for frontend hosting](./cloudflare-setup.md)
2. [Review deployed smart contracts](./base-sepolia.md)
3. [Update application URLs](./url-configuration.md)
