# Backend Deployment Guide

Complete guide for deploying the Aurum Labs backend API and services to production.

## Overview

The Aurum Labs backend is a Node.js/Express application that provides:

- REST API for market data and user interactions
- WebSocket connections for real-time updates
- Blockchain event indexing and caching
- Oracle integration for market resolution

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        Render                               │
│  ┌─────────────────────────────────────────────────────┐   │
│  │                 Aurum Labs Backend                   │   │
│  │  ┌─────────┐  ┌─────────┐  ┌─────────────────────┐  │   │
│  │  │ Express │  │WebSocket│  │  Event Indexer      │  │   │
│  │  │   API   │  │ Server  │  │                     │  │   │
│  │  └─────────┘  └─────────┘  └─────────────────────┘  │   │
│  └─────────────────────────────────────────────────────┘   │
│                          │                                  │
│  ┌─────────────────────────────────────────────────────┐   │
│  │                   PostgreSQL                         │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      Base Network                           │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐ │
│  │ Conditional │  │   Market    │  │      Aurum          │ │
│  │   Tokens    │  │   Factory   │  │      Oracle         │ │
│  └─────────────┘  └─────────────┘  └─────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

## Prerequisites

- Node.js 18+ installed locally
- PostgreSQL 14+ database
- Access to the `aurum-backend` repository
- Render account (see [Render Setup Guide](./render-setup.md))
- Deployed smart contracts (see [Base Sepolia Deployment](./base-sepolia.md))

## Local Development Setup

### 1. Clone the Repository

```bash
git clone https://github.com/0xsyung/aurum-backend.git
cd aurum-backend
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Set Up PostgreSQL

```bash
# Create database
createdb aurum_dev

# Or using Docker
docker run --name aurum-postgres \
  -e POSTGRES_DB=aurum_dev \
  -e POSTGRES_USER=aurum \
  -e POSTGRES_PASSWORD=password \
  -p 5432:5432 \
  -d postgres:15
```

### 4. Configure Environment Variables

Create a `.env` file:

```env
# Server Configuration
NODE_ENV=development
PORT=3001
HOST=0.0.0.0

# Database
DATABASE_URL=postgresql://aurum:password@localhost:5432/aurum_dev

# Authentication
JWT_SECRET=your-development-jwt-secret
JWT_EXPIRES_IN=7d

# CORS
CORS_ORIGIN=http://localhost:5173

# Blockchain Configuration
RPC_URL=https://sepolia.base.org
CHAIN_ID=84532
PRIVATE_KEY=0x... # Backend wallet for transactions

# Contract Addresses
CONDITIONAL_TOKENS_ADDRESS=0x...
MARKET_FACTORY_ADDRESS=0x...
AURUM_TOKEN_ADDRESS=0x...
AURUM_ORACLE_ADDRESS=0x...
FEE_COLLECTOR_ADDRESS=0x...

# Indexer Configuration
INDEXER_START_BLOCK=0
INDEXER_BATCH_SIZE=1000

# Rate Limiting
RATE_LIMIT_WINDOW=60000
RATE_LIMIT_MAX=100
```

### 5. Run Database Migrations

```bash
npm run migrate
```

### 6. Start Development Server

```bash
npm run dev
```

The API will be available at `http://localhost:3001`.

## Database Schema

### Core Tables

```sql
-- Markets table
CREATE TABLE markets (
    id UUID PRIMARY KEY,
    market_id BYTES32 NOT NULL UNIQUE,
    question_id BYTES32 NOT NULL,
    condition_id BYTES32 NOT NULL,
    creator VARCHAR(42) NOT NULL,
    collateral_token VARCHAR(42) NOT NULL,
    amm_address VARCHAR(42),
    question TEXT NOT NULL,
    outcomes JSONB NOT NULL,
    resolution_time TIMESTAMP NOT NULL,
    resolved BOOLEAN DEFAULT FALSE,
    payouts JSONB,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

-- Positions table
CREATE TABLE positions (
    id UUID PRIMARY KEY,
    user_address VARCHAR(42) NOT NULL,
    market_id BYTES32 NOT NULL,
    outcome_index INTEGER NOT NULL,
    amount NUMERIC(78, 0) NOT NULL,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

-- Events table (for indexer)
CREATE TABLE indexed_events (
    id UUID PRIMARY KEY,
    block_number BIGINT NOT NULL,
    transaction_hash VARCHAR(66) NOT NULL,
    log_index INTEGER NOT NULL,
    event_name VARCHAR(100) NOT NULL,
    event_data JSONB NOT NULL,
    processed BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT NOW()
);
```

### Running Migrations

```bash
# Run all pending migrations
npm run migrate

# Rollback last migration
npm run migrate:rollback

# Create new migration
npm run migrate:create -- --name add_new_table
```

## API Endpoints

### Markets

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/markets` | List all markets |
| GET | `/api/markets/:id` | Get market details |
| GET | `/api/markets/:id/positions` | Get positions for market |
| GET | `/api/markets/:id/prices` | Get current prices |

### Users

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/users/:address/positions` | Get user positions |
| GET | `/api/users/:address/history` | Get user trade history |

### Oracle

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/oracle/questions` | List oracle questions |
| GET | `/api/oracle/questions/:id` | Get question details |

### Health

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/health` | Health check |
| GET | `/api/status` | API status |

## Deployment Options

### Option 1: Render (Recommended)

See the [Render Setup Guide](./render-setup.md) for detailed instructions.

**Quick Deploy:**

1. Connect repository to Render
2. Create PostgreSQL database
3. Configure environment variables
4. Deploy

### Option 2: Railway

1. Create new project
2. Add PostgreSQL plugin
3. Deploy from GitHub
4. Configure environment variables

### Option 3: DigitalOcean App Platform

1. Create new app
2. Connect GitHub repository
3. Add managed PostgreSQL
4. Configure environment variables
5. Deploy

### Option 4: Self-Hosted (Docker)

1. Build Docker image:
   ```bash
   docker build -t aurum-backend .
   ```

2. Run with Docker Compose:
   ```yaml
   version: '3.8'
   services:
     api:
       image: aurum-backend
       ports:
         - "3001:3001"
       environment:
         - DATABASE_URL=postgresql://...
         - JWT_SECRET=...
       depends_on:
         - postgres
     
     postgres:
       image: postgres:15
       volumes:
         - postgres_data:/var/lib/postgresql/data
       environment:
         - POSTGRES_DB=aurum
         - POSTGRES_USER=aurum
         - POSTGRES_PASSWORD=secure_password
   
   volumes:
     postgres_data:
   ```

## Environment Configuration

### Development

```env
NODE_ENV=development
LOG_LEVEL=debug
CORS_ORIGIN=http://localhost:5173
```

### Staging

```env
NODE_ENV=staging
LOG_LEVEL=info
CORS_ORIGIN=https://staging.aurumlabs.io
```

### Production

```env
NODE_ENV=production
LOG_LEVEL=warn
CORS_ORIGIN=https://app.aurumlabs.io
```

## Event Indexer

The backend includes an event indexer that listens to blockchain events and stores them in the database.

### Starting the Indexer

```bash
# Run indexer as separate process
npm run indexer

# Or run with API
npm run start:with-indexer
```

### Indexer Configuration

```env
# Starting block for indexing
INDEXER_START_BLOCK=0

# Number of blocks to process per batch
INDEXER_BATCH_SIZE=1000

# Polling interval (ms)
INDEXER_POLL_INTERVAL=5000
```

### Indexed Events

- `MarketCreated` - New market creation
- `MarketResolved` - Market resolution
- `AMMDeployed` - AMM deployment
- `PositionSplit` - Token splits
- `PositionMerge` - Token merges
- `PayoutRedemption` - Payout claims

## WebSocket API

### Connection

```javascript
const ws = new WebSocket('wss://api.aurumlabs.io/ws');
```

### Subscriptions

```javascript
// Subscribe to market updates
ws.send(JSON.stringify({
  type: 'subscribe',
  channel: 'market',
  marketId: '0x...'
}));

// Subscribe to price updates
ws.send(JSON.stringify({
  type: 'subscribe',
  channel: 'prices',
  marketId: '0x...'
}));
```

### Message Types

```javascript
// Price update
{
  type: 'price_update',
  marketId: '0x...',
  prices: [0.65, 0.35],
  timestamp: 1234567890
}

// Trade notification
{
  type: 'trade',
  marketId: '0x...',
  trader: '0x...',
  outcome: 0,
  amount: '1000000000000000000',
  price: 0.65
}
```

## Monitoring and Logging

### Structured Logging

```javascript
// Log format
{
  level: 'info',
  message: 'Market created',
  marketId: '0x...',
  timestamp: '2024-01-15T10:30:00Z',
  requestId: 'abc-123'
}
```

### Health Checks

```bash
# Check API health
curl https://api.aurumlabs.io/health

# Response
{
  "status": "healthy",
  "database": "connected",
  "indexer": "running",
  "uptime": 86400
}
```

### Metrics (Prometheus)

```env
METRICS_ENABLED=true
METRICS_PORT=9090
```

Exposed metrics:
- `http_requests_total` - Total HTTP requests
- `http_request_duration_seconds` - Request duration
- `database_queries_total` - Database queries
- `indexer_blocks_processed` - Blocks processed

## Security Checklist

- [ ] JWT secret is strong and unique
- [ ] Database credentials are secure
- [ ] Private key is stored securely
- [ ] CORS is properly configured
- [ ] Rate limiting is enabled
- [ ] Input validation on all endpoints
- [ ] SQL injection prevention
- [ ] HTTPS enforced

## Troubleshooting

### Database Connection Issues

```bash
# Test connection
psql $DATABASE_URL -c "SELECT 1"

# Check connection pool
npm run db:status
```

### Indexer Falling Behind

```bash
# Check indexer status
curl https://api.aurumlabs.io/api/indexer/status

# Reset indexer
npm run indexer:reset
```

### High Memory Usage

- Increase Node.js memory limit: `NODE_OPTIONS=--max-old-space-size=4096`
- Check for memory leaks with `--inspect`
- Review database connection pool settings

## Scaling Considerations

### Horizontal Scaling

- Use Redis for session storage
- Configure sticky sessions for WebSocket
- Use read replicas for database

### Vertical Scaling

- Increase instance size
- Optimize database queries
- Add database indexes

## Backup and Recovery

### Database Backups

```bash
# Manual backup
pg_dump $DATABASE_URL > backup.sql

# Restore
psql $DATABASE_URL < backup.sql
```

### Automated Backups (Render)

Render PostgreSQL includes automatic daily backups on paid plans.

## Next Steps

- [Set up frontend on Cloudflare](./cloudflare-setup.md)
- [Configure URL updates](./url-configuration.md)
- [Deploy smart contracts](./base-sepolia.md)
