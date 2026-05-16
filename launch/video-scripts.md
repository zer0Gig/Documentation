---
Date Created: 2026-05-11
Date Modified: 2026-05-11
Title: zer0Gig Video Scripts — How It Works + Pitch Video
Audience: Hans, Dave, anyone recording video content for the hackathon submission
---

# Video Scripts — Two Videos, Two Audiences

> Per Dragon (0G DevRel): you need **two videos**, not one. Each speaks to a different audience with a completely different narrative arc. Footage can be reused, but the story can't.

---

## Why Two Videos

| Video | Audience | Length | Goal | Narrative arc |
|---|---|---|---|---|
| **Video 1 — How It Works** | Users (clients + agent owners) | 2-3 min | After watching, people should know *what they can do* and *how* | User journey, visuals heavy, light narration |
| **Video 2 — Pitch Video** | Judges + investors | 3-5 min | After watching, judges should know *why this matters* and *why us* | Problem → Product → Architecture → Why 0G → Why Team |

**Common mistake:** ship one video that tries to do both jobs. The user-facing video loses depth; the pitch loses warmth. Make both.

---

## Video 1 — How It Works (2-3 min, user-facing)

### Beat-by-beat script

| Beat | Time | VO (target ~140 wpm) | Visual |
|---|---|---|---|
| **Hook** | 0-10s | "What if hiring a freelancer was as easy as ordering a ride — and you only paid if the work passed a quality check?" | Fast cuts: traditional freelance frustration (slow chat threads, fake portfolios, missed deadlines), then dissolve into the zer0Gig landing page |
| **Pain** | 10-30s | "On most platforms, you pay upfront and pray. Reviews are mutable. Disputes drag on. And nobody can actually verify that the work was good." | Generic Upwork / Fiverr screenshots, blurred. Cut to "centralized" tag with a red X. |
| **Setup** | 30-45s | "zer0Gig flips the model. AI agents do the work, smart contracts hold the money, and a network of cryptographic Alignment Nodes scores every output before payment releases." | Hero shot of zer0Gig landing page. Live on-chain stats counter ticking up. |
| **Live demo — Client posts a job** | 45s-1m15s | "Watch what happens when I post a job. I describe the task, lock 0.5 OG into escrow, and define three milestones." | Screen recording: `/dashboard/create-job` → fill form → wallet signs `postJob` → tx hash on scan-testnet |
| **Live demo — Agent picks it up** | 1m15s-1m45s | "Within seconds, an autonomous agent on the 0G runtime sees the job, downloads the encrypted brief, runs it through 0G Compute, and submits a proposal." | Split screen: terminal logs of Agent Runtime + frontend showing new proposal arriving |
| **Live demo — Milestone payout** | 1m45s-2m20s | "When the agent finishes a milestone, it runs a self-evaluation. If it scores above 80%, a 0G Alignment Node signs an attestation. The smart contract verifies that signature on-chain — and only then does the escrow release. No invoices. No middlemen." | Screen recording: agent log → alignment signature → `releaseMilestone` tx → balance changes on agent wallet → Telegram notif arrives |
| **Switch perspective — Agent owner** | 2m20s-2m45s | "If you own an AI agent, you mint it as an Intelligent NFT and put it to work. Reputation is on-chain. Earnings are autonomous. You don't even need to be online." | Quick cuts of `/dashboard/register-agent`, agent detail page with stats, Telegram client receiving milestone card |
| **CTA** | 2m45s-3m | "We're live on 0G Newton testnet right now. Connect a wallet, mint an agent, post a job. Link in the description." | Landing hero with URL `zer0gig.vercel.app` on screen for 5 seconds |

### Visual & B-roll checklist

- [ ] Landing page in 1080p, no DevTools open
- [ ] Wallet with prefunded OG (so signing is instant on demo)
- [ ] Agent Runtime terminal in a readable font (16pt+) on a dark theme
- [ ] At least one Telegram milestone card screenshot or short capture
- [ ] On-chain explorer (scan-testnet) showing actual tx — open in a tab for cutaway

### Tone notes

- Casual, confident — not salesy
- VO should land like a tutorial, not an ad
- Music: light electronic / lo-fi, sub-bass low so VO sits on top

---

## Video 2 — Pitch Video (3-5 min, judges/investors)

### Beat-by-beat script

Structured exactly as Dragon recommended: **Problem → Product → Architecture → Why 0G → Why Team**.

#### Beat 1 — Problem (45s)

> "We are living through two simultaneous revolutions. First — autonomous AI agents. LangChain, AutoGPT, CrewAI. Agents are no longer demos; they're doing real work. Second — decentralized infrastructure. Smart contracts that execute trustlessly, identity that lives on-chain.
>
> But these two worlds haven't actually converged. Wrapping a chatbot in a Web3 frontend isn't integration — that's packaging. And it leaves three real problems unsolved.
>
> One: AI agents have no verifiable identity. Two: payment isn't connected to result quality. Three: agents on Platform A can't be discovered, hired, or composed with agents on Platform B. These are protocol-level problems. They require protocol-level solutions."

**Visual:** split-screen — left side LangChain logos / AutoGPT screenshots; right side Ethereum / 0G logos. Dissolve into a venn diagram with "zer0Gig" at the intersection.

#### Beat 2 — Product (60s)

> "zer0Gig is the first production deployment of two emerging Ethereum standards that solve these three problems.
>
> ERC-7857 — the Intelligent NFT standard proposed by 0G Labs — gives every agent a cryptographic identity with encrypted capability data, oracle-proven transfer, and time-bounded usage licensing. Agents aren't just NFTs. They're on-chain economic entities with their own wallet.
>
> ERC-8183 — the Agentic Commerce Protocol proposed by Virtuals and the Ethereum Foundation — standardizes the entire job lifecycle. Post a job, accept a proposal, define milestones, release escrow — all on-chain, all gated by a cryptographically signed alignment score.
>
> And SubscriptionEscrow extends the same pattern to recurring work — daily reports, monitoring, ongoing services — with three interval modes that range from client-set schedules to fully autonomous agent decisions."

**Visual:** Trigger the contract addresses on screen as you say them. Then quick cuts of `/dashboard/agents/[id]` showing the ERC-7857 Agent Actions section, and `/dashboard/jobs/[id]` showing the milestone timeline.

#### Beat 3 — Architecture (60s)

> "Four contracts. Live on 0G Newton testnet since April 28.
>
> AgentRegistry holds the iNFT identities. ProgressiveEscrow handles one-time jobs. SubscriptionEscrow handles recurring work. UserRegistry manages roles.
>
> Above the contracts: a Next.js frontend deployed on Vercel, with Privy for wallet authentication.
>
> Below the contracts: an autonomous agent runtime deployed on Railway. Seventeen services that listen for events, decrypt encrypted briefs, route LLM inference through 0G Compute or fallback providers, self-evaluate output quality, store results on 0G Storage, sign alignment attestations, and claim payment — all without a human in the loop.
>
> And cutting across everything — the 0G Alignment Nodes that sign every milestone's quality score before the contract releases the escrow."

**Visual:** the architecture diagram from `Project/docs/agent-runtime/README.md` — the one with the runtime↔0G stack mapping. Pause for 8 seconds on the diagram so judges can read it.

#### Beat 4 — Why 0G (45s)

> "Why 0G specifically — and this is where most hackathon teams hand-wave. Here's the precise answer.
>
> First, ERC-7857 originated from 0G Labs. We're building alongside the team that proposed the standard, not retrofitting it onto a chain that was never meant for it.
>
> Second, we use the full 0G stack, not just the chain. 0G Storage for outputs. 0G Compute for inference. 0G KV for agent memory. 0G Alignment Nodes for cryptographic verification. Four components, all load-bearing.
>
> Third, 0G's architecture — high-throughput data availability, fast finality, native modular design — is engineered for AI workloads. For a marketplace where every job involves on-chain transactions, that infrastructure fit is decisive.
>
> And fourth, 0G ships the foundation but not the autonomous runtime. We fill that gap. zer0Gig is the first autonomous agent runtime native to the 0G ecosystem."

**Visual:** show the 0G stack mapping table from `agent-runtime/README.md` highlighting the "Provided by zer0Gig agent-runtime" column.

#### Beat 5 — Why Our Team (45s)

> "Why us. Three reasons.
>
> One — we shipped. Four contracts deployed and verified. A 35-document technical reference. Daily marketing cadence since week one. A live demo to the head of computing at our university and a partnership pipeline with five to eight Indonesian enterprises through Jadid Purwaka Aji.
>
> Two — we have distribution. Indonesia's AI builder community is underserved by Western platforms. We have native-language presence, cultural fit, and a direct line into the Southeast Asia startup ecosystem.
>
> Three — we move fast. Seven weeks from project initiation to production deployment of two unmerged EIPs. Public testnet opening tonight. Mainnet preparation in motion."

**Visual:** founder photo (Hans), then quick montage — POSTON-X day cards, UKDW demo photo, contract addresses on scan-testnet, partnership announcement.

#### Beat 6 — Closing (30s)

> "We're not aiming for the agentic economy. We're already running it.
>
> zer0Gig — the first production deployment of ERC-7857 and ERC-8183 on 0G.
>
> Live now at zer0gig.vercel.app."

**Visual:** logo on dark navy background, URL underneath, hold for 5 seconds.

### Recording checklist

- [ ] 1080p minimum, 4K if your machine handles it
- [ ] Wired headset for VO (no AirPods — compression artifacts)
- [ ] Studio Display or external monitor for screen captures
- [ ] All contract addresses validated on scan-testnet before recording
- [ ] Wallet OG balance > 1.0 (room for multiple demo TXs)
- [ ] Telegram bot pre-paired to demo wallet (so notif arrives instantly)
- [ ] Background noise check — windows closed, AC off if loud, second take if neighbor revs motorcycle

---

## Footage Reuse Strategy

You don't need to record every clip twice. The two videos share these primitives:

| Clip | Used in Video 1 | Used in Video 2 | Notes |
|---|---|---|---|
| Landing page hero | Yes | Yes | Reuse |
| `postJob` wallet signing | Yes | No | V1 only — V2 doesn't show TXs at this level |
| Agent runtime terminal logs | Yes | Yes | Different VO over identical footage |
| `releaseMilestone` flow | Yes | No | V1 demonstrates; V2 explains via diagram instead |
| Architecture diagram | No | Yes | V2 only — too dense for users |
| Telegram milestone card | Yes | Optional | V1 hero feature; V2 quick mention |
| Contract addresses on scan-testnet | Brief | Long pause | V2 lingers on the chain receipts |

---

## Production Tools

| Step | Tool | Why |
|---|---|---|
| Screen + cam recording | OBS Studio or ScreenStudio | Both free; ScreenStudio gives nicer auto-zoom |
| VO recording | Audacity or Descript | Descript can clean up "uhs" and breaths |
| Editing | DaVinci Resolve (free) or CapCut | Resolve for polish; CapCut for fast turnaround |
| Captions | Whisper (or Descript) | Auto-transcribe, manually correct contract addresses |
| Music | FreePD, Pixabay Music, Epidemic Sound (paid) | Don't risk YouTube copyright |
| Subtitles | English + Indonesian (.srt) | Bilingual = wider reach for Southeast Asia audience |

---

## Distribution Plan

### Video 1 — How It Works

- Landing page hero — embed as autoplay-muted hero video
- YouTube — public, unlisted backup if YT processes slow
- X thread — "Here's zer0Gig in 3 minutes" + thread breakdown of each beat
- Telegram + Discord — pin in 0G community channels with one-liner intro

### Video 2 — Pitch Video

- Hackathon submission portal — primary upload
- YouTube — unlisted, link only to judges
- Investor email attachments — direct send to target list
- X post — gated tease ("Pitch video below — DM for unlisted link") to drive engagement

---

## Approval Flow

Before publishing either video:

1. Hans + Dave do a private watch-through, write feedback in shared doc
2. Re-record any segment scoring < 7/10 for clarity
3. Tighten cuts — every video should feel 10-20% shorter than the script length
4. Caption check — every contract address, every URL must be in subtitles correctly
5. Public ship

---

## Related

- [One-Liner](one-liner.md) — the canonical sentence behind every VO segment
- [GTM Playbook](gtm-playbook.md) — where to post the finished videos
- [Why Our Team](team.md) — full content of Pitch Video Beat 5
