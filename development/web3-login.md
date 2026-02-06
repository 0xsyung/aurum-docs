# Web3 Login Research (Feb 2026)

## Requirements

- Browser extension wallets (MetaMask, Rabby)
- WalletConnect for long-tail wallets
- Email login
- Passkey login

## Options Considered

### 1. Privy (Recommended)

**Why it fits**:

- **MetaMask + Rabby + WalletConnect**: Privy’s wallet list supports `metamask`, `rabby_wallet`, and `wallet_connect` as configurable entries.citeturn0search2
- **WalletConnect UX**: Including `wallet_connect` in the wallet list enables the WalletConnect registry and its in-UI connection flow.citeturn0search1
- **Passkeys**: Privy supports passkey login via `useLoginWithPasskey`.citeturn0search0
- **Email login**: Privy supports passwordless email login via `useLoginWithEmail`.citeturn0search4

**Conclusion**: Privy covers all requirements in a single SDK and has explicit support for Rabby + WalletConnect + passkeys + email.citeturn0search2turn0search1turn0search0turn0search4

### 2. Dynamic (Alternative)

Dynamic supports external wallet authentication and explicit wallet connector packages, but the docs we reviewed focus on wallet connectivity rather than passkey login.citeturn1search1turn1search4

### 3. Web3Auth / MetaMask Embedded Wallets (Alternative)

Embedded Wallets (Web3Auth) supports passwordless email login, and Web3Auth’s product docs highlight email and passkey onboarding.citeturn1search3turn1search7 However, extension wallet + WalletConnect coverage would still need to be validated in detail for this project.

## Recommendation

**Use Privy** as the primary authentication layer for Aurum:

- External wallets: MetaMask, Rabby, Coinbase Wallet
- WalletConnect for broader coverage
- Email login + passkey login via Privy’s auth methods

This aligns with the current frontend direction (UI-first, no backend dependency) and keeps the login surface consistent across email/passkey and wallet-based flows.
