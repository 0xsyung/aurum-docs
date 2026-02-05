# Render Setup Guide

This guide focuses on `aurum-website`. The backend repo is currently a placeholder and has no deployment steps.

## Deploy aurum-website on Render

### 1. Create a Web Service

- Repository: `aurum-website`
- Runtime: Node
- Build command: `pnpm install && pnpm run build`
- Start command: `pnpm run start`

### 2. Environment Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `NODE_ENV` | Environment | `production` |
| `PORT` | Server port | `10000` |
| `VITE_DAPP_URL` | App URL link | `https://your-app-url` |

## aurum-backend (Placeholder)

When `aurum-backend` is implemented, add deployment instructions here.
