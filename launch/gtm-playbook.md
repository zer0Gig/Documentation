---
Date Created: 2026-05-11
Date Modified: 2026-05-11
Title: zer0Gig Go-To-Market Playbook
Audience: Hans, Dex, Dave, Natalie, anyone running outreach for the 5-day sprint to demo day
---

# Go-To-Market Playbook — Final 5 Days

> Per Dragon: *"Early traction is won by doing things that don't scale. Go where your users already hang out and reply one by one. Drop your product where your audience is already gathered, and pull the first 100 real pieces of feedback out by hand. Boring, manual, high-leverage. Most founders skip this and wonder why nothing sticks."*

This is the unsexy work. It is also the work that wins.

---

## Three Audience Segments

| Segment | Where they hang out | Pain we solve | Bait that converts |
|---|---|---|---|
| **Web3 builders (Agent Owners)** | AI/Web3 Twitter, 0G Discord, LangChain Discord, Telegram dev groups | Their agents have no income stream | "Mint your agent as an iNFT and start earning OG" |
| **AI builders & SaaS founders (Clients)** | r/LocalLLaMA, r/LangChain, r/AIagents, Product Hunt, ProductHunt-adjacent Slack groups | Pay-and-pray with freelancer / no quality guarantee | "Hire an AI agent. Pay only if alignment score ≥ 80%" |
| **Investors / partners** | LinkedIn, X DMs from mutual founders, Indonesian startup circles | Looking for production-grade dAI deployments | "First production deployment of ERC-7857 + ERC-8183 — live demo + contract addresses" |

---

## Channel-by-Channel Playbook

For each channel: where, what to post, how often, target metric for the next 5 days.

### X / Twitter

| What | How often | Target |
|---|---|---|
| Continue daily POSTON-X cadence (Day 14, 15, 16, 17, 18) | Daily | 1 thread/day, ≥ 5 replies per thread |
| Reply to AI builder + 0G ecosystem threads | 3-5 times/day | Add value, drop zer0Gig only when it fits |
| Quote-tweet 0G Labs / DevRel updates with our angle | Whenever 0G posts | Build co-marketing reciprocity |
| Pin updated tweet with the canonical [one-liner](one-liner.md) and the How It Works video link | Once | Pinned for the rest of the hackathon |
| DM 10 builders/day | Daily | Cold + warm — use [templates](#dm-templates) |

**Lists to scrape for outreach:**
- Anyone who liked/replied to 0G Labs posts in the last 14 days
- Builders who mentioned LangChain/AutoGPT/CrewAI in the last 7 days
- ETH builders shipping AI things (filter by "agent", "AI", "LLM" in bio)
- Hackathon judges' recent likes/replies (warm context)

### Telegram

| Group / channel | Approach |
|---|---|
| 0G official Discord/Telegram | Drop demo URL + How It Works video as a casual share, then engage with questions |
| LangChain Indonesia community | Post about ERC-8183 use case for LangChain agents needing payment |
| Indonesian Web3 dev groups (via Jadid) | Pre-announce mainnet plans, ask for testers |
| 0G ecosystem hackathon channels | Daily check-in: what we shipped today, ask for feedback |
| Crypto VC ecosystem channels (low-key) | Soft mention when relevant, never spam |

**Don't:** copy-paste the same announcement across multiple groups. Customize per group's vibe.

### Reddit

Soft launch — Reddit punishes promotional content but rewards educational/builder content.

| Subreddit | Post angle | When |
|---|---|---|
| r/LocalLLaMA | Technical writeup on ECIES-encrypted briefs + Alignment Nodes (educational, link to GitHub) | Day 14 |
| r/LangChain | "How we settled LangChain agent payments on-chain" — code + screenshots | Day 15 |
| r/AIagents | Demo video + open call for testers, soft mention of testnet OG faucet | Day 16 |
| r/AutoGPT | Architecture deep-dive — compare to centralized agent platforms | Day 17 |
| r/ethereum | "First production deployment of ERC-7857 + ERC-8183 on 0G — testnet live" with verifiable contract addresses | Day 18 |
| r/CryptoCurrency / r/0g (if exists) | Cross-post the r/ethereum thread; less editorial, just facts | Day 18 |

**Pattern:** answer top 10 comments within the first hour of posting. Reddit ranks engagement velocity.

### LinkedIn

Bridge: Jadid Purwaka Aji has direct connections to 5-8 Indonesian enterprise partners.

| Action | Owner |
|---|---|
| Hans posts: "What we shipped this week" with 4-image carousel (screenshots) | Daily |
| Jadid intros to PT partners via DM, Hans follows with 60-second elevator pitch | Throughout the 5 days |
| UKDW kaprodi (Halim Budi Santoso) endorsement post — testimonial + photo | Day 14 |
| Indonesian startup ecosystem comments: leave 3-5 thoughtful comments daily on relevant posts | Daily |

### Product Hunt

**Don't launch on PH yet.** Save it for post-mainnet. Hackathon traffic on PH is shallow; mainnet launch with PR-supported PH push will land harder.

But do: **set up the PH "Coming Soon" page** now. Stamp launch date placeholder, build the followers list. Free pre-launch signal.

### Direct outreach (the most underrated)

| Audience | Channel | Target | Daily quota |
|---|---|---|---|
| AI agent builders | X DM + LinkedIn | 30 names | 10 DMs/day |
| 0G Discord active builders | Discord DM | 20 names | 5 DMs/day |
| Indonesian Web3 founders | LinkedIn | 25 names (curated by Jadid) | 5 DMs/day |
| Mutual founder warm intros | Various | 10 priority names | 2/day, expect 1 follow-up |

Target: **150 direct touches by Day 18.** Realistic 20-30% reply rate = 30-45 real conversations.

---

## DM Templates

Three variants. Customize per recipient — never paste verbatim.

### Template 1 — Cold builder (someone shipping AI agents)

> Hey [Name] — saw your work on [specific project / tweet]. We just shipped zer0Gig: the first production deployment of ERC-7857 (Intelligent NFT) and ERC-8183 (Agentic Commerce) on 0G Network. Basically lets AI agents like the ones you build get hired on-chain and paid only when their work passes alignment attestation. No invoices, no middlemen.
>
> Live demo: zer0gig.vercel.app. Would love your take — even 60 seconds of feedback would mean a lot.

### Template 2 — Cold business (SaaS founder, consultancy, agency)

> Hi [Name] — quick context: I'm Hans, building zer0Gig with [link]. We help businesses hire AI agents for recurring work — daily reports, monitoring, research — with on-chain escrow that pays only when alignment scores hit 80%. Three interval modes including agent-proposed schedules.
>
> Your team seems to ship daily on [topic]. Curious whether something like this would fit your workflow. Mind a 15-minute chat? I can show a live demo of the recurring subscription mode.

### Template 3 — Warm intro reply (when introduced via mutual contact)

> Thanks for the intro, [Connector]. [Name] — short version: zer0Gig is the first production deployment of ERC-7857 + ERC-8183 on 0G. We turn AI agents into Intelligent NFTs that get hired via smart-contract escrow with cryptographic quality attestation.
>
> Three docs in increasing depth:
> - 60-second pitch: [video link]
> - 5-minute architecture: [GitBook link]
> - Live testnet: zer0gig.vercel.app
>
> Happy to do a 20-minute walkthrough whenever works. I'm in GMT+7 but flexible.

### Template 4 — Indonesian-language follow-up (Jadid pipeline)

> Halo [Nama] — saya Hans dari zer0Gig. Sebelumnya Jadid sudah cerita konteksnya. Kami baru live di 0G Newton testnet — marketplace di mana AI agent di-mint sebagai NFT identitas (ERC-7857), di-hire lewat smart contract escrow (ERC-8183), dan dibayar hanya kalau output-nya passing quality threshold yang ditandatangani secara kriptografis.
>
> Untuk konteks bisnis [PT Name]: salah satu use case yang potensial adalah [contoh konkret — recurring monitoring, automated reports, customer service bot autonomous]. Boleh minta waktu 15-20 menit untuk demo singkat minggu depan?

---

## Daily Activity Checklist (5-day sprint)

Print this. Stick it to your monitor.

```
☐ 1 X thread published (continue POSTON-X day cadence)
☐ 3-5 thoughtful replies on AI/Web3 Twitter threads
☐ 10 cold DMs sent
☐ 5 Discord/Telegram engagements (real conversation, not "hi")
☐ 1 community post (rotate: Reddit / LinkedIn / Telegram / Discord)
☐ 1 hour answering questions in 0G Discord / hackathon channels
☐ Log every conversation in tracker (see Metrics below)
☐ End of day: write tomorrow's X thread draft so morning starts running
```

If you only do 3 of the 7 things on a given day, prioritize: **DMs, X thread, community post.** Those have the highest signal-to-noise ratio in our specific situation.

---

## Metrics to Track Per Day

Maintain a simple spreadsheet (or Supabase table — eat your own dog food):

| Metric | Source | Target by Day 18 |
|---|---|---|
| DM response rate | Manual track | ≥ 20% |
| X impressions | X Analytics | ≥ 50k cumulative |
| X profile clicks | X Analytics | ≥ 500 |
| Wallet connects on app | Supabase / event log | ≥ 100 |
| `registerUser` TXs | scan-testnet | ≥ 60 |
| `mintAgent` TXs | scan-testnet | ≥ 25 agents minted |
| `postJob` TXs | scan-testnet | ≥ 40 jobs posted |
| Repeat user count (≥ 2 sessions) | Supabase analytics | ≥ 20 |
| Discord/Telegram members | Direct count | ≥ 200 |
| Newsletter / Notion waitlist | Custom form | ≥ 80 |

**Dragon's note:** "Repeat user behavior" is the metric judges care about most. Shipping 100 first-time wallet connects is fine. Shipping 20 users who came back twice is the signal that you have a product.

---

## "First 100 Users" Definition

Not just wallet connections. The bar:

> **A "real user" = wallet connected + 1 on-chain interaction (`registerUser` minimum) + 1 follow-up action within 48 hours (return visit, second TX, joined Discord, replied to a follow-up DM).**

This is harder than "100 visitors" and more meaningful.

---

## Anti-Patterns (Don't)

| Don't | Why |
|---|---|
| Broadcast on Twitter without engaging in conversations | Algo punishes one-way posting |
| Cross-post identical text across Reddit subs | Reddit ranks original content; copies get auto-buried |
| DM with a long pitch immediately | Open with one-line context; pitch on reply |
| Lead with "We're building..." | Lead with the user's pain or the surprising fact |
| Engage only when promoting | Be in the community for the 4 days you're NOT shipping news, too |
| Drop links without context | Bare links die — always lead with a 1-line hook |
| Promise mainnet timeline you can't keep | Underpromise, overdeliver |
| Forget to follow up at 48h on no-replies | The follow-up converts more than the original DM |

---

## Roles & Owners

| Activity | Primary owner | Backup |
|---|---|---|
| X content + replies | Hans | — |
| LinkedIn (English + Indonesian) | Hans | Jadid for amplification |
| Reddit posts | Hans | — |
| Discord/Telegram engagement | Hans | — |
| Indonesian enterprise outreach | Jadid (via warm intro) → Hans for follow-up | — |
| Technical questions (architecture, contracts) | Hans | Dex / Dave for docs references |
| Tracking spreadsheet updates | Hans (5 min end of day) | — |

---

## End-of-Sprint Retrospective (Day 18 evening)

After demo day submission, do a 30-minute retro:

- Which channel produced the highest-quality conversations?
- Which DM template had the best reply rate?
- What was the most surprising piece of feedback?
- What would we double down on for the next 30 days?
- What's the one thing we'd never do again?

Write it up in 500 words. Future you (and any new team member) will thank you.

---

## Related

- [One-Liner](one-liner.md) — copy used across all outreach
- [Video Scripts](video-scripts.md) — assets linked from every channel
- [Why Our Team](team.md) — background for warm intro emails + investor messages
