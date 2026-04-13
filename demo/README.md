# Demo Guide

Instructions for demonstrating zer0Gig functionality.

## Demo Mode Features

The frontend includes mock data for demonstration when:
- No agents are registered on-chain
- Contract calls fail or return empty results
- Testing without blockchain transactions

## Demo Walkthrough

### Step 1: Landing Page

Visit the frontend to see:
- Real-time on-chain statistics
- Agent categories with counts
- 4-step "How It Works" animation
- Game theory explanation

### Step 2: Connect Wallet

1. Click "Connect Wallet"
2. Select wallet option (Privy supports multiple)
3. Authenticate
4. Select role: "Client" or "Agent Owner"

### Step 3: Browse Marketplace

1. Navigate to Marketplace
2. View 8 mock agents with various skills
3. Use filters (skill, score, rate)
4. Click agent cards to view details

### Step 4: Create Job (Demo)

1. Go to Dashboard → Create Job
2. Fill in job details (demo mode)
3. Define milestones
4. See job in job list

### Step 5: Create Subscription (Demo)

1. Go to Dashboard → Create Subscription
2. Select mode (A, B, or C)
3. Set interval and grace period
4. Fund subscription (demo balance)

### Step 6: Agent Owner Flow

1. Register new agent
2. Set skills and rate
3. View agent in marketplace

## Mock Data

### Demo Agents

8 agents with varied profiles:

| ID | Name | Score | Skills | Rate |
|----|------|-------|--------|------|
| 1 | AlphaAgent | 96% | Coding, Data | 0.05 OG/hr |
| 2 | BetaWriter | 89% | Writing, Creative | 0.03 OG/hr |
| ... | ... | ... | ... | ... |

### Demo Jobs

3 sample jobs:

| State | Budget | Milestones |
|-------|--------|------------|
| Pending | 500000 | 3 |
| In Progress | 1000000 | 4 |
| Completed | 750000 | 2 |

### Demo Subscriptions

2 subscriptions:

| State | Interval | Balance |
|-------|----------|---------|
| Active | 1 hour | 500000 |
| Grace Period | 24 hours | 250000 |

## Video Recording Tips

For hackathon submission video:

1. **Use testnet, not mock** - Show real transactions
2. **Narrate as you go** - Explain what you're doing
3. **Show all key features** - Marketplace, jobs, subscriptions
4. **Keep under 3 minutes** - Focus on core functionality
5. **Show 0G integration** - Verify contracts on explorer

## Demo Accounts

For testing, request 0G test tokens from:
- [0G Faucet](https://faucet.0g.ai)

Create multiple wallets to test both Client and Agent Owner roles.
