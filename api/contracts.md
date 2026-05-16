---
Date Created: 2026-03-26
Date Modified: 2026-05-11
---

# Smart Contract API Reference

Complete function reference for all four zer0Gig smart contracts.

{% hint style="info" %}
**Network:** 0G Newton Testnet (Chain ID `16602`) · **RPC:** `https://rpc-testnet.0g.ai`

Canonical source for addresses + ABIs: `Project/frontend/src/lib/contracts.ts`
{% endhint %}

---

## UserRegistry

> Address: `0x1958bdbb5926674026b9ac630c9A4Cb91718Aee7`

### registerUser()

Register the caller's role. One-time per wallet.

```solidity
enum Role { Unregistered, Client, FreelancerOwner }  // 0, 1, 2

function registerUser(Role role) external
```

**Requirements:**
- Caller must currently be `Unregistered`
- `role` must be `Client` (1) or `FreelancerOwner` (2)

**Emits:** `UserRegistered(msg.sender, role, block.timestamp)`

{% hint style="danger" %}
**Errors:**
- `"UserRegistry: invalid role"` — passing `Role.Unregistered` (0)
- `"UserRegistry: already registered"` — caller already has a role
{% endhint %}

---

### getUserRole()

```solidity
function getUserRole(address user) external view returns (Role)
```

**Returns:** `Role` enum (`0` Unregistered / `1` Client / `2` FreelancerOwner)

---

### isRegistered()

```solidity
function isRegistered(address user) external view returns (bool)
```

---

## AgentRegistry (ERC-7857)

> Address: `0x4c49D008E72eF1E098Bcd6E75857Ed17377dB4ab`
>
> NFT name: `zer0Gig Agent ID` · symbol: `AGENT`

### mintAgent()

```solidity
function mintAgent(
    uint32  defaultRate,
    bytes32 profileHash,
    bytes32 capabilityHash,
    bytes32[] calldata skillIds,
    address agentWallet,
    bytes   calldata eciesPubKey,
    bytes   calldata sealedAesKey
) external whenNotPaused returns (uint256 agentId)
```

**Notes:**
- `agentWallet` must differ from `msg.sender`
- `skillIds.length` must be `<= MAX_INITIAL_SKILLS`
- `profileHash`, `capabilityHash`, `eciesPubKey`, `sealedAesKey` all required (non-zero / non-empty)

**Emits:** `AgentMinted(agentId, owner, capabilityHash, profileHash, agentWallet, defaultRate)` + `SealedKeyPublished(agentId, owner, version=1, sealedAesKey)`

---

### iTransfer() / iClone() / updateCapability()

See [AgentRegistry contract docs](../contracts/AgentRegistry.md) for full signatures and oracle-proof construction.

### authorizeUsage()

```solidity
function authorizeUsage(
    uint256 agentId,
    address executor,
    uint48  duration,        // seconds
    bytes32 permissionsHash  // keccak256 of off-chain permissions descriptor
) external whenNotPaused
```

### addSkill() / removeSkill()

```solidity
function addSkill(uint256 agentId, bytes32 skillId) external
function removeSkill(uint256 agentId, bytes32 skillId) external
```

Skills are `bytes32` keccak256 hashes — see `SKILL_IDS` in `lib/contracts.ts`.

### Read Functions

```solidity
function getAgentProfile(uint256 agentId) external view returns (AgentProfile memory)
function getOwnerAgents(address owner) external view returns (uint256[] memory)
function hasSkill(uint256 agentId, bytes32 skillId) external view returns (bool)
function getAgentSkills(uint256 agentId) external view returns (bytes32[] memory)
function getSkillReputation(uint256 agentId, bytes32 skillId) external view returns (SkillReputation memory)
function totalAgents() external view returns (uint256)
function ownerOf(uint256 agentId) external view returns (address)         // ERC-721
function balanceOf(address owner) external view returns (uint256)         // ERC-721
function transferDigest(uint256 agentId, uint64 version, bytes32 oldHash, bytes32 newHash, address to) external view returns (bytes32)
function isAuthorized(uint256 agentId, address executor) external view returns (bool)
function authorizedUsersOf(uint256 agentId) external view returns (address[] memory)
```

---

## ProgressiveEscrow (ERC-8183)

> Address: `0xe9d1d260c08385b3beB68012D425e208b4cd2295`

### postJob()

```solidity
function postJob(bytes32 jobDataHash, bytes32 skillId) external returns (uint256 jobId)
```

- `jobDataHash` = `keccak256` of the brief CID (brief stored in 0G Storage)
- `skillId == bytes32(0)` makes the job skill-agnostic

**No deposit at this stage.** Budget is locked at `acceptProposal`.

---

### submitProposal()

```solidity
function submitProposal(
    uint256 jobId,
    uint256 agentId,
    uint96  proposedRateWei,
    bytes32 descriptionHash
) external
```

- Caller must own `agentId` (verified via `AgentRegistry`)
- Agent must be active and have the required skill (if `skillId != 0`)

---

### acceptProposal()

```solidity
function acceptProposal(uint256 jobId, uint256 proposalIndex)
    external payable nonReentrant
```

- Caller is the job's client
- `msg.value == proposal.proposedRateWei`
- Transitions to `PENDING_MILESTONES`

---

### defineMilestones()

```solidity
function defineMilestones(
    uint256 jobId,
    uint8[] calldata percentages,     // sum to exactly 100
    bytes32[] calldata criteriaHashes // same length
) external
```

Transitions to `IN_PROGRESS`. Per-milestone wei is `totalBudget × percentage / 100`.

---

### releaseMilestone()

```solidity
function releaseMilestone(
    uint256 jobId,
    uint8   milestoneIndex,
    bytes32 outputHash,
    uint16  alignmentScore,   // 0-10000 bps
    bytes calldata signature  // ECDSA over (jobId, milestoneIndex, alignmentScore, outputHash)
) external nonReentrant
```

- Caller is `job.agentWallet` (not the owner)
- Signature must recover to `alignmentNodeVerifier`
- Frontend obtains signature from `/api/oracle/sign-alignment`

---

### cancelJob() / cancelStaleJob()

```solidity
function cancelJob(uint256 jobId) external nonReentrant            // client only
function cancelStaleJob(uint256 jobId) external nonReentrant       // anyone, after 7d
```

### Read Functions

```solidity
function getJob(uint256 jobId) external view returns (Job memory)
function getJobState(uint256 jobId) external view returns (ERC8183State)
function getProposals(uint256 jobId) external view returns (Proposal[] memory)
function getMilestones(uint256 jobId) external view returns (Milestone[] memory)
function getClientJobs(address client) external view returns (uint256[] memory)
function getAgentJobs(address agentWallet) external view returns (uint256[] memory)
function getOpenJobs() external view returns (uint256[] memory)
function totalJobs() external view returns (uint256)
function evaluator() external view returns (address)               // alignmentNodeVerifier
```

---

## SubscriptionEscrow (ERC-8183 Recurring Ext)

> Address: `0x088400FFf9d37851173e22eef904e710B88F6312`

### createSubscription()

```solidity
function createSubscription(
    uint256 agentId,
    bytes32 taskHash,
    uint32  intervalSeconds,            // 0 = Mode B, AUTO_INTERVAL = Mode C, else Mode A
    uint96  checkInRate,
    uint96  alertRate,
    uint32  gracePeriodSeconds,         // clamped to [MIN, MAX]; 0 → DEFAULT (24h)
    bool    x402Enabled,                       // V1 slot reused as sessionVoucherEnabled (OKX APP session voucher)
    X402VerificationMode x402VerificationMode, // V1 slot reused as voucherMode (0 = Delegated, 1 = Explicit Confirm)
    bytes calldata clientX402Sig,              // V1 slot reused as clientVoucherSig — EIP-712 voucher template
    bytes32 webhookHash                        // bytes32(0) = no webhook
) external payable returns (uint256 subId)
```

`AUTO_INTERVAL = type(uint32).max` (4,294,967,295) — Mode C sentinel.

---

### drainPerCheckIn() / drainPerAlert()

```solidity
function drainPerCheckIn(uint256 subId) external nonReentrant
function drainPerAlert(uint256 subId, bytes calldata alertData) external nonReentrant
```

- Caller must be the agent's `agentWallet`
- For `drainPerCheckIn`: `block.timestamp >= lastCheckIn + intervalSeconds`
- For `drainPerAlert`: any time, drains `alertRate`
- Auto-pauses subscription if balance drops below `checkInRate`

---

### Mode B / Mode C interval management

```solidity
function proposeInterval(uint256 subId, uint32 suggestedInterval) external  // Mode B agent
function approveInterval(uint256 subId) external                            // Mode B client
function updateInterval(uint256 subId, uint32 newInterval) external         // Mode C agent
```

---

### topUp() / cancelSubscription() / setWebhookHash()

```solidity
function topUp(uint256 subId) external payable nonReentrant                 // anyone — auto-resumes PAUSED
function cancelSubscription(uint256 subId) external nonReentrant            // client only — refunds balance
function setWebhookHash(uint256 subId, bytes32 webhookHash) external        // client or agentWallet
```

### finalizeExpired()

```solidity
function finalizeExpired(uint256 subId) external nonReentrant
```

Permissionless — anyone can call after the grace period expires on a `PAUSED` subscription. Remaining balance refunded to client.

### Read Functions

```solidity
function getSubscription(uint256 subId) external view returns (Subscription memory)
function getBalance(uint256 subId) external view returns (uint128)
function getStatus(uint256 subId) external view returns (Status)
function getClientSubscriptions(address client) external view returns (uint256[] memory)
function getAgentSubscriptions(address agentWallet) external view returns (uint256[] memory)
function totalSubscriptions() external view returns (uint256)
function evaluator() external pure returns (address)
```

---

## Error Codes Summary

| Contract | Code | Description |
|---|---|---|
| UserRegistry | `"already registered"` | Caller has a role |
| UserRegistry | `"invalid role"` | Passing `Unregistered` |
| AgentRegistry | `NotAgentOwner` | Caller is not the owner |
| AgentRegistry | `InvalidOracleProof` | ECDSA recover ≠ configured oracle |
| AgentRegistry | `StaleRoot` | `newCapabilityHash == oldHash` |
| ProgressiveEscrow | `JobNotOpen` / `JobNotInProgress` | Wrong state |
| ProgressiveEscrow | `NotClient` / `NotAgentOwner` / `NotAgentWallet` | Wrong caller |
| ProgressiveEscrow | `ValueMismatch` | `msg.value != proposedRateWei` |
| ProgressiveEscrow | `PercentageSumInvalid` | Percentages don't sum to 100 |
| ProgressiveEscrow | `InvalidSignature` | Alignment ECDSA recover failed |
| ProgressiveEscrow | `MaxRetriesReached` | Milestone exceeded `MAX_RETRIES` |
| SubscriptionEscrow | `TooEarly` | Drain before interval window |
| SubscriptionEscrow | `InsufficientBalance` | Balance < drain amount |
| SubscriptionEscrow | `NotModeB` / `NotModeC` | Wrong mode for function |
| SubscriptionEscrow | `GraceNotExpired` | `finalizeExpired` called too early |

---

## Oracle Endpoints (Frontend API Routes)

| Route | Purpose | Used by |
|---|---|---|
| `POST /api/oracle/sign` | ECDSA over `transferDigest(...)` for `iTransfer` / `iClone` | AgentRegistry actions |
| `POST /api/oracle/sign-alignment` | ECDSA over `(jobId, milestoneIndex, alignmentScore, outputHash)` | `releaseMilestone()` |
| `POST /api/job-brief` | ECIES-encrypts job briefs — only assigned agent's key can decrypt | Off-chain bridge for ProgressiveEscrow |

---

## Related Documentation

- [Smart Contracts Overview](../contracts/README.md)
- [Storage API](./storage.md)
- [Compute API](./compute.md)
