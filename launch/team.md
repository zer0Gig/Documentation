---
Date Created: 2026-05-11
Date Modified: 2026-05-11
Title: Why Our Team — The Unfair Advantage
Audience: Pitch video viewers, investors, judges, anyone asking "why these founders?"
---

# Why Our Team — The Unfair Advantage

> Per Dragon (0G DevRel): *"Don't skip why your team — what's your unfair advantage? Domain expertise, prior shipping track record, unique distribution, deep user insight? Judges bet on founders, not just ideas."*

This is the team narrative that closes the pitch. Use it verbatim in the Pitch Video Beat 5 (`video-scripts.md`), as the team slide in the deck, and as the founder paragraph in investor emails.

---

## TL;DR (use this when you have 30 seconds)

We're a small team that **shipped the first production deployment of two unmerged EIPs in 7 weeks**, have **academic and enterprise pipeline already onboarded**, and have **native access to Southeast Asia's AI builder community** — a market most Western teams can't penetrate.

---

## Founders

### Hans Gunawan — Founder, PM, Frontend, Agentic Economy Layer

- **Background:** UKDW (Universitas Kristen Duta Wacana) — Indonesia. Builder with full-stack chops; daily-shipping discipline.
- **Role on zer0Gig:** product direction, frontend architecture, economic model design, daily marketing cadence, partnerships
- **Track record evidence:**
  - 7 weeks from project initiation to mainnet-ready ERC-7857 + ERC-8183 deployment
  - Daily marketing cadence (POSTON-X day 1 through day 13, continuing)
  - 35-document technical reference shipped end-to-end
  - Live demo received by UKDW kaprodi (head of computing) Halim Budi Santoso on 2026-05-11
- **Domain insight:** Indonesia's AI builder community is large, growing, and underserved by Western platforms. Hans's local network = a distribution channel competitors can't replicate.
- **Links:** [LinkedIn](https://www.linkedin.com/in/hans-gunawan01/) · GitHub: [github.com/zer0Gig](https://github.com/zer0Gig)

### Dave — Smart Contracts, Agent Runtime

- **Role on zer0Gig:** Solidity contracts (AgentRegistry, ProgressiveEscrow, SubscriptionEscrow, UserRegistry), Node.js autonomous agent runtime, 0G ecosystem integration
- **Track record evidence:**
  - Four contracts deployed, verified, and live on 0G Newton testnet since 2026-04-28
  - Migrated from ERC-721 to ERC-7857 + ERC-8183 in a single sprint without breaking the frontend
  - Built the 17-service agent runtime — the first autonomous AI agent runtime native to 0G
  - Handled the encryption layer (ECIES briefs, sealed AES keys for ERC-7857 transfers, alignment ECDSA signing)
- **Domain insight:** rare combination of smart-contract engineering + autonomous agent runtime engineering. Most Web3 hires do one or the other; Dave does both.

---

## Partners & Collaborators

### Halim Budi Santoso — Kaprodi (Head of Computing), UKDW

- **Role:** academic mentor and validation channel
- **Status:** received private full-platform demo on **2026-05-11** — endorsed direction, agreed to introduce zer0Gig to the broader academic AI/Web3 community
- **Why this matters:** academic validation is the underrated trust signal. UKDW students = early user pool. UKDW faculty = research collaboration opportunities.

### Jadid Purwaka Aji — Indonesia Startup Ecosystem Builder

- **Role:** partnership bridge to Indonesian enterprises
- **Status:** partnership sealed **2026-05-11** — bringing **5-8 PT (private limited corporation) partners** into the pipeline for the next 30 days
- **Why this matters:** Indonesian enterprises hire AI talent locally because of language and cultural fit. Jadid's network gives zer0Gig direct access to that demand layer — something neither Upwork nor Fiverr serves well.
- **Links:** [LinkedIn](https://www.linkedin.com/in/jadid-purwaka-aji-408961144/)

### Past AI collaborators

Hans has built an unusual but well-documented multi-AI-assistant collaboration workflow throughout the project — orchestrating Claude, MiniMax, Qwen, and Agent5 alongside human collaborator Dave. Every contributor's work is logged in `Progress/`. This is itself evidence of a battle-tested human-in-loop AI development process — relevant because zer0Gig productizes that exact workflow.

---

## The Five Unfair Advantages

The points below are **concrete and falsifiable**. We avoid generic claims like "passionate team" or "domain experts." Every claim has a piece of evidence.

### 1. First-mover on standards — and we shipped them, not just talked about them

ERC-7857 and ERC-8183 are both in **Draft** status on the EIP repository as of 2026-05-11. We're not implementing settled standards — we're shipping ahead of them. That positions us as a reference implementation and gives us a defensible 6-12 month lead on any competitor who starts from a finalized spec.

**Evidence:** four contracts live and verified on 0G Newton testnet — [scan-testnet.0g.ai](https://scan-testnet.0g.ai/) shows all addresses with deployment timestamp 2026-04-28.

### 2. Built alongside 0G Labs — the team that proposed ERC-7857

We didn't pick 0G to score hackathon points. We picked it because the iNFT standard we implement originated from 0G Labs. We're building with the people who designed the protocol, not retrofitting it onto an unfamiliar chain.

**Evidence:** every architectural decision (sealed AES keys, oracle re-encryption, capability hash discipline) matches the ERC-7857 draft spec. The agent runtime fills the gap 0G doesn't ship — making zer0Gig and 0G structurally complementary.

### 3. Academic + enterprise pipeline already onboarded

We haven't built in a vacuum. As of demo day:
- **UKDW kaprodi** (Halim Budi Santoso) demo received with endorsement
- **5-8 PT partners** in the Jadid pipeline scheduled for Q3
- Public testnet opening tonight (2026-05-11) — distribution begins

**Evidence:** demo screenshots, signed partnership notes, daily marketing cadence proving outbound discipline.

### 4. Ship cadence — 7 weeks, daily

From project initiation (2026-03-26) to today (2026-05-11), we've shipped:
- 4 production smart contracts (with ERC-721 → ERC-7857 + ERC-8183 migration)
- 17-service autonomous agent runtime
- Full Next.js dashboard (register, marketplace, jobs, subscriptions)
- 35-document technical reference (this docs site)
- 13 public daily posts on X
- 1 live pitch to an academic head of computing
- 1 sealed partnership with the Indonesian startup ecosystem

That's not just velocity — it's velocity with documentation, marketing, and external validation in parallel. Most hackathon teams ship two of those three. We ship all four.

### 5. Native distribution into Southeast Asia AI/Web3

The Indonesian Web3 and AI builder community is large, growing, and structurally underserved by Western platforms. Hans, Dave, and Jadid are all natively embedded in this community.

**What this gives us that competitors don't have:**
- Native-language marketing reach (POSTON-TELE Bahasa Indonesia threads)
- Cultural fit with enterprise procurement processes (longer sales cycle, relationship-based)
- A defensible launch pad before expanding to other geographies

---

## Why Now — The Two-Revolutions Convergence

We're at a specific moment in time where two things just became true simultaneously:

1. **Autonomous AI agents are commercially capable.** LangChain, AutoGPT, CrewAI, and their successors have made the cognitive architecture mature. Agents now do real economic work — writing code, executing trades, generating reports, running customer support.
2. **The infrastructure to coordinate autonomous agents on-chain just arrived.** ERC-7857 was proposed by 0G Labs in mid-2025. ERC-8183 was proposed by Virtuals and the Ethereum Foundation around the same time. 0G's Newton testnet is the first chain with the storage + compute + alignment-node stack to make these standards usable in production.

The window to be the first production implementation is **right now**. A year from now there will be 20 teams shipping ERC-8183 marketplaces. We're shipping the first one.

---

## Risks & Mitigations

(Honest section — judges and investors notice when you list real risks.)

| Risk | Our mitigation |
|---|---|
| ERC-7857 + ERC-8183 specs may change before finalization | We track EIP discussions weekly. Migration scripts are part of the contracts repo. |
| 0G ecosystem is still early — fewer users than Ethereum mainnet | Testnet is our beachhead. Mainnet launch coordinated with 0G mainnet rollout. |
| Alignment Node decentralization is a long-term goal, not Day 1 | Today: single verifier. Roadmap: multi-verifier consensus, plus reputation slashing. Disclosed transparently in pitch. |
| Two-person founding team is small for scaling | Indonesian engineering hiring pipeline through UKDW alumni network is one of Hans's GTM levers. |
| Agent-runtime is novel software — bugs will surface in production | Cross-restart memory persistence proven via `e2e-memory-persistence.js` test. EventWatcher fallback for RPC filter drops. Continuous monitoring via Telegram bot for client subscriptions. |

---

## What We're Asking For (Post-Demo Day)

If you're an investor or partner reading this after demo day:

- **Strategic intros** — to AI agent builders, enterprise AI buyers, and 0G ecosystem partners
- **Indonesian enterprise pilots** — we ship custom integration support for first 10 partners
- **Mainnet co-marketing** — paired launch announcement with 0G + zer0Gig + first enterprise partners
- **Pre-seed funding** — to grow Indonesian engineering team to 5 and extend runtime to 12 months while we onboard the first 1,000 paying agents

---

## Closing Statement

> *We are not aiming for the agentic economy. We are already running it.*
>
> *Four contracts live. A working autonomous runtime. A real partnership pipeline. And a team built to compound across the next 12 months.*
>
> *zer0Gig — the first production deployment of ERC-7857 and ERC-8183 on 0G.*

---

## Related

- [One-Liner](one-liner.md) — canonical copy across all surfaces
- [Video Scripts](video-scripts.md) — full Pitch Video script with this team narrative
- [GTM Playbook](gtm-playbook.md) — channels where this story gets told
