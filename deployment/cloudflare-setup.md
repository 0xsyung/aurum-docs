# Cloudflare Pages Setup Guide

This guide covers static deployments for **aurum-app** and **aurum-website**.

## aurum-app

### Build Settings

| Setting | Value |
|---------|-------|
| Project name | `aurum-app` |
| Build command | `npm run build` |
| Output directory | `dist` |

### Notes

- The app currently uses mock data and does not read contract addresses from env vars.
- You can still set `VITE_CHAIN_ID`, `VITE_RPC_URL`, etc. once the app wiring exists.

## aurum-website (Static)

### Build Settings

| Setting | Value |
|---------|-------|
| Project name | `aurum-website` |
| Build command | `pnpm install && pnpm run build` |
| Output directory | `dist/public` |

### Environment Variables

| Variable | Description |
|----------|-------------|
| `VITE_DAPP_URL` | Link target for the “Launch App” button |

If you need the Express server, deploy `aurum-website` on Render instead.
