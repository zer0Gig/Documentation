---
Date Created: 2026-05-13
Date Modified: 2026-05-13
Title: V2 — zer0Gig Indonesian Marketplace Operator Network (Local Distribution Layer for 0G Stack)
Status: DESIGN — formal blueprint, post-demo implementation target Q1-Q2 2027
Audience: zer0Gig contract + agent-runtime contributors planning V2; Telkom Indigo + PT partners as first operator cohort; investors evaluating distribution strategy
Pivoted: 2026-05-13 — original framing as "decentralized agent-runtime infrastructure" was vulnerable because 0G Labs can build runtime themselves using their GPU/TEE network. Pivoted to **marketplace + commerce + distribution layer on top of 0G**. zer0Gig is 0G's Indonesian distribution channel (analogy: Salesforce + AWS, where Salesforce isn't redundant just because AWS exists).
Related: `OKX_session_voucher_design.md` (parallel V2 work on payment rails), `video/pitching/zer0Gig_Pitch_Narrative_Foundation.md` (uses this doc as roadmap)
---

# V2 — zer0Gig Indonesian Marketplace Operator Network

> Convert agent-runtime from a single Railway service into an Indonesian **marketplace operator network** with local distribution and reseller economics. Operators are **NOT runtime infrastructure competitors** — they're **local marketplace distributors / resellers** providing business layer ON TOP of 0G Compute. Indonesian PT partners (via Telkom Indigo / Jadid Purwaka Aji) become first cohort of local distribution operators.

---

## 0. Why this matters (THE HONEST FRAMING — PIVOTED 2026-05-13)

### The pivot reasoning

**Original V2 framing (now deprecated):** "decentralized agent-runtime infrastructure with stake/slash."

**Why deprecated:** 0G Labs themselves can ship decentralized agent runtime as a foundation service on their existing GPU/TEE infrastructure. Their primitives (0G Compute, Sealed Inference, Alignment Nodes) are sufficient to build native agent runtime. **zer0Gig cannot compete with 0G at infrastructure layer.**

**New framing:** zer0Gig V2 = **marketplace + commerce + Indonesian distribution layer**. We don't replicate 0G's compute infrastructure — we **consume it**. PT operators are **local distribution channel** (resellers), not compute providers.

**Analogy:**
- AWS = infrastructure layer
- Salesforce = SaaS business application (runs on AWS, builds CRM)
- Salesforce Indonesia resellers = local distributors

Translated:
- 0G = infrastructure layer
- zer0Gig = marketplace + commerce protocol (runs on 0G, builds agent commerce)
- Indonesian PT partners (V2) = local marketplace operators / resellers

**Salesforce doesn't compete with AWS.** Same with zer0Gig and 0G.

### V1 (today) — agent-runtime is single web2 service

- Single Node.js service deployed to Railway
- 17 services: EventListener, EventWatcher, JobProcessor, SelfEvaluator, MemoryService, TelegramConnector, ExtendedComputeService, scheduler, platformDispatcher, etc.
- Alignment oracle endpoint: single ECDSA key on a single hosted endpoint
- **Compute inference happens on 0G Compute** (with 6 external provider fallbacks)

**Failure modes V1 carries:**
1. If zer0Gig team disappears → no event listener → agents stuck
2. If Railway / oracle host blocked by jurisdiction → agents stuck
3. If alignment oracle private key compromised → bogus high scores can drain escrow

### V1.5 (Q3 2026) — Multi-sig alignment oracle

Replace single-key oracle with 3-of-5 threshold ECDSA. Closes critical V1 security gap with minimal investment (~6 weeks, $25-45k). NOT yet operator network — just oracle decentralization.

### V2 (Q1-Q2 2027) — Indonesian marketplace operator network

**The pivoted vision:**

- **OperatorRegistry contract** — operators stake OG as **reseller commitment bond + quality SLA**, NOT compute infrastructure stake
- **Operator role = LOCAL MARKETPLACE DISTRIBUTOR:**
  - Bring customers (PT's existing client network onboarded as zer0Gig users)
  - Provide local language support (Bahasa Indonesia + regional)
  - Handle compliance for vertical (Kominfo ESO, OJK regulated industries, BPS regulated SOEs)
  - Customize agent catalog for vertical (F&B, fintech, logistics, manufacturing, agri)
  - Customer service + training + onboarding
- **Operators do NOT run compute inference** — that stays on **0G Compute** (operators are 0G Compute customers, driving 0G adoption in Indonesia)
- **Operators do NOT run alignment attestation** — that stays on 0G Alignment Nodes
- **Operators DO run:**
  - Customer-facing marketplace UI (their branded portal pointing to zer0Gig contracts)
  - Local data residency (customer PII, support tickets — for compliance)
  - Bahasa Indonesia chat support
  - Onboarding workflows for their PT's specific vertical
  - Reseller dashboards for revenue tracking
- **Multi-sig ECDSA (3-of-5)** for marketplace governance (customer disputes, refunds, listing curation) — NOT inference verification
- **Indonesian PT partners (Jadid network) form first operator cohort**

After V2: zer0Gig has **institutional Indonesian distribution moat**. 0G Labs WANT zer0Gig to succeed because we drive 0G Compute consumption in Indonesia.

---

## 1. Architecture Overview (PIVOTED — Distribution Layer)

```
┌──────────────────────────────────────────────────────────────────────┐
│  0G FOUNDATION (Infrastructure — NOT our responsibility to build)    │
│  - 0G Chain (settlement)                                             │
│  - 0G Storage (data persistence)                                     │
│  - 0G Compute (LLM inference + TEE — runs ALL agent inference)       │
│  - 0G KV (agent memory)                                              │
│  - 0G Alignment Nodes (175K ECDSA-signing quality scores)            │
│  zer0Gig CONSUMES this — doesn't replicate it                       │
└──────────────────────────────────────────────────────────────────────┘
                                  ↓
┌──────────────────────────────────────────────────────────────────────┐
│  zer0Gig PROTOCOL LAYER (Commerce primitives on 0G Chain)            │
│  - AgentRegistry (ERC-7857 iNFT)                                     │
│  - ProgressiveEscrow (ERC-8183 + alignment gate)                     │
│  - SubscriptionEscrow (ERC-8183 Recurring + OKX session voucher)     │
│  - AgentMarketplace (secondary market for agent NFTs)                │
│  - AgentEarningsVault (keyless harvest)                              │
│  - UserRegistry (role management)                                    │
└──────────────────────────────────────────────────────────────────────┘
                                  ↓
┌──────────────────────────────────────────────────────────────────────┐
│  V2 OPERATOR REGISTRY (NEW — Distribution Layer)                     │
│  - Operators stake OG as RESELLER COMMITMENT BOND (10,000 OG min)    │
│  - SLA enforcement: response time, escalation, customer satisfaction │
│  - Slashing for proven SLA violation (fraud / abandonment)           │
│  - Reseller margin: 5-15% of every subscription/job through channel  │
│  - Cooldown period (7 days) for graceful unstake                     │
└──────────────────────────────────────────────────────────────────────┘
                                  ↓
┌──────────────────────────────────────────────────────────────────────┐
│  Agent — picks operator set                                          │
│  - Default: 5 operators per agent (configurable)                     │
│  - Consensus: 3-of-5 ECDSA signatures required for action            │
│  - Operator rotation allowed (governed by agent owner)               │
└──────────────────────────────────────────────────────────────────────┘
                                  ↓
┌──────────────────────────────────────────────────────────────────────┐
│  Operator local platform (NOT runtime — customer-facing biz layer)   │
│  - Branded portal pointing to zer0Gig contracts on 0G                │
│  - Indonesian language UX (Bahasa + regional)                        │
│  - Local data residency (customer PII, support tickets — compliance) │
│  - PT vertical customization (F&B / fintech / logistics / agri)      │
│  - Inference still consumed FROM 0G Compute (not replicated)         │
│  - Customer onboarding + training + support                          │
└──────────────────────────────────────────────────────────────────────┘
                                  ↓
┌──────────────────────────────────────────────────────────────────────┐
│  V2 Multi-sig Governance Actions (operator coordination)             │
│  - resolveDispute(jobId, [sig1..sig5])    — refund / partial refund  │
│  - curateListing(agentId, action)         — flag / unflag agents     │
│  - updateOperatorReputation(operatorId)   — periodic scoring         │
│  - Contract verifies 3-of-5 valid → executes; else reverts           │
│  NOTE: NOT for compute attestation — that's 0G Alignment Nodes       │
└──────────────────────────────────────────────────────────────────────┘
                                  ↓
┌──────────────────────────────────────────────────────────────────────┐
│  SLA Enforcement Window (24h after each major action)                │
│  - Customer can submit complaint with proof                          │
│  - Successful complaint → operator stake slash (5-50% based on harm) │
│  - Customer receives portion of slashed stake (compensation)         │
└──────────────────────────────────────────────────────────────────────┘
                                  ↓
┌──────────────────────────────────────────────────────────────────────┐
│  Multi-sig Alignment Oracle (V1.5 — separate from V2 operators)      │
│  - 3-of-5 threshold ECDSA verifier nodes                             │
│  - Each verifier scores output independently                         │
│  - Aggregate signature submitted with releaseMilestone               │
│  - Independent of operator network (oracle ≠ marketplace operator)   │
└──────────────────────────────────────────────────────────────────────┘
```

---

## 2. New Solidity Contracts

### 2.1 `OperatorRegistry.sol`

```solidity
contract OperatorRegistry {
    struct Operator {
        address operatorWallet;
        uint256 stake;                  // OG locked
        uint64  registeredAt;
        uint64  unstakeRequestedAt;     // 0 if not requested
        uint32  actionsProcessed;
        uint32  successfulActions;
        uint16  slashCount;
        OperatorStatus status;          // ACTIVE | UNSTAKING | SLASHED | EXITED
        string  metadataURI;            // off-chain operator info (location, capacity, etc.)
    }

    enum OperatorStatus { ACTIVE, UNSTAKING, SLASHED, EXITED }

    uint256 public constant MIN_STAKE = 10_000 ether;       // 10,000 OG
    uint64  public constant UNSTAKE_COOLDOWN = 7 days;
    uint16  public constant SLASH_RATE_MIN = 5_000;         // 50% bps for minor offense
    uint16  public constant SLASH_RATE_MAX = 10_000;        // 100% bps for severe

    mapping(address => Operator) public operators;
    address[] public activeOperators;

    function register(string calldata metadataURI) external payable {
        require(msg.value >= MIN_STAKE, "Insufficient stake");
        require(operators[msg.sender].status == OperatorStatus(0), "Already registered");
        // ... add to activeOperators ...
    }

    function requestUnstake() external {
        // Sets unstakeRequestedAt = now; cooldown starts
    }

    function completeUnstake() external {
        require(block.timestamp >= operators[msg.sender].unstakeRequestedAt + UNSTAKE_COOLDOWN, "Cooldown");
        // Transfer stake back, mark EXITED
    }

    function slash(
        address operator,
        uint16 slashRateBps,
        bytes32 reason,
        bytes calldata fraudProof
    ) external onlySlasher {
        // verify fraudProof; reduce stake; redistribute
    }
}
```

### 2.2 Updated `SubscriptionEscrow.sol` / `ProgressiveEscrow.sol`

Add quorum-signed action paths:

```solidity
function releaseMilestoneQuorum(
    uint256 jobId,
    uint256 milestoneIndex,
    bytes32 outputHash,
    uint16 alignmentScore,
    bytes[5] calldata operatorSignatures,    // 3-of-5 required
    address[5] calldata signers,
    bytes calldata alignmentMultisigSig       // multi-sig alignment oracle
) external nonReentrant {
    // 1. Verify 3-of-5 operator sigs (each operator must be ACTIVE in OperatorRegistry)
    require(_verifyOperatorQuorum(jobId, signers, operatorSignatures), "Quorum fail");
    // 2. Verify alignment multi-sig signature
    require(_verifyAlignmentMultisig(...), "Alignment fail");
    // 3. Standard checks (alignment threshold, milestone exists, etc.)
    // 4. Execute release
}
```

### 2.3 `AlignmentVerifierMultisig.sol` (NEW)

Replaces single-key oracle. 3-of-5 threshold ECDSA.

```solidity
contract AlignmentVerifierMultisig {
    address[5] public verifiers;
    uint8 public constant THRESHOLD = 3;

    function verify(
        bytes32 messageHash,
        bytes[5] calldata sigs,
        address[5] calldata signers
    ) external view returns (bool) {
        uint8 validCount = 0;
        for (uint8 i = 0; i < 5; i++) {
            if (_isVerifier(signers[i]) && ECDSA.recover(messageHash, sigs[i]) == signers[i]) {
                validCount++;
            }
        }
        return validCount >= THRESHOLD;
    }
}
```

---

## 3. Operator Daemon (Off-chain Runtime)

### 3.1 What each operator runs

Open-source repo: `github.com/zer0Gig/Agent-Runtime` (already public, V1).

V2 additions:
- **GossipManager** — libp2p-based peer discovery + message gossip
- **ConsensusEngine** — propose action → collect peer signatures → broadcast quorum
- **SlashMonitor** — watch peers for misbehavior; submit fraud proof if detected
- **StakeManager** — handle on-chain stake/unstake calls
- **MetricsExporter** — Prometheus-compatible operator health metrics

### 3.2 Hardware requirements

| Tier | Spec | Cost (estimated) | Suitable for |
|---|---|---|---|
| **Light** | 2 vCPU / 4 GB RAM / 50 GB SSD | ~$10-20/mo (Hetzner / DO) | Solo operator, lab |
| **Standard** | 4 vCPU / 8 GB RAM / 100 GB SSD | ~$30-60/mo (Hetzner / Telkomsigma) | PT mid-size, university |
| **Pro** | 8 vCPU / 16 GB RAM / 200 GB SSD + redundancy | ~$150-300/mo (BUMN cloud / on-prem) | Telkom Indigo, large PT |

A "Light" tier operator can comfortably handle 50-100 active agents. "Pro" can handle 500+.

### 3.3 Operator daily ops (PIVOTED — Marketplace Distribution, NOT Runtime)

Daily operations focus on **customer-facing business**, not compute infrastructure:

1. **Customer acquisition** — onboard new UMKM clients from PT's existing network onto zer0Gig via operator's branded portal
2. **Customer support** — Bahasa Indonesia chat support, training sessions, onboarding workshops for new agent owners
3. **Vertical customization** — curate agent catalog for operator's specialty (e.g., F&B operator features food-ordering agents, logistic operator features shipment-tracking agents)
4. **Compliance handling** — keep customer PII in-country (Kominfo ESO + OJK requirements), handle KYC for PT clients, ensure agents comply with industry-specific regulation
5. **Quality assurance** — review agent performance metrics, flag underperforming agents, work with agent owners on improvement
6. **Reseller margin collection** — 5-15% margin on every subscription/job that flows through operator's channel → settled to operator wallet periodically
7. **SLA monitoring** — ensure response time meets commitments (else risk stake slash)
8. **NOT compute inference** — that all flows through 0G Compute (operator is 0G Compute customer, drives 0G adoption)
9. **NOT alignment attestation** — that all flows through 0G Alignment Nodes (operator does not run quality oracle)

---

## 4. Economic Design

### 4.1 Operator revenue

Every escrow release / voucher settlement carries a 0.5% **protocol fee**.

```
Job value: 10 OG
Milestone release: 10 OG
Protocol fee: 0.5% = 0.05 OG
  ↓
  ├── 0.03 OG (60%) → split among operator quorum (e.g., 5 operators × 0.006 OG each)
  └── 0.02 OG (40%) → zer0Gig treasury (fund development, audit, support)
```

For a mid-volume operator processing 100 actions/day at 10 OG average:
- Daily fee earnings: 100 × 0.006 OG = 0.6 OG (~$340/day at $0.57/OG)
- Monthly: ~$10,200
- Annual: ~$120,000

Light-tier operator costs ~$240/year. **Net margin: 99%+ for active operators.**

### 4.2 Slashing penalties

| Offense | Severity | Slash rate |
|---|---|---|
| Failed liveness (no signature for 24h) | Minor | 5% stake (reversible if cured) |
| Mis-attest quality (wrong alignment score) | Severe | 50% stake |
| Submit fake milestone (collusion) | Critical | 100% stake + permanent ban |
| Sign for inactive peer (Sybil signal) | Severe | 50% stake |

Slashed stake distribution:
- 30% to challenger (bounty for catching fraud)
- 40% to harmed agent owner (compensation)
- 30% burned (deflationary pressure on OG token)

### 4.3 Stake requirements

| Operator tier | Min stake | Implied capacity |
|---|---|---|
| Tier 1 (entry) | 10,000 OG (~$5,700) | Up to 50 active agents |
| Tier 2 (growth) | 50,000 OG (~$28,500) | Up to 500 active agents |
| Tier 3 (enterprise) | 200,000 OG (~$114,000) | Up to 5,000 active agents |

Higher stake = higher trust = more agents will pick you = more fees.

---

## 5. The Indonesian Operator Cohort — Strategic Angle

This is where zer0Gig V2 wins narrative and distribution simultaneously.

### 5.1 First-cohort operators (target launch Q4 2026)

| Operator | Tier | Capacity | Strategic role |
|---|---|---|---|
| **Telkom Indigo** | Tier 3 | 5,000 agents | Institutional credibility, BUMN signal |
| **PT Partner 1** (via Jadid) | Tier 2 | 500 agents | Geographic coverage (Jakarta) |
| **PT Partner 2** | Tier 2 | 500 agents | Vertical specialization (e.g., F&B) |
| **PT Partner 3** | Tier 2 | 500 agents | Vertical specialization (e.g., logistics) |
| **PT Partner 4** | Tier 2 | 500 agents | Vertical specialization (e.g., fintech) |
| **PT Partner 5** | Tier 2 | 500 agents | Regional coverage (Surabaya / Yogya / Bandung) |
| **UKDW** | Tier 1 | 50 agents | Academic + research operator |
| **3-5 solo operators** | Tier 1 | 250 agents | Crypto-native independent operators |

Total target capacity: 7,500+ active agents at launch. Scales to 50,000+ as more operators join.

### 5.2 Why Indonesian PT partners are natural operators

| Reason | Implication |
|---|---|
| **They already use zer0Gig** (V1 customers) | Familiarity with platform; lower onboarding friction |
| **They want their agent's data in Indonesia** | Operator running in-country = compliance ESO/Kominfo automatically |
| **They have IT infra + budget** | Server cost is rounding error |
| **They can earn passive OG income** | Operator fee = additional revenue stream |
| **Their brand becomes co-guarantee** | "Telkom Indigo is a zer0Gig operator" = institutional credibility |
| **They form natural redundancy** | 5+ operators across Indonesia = no single failure point |

### 5.3 Coalition narrative for pitch

> *"5-8 PT enterprise partners that Jadid is bringing aren't just customers. They're co-owners of the infrastructure. Each PT runs a zer0Gig operator node. Each PT stakes OG as collateral. Each PT earns fees from agent operations. The platform doesn't just have users — it has an institutional Indonesian backbone."*

This is **stronger than "decentralized"**. It's **"Indonesian-sovereign decentralized AI agent infrastructure."** No other agent platform globally has this configuration.

---

## 6. Reference Projects — Why V2 Pattern is Proven

| Project | Pattern | Scale | Relevance to V2 |
|---|---|---|---|
| **Ethereum validators** (post-Merge) | Stake/slash; client diversity; 3+ years live | 800,000+ validators, $80B+ staked | Proves stake/slash works at scale |
| **EigenLayer AVS** | Restaking secures actively-validated services | $14B+ TVL, 100+ AVS designs | Direct model for V2 operator economic security |
| **Chainlink CCIP** | Multi-node consensus oracle signing | $14T+ enabled value | Multi-node consensus precedent |
| **Olas Protocol (Autonolas)** | Decentralized autonomous services with operator stake | Live, growing | Closest model to V2 |
| **Filecoin storage providers** | Stake + slashed on data loss | 4000+ providers, 22 EiB | Sustained 5+ years |
| **Bittensor subnets** | Miners + validators with token incentives | $1B+ market cap | Web3 + AI economic loop precedent |
| **Akash Network** | Decentralized GPU marketplace | $30M+ network value | Reference for operator-as-service |

**Punchline:** *"V2 model is not untested. It's the same proven pattern that secures $80B+ in Ethereum, $14B+ in EigenLayer, and $14T+ in Chainlink. We're applying it to agent-runtime — with Indonesian operators as first cohort."*

---

## 7. Implementation Plan

### 7.1 Required engineering work

| Component | Effort | Owner |
|---|---|---|
| `OperatorRegistry.sol` + tests | 2 weeks | Solidity dev |
| Quorum signing in escrow contracts | 2 weeks | Solidity dev |
| `AlignmentVerifierMultisig.sol` + tests | 1 week | Solidity dev |
| GossipManager (libp2p integration) | 4 weeks | Backend / Web3 dev |
| ConsensusEngine | 3 weeks | Backend / Web3 dev |
| SlashMonitor + fraud-proof generation | 3 weeks | Backend / Web3 dev |
| Operator daemon SDK + docs | 2 weeks | DevRel |
| Operator dashboard (monitoring + rewards) | 4 weeks | Frontend |
| Multi-sig alignment oracle deployment + key ceremony | 1 week | Security ops |
| Security audit (Sigma Prime / Hexens tier) | 4-6 weeks | External |
| First-cohort operator onboarding | 2 weeks | Partnerships |

**Total: ~16-20 weeks of engineering + 4-6 weeks audit = 5-6 months realistic timeline.**

### 7.2 Cost estimate

| Item | Cost (USD) |
|---|---|
| Engineering team (3 devs × 5 months) | $90-150k |
| Security audit | $80-150k |
| Infrastructure (testnet operator pilots) | $5-10k |
| Operator onboarding + training | $5-10k |
| Legal review (operator agreements, slashing terms) | $10-20k |
| Reserve / contingency | $20k |
| **Total** | **$210-360k** |

Pre-seed raise minimum: **$500k-$1M** covers V2 + 12-month runway.

### 7.3 Timeline (realistic, NOT aspirational)

| Phase | Date | Milestone |
|---|---|---|
| Hackathon submission | 2026-05-13 | V1 ships (current focus) |
| Pre-seed raise | Jun-Aug 2026 | Funding closes |
| V1.5 (multi-sig oracle only) | Sep 2026 | Quick decentralization win |
| V2 engineering | Sep-Dec 2026 | Core contracts + daemon |
| Audit | Jan 2027 | External review |
| Operator onboarding | Feb 2027 | Telkom Indigo + first 5 PTs |
| **V2 mainnet** | **Mar-Apr 2027** | **Launch with 8-10 operators** |
| V2 expansion | 2027 H2 | 50+ operators, regional growth |

**Honest framing:** Q4 2026 V2 launch was aspirational. **Q1-Q2 2027 is realistic.** Pitch this honestly — judges respect rigorous timing over optimistic vapor.

---

## 8. V1.5 — Quick Win Before Full V2

Before V2 multi-operator launches, we can ship **V1.5** to capture 80% of decentralization benefit at 10% of effort:

### 8.1 V1.5 = Multi-sig alignment oracle only

- Replace single-key oracle with 3-of-5 ECDSA verifier
- Keep agent-runtime centralized for now
- Already eliminates the most concerning V1 risk (oracle key compromise)

### 8.2 V1.5 effort

| Component | Effort |
|---|---|
| `AlignmentVerifierMultisig.sol` deployment | 1 week |
| Key ceremony with 5 verifier nodes | 1 week |
| Update escrow contracts to use multi-sig verify | 1 week |
| Audit (lite, smaller scope) | 2-3 weeks, ~$20-40k |

**Total: 5-6 weeks + $25-45k.** Doable as immediate post-demo task with minimal fundraise.

### 8.3 Honest framing in pitch

> *"V1 ships today with single-key alignment oracle (transparent risk, documented). V1.5 — Q3 2026 — replaces with 3-of-5 multi-sig. V2 — Q1 2027 — full operator network. We don't pretend; we ship in layers and improve."*

---

## 9. Risks + Mitigations

| Risk | Probability | Mitigation |
|---|---|---|
| Operators collude to mis-attest | Low (incentive misaligned) | Slash 100% stake + agent owner can rotate operators |
| Stake too high → no operators join | Medium | Start with tiered stake; subsidize first cohort stake from treasury |
| Stake too low → not economic security | Medium | Calibrate against expected agent volume; raise tier minimums as agent value grows |
| Operator daemon bugs cause cascading failure | Medium | Required client diversity (encourage 2-3 implementations); deterministic spec |
| Alignment oracle key holder disappears | Low (multi-sig) | 5 independent verifier nodes with separate operations |
| First-cohort PT partners back out | Medium | Multiple parallel partnership tracks; Indonesian crypto-native operator backup pool |
| Indonesian regulation against operator economic activity | Medium | Legal review pre-launch; structure operator as service provider, not financial activity |
| 0G mainnet delays | High (external dependency) | V1.5 can ship without 0G mainnet; V2 contingent on 0G timeline |

---

## 10. Open Questions

1. **Operator selection algorithm** — does agent owner pick operators manually, or is there auto-allocation? Recommendation: hybrid — owner picks "operator set type" (e.g., "5 random tier-2") and registry auto-assigns.
2. **Operator client diversity** — should we require multiple runtime implementations (TypeScript + Go + Rust) like Ethereum? Recommendation: yes long-term, single implementation V2 launch.
3. **Fee market dynamics** — fixed protocol fee (0.5%) vs operator-set bid (free market)? Recommendation: fixed at launch, evolve to free market V3+.
4. **PT regulatory framework** — does "operating zer0Gig node" require Bappebti registration? Legal review required pre-launch.
5. **OG token economic implications** — V2 locks significant OG in operator stake. Coordinate with 0G Labs on tokenomics impact.
6. **Cross-chain operators** — can EVM-compatible chain operators participate (e.g., operator on Polygon signing 0G actions)? V3 question.

---

## 11. Reference

- [EigenLayer whitepaper](https://docs.eigenlayer.xyz/) — restaking + AVS economic security model
- [Chainlink CCIP architecture](https://docs.chain.link/ccip/architecture) — multi-node consensus signing
- [Olas Protocol docs](https://docs.autonolas.network/) — decentralized autonomous services
- [Filecoin proofs](https://docs.filecoin.io/basics/the-blockchain/proofs) — slashing on data unavailability
- [Ethereum validator economics](https://docs.prylabs.network/docs/concepts/proof-of-stake/) — stake/slash at $80B+ scale
- `Project/docs/contracts/OKX_session_voucher_design.md` — parallel V2 work on payment rails
- `video/pitching/zer0Gig_Pitch_Narrative_Foundation.md` — pitch framing that uses this doc

---

*V2 is the moment "survives us" stops being a tagline and becomes architecture. Built with the Indonesian institutional coalition Jadid brings — Telkom Indigo + PT partners + UKDW + crypto-native operators. Coalition is open.*
