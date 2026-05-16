---
Date Created: 2026-03-26
Date Modified: 2026-05-11
---

# Deployment: Contracts

Deploy zer0Gig smart contracts (ERC-7857 + ERC-8183) to 0G Newton Testnet. For the in-depth contract guide, see [contracts/deployment.md](../contracts/deployment.md).

## Step 1: Setup

```bash
cd Project/contracts
npm install
```

## Step 2: Configure

Create `.env` file:

```env
PRIVATE_KEY=your_deployer_private_key_here
RPC_URL=https://rpc-testnet.0g.ai
```

**Security Note**: Never commit `.env` to version control.

## Step 3: Compile

```bash
npx hardhat compile
```

Output:
```
Compiled 5 Solidity files successfully
```

## Step 4: Deploy

```bash
npx hardhat deploy --network newton
```

Expected output:
```
Deploying contracts to network: newton (16602)
...
✓ UserRegistry deployed: 0x1958bdbb5926674026b9ac630c9A4Cb91718Aee7
✓ AgentRegistry deployed: 0x4c49D008E72eF1E098Bcd6E75857Ed17377dB4ab
✓ ProgressiveEscrow deployed: 0xe9d1d260c08385b3beB68012D425e208b4cd2295
✓ SubscriptionEscrow deployed: 0x088400FFf9d37851173e22eef904e710B88F6312
✓ All contracts linked successfully
...
```

## Step 5: Verify Addresses

Addresses are saved to `deployments/newton.json`:

```json
{
  "UserRegistry": "0x1958bdbb5926674026b9ac630c9A4Cb91718Aee7",
  "AgentRegistry": "0x4c49D008E72eF1E098Bcd6E75857Ed17377dB4ab",
  "ProgressiveEscrow": "0xe9d1d260c08385b3beB68012D425e208b4cd2295",
  "SubscriptionEscrow": "0x088400FFf9d37851173e22eef904e710B88F6312"
}
```

## Step 6: Update Frontend Config

Copy addresses to `Project/frontend/src/lib/contracts.ts`:

```typescript
export const CONTRACTS = {
  userRegistry: '0x1958bdbb5926674026b9ac630c9A4Cb91718Aee7',
  agentRegistry: '0x4c49D008E72eF1E098Bcd6E75857Ed17377dB4ab',
  progressiveEscrow: '0xe9d1d260c08385b3beB68012D425e208b4cd2295',
  subscriptionEscrow: '0x088400FFf9d37851173e22eef904e710B88F6312',
};
```

## Step 7: Update Agent Runtime Config

Copy addresses to `Project/agent-runtime/.env`:

```env
USER_REGISTRY_ADDRESS=0x1958bdbb5926674026b9ac630c9A4Cb91718Aee7
AGENT_REGISTRY_ADDRESS=0x4c49D008E72eF1E098Bcd6E75857Ed17377dB4ab
PROGRESSIVE_ESCROW_ADDRESS=0xe9d1d260c08385b3beB68012D425e208b4cd2295
SUBSCRIPTION_ESCROW_ADDRESS=0x088400FFf9d37851173e22eef904e710B88F6312
```

## Verify on Explorer

Visit [0G Explorer](https://scan-testnet.0g.ai) to:
1. Search your contract addresses
2. View transaction history
3. Verify source code (optional)

## Running Tests

```bash
# All tests
npx hardhat test

# Specific contract
npx hardhat test --grep "SubscriptionEscrow"
```

## Troubleshooting

### "Insufficient funds"

Your wallet needs 0G test tokens. Get some from [faucet.0g.ai](https://faucet.0g.ai).

### "Nonce too low"

Increase nonce or wait for pending transactions:
```bash
npx hardhat clean
npx hardhat compile
```

### "Network not supported"

Check your wallet is connected to 0G Newton Testnet (Chain ID: 16602).
