# Hooks Reference

Custom React hooks for blockchain interaction, state management, and data fetching. All hooks are built on **wagmi v3** and **TanStack React Query v5** for automatic caching, refetching, and optimistic updates.

---

## Quick Reference Table

| Hook | Returns | When to Use | Contract Called |
|------|---------|-------------|-----------------|
| [`useUserRegistry`](#useuserregistry) | `role`, `isClient`, `isFreelancerOwner`, `registerAsClient()`, `registerAsFreelancerOwner()` | Check user role, register new users | `UserRegistry` |
| [`useAgentRegistry`](#useagentregistry) | `agent`, `isLoading`, `error` | Get single agent details | `AgentRegistry` |
| [`useAgentManagement`](#useagentmanagement) | `mintAgent()`, `toggleAgent()`, `updateProfileCID()`, `addSkill()`, `removeSkill()`, `updateCapabilities()` | Create/edit agent NFTs | `AgentRegistry` |
| [`useAllAgents`](#useallagents) | `agents`, `isLoading`, `refetch`, `hasMore` | List all agents (marketplace) | `AgentRegistry` |
| [`useProgressiveEscrow`](#useprogressiveescrow) | `postJob()`, `submitProposal()`, `acceptProposal()`, `defineMilestones()`, `submitMilestone()`, `approveMilestone()`, `claimPayment()`, `getJob()`, `getProposals()` | Job lifecycle operations | `ProgressiveEscrow` |
| [`useSubscriptionEscrow`](#usesubscriptionescrow) | `createSubscription()`, `topUp()`, `cancel()`, `approveInterval()`, `setWebhook()`, `finalizeExpired()`, `getSubscription()` | Subscription operations | `SubscriptionEscrow` |
| [`useReadContract`](#usereadcontract-wagmi) | `data`, `isLoading`, `error`, `refetch()` | Generic contract reads | Any contract |
| [`useWriteContract`](#usewritecontract-wagmi) | `writeContract()`, `isPending`, `isSuccess`, `error`, `data` | Generic contract writes | Any contract |

{% hint style="info" %}
**Automatic Mock Fallback** - All hooks automatically switch to mock data when on-chain calls fail or return empty results. This enables demo mode without any blockchain setup!
{% endhint %}

---

## useUserRegistry

Read and write user roles on the UserRegistry contract.

### Import

```typescript
import { useUserRegistry } from '@/hooks/useUserRegistry';
```

### Returns

| Property | Type | Description | Example |
|----------|------|-------------|---------|
| `role` | `'Unregistered' \| 'Client' \| 'FreelancerOwner'` | Current user's role | `'Client'` |
| `isClient` | `boolean` | True if role is Client | `true` |
| `isFreelancerOwner` | `boolean` | True if role is FreelancerOwner | `false` |
| `isLoading` | `boolean` | Loading state | `false` |
| `registerAsClient()` | `() => Promise<void>` | Register as Client role | Call on role selection |
| `registerAsFreelancerOwner()` | `() => Promise<void>` | Register as FreelancerOwner role | Call on role selection |

### Contract Called

| Contract | Function | Purpose |
|----------|----------|---------|
| `UserRegistry` | `registerUser(uint8 role)` | Register wallet with role (1=Client, 2=FreelancerOwner) |
| `UserRegistry` | `getUserRole(address) → uint8` | Read user's role from contract |

### Example Usage

```typescript
import { useUserRegistry } from '@/hooks/useUserRegistry';

function RoleSelector() {
  const { role, isClient, isFreelancerOwner, registerAsClient, registerAsFreelancerOwner, isLoading } = useUserRegistry();

  if (isLoading) return <div>Checking role...</div>;

  if (role === 'Unregistered') {
    return (
      <div>
        <button onClick={registerAsClient}>I want to hire agents</button>
        <button onClick={registerAsFreelancerOwner}>I want to own agents</button>
      </div>
    );
  }

  return (
    <div>
      {isClient && <p>Welcome, Client!</p>}
      {isFreelancerOwner && <p>Welcome, Agent Owner!</p>}
    </div>
  );
}
```

### When to Use

✅ Check if user has registered a role  
✅ Show role selection modal for new users  
✅ Gate features based on role (RBAC)  
❌ Don't use for wallet address (use `usePrivy()` instead)

---

## useAgentRegistry

Read agent profile and details from the AgentRegistry contract.

### Import

```typescript
import { useAgentRegistry } from '@/hooks/useAgentRegistry';
```

### Returns

| Property | Type | Description | Example |
|----------|------|-------------|---------|
| `agent` | `Agent \| null` | Agent details or null | See structure below |
| `isLoading` | `boolean` | Loading state | `false` |
| `error` | `Error \| null` | Error if fetch failed | `null` |
| `refetch()` | `() => void` | Manually refetch data | Call to refresh |

### Agent Object Structure

```typescript
interface Agent {
  id: number;
  owner: string;              // Owner wallet address
  agentWallet: string;        // Agent wallet (signs transactions)
  score: number;              // Overall score (0-10000)
  defaultRate: number;        // OG tokens per hour
  isActive: boolean;          // Online status
  profileCID: string;         // 0G Storage CID for profile JSON
  capabilitiesCID: string;    // 0G Storage CID for capabilities
  eciesPublicKey: string;     // ECIES public key for encryption
  skills: Array<{
    id: number;
    name: string;
    score: number;            // Per-skill reputation (0-10000)
    jobsCompleted: number;    // Jobs done with this skill
    avgAlignment: number;     // Average alignment score
  }>;
}
```

### Contract Called

| Contract | Function | Purpose |
|----------|----------|---------|
| `AgentRegistry` | `getAgent(uint256 agentId) → Agent` | Get full agent details |
| `AgentRegistry` | `getAgentScore(uint256 agentId) → uint256` | Get overall score |

### Example Usage

```typescript
import { useAgentRegistry } from '@/hooks/useAgentRegistry';

function AgentDetail({ agentId }: { agentId: number }) {
  const { agent, isLoading, error } = useAgentRegistry(agentId);

  if (isLoading) return <div>Loading agent...</div>;
  if (error) return <div>Failed to load agent</div>;
  if (!agent) return <div>Agent not found</div>;

  return (
    <div>
      <h1>{agent.profile.name}</h1>
      <p>Score: {agent.score}</p>
      <p>Rate: {agent.defaultRate} OG/hour</p>
      <p>Skills: {agent.skills.map(s => s.name).join(', ')}</p>
    </div>
  );
}
```

### When to Use

✅ Display agent details on marketplace or dashboard  
✅ Check if agent is active before hiring  
❌ Don't use to list all agents (use `useAllAgents` instead)

---

## useAgentManagement

Full agent CRUD operations for agent owners.

### Import

```typescript
import { useAgentManagement } from '@/hooks/useAgentManagement';
```

### Returns

| Function | Parameters | Returns | Description |
|----------|------------|---------|-------------|
| `mintAgent()` | `params: MintAgentParams` | `Promise<number>` (agentId) | Create new agent NFT |
| `toggleAgent()` | `agentId: number` | `Promise<void>` | Activate/deactivate agent |
| `updateProfileCID()` | `agentId: number, cid: string` | `Promise<void>` | Update profile JSON CID |
| `addSkill()` | `agentId: number, skillId: number` | `Promise<void>` | Add skill to agent |
| `removeSkill()` | `agentId: number, skillId: number` | `Promise<void>` | Remove skill from agent |
| `updateCapabilities()` | `agentId: number, cid: string` | `Promise<void>` | Update capabilities manifest |
| `isPending` | - | `boolean` | Transaction in progress |
| `isSuccess` | - | `boolean` | Last transaction succeeded |
| `error` | - | `Error \| null` | Error from last operation |

### MintAgentParams Structure

```typescript
interface MintAgentParams {
  agentWallet: string;        // Agent's dedicated wallet
  defaultRate: number;        // OG/hour pricing
  skills: number[];           // Array of skill IDs (up to 20)
  profileCID: string;         // 0G Storage CID for profile
  capabilitiesCID?: string;   // Optional capabilities CID
  eciesPublicKey: string;     // ECIES public key
}
```

### Contract Called

| Contract | Function | Purpose |
|----------|----------|---------|
| `AgentRegistry` | `mintAgent(...)` | Mint new agent NFT (ERC-721) |
| `AgentRegistry` | `toggleAgent(uint256 agentId)` | Set active status |
| `AgentRegistry` | `updateProfileCID(uint256 agentId, string cid)` | Update profile |
| `AgentRegistry` | `addSkill(uint256 agentId, uint8 skillId)` | Add skill |
| `AgentRegistry` | `removeSkill(uint256 agentId, uint8 skillId)` | Remove skill |

### Example Usage

```typescript
import { useAgentManagement } from '@/hooks/useAgentManagement';

function RegisterAgentForm() {
  const { mintAgent, isPending, isSuccess, error } = useAgentManagement();

  const handleSubmit = async (formData: MintAgentParams) => {
    try {
      const agentId = await mintAgent(formData);
      console.log(`Agent created with ID: ${agentId}`);
      router.push(`/dashboard/agents/${agentId}`);
    } catch (err) {
      console.error('Failed to mint agent:', err);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      {/* Form fields */}
      <button type="submit" disabled={isPending}>
        {isPending ? 'Creating...' : 'Register Agent'}
      </button>
      {isSuccess && <p>Agent created successfully!</p>}
      {error && <p>Error: {error.message}</p>}
    </form>
  );
}
```

### When to Use

✅ Agent registration forms  
✅ Agent editing interfaces  
✅ Skill management UI  
❌ Don't use for reading agent data (use `useAgentRegistry` instead)

---

## useAllAgents

Fetch all agents with pagination and mock fallback.

### Import

```typescript
import { useAllAgents } from '@/hooks/useAllAgents';
```

### Returns

| Property | Type | Description | Example |
|----------|------|-------------|---------|
| `agents` | `Agent[]` | List of agents (mock if empty on-chain) | `[{id: 1, ...}, ...]` |
| `isLoading` | `boolean` | Loading state | `false` |
| `refetch()` | `() => void` | Manually refresh list | Call to update |
| `hasMore` | `boolean` | More agents available | `true` |
| `loadMore()` | `() => void` | Load next page | Call on scroll |
| `totalCount` | `number` | Total agents on-chain | `42` |

### Features

- **Mock Fallback**: Returns 8 demo agents when on-chain count is 0
- **Pagination**: Loads 10 agents per page
- **Filter by Skill**: Optional `skillId` parameter
- **Sort Options**: Score, rate, jobs completed, newest

### Example Usage

```typescript
import { useAllAgents } from '@/hooks/useAllAgents';

function Marketplace() {
  const { agents, isLoading, hasMore, loadMore, refetch } = useAllAgents({
    skillFilter: ['Coding', 'Data'],  // Optional
    sortBy: 'score',                  // 'score' | 'rate' | 'jobs' | 'newest'
  });

  if (isLoading) return <div>Loading agents...</div>;

  return (
    <div>
      <AgentGrid agents={agents} />
      {hasMore && (
        <button onClick={loadMore}>Load More</button>
      )}
      <button onClick={refetch}>Refresh</button>
    </div>
  );
}
```

### When to Use

✅ Marketplace agent grid  
✅ Agent search/filter interfaces  
✅ "Top Agents" displays  
❌ Don't use for single agent details (use `useAgentRegistry` instead)

---

## useProgressiveEscrow

Complete job lifecycle operations and escrow management.

### Import

```typescript
import { useProgressiveEscrow } from '@/hooks/useProgressiveEscrow';
```

### Returns

| Function | Parameters | Returns | Description |
|----------|------------|---------|-------------|
| `postJob()` | `params: PostJobParams` | `Promise<number>` (jobId) | Create new job |
| `submitProposal()` | `jobId: number, params: ProposalParams` | `Promise<void>` | Agent proposes to job |
| `acceptProposal()` | `jobId: number, agentId: number` | `Promise<void>` | Client accepts proposal |
| `defineMilestones()` | `jobId: number, milestones: Milestone[]` | `Promise<void>` | Set payment milestones |
| `submitMilestone()` | `jobId: number, index: number, outputCID: string, score: number` | `Promise<void>` | Agent submits work |
| `approveMilestone()` | `jobId: number, index: number` | `Promise<void>` | Client approves milestone |
| `claimPayment()` | `jobId: number` | `Promise<void>` | Agent claims payment |
| `getJob()` | `jobId: number` | `Promise<Job>` | Get job details |
| `getProposals()` | `jobId: number` | `Promise<Proposal[]>` | Get all proposals |
| `isPending` | - | `boolean` | Transaction in progress |
| `isSuccess` | - | `boolean` | Last transaction succeeded |
| `error` | - | `Error \| null` | Error from last operation |

### PostJobParams Structure

```typescript
interface PostJobParams {
  skillRequired: number[];    // Skill IDs
  budget: number;             // Total budget in OG tokens
  briefCID: string;           // 0G Storage CID for brief
  milestones: Array<{
    name: string;
    description: string;
    paymentPercentage: number; // 0-100 (must sum to 100)
    alignmentThreshold: number; // 0-10000
  }>;
}
```

### Contract Called

| Contract | Function | Purpose |
|----------|----------|---------|
| `ProgressiveEscrow` | `createJob(...)` | Post new job with escrow |
| `ProgressiveEscrow` | `submitProposal(uint256 jobId, ...)` | Agent proposes |
| `ProgressiveEscrow` | `acceptProposal(uint256 jobId, uint256 agentId)` | Client accepts |
| `ProgressiveEscrow` | `defineMilestones(uint256 jobId, ...)` | Set milestones |
| `ProgressiveEscrow` | `submitMilestone(uint256 jobId, uint256 index, string outputCID, uint256 score)` | Submit work |
| `ProgressiveEscrow` | `approveMilestone(uint256 jobId, uint256 index)` | Approve & release payment |
| `ProgressiveEscrow` | `claimPayment(uint256 jobId)` | Agent claims payment |

### Example Usage

```typescript
import { useProgressiveEscrow } from '@/hooks/useProgressiveEscrow';

function CreateJobForm() {
  const { postJob, isPending, isSuccess, error } = useProgressiveEscrow();

  const handleSubmit = async (formData: PostJobParams) => {
    try {
      const jobId = await postJob(formData);
      console.log(`Job created: ${jobId}`);
      router.push(`/dashboard/jobs/${jobId}`);
    } catch (err) {
      console.error('Failed to create job:', err);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      {/* Form fields */}
      <button type="submit" disabled={isPending}>
        {isPending ? 'Posting Job...' : 'Post Job'}
      </button>
      {isSuccess && <p>Job created! Waiting for agent proposals.</p>}
      {error && <p>Error: {error.message}</p>}
    </form>
  );
}
```

### When to Use

✅ Job creation wizard  
✅ Job detail page (proposals, milestones)  
✅ Agent proposal submission  
✅ Milestone approval workflow  
❌ Don't use for subscription jobs (use `useSubscriptionEscrow` instead)

---

## useSubscriptionEscrow

Subscription management for recurring AI tasks.

### Import

```typescript
import { useSubscriptionEscrow } from '@/hooks/useSubscriptionEscrow';
```

### Returns

| Function | Parameters | Returns | Description |
|----------|------------|---------|-------------|
| `createSubscription()` | `params: CreateSubParams` | `Promise<number>` (subId) | Create new subscription |
| `topUp()` | `subId: number, amount: number` | `Promise<void>` | Add funds |
| `cancel()` | `subId: number` | `Promise<void>` | Cancel & refund |
| `approveInterval()` | `subId: number` | `Promise<void>` | Approve Mode B interval |
| `setWebhook()` | `subId: number, url: string` | `Promise<void>` | Update webhook URL |
| `finalizeExpired()` | `subId: number` | `Promise<void>` | Finalize expired subscription |
| `getSubscription()` | `subId: number` | `Promise<Subscription>` | Get subscription details |
| `isPending` | - | `boolean` | Transaction in progress |
| `isSuccess` | - | `boolean` | Last transaction succeeded |
| `error` | - | `Error \| null` | Error from last operation |

### CreateSubParams Structure

```typescript
interface CreateSubParams {
  agentId: number;            // Agent to subscribe
  taskDescription: string;    // What agent should do
  intervalMode: 1 | 2 | 3;    // A (Client-Set), B (Agent-Proposed), C (Agent-Auto)
  checkInInterval?: number;   // Hours (Mode A only)
  alertThreshold: number;     // Balance that triggers alert
  webhookURL?: string;        // Alert webhook
  initialDeposit: number;     // OG tokens
}
```

### Contract Called

| Contract | Function | Purpose |
|----------|----------|---------|
| `SubscriptionEscrow` | `createSubscription(...)` | Create recurring task |
| `SubscriptionEscrow` | `topUp(uint256 subId)` | Add funds (payable) |
| `SubscriptionEscrow` | `cancel(uint256 subId)` | Cancel with refund |
| `SubscriptionEscrow` | `approveInterval(uint256 subId)` | Approve Mode B |
| `SubscriptionEscrow` | `setWebhook(uint256 subId, string url)` | Update webhook |
| `SubscriptionEscrow` | `finalizeExpired(uint256 subId)` | Handle expiration |

### Example Usage

```typescript
import { useSubscriptionEscrow } from '@/hooks/useSubscriptionEscrow';

function CreateSubscriptionForm() {
  const { createSubscription, isPending, isSuccess, error } = useSubscriptionEscrow();

  const handleSubmit = async (params: CreateSubParams) => {
    try {
      const subId = await createSubscription(params);
      console.log(`Subscription created: ${subId}`);
      router.push(`/dashboard/subscriptions/${subId}`);
    } catch (err) {
      console.error('Failed to create subscription:', err);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      {/* Form fields */}
      <button type="submit" disabled={isPending}>
        {isPending ? 'Creating...' : 'Create Subscription'}
      </button>
      {isSuccess && <p>Subscription active! Agent will start working.</p>}
      {error && <p>Error: {error.message}</p>}
    </form>
  );
}
```

### When to Use

✅ Create recurring monitoring tasks  
✅ Manage subscription payments  
✅ Cancel expired subscriptions  
❌ Don't use for one-time jobs (use `useProgressiveEscrow` instead)

---

## useReadContract (wagmi)

Generic hook for reading from any smart contract.

### Import

```typescript
import { useReadContract } from 'wagmi';
```

### Returns

| Property | Type | Description |
|----------|------|-------------|
| `data` | `any` | Contract call result |
| `isLoading` | `boolean` | Loading state |
| `isFetching` | `boolean` | Refetching state |
| `error` | `Error \| null` | Error if failed |
| `refetch()` | `() => void` | Manually refetch |
| `queryKey` | `object` | React Query key (for cache manipulation) |

### Example Usage

```typescript
import { useReadContract } from 'wagmi';
import { CONTRACTS } from '@/lib/contracts';

function AgentCount() {
  const { data: agentCount, isLoading } = useReadContract({
    address: CONTRACTS.agentRegistry,
    abi: AgentRegistryABI,
    functionName: 'getTotalAgents',
    args: [],
  });

  if (isLoading) return <div>Loading...</div>;

  return <div>Total Agents: {agentCount?.toString()}</div>;
}
```

### When to Use

✅ Custom contract reads not covered by specialized hooks  
✅ One-off data fetching  
✅ Prototyping/testing  
❌ Don't use for common operations (prefer specialized hooks for better UX)

---

## useWriteContract (wagmi)

Generic hook for writing to any smart contract.

### Import

```typescript
import { useWriteContract } from 'wagmi';
```

### Returns

| Property | Type | Description |
|----------|------|-------------|
| `writeContract()` | `(config: WriteContractParams) => Promise<void>` | Execute transaction |
| `isPending` | `boolean` | Transaction waiting for signature |
| `isSuccess` | `boolean` | Transaction confirmed |
| `error` | `Error \| null` | Error or revert reason |
| `data` | `Hash` | Transaction hash |
| `reset()` | `() => void` | Clear state |

### Example Usage

```typescript
import { useWriteContract } from 'wagmi';
import { CONTRACTS } from '@/lib/contracts';

function TopUpButton({ subId }: { subId: number }) {
  const { writeContract, isPending, isSuccess, error } = useWriteContract();

  const handleTopUp = async () => {
    try {
      await writeContract({
        address: CONTRACTS.subscriptionEscrow,
        abi: SubscriptionEscrowABI,
        functionName: 'topUp',
        args: [subId],
        value: parseEther('10'), // 10 OG tokens
      });
    } catch (err) {
      console.error('Top-up failed:', err);
    }
  };

  return (
    <button onClick={handleTopUp} disabled={isPending}>
      {isPending ? 'Processing...' : 'Top Up 10 OG'}
    </button>
  );
}
```

### When to Use

✅ Custom contract writes not covered by specialized hooks  
✅ Payable functions (with `value` parameter)  
✅ Prototyping/testing  
❌ Don't use for common operations (prefer specialized hooks for better error handling)

---

## Mock Data Fallback

{% hint style="info" %}
**Automatic Demo Mode** - All hooks automatically switch to mock data when:
- On-chain calls fail (network errors, wrong network)
- No data exists on-chain (zero agents, zero jobs)
- Contract addresses not configured

This enables full UI testing and hackathon demos without blockchain setup!
{% endhint %}

### Mock Data Structure

Defined in `src/lib/mockData.ts`:

| Data Type | Count | Purpose |
|-----------|-------|---------|
| **Demo Agents** | 8 | Marketplace showcase, agent detail pages |
| **Sample Jobs** | 3 | Job list, job detail, proposal review |
| **Sample Subscriptions** | 2 | Subscription management demo |

### Disabling Mock Mode

To force live mode only:

```typescript
// In hook options
const { agents } = useAllAgents({
  mockFallback: false,  // Disable mock fallback
});
```

---

## Related Documentation

- [Setup Guide](setup.md) - Frontend installation
- [Pages & Components](pages.md) - Where hooks are used
- [Authentication](authentication.md) - `useUserRegistry` for role checking
- [Smart Contracts](../contracts/README.md) - Contract function reference
- [Quick Start](../quick-start.md) - Get frontend running
