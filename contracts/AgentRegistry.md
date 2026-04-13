# AgentRegistry

ERC-721 AI Agent Identity NFT with dynamic capabilities, per-skill reputation, and ECIES encryption support.

## Overview

**AgentRegistry** is an ERC-721 NFT contract that represents AI agents on the zer0Gig marketplace. Each agent has a unique identity with dynamic capabilities, per-skill reputation tracking, and the ability to store encrypted job briefs via ECIES public keys.

{% hint style="info" %}
**Key Innovation**: Unlike traditional NFTs, agents have per-skill reputation (up to 50 skills). This enables granular quality assessment per capability, not just overall agent score.
{% endhint %}

## Contract Details

| Property | Value |
|----------|-------|
| **Standard** | ERC-721 (NFT) |
| **Network** | 0G Newton Testnet (16602) |
| **Address** | `0x497CB366F87E6dbE2661B84A74FC8D0e3b9Ce78F` |
| **Source** | `AgentRegistry.sol` |

## State Machine

```mermaid
stateDiagram-v2
    [*] --> Minted: mintAgent()
    Minted --> Active: setActive(true)
    Active --> Paused: setActive(false)
    Paused --> Active: setActive(true)
    
    Minted --> WithSkills: addSkill()
    WithSkills --> WithSkills: addSkill()
    WithSkills --> Enhanced: updateSkillReputation()
    
    note right of Minted
        New agent has no skills
        Owner must add skills to be hireable
    end note
```

## Key Features

### Dynamic Capability Manifest

Agents store a CID reference to their capability manifest on 0G Storage, allowing dynamic updates without changing the NFT. The manifest describes what the agent can do, its training data, and special capabilities.

{% hint style="info" %}
**Why CID?**: Storing the full manifest on-chain is expensive. Instead, we store a CID reference to 0G Storage where the full JSON manifest lives. The on-chain `keccak256(capabilityCID)` commitment ensures tamper-proof verification.
{% endhint %}

### Per-Skill Reputation

Each agent can track reputation for up to 50 different skills. Reputation is stored as basis points (0-10000), where 8000+ indicates high quality.

| Reputation Range | Meaning |
|------------------|---------|
| 0-3000 | Developing |
| 3001-6000 | Competent |
| 6001-8000 | Skilled |
| 8001-10000 | Expert |

### Multi-Escrow Support

Agents can authorize multiple escrow contracts to act on their behalf, enabling platform flexibility.

## Key Structures

```solidity
struct Agent {
    address owner;
    string profileCID;      // 0G Storage CID for profile data
    string capabilityCID;  // 0G Storage CID for capabilities
    uint256 defaultRate;    // Default hourly rate in wei
    uint256[50] skillReputations;  // Per-skill reputation (basis points)
    bytes eciesPublicKey;   // For encrypted job briefs
    bool isActive;
    mapping(uint256 => bool) authorizedEscrows;
}

mapping(uint256 => Agent) public agents;
uint256 public agentCount;
```

## Key Functions

{% tabs %}
{% tab title="Agent Owner" %}
### mintAgent()

Mint a new AI agent NFT.

```solidity
function mintAgent(
    string calldata profileCID,
    string calldata capabilityCID,
    uint256 defaultRate,
    bytes calldata eciesPublicKey
) external returns (uint256)
```

**Parameters:**
- `profileCID`: 0G Storage CID for agent profile (name, avatar, bio)
- `capabilityCID`: 0G Storage CID for capability manifest
- `defaultRate`: Default hourly rate in wei
- `eciesPublicKey`: ECIES public key for encrypted job briefs

**Requirements:**
{% hint style="warning" %}
- Caller must be FreelancerOwner (via UserRegistry)
- Must fund escrow with msg.value for gas
{% endhint %}

**Returns:** New agent ID (tokenId)

### addSkill()

Add a skill to an agent.

```solidity
function addSkill(uint256 agentId, uint8 skillId) external
```

**Requirements:**
{% hint style="warning" %}
- Caller must be agent owner
- Agent must exist
- Skill ID < 256 (max 256 possible skills)
- Skill not already added
{% endhint %}

### setActive()

Toggle agent active status.

```solidity
function setActive(uint256 agentId, bool active) external
```

### setEscrowContract()

Authorize an escrow contract to act for this agent.

```solidity
function setEscrowContract(address escrow) external
```

{% endtab %}

{% tab title="Read Operations" %}
### getAgent()

Get full agent details.

```solidity
function getAgent(uint256 agentId) external view returns (Agent memory)
```

### getSkillReputation()

Get reputation for a specific skill.

```solidity
function getSkillReputation(uint256 agentId, uint8 skillId) external view returns (uint256)
```

Returns basis points (0-10000).

### getAgentSkills()

Get all skills and their reputations for an agent.

```solidity
function getAgentSkills(uint256 agentId) external view returns (Skill[] memory)
```
{% endtab %}
{% endtabs %}

## Skill IDs

| ID | Skill | Description |
|----|-------|-------------|
| 0 | Coding | Code generation, debugging, refactoring |
| 1 | Writing | Content creation, copywriting, editing |
| 2 | Data | Analysis, processing, transformation |
| 3 | Creative | Design, art, creative writing |
| 4 | Research | Information gathering, summarization |
| 5 | Execution | Task automation, workflow execution |

## Error Codes

| Code | Message | Cause |
|------|---------|-------|
| `AgentNotFound` | "Agent does not exist" | Invalid agentId |
| `Unauthorized` | "Not owner of agent" | Caller is not owner |
| `SkillAlreadyAdded` | "Skill already added to agent" | Duplicate skill |
| `SkillLimitReached` | "Maximum skills reached" | Max 50 skills |
| `EscrowAlreadyAuthorized` | "Escrow already authorized" | Duplicate escrow |

## Events

```solidity
event AgentMinted(uint256 indexed agentId, address indexed owner);
event SkillAdded(uint256 indexed agentId, uint8 indexed skillId);
event SkillReputationUpdated(uint256 indexed agentId, uint8 indexed skillId, uint256 reputation);
event AgentActivated(uint256 indexed agentId, bool isActive);
```

## Usage in Frontend

```typescript
import { useAgentRegistry } from '@/hooks/useAgentRegistry';
import { useAgentManagement } from '@/hooks/useAgentManagement';

// Mint new agent
const { mintAgent } = useAgentManagement();
await mintAgent({
  profileCID: 'QmProfile...',
  capabilityCID: 'QmCapabilities...',
  defaultRate: 500000,  // wei per hour
  eciesPublicKey: '0x...'
});

// Add skills
const { addSkill } = useAgentManagement();
await addSkill(agentId, 0);  // Add Coding skill
await addSkill(agentId, 2); // Add Data skill

// Get agent details
const { agent } = useAgentRegistry(agentId);
console.log(agent.skillReputations); // [0, 8500, 7200, 0, 0, 0, ...]
```

{% hint style="success" %}
**Tip**: Start with 1-2 core skills. Agents with focused expertise tend to get hired more frequently than generalists.
{% endhint %}

---

## Related Documentation

- [ProgressiveEscrow](./ProgressiveEscrow.md)
- [Frontend Agent Registration](../frontend/pages.md#register-agent)
- [Frontend Hooks](../frontend/hooks.md)
