---
Date Created: 2026-03-26
Date Modified: 2026-05-11
---

# Contract Deployment

Guide for deploying zer0Gig smart contracts to 0G Newton Testnet.

{% hint style="info" %}
**Prerequisites:** Private key with testnet OG, Node.js 18+, npm
{% endhint %}

## Network Configuration

| Network | Chain ID | RPC URL | Explorer |
|---|---|---|---|
| 0G Newton Testnet | `16602` | `https://rpc-testnet.0g.ai` | `https://scan-testnet.0g.ai` |

## Current Deployment

> **Deployed:** 2026-04-28 (ERC-7857 + ERC-8183 migration) · Deployer: `0x48379F4d1427209311E9FF0bcC4a354953ea631B`

| Contract | Address | Standard |
|---|---|---|
| **AgentRegistry** | `0x4c49D008E72eF1E098Bcd6E75857Ed17377dB4ab` | ERC-7857 |
| **ProgressiveEscrow** | `0xe9d1d260c08385b3beB68012D425e208b4cd2295` | ERC-8183 |
| **SubscriptionEscrow** | `0x088400FFf9d37851173e22eef904e710B88F6312` | ERC-8183 Recurring Ext |
| **UserRegistry** | `0x1958bdbb5926674026b9ac630c9A4Cb91718Aee7` | Custom |

> **Canonical source:** `Project/frontend/src/lib/contracts.ts`. Update this file whenever you redeploy.

## Deployment Process

### 1. Setup Environment

```bash
cd Project/contracts
npm install
cp .env.example .env
```

Edit `.env`:
```env
PRIVATE_KEY=your_deployer_private_key
RPC_URL=https://rpc-testnet.0g.ai
ORACLE_ADDRESS=0x...                # Alignment Node signer for ERC-7857 oracle proofs
ALIGNMENT_SIGNER_ADDRESS=0x...      # Alignment Node signer for ERC-8183 milestone releases
```

### 2. Compile Contracts

```bash
npx hardhat compile
```

### 3. Deploy All Contracts

```bash
npx hardhat run scripts/deploy.js --network newton
```

Deployment order (encoded in the script):

```mermaid
graph LR
    D1[UserRegistry] --> D2[AgentRegistry]
    D2 --> D3[ProgressiveEscrow]
    D2 --> D4[SubscriptionEscrow]
    D3 --> D5[Authorize escrow contracts on AgentRegistry]
    D4 --> D5
    D5 --> D6[Save addresses to deployments/newton.json]
```

### 4. Authorize Escrow Contracts

After deployment, the AgentRegistry needs to know which escrow contracts can call `recordJobResult`:

```bash
npx hardhat run scripts/authorize-escrows.js --network newton
```

This calls `AgentRegistry.addEscrowContract(...)` for both `ProgressiveEscrow` and `SubscriptionEscrow`.

### 5. Verify Addresses

Addresses are written to `Project/contracts/deployments/newton.json`:

```json
{
  "AgentRegistry": "0x4c49D008E72eF1E098Bcd6E75857Ed17377dB4ab",
  "ProgressiveEscrow": "0xe9d1d260c08385b3beB68012D425e208b4cd2295",
  "SubscriptionEscrow": "0x088400FFf9d37851173e22eef904e710B88F6312",
  "UserRegistry": "0x1958bdbb5926674026b9ac630c9A4Cb91718Aee7",
  "deployer": "0x48379F4d1427209311E9FF0bcC4a354953ea631B",
  "deployedAt": 1745798400
}
```

## Update Downstream Configs

After deployment, propagate the new addresses to every consumer:

### Frontend (`Project/frontend/src/lib/contracts.ts`)

```typescript
export const CONTRACT_ADDRESSES = {
  AgentRegistry: "0x4c49D008E72eF1E098Bcd6E75857Ed17377dB4ab",
  ProgressiveEscrow: "0xe9d1d260c08385b3beB68012D425e208b4cd2295",
  SubscriptionEscrow: "0x088400FFf9d37851173e22eef904e710B88F6312",
  UserRegistry: "0x1958bdbb5926674026b9ac630c9A4Cb91718Aee7",
} as const;
```

Also re-export the ABIs by re-running the build that copies Hardhat artifacts into `Project/frontend/src/lib/abis/`.

### Agent Runtime (`Project/agent-runtime/.env`)

```env
USER_REGISTRY_ADDRESS=0x1958bdbb5926674026b9ac630c9A4Cb91718Aee7
AGENT_REGISTRY_ADDRESS=0x4c49D008E72eF1E098Bcd6E75857Ed17377dB4ab
PROGRESSIVE_ESCROW_ADDRESS=0xe9d1d260c08385b3beB68012D425e208b4cd2295
SUBSCRIPTION_ESCROW_ADDRESS=0x088400FFf9d37851173e22eef904e710B88F6312
```

### Oracle Signing Service

The `/api/oracle/sign` and `/api/oracle/sign-alignment` endpoints in the frontend need the deployer-time oracle private keys (kept server-side, not committed). Their public addresses must match:

- `AgentRegistry.oracle()` — signs `transferDigest` for `iTransfer` / `iClone`
- `ProgressiveEscrow.alignmentNodeVerifier()` — signs milestone alignment scores

## Running Tests

```bash
# All tests
npx hardhat test

# Specific contract
npx hardhat test --grep "SubscriptionEscrow"
npx hardhat test --grep "UserRegistry"
```

{% hint style="warning" %}
**Known limitation (as of 2026-05-11):** The AgentRegistry and ProgressiveEscrow test suites are being updated to match the new ERC-7857 / ERC-8183 interfaces. `mintAgent` now takes 7 args (including `sealedAesKey`), `releaseMilestone` replaces `submitMilestone`, etc. SubscriptionEscrow and UserRegistry tests pass cleanly.
{% endhint %}

## Source Code Verification

To verify on the explorer:

```bash
npx hardhat verify --network newton <CONTRACT_ADDRESS> [CONSTRUCTOR_ARGS...]
```

{% hint style="info" %}
Constructor argument formatting depends on `scan-testnet.0g.ai` support. Check the explorer page for the contract first.
{% endhint %}

## Troubleshooting

### "Insufficient funds"

Deployer wallet needs ~0.5 OG. Get from [faucet.0g.ai](https://faucet.0g.ai).

### "Nonce too low"

Wait for previous tx to confirm, or:
```bash
npx hardhat clean
npx hardhat compile
```

### "Network not supported"

Check your `hardhat.config.ts` `networks.newton` entry uses `chainId: 16602` and `url: 'https://rpc-testnet.0g.ai'`.

---

## Related Documentation

- [Smart Contracts Overview](./README.md)
- [Frontend Setup](../frontend/setup.md)
- [Agent Runtime Setup](../agent-runtime/setup.md)
- [Deployment Guide](../deployment/contracts.md)
