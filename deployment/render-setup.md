# Render Setup Guide

This guide walks you through setting up Render for hosting the Aurum Labs backend API and services.

## Prerequisites

- A Render account (free tier available)
- Access to the Aurum Labs backend repository
- PostgreSQL database (can be provisioned on Render)

## Step 1: Create a Render Account

1. Navigate to [render.com](https://render.com/)
2. Click **Get Started for Free**
3. Sign up with GitHub (recommended) or email
4. Verify your email if signing up with email
5. Complete the onboarding process

## Step 2: Create a PostgreSQL Database

### Option A: Render PostgreSQL (Recommended)

1. From the Render dashboard, click **New +**
2. Select **PostgreSQL**
3. Configure the database:

| Setting | Value |
|---------|-------|
| **Name** | `aurum-db` |
| **Database** | `aurum` |
| **User** | `aurum_user` |
| **Region** | Choose closest to your users |
| **PostgreSQL Version** | `15` |
| **Instance Type** | `Free` (for development) or `Starter` (for production) |

4. Click **Create Database**
5. Copy the **Internal Database URL** for later use

### Option B: External PostgreSQL

If using an external PostgreSQL provider (e.g., Supabase, Neon):

1. Create a database on your provider
2. Note the connection string
3. Ensure the database is accessible from Render's IP ranges

## Step 3: Deploy the Backend Web Service

1. From the Render dashboard, click **New +**
2. Select **Web Service**
3. Connect your GitHub repository:
   - Click **Connect account** if not already connected
   - Select the **aurum-backend** repository
4. Configure the service:

| Setting | Value |
|---------|-------|
| **Name** | `aurum-api` |
| **Region** | Same as database |
| **Branch** | `main` |
| **Root Directory** | `/` |
| **Runtime** | `Node` |
| **Build Command** | `npm install && npm run build` |
| **Start Command** | `npm start` |
| **Instance Type** | `Free` (development) or `Starter` (production) |

## Step 4: Configure Environment Variables

In the **Environment** section, add the following variables:

### Required Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `NODE_ENV` | Environment mode | `production` |
| `PORT` | Server port | `10000` |
| `DATABASE_URL` | PostgreSQL connection string | `postgresql://...` |
| `JWT_SECRET` | JWT signing secret | `your-secure-secret` |
| `CORS_ORIGIN` | Frontend URL | `https://app.aurumlabs.io` |

### Blockchain Configuration

| Variable | Description | Example |
|----------|-------------|---------|
| `RPC_URL` | Base Sepolia RPC URL | `https://sepolia.base.org` |
| `CHAIN_ID` | Target chain ID | `84532` |
| `PRIVATE_KEY` | Backend wallet private key | `0x...` |
| `CONDITIONAL_TOKENS_ADDRESS` | Contract address | `0x...` |
| `MARKET_FACTORY_ADDRESS` | Contract address | `0x...` |
| `AURUM_TOKEN_ADDRESS` | Contract address | `0x...` |
| `AURUM_ORACLE_ADDRESS` | Contract address | `0x...` |
| `FEE_COLLECTOR_ADDRESS` | Contract address | `0x...` |

### Optional Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `LOG_LEVEL` | Logging verbosity | `info` |
| `RATE_LIMIT_WINDOW` | Rate limit window (ms) | `60000` |
| `RATE_LIMIT_MAX` | Max requests per window | `100` |

## Step 5: Configure Health Check

1. In service settings, find **Health Check Path**
2. Set it to `/health` or `/api/health`
3. This ensures Render can monitor your service status

## Step 6: Deploy

1. Click **Create Web Service**
2. Wait for the initial deployment (5-10 minutes)
3. Once deployed, you'll receive a `*.onrender.com` URL

## Step 7: Configure Custom Domain (Optional)

1. Go to your web service settings
2. Click **Custom Domains**
3. Click **Add Custom Domain**
4. Enter your domain (e.g., `api.aurumlabs.io`)
5. Configure DNS:

### DNS Configuration

Add a CNAME record:

```
Type: CNAME
Name: api
Target: aurum-api.onrender.com
```

6. Wait for SSL certificate provisioning (automatic)

## Step 8: Set Up Background Workers (Optional)

If your backend requires background job processing:

1. Click **New +** > **Background Worker**
2. Connect the same repository
3. Configure:

| Setting | Value |
|---------|-------|
| **Name** | `aurum-worker` |
| **Build Command** | `npm install && npm run build` |
| **Start Command** | `npm run worker` |

4. Add the same environment variables as the web service

## Database Migrations

### Running Migrations on Deploy

Add a pre-deploy command in your `render.yaml` or service settings:

```yaml
preDeployCommand: npm run migrate
```

### Manual Migration

1. Go to your web service
2. Click **Shell**
3. Run: `npm run migrate`

## Monitoring and Logs

### Viewing Logs

1. Go to your service dashboard
2. Click **Logs** tab
3. Use filters to search specific log entries

### Setting Up Alerts

1. Go to **Settings** > **Notifications**
2. Configure alerts for:
   - Deploy failures
   - Service downtime
   - High error rates

## Auto-Scaling (Paid Plans)

For production workloads on paid plans:

1. Go to **Settings** > **Scaling**
2. Enable **Auto-Scaling**
3. Configure min/max instances
4. Set scaling thresholds

## Continuous Deployment

Render automatically deploys when you push to the configured branch:

- **Production**: Deploys on push to `main`
- **Preview**: Can be configured for pull requests

### Disabling Auto-Deploy

If you prefer manual deployments:

1. Go to **Settings** > **Build & Deploy**
2. Toggle off **Auto-Deploy**

## Cost Considerations

### Free Tier Limitations

- Services spin down after 15 minutes of inactivity
- 750 hours/month of free usage
- Limited to 512 MB RAM
- No custom domains on free tier

### Recommended Production Setup

| Service | Instance Type | Monthly Cost |
|---------|---------------|--------------|
| Web Service | Starter | $7 |
| PostgreSQL | Starter | $7 |
| Background Worker | Starter | $7 |

## Troubleshooting

### Service Won't Start

1. Check build logs for errors
2. Verify all environment variables are set
3. Ensure start command is correct
4. Check for port binding issues (use `PORT` env var)

### Database Connection Issues

1. Verify `DATABASE_URL` is correct
2. Check if using Internal URL (for Render PostgreSQL)
3. Ensure database is running
4. Check connection limits

### Slow Cold Starts (Free Tier)

Free tier services spin down after inactivity. Options:

1. Upgrade to paid tier
2. Use a health check service to keep alive
3. Accept cold start latency

## Security Best Practices

1. **Use environment variables** for all secrets
2. **Enable SSL** (automatic on Render)
3. **Configure CORS** properly
4. **Use strong JWT secrets**
5. **Rotate credentials** regularly
6. **Enable IP allowlisting** for database (if external)

## Next Steps

- [Configure Cloudflare for frontend hosting](./cloudflare-setup.md)
- [Deploy smart contracts to testnet](./base-sepolia.md)
- [Update application URLs](./url-configuration.md)
