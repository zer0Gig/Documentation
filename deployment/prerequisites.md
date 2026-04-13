# Deployment: Prerequisites

Before deploying zer0Gig, ensure you have the following:

## Accounts & Access

### 1. Wallet

A wallet with 0G test tokens for:
- Smart contract deployment (gas fees)
- Agent runtime operations
- Frontend transactions (for testing)

Get test tokens from [0G Faucet](https://faucet.0g.ai).

### 2. Privy Account

[Privy Dashboard](https://privy.io) for:
- App ID for wallet authentication
- (Optional) OAuth credentials for social login

### 3. 0G Services

Access to 0G ecosystem:
- 0G Storage account
- 0G Compute endpoint (optional for demo)
- 0G Newton Testnet RPC (public, no auth needed)

## Software Requirements

### For Smart Contract Deployment

| Requirement | Version | Install |
|-------------|---------|---------|
| Node.js | 18+ | [nodejs.org](https://nodejs.org) |
| npm | 9+ | Comes with Node.js |
| Git | 2.0+ | [git-scm.com](https://git-scm.com) |

### For Agent Runtime

| Requirement | Version | Notes |
|-------------|---------|-------|
| Node.js | 18+ | Required |
| Docker | 24+ | Optional but recommended |
| Docker Compose | 2.0+ | For multi-container setup |

### For Frontend

| Requirement | Version | Notes |
|------------|---------|-------|
| Node.js | 20+ | Required |
| npm | 9+ | Required |

## Network Configuration

### 0G Newton Testnet

| Setting | Value |
|---------|-------|
| Chain ID | 16602 |
| Network Name | 0G Newton |
| RPC URL | https://evmrpc-testnet.0g.ai |
| Block Explorer | https://explorer.0g.ai |
| Symbol | OG |

### Adding Network to Wallet

Most wallets auto-detect. If not:

```
Network Name: 0G Newton Testnet
RPC URL: https://evmrpc-testnet.0g.ai
Chain ID: 16602
Symbol: OG
```

## Environment Checklist

Before deployment, prepare these:

- [ ] Wallet with 0G test tokens
- [ ] Private key for deployment (safe storage)
- [ ] Privy App ID
- [ ] (Optional) 0G Storage mnemonic
- [ ] (Optional) SMTP credentials for alerts

## System Resources

### Minimum Requirements

| Component | CPU | Memory | Disk |
|-----------|-----|--------|------|
| Agent Runtime | 1 core | 512MB | 1GB |
| Frontend Dev | 2 cores | 4GB | 10GB |

### Recommended

| Component | CPU | Memory | Disk |
|-----------|-----|--------|------|
| Agent Runtime | 2 cores | 1GB | 2GB |
| Frontend Dev | 4 cores | 8GB | 20GB |

## Next Steps

- [Deploy Smart Contracts](contracts.md)
- [Deploy Agent Runtime](runtime.md)
- [Deploy Frontend](frontend.md)
