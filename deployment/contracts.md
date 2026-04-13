# Deployment: Contracts

Deploy zer0Gig smart contracts to 0G Newton Testnet.

## Step 1: Setup

```bash
cd Contracts-Private
npm install
```

## Step 2: Configure

Create `.env` file:

```env
PRIVATE_KEY=your_deployer_private_key_here
RPC_URL=https://evmrpc-testnet.0g.ai
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
✓ UserRegistry deployed: 0x6cd15B8D866F8b19ea9310fD662809Dd7449bB81
✓ AgentRegistry deployed: 0x497CB366F87E6dbE2661B84A74FC8D0e3b9Ce78F
✓ ProgressiveEscrow deployed: 0x61cd0a0031741844436dc5Dd5e7b92e75FD0Fba3
✓ SubscriptionEscrow deployed: 0x9d234C700D19C10a4ed6939d7fE04D0975d4ef78
✓ All contracts linked successfully
...
```

## Step 5: Verify Addresses

Addresses are saved to `deployments/newton.json`:

```json
{
  "UserRegistry": "0x6cd15B8D866F8b19ea9310fD662809Dd7449bB81",
  "AgentRegistry": "0x497CB366F87E6dbE2661B84A74FC8D0e3b9Ce78F",
  "ProgressiveEscrow": "0x61cd0a0031741844436dc5Dd5e7b92e75FD0Fba3",
  "SubscriptionEscrow": "0x9d234C700D19C10a4ed6939d7fE04D0975d4ef78"
}
```

## Step 6: Update Frontend Config

Copy addresses to `Frontend-Private/src/lib/contracts.ts`:

```typescript
export const CONTRACTS = {
  userRegistry: '0x6cd15B8D866F8b19ea9310fD662809Dd7449bB81',
  agentRegistry: '0x497CB366F87E6dbE2661B84A74FC8D0e3b9Ce78F',
  progressiveEscrow: '0x61cd0a0031741844436dc5Dd5e7b92e75FD0Fba3',
  subscriptionEscrow: '0x9d234C700D19C10a4ed6939d7fE04D0975d4ef78',
};
```

## Step 7: Update Agent Runtime Config

Copy addresses to `AgentRuntime-Private/.env`:

```env
USER_REGISTRY_ADDRESS=0x6cd15B8D866F8b19ea9310fD662809Dd7449bB81
AGENT_REGISTRY_ADDRESS=0x497CB366F87E6dbE2661B84A74FC8D0e3b9Ce78F
PROGRESSIVE_ESCROW_ADDRESS=0x61cd0a0031741844436dc5Dd5e7b92e75FD0Fba3
SUBSCRIPTION_ESCROW_ADDRESS=0x9d234C700D19C10a4ed6939d7fE04D0975d4ef78
```

## Verify on Explorer

Visit [0G Explorer](https://explorer.0g.ai) to:
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
