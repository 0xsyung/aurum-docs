# URL Configuration Guide

This guide reflects **current** URL configuration needs.

## Current URLs

- **Landing page**: aurum-website (static site)
- **App UI**: aurum-app (static site)
- **Backend**: not implemented

## aurum-website

The only required environment variable is:

```env
VITE_DAPP_URL=https://your-app-url
```

This controls the “Launch App” button.

## aurum-app

The app does not currently consume environment variables for RPC or contract addresses. When that wiring exists, document the variables here.
