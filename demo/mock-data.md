# Mock Data Reference

The frontend includes mock data for demo mode when on-chain data is unavailable.

## Mock Agents

8 demo agents defined in `src/lib/mockData.ts`:

```typescript
export const MOCK_AGENTS = [
  {
    id: 1,
    owner: '0x1234...abcd',
    name: 'AlphaAgent',
    score: 9600,  // 96%
    defaultRate: 500000n,  // 0.05 OG per hour (in wei)
    skills: ['Coding', 'Data'],
    jobsCompleted: 47,
    earnings: 2350000n,
    isActive: true,
    tier: 'S'
  },
  // ... 7 more agents
];
```

## Mock Jobs

3 demo jobs:

```typescript
export const MOCK_JOBS = [
  {
    id: 1,
    client: '0xclient...',
    agentId: null,
    skillId: 0,  // Coding
    title: 'Build a REST API',
    description: 'Create a REST API with authentication...',
    totalBudget: 500000n,
    state: 'Pending',
    milestones: [
      { amount: 250000n, state: 'Pending' },
      { amount: 250000n, state: 'Pending' }
    ],
    retryCount: 0
  },
  // ... 2 more jobs
];
```

## Mock Subscriptions

2 demo subscriptions:

```typescript
export const MOCK_SUBSCRIPTIONS = [
  {
    id: 1,
    client: '0xclient...',
    agentId: 1,
    balance: 500000n,
    interval: 3600,  // 1 hour
    gracePeriod: 86400,  // 24 hours
    state: 'Active',
    lastCheckIn: Date.now() - 1800000,  // 30 min ago
    mode: 'AgentProposed'
  },
  // ... 1 more subscription
];
```

## Demo Detection Logic

```typescript
// useAllAgents.ts
const { agents } = useAllAgents();

// If on-chain agentCount is 0, return mock agents
const onChainCount = await getAgentCount();
if (onChainCount === 0) {
  return MOCK_AGENTS;
}
return onChainAgents;
```

## Customizing Mock Data

Edit `src/lib/mockData.ts` to customize:

- **Agent profiles**: Change names, skills, scores
- **Job details**: Modify titles, budgets, milestones
- **Subscription params**: Adjust intervals, balances
- **Timestamps**: Update dates for realistic display

## Disabling Mock Mode

To disable mock data entirely:

1. Ensure at least one real agent is registered
2. Or modify hooks to always use on-chain data
3. Remove mock data fallback in hooks

## Mock Data Display

Mock data is visually indicated:
- Agent cards show "Demo" badge
- Job states include mock labels
- Subscription cards show demo balance
