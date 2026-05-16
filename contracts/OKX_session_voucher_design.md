---
Date Created: 2026-05-12
Date Modified: 2026-05-12
Title: OKX APP session voucher — integration design for SubscriptionEscrow
Audience: zer0Gig contract + agent-runtime contributors planning the post-demo upgrade path
Status: DESIGN — not yet implemented in contracts
---

# OKX APP `session` Voucher — Integration Design

> Replace the current x402 stub in `SubscriptionEscrow` with a working monotonic voucher path borrowed from **OKX Agent Payments Protocol v1.0** (April 2026). Keep the alignment-attestation gate and ERC-8183 lifecycle. Add replay-proof per-tick billing without per-tick on-chain TXs.

---

## Why

### Current state — `drainPerCheckIn` (working)

Agent calls on-chain `drainPerCheckIn(subId)` per scheduled tick. Contract verifies caller is `agentWallet` and `block.timestamp ≥ lastCheckIn + intervalSeconds`. Transfers `checkInRate` to agent.

**Cost:** One on-chain TX per tick. For an hourly subscription running a year, that's 8,760 TXs.

### Current state — x402 stub (broken)

`createSubscription(...)` accepts `x402Enabled`, `x402VerificationMode`, and `clientX402Sig` args. They are **stored on-chain** but no function consumes them. Frontend currently passes `"0x"` (empty bytes) — contract guard `if (x402Enabled && clientX402Sig.length > 0)` treats as no-voucher.

**Status:** Schema present, no runtime path, no signing implementation, no drain function that accepts vouchers.

### Target — `session` voucher

A single client signature at subscription creation grants the agent a **monotonic voucher chain**. For each tick the agent submits a typed voucher with `sequence = previous + 1`. The contract verifies signature + monotonicity → drains `checkInRate`. Older vouchers are obsolete by construction — replay-proof.

**Cost saved:** Drains can be batched (e.g., daily settlement vs hourly). Agent submits one voucher with sequence N covering N ticks → single on-chain TX.

**OKX spec parity:** This is the `session` intent from OKX APP v1.0.

---

## Spec Mapping — OKX `session` → zer0Gig

| OKX APP concept | zer0Gig SubscriptionEscrow |
|---|---|
| Buyer (client) | `subscription.client` |
| Seller (agent) | `subscription.agentWallet` |
| `session` intent | One subscription = one session |
| Voucher signature | Client EIP-712 signature over typed data |
| Monotonic sequence | `subscription.lastVoucherSeq` advances per submission |
| Custody contract | `SubscriptionEscrow` itself (we already hold balance on-chain) |
| Dispute window | We deliberately *do not* adopt OKX's dispute window — alignment attestation is our gate (stronger, cryptographic) |
| Settlement | Direct transfer on `submitSessionVoucher` call |

### What we keep that OKX doesn't have

- **Alignment Node ECDSA attestation gate** — every voucher submission still requires `alignmentScore` parameter, and the contract verifies the alignment signature against `alignmentNodeVerifier`. Without that signature, voucher submission reverts.
- **ERC-8183 lifecycle structure** — subscription state machine (PENDING → ACTIVE → PAUSED → CANCELLED) unchanged.

### What we adopt from OKX

- **Replay-proof monotonic sequence** — vouchers with sequence ≤ `lastVoucherSeq` revert.
- **Pre-authorized cap** — `upto` intent semantics applied via `subscription.balance` (already capped at deposit value).
- **EIP-712 typed data** — replaces our current empty-bytes placeholder.

---

## Solidity Design — New Function

Add to `SubscriptionEscrow.sol`:

```solidity
/// @notice EIP-712 typed data hash for SessionVoucher.
/// Domain separator built from contract address + chain id + name="zer0Gig SessionVoucher" + version="1".
bytes32 public constant SESSION_VOUCHER_TYPEHASH = keccak256(
    "SessionVoucher(uint256 subId,uint64 sequence,uint64 settledAt,uint96 amount,uint16 alignmentScore,bytes32 outputHash)"
);

struct SessionVoucher {
    uint256 subId;
    uint64  sequence;       // must be > subscription.lastVoucherSeq
    uint64  settledAt;      // timestamp at which this voucher was signed
    uint96  amount;         // cumulative drain through this sequence (anti-front-run)
    uint16  alignmentScore; // 0-10000 bps, gates payout (≥ 8000 required)
    bytes32 outputHash;     // keccak256 of work output stored in 0G Storage
}

/// @notice Submit a client-signed monotonic voucher to drain accumulated check-in payments.
///         Replaces N individual `drainPerCheckIn` calls with a single batched settlement.
function submitSessionVoucher(
    SessionVoucher calldata v,
    bytes calldata clientSignature,    // EIP-712 from client
    bytes calldata alignmentSignature  // ECDSA from alignmentNodeVerifier
) external nonReentrant {
    Subscription storage sub = subscriptions[v.subId];

    // ── Caller + state ───────────────────────────────────────────────
    if (msg.sender != sub.agentWallet) revert NotAgent();
    if (sub.status != Status.ACTIVE)   revert InvalidStatus();

    // ── Monotonic ────────────────────────────────────────────────────
    if (v.sequence <= sub.lastVoucherSeq) revert StaleVoucher();
    if (v.amount   <= sub.totalDrainedViaVoucher) revert StaleAmount();

    // ── Alignment gate (keep zer0Gig USP) ────────────────────────────
    if (v.alignmentScore < ALIGNMENT_THRESHOLD) revert BelowAlignmentThreshold();
    if (v.alignmentScore > 10000)               revert InvalidScore();
    _verifyAlignment(v, alignmentSignature);

    // ── Client EIP-712 signature ─────────────────────────────────────
    _verifyClientSignature(v, clientSignature, sub.client);

    // ── Compute drain ────────────────────────────────────────────────
    uint96 delta;
    unchecked { delta = uint96(v.amount - sub.totalDrainedViaVoucher); }
    if (delta == 0)                  revert ZeroDelta();
    if (delta > sub.balance)         revert InsufficientBalance();

    // ── State update ─────────────────────────────────────────────────
    unchecked {
        sub.balance              -= delta;
        sub.totalDrained         += delta;
        sub.totalDrainedViaVoucher += delta;
    }
    sub.lastVoucherSeq = v.sequence;
    sub.lastCheckIn    = uint64(block.timestamp);

    // ── Transfer ─────────────────────────────────────────────────────
    (bool sent, ) = payable(sub.agentWallet).call{value: delta}("");
    if (!sent) revert TransferFailed();

    emit SessionVoucherSettled(
        v.subId, v.sequence, delta, v.alignmentScore, v.outputHash, uint64(block.timestamp)
    );

    // ── Auto-pause check (reuse existing logic) ──────────────────────
    if (sub.checkInRate > 0 && sub.balance < sub.checkInRate) {
        _pauseSubscription(v.subId, "INSUFFICIENT_BALANCE");
    }
}
```

### Storage additions

```solidity
struct Subscription {
    // ... existing fields ...
    uint64  lastVoucherSeq;          // NEW: monotonic sequence guard
    uint128 totalDrainedViaVoucher;  // NEW: cumulative voucher amount (allows mixing with drainPerCheckIn)
}
```

### Events

```solidity
event SessionVoucherSettled(
    uint256 indexed subId,
    uint64  indexed sequence,
    uint96  amountDrained,
    uint16  alignmentScore,
    bytes32 outputHash,
    uint64  timestamp
);
```

### Why mixing is safe

`drainPerCheckIn` keeps its time-window check. `submitSessionVoucher` keeps its sequence check. Both can coexist on the same subscription. The client's deposited `balance` is the ultimate cap — neither path can drain past it. `totalDrainedViaVoucher` is tracked separately so voucher amounts are anti-front-runnable.

---

## Frontend — EIP-712 Typed Data

```typescript
// Project/frontend/src/lib/session-voucher.ts (NEW)

import { TypedData } from "viem";

export const SESSION_VOUCHER_DOMAIN = {
  name: "zer0Gig SessionVoucher",
  version: "1",
  chainId: 16602,             // 0G Newton
  verifyingContract: "0x088400FFf9d37851173e22eef904e710B88F6312" as const,
};

export const SESSION_VOUCHER_TYPES = {
  SessionVoucher: [
    { name: "subId",          type: "uint256" },
    { name: "sequence",       type: "uint64"  },
    { name: "settledAt",      type: "uint64"  },
    { name: "amount",         type: "uint96"  },
    { name: "alignmentScore", type: "uint16"  },
    { name: "outputHash",     type: "bytes32" },
  ],
} as const;
```

### Client signs once at subscription creation (and re-signs on top-up)

```typescript
import { useSignTypedData } from "wagmi";

const { signTypedDataAsync } = useSignTypedData();

const voucherTemplate = {
  subId:          0n,           // assigned post-create
  sequence:       0n,
  settledAt:      0n,
  amount:         0n,           // upper bound = balance at creation
  alignmentScore: 0,
  outputHash:     "0x" + "00".repeat(32) as `0x${string}`,
};

const signature = await signTypedDataAsync({
  domain: SESSION_VOUCHER_DOMAIN,
  types:  SESSION_VOUCHER_TYPES,
  primaryType: "SessionVoucher",
  message: voucherTemplate,
});
```

The signature signs the **template** with sequence/amount/score set to 0. The agent's runtime later increments these fields and re-signs **on the client's behalf** using a delegated authorization — OR — the client signs each batch settlement explicitly via a Telegram push when triggered by the agent.

> **Design choice required:** delegated-signing (agent has limited authorization) vs explicit-confirm (client clicks each batch). See Open Questions.

---

## Agent Runtime — Per-tick Flow

```typescript
// Project/agent-runtime/src/services/sessionVoucher.js (NEW)

class SessionVoucherService {
  constructor({ subId, sub, signer, alignmentOracle }) {
    this.subId = subId;
    this.sub   = sub;
    this.lastSeq = sub.lastVoucherSeq;
    this.lastAmount = sub.totalDrainedViaVoucher;
    this.signer = signer;             // agent wallet
    this.oracle = alignmentOracle;    // POST /api/oracle/sign-alignment
  }

  async settleBatch({ ticksCount, outputHash, alignmentScore }) {
    const newSeq    = this.lastSeq + ticksCount;
    const newAmount = this.lastAmount + (this.sub.checkInRate * BigInt(ticksCount));

    const voucher = {
      subId:          this.subId,
      sequence:       newSeq,
      settledAt:      Math.floor(Date.now() / 1000),
      amount:         newAmount,
      alignmentScore,
      outputHash,
    };

    // 1. Fetch client signature (delegated or live)
    const clientSig = await this.fetchClientSignature(voucher);

    // 2. Fetch alignment node signature
    const alignmentSig = await this.oracle.signSessionVoucher(voucher);

    // 3. Submit on-chain
    const tx = await this.contract.submitSessionVoucher(voucher, clientSig, alignmentSig);
    await tx.wait();

    this.lastSeq = newSeq;
    this.lastAmount = newAmount;
  }
}
```

### Settlement cadence

Configurable per subscription:
- Daily (default for hourly ticks → 24× per-day batch)
- Hourly (for sub-minute ticks)
- On-demand (agent batches whenever revenue exceeds gas cost × N)

---

## Migration Path from Current x402 Stub

| Step | Action |
|---|---|
| 1 | Deploy new SubscriptionEscrow contract version (V2) with `submitSessionVoucher` |
| 2 | Frontend `create-subscription` page: rename the "x402 Protocol" toggle to "Session Voucher (replay-proof)". Keep the same UI affordances (Agent-Side vs On-Chain verification modes map to voucher delegation vs explicit confirm) |
| 3 | Frontend signs EIP-712 template on `createSubscription` submission |
| 4 | Agent runtime emits `submitSessionVoucher` instead of `drainPerCheckIn` for x402-enabled subs |
| 5 | Keep `drainPerCheckIn` for backwards compat with existing subs and as fallback when no client signature is available |
| 6 | Update docs `Project/docs/contracts/SubscriptionEscrow.md` to mark session voucher as the recommended path |
| 7 | Decommission `x402Enabled` / `x402VerificationMode` storage in V3 (long-term cleanup) |

---

## Trust & Threat Model

### What client trusts

- Agent will not over-bill (amount + sequence monotonicity makes over-billing visible to alignment oracle)
- Alignment oracle will correctly score outputs

### What agent trusts

- Client did sign the voucher template (verified on-chain at every submission)
- Client did fund the balance up-front

### Replay protection

- `sequence` monotonic → old vouchers obsolete
- `amount` monotonic → cumulative; over-claiming reverts on `delta > balance`
- `subId` in typed data → cross-subscription replay blocked
- `chainId` in domain → cross-chain replay blocked

### What's still vulnerable

- **Alignment oracle compromise** — if `alignmentNodeVerifier` key is leaked, attacker can sign bogus high scores and drain. Mitigation: multi-signer threshold (roadmap), key rotation, on-chain alignment node reputation.
- **Client signature compromise** — if client's wallet is compromised, attacker can pre-sign max-amount voucher. Mitigation: per-subscription bounded amount in signature, short `settledAt` validity window (e.g., 24 hours from signature).

---

## Comparison Table (zer0Gig native vs x402 vs OKX session voucher)

| Aspect | `drainPerCheckIn` (current) | x402 stub (current) | `submitSessionVoucher` (proposed) |
|---|---|---|---|
| Replay protection | Time window | Nonce + window | ✅ Monotonic sequence |
| Per-tick on-chain TX | Yes (gas-heavy) | N/A (not impl) | ✅ Batchable |
| Alignment gate | No (drain is time-only) | N/A | ✅ Mandatory ≥ 8000 bps |
| Client consent per drain | Implicit (deposit) | Per-call sig | ✅ Per-batch sig (or delegated template) |
| Refund | Via `cancelSubscription` | N/A | Same (balance refund) |
| Compatible with ERC-8183 | ✅ | ⚠️ | ✅ |
| Cost per 30 daily ticks | 30 TX | N/A | ✅ 1-30 TX (configurable) |

---

## Open Questions

1. **Delegated signing vs explicit confirm** — does the client sign once-upfront and the agent batches via delegated authorization (better UX, higher trust), or does the client confirm each batch via Telegram push (lower trust, more friction)?
2. **Voucher validity window** — should signatures expire after N hours from `settledAt`? Tradeoff: shorter window → more re-signs but better security; longer → smoother UX.
3. **OKX APP `escrow` intent** — do we also adopt the dispute-window escrow intent for **ProgressiveEscrow**, or keep ERC-8183 alignment-gated release as the canonical path? Recommendation: keep ERC-8183, mention APP compatibility for cross-protocol bridges only.
4. **Compute-marketplace integration** — when OKX APP marketplace lists a zer0Gig agent, do we emit standardized intent payloads so APP-aware clients can discover and hire? Probably yes — write a Broker adapter post-demo.
5. **Audit timing** — submitSessionVoucher introduces ECDSA verification + monotonic state; recommend re-audit before mainnet.

---

## Rollout Plan

### Pre-demo (now → 2026-05-18)

- ✅ This design doc committed
- ✅ x402 frontend toggle relabeled "Preview"
- ✅ Empty `0x` sig instead of fake `0x00`
- ❌ No contract changes yet — too risky for demo day

### Post-demo (2026-05-19 → 2026-06-02)

- Write Solidity prototype in a branch
- Foundry/Hardhat unit tests for voucher monotonicity + alignment gate
- Frontend EIP-712 signing implementation
- Agent runtime `sessionVoucher.js` service

### Mainnet prep (2026-06 → 2026-08)

- External audit (Sigma Prime, Hexens, or comparable tier-1)
- Migration script from V1 SubscriptionEscrow to V2
- Public bug bounty round
- Documentation + SDK example for third-party integrators

---

## References

- [OKX Agent Payments Protocol — Official](https://www.okx.com/en-us/learn/agent-payments-protocol)
- [OKX APP Whitepaper V1.0 PDF](https://web3.okx.com/whitepaper/okx-app-whitepaper.pdf)
- [Stripe Machine Payments Protocol](https://stripe.com/about) (grammar reference for EVM payment methods)
- [EIP-712: Typed structured data hashing and signing](https://eips.ethereum.org/EIPS/eip-712)
- [ERC-8183: Agentic Commerce Protocol](https://eips.ethereum.org/EIPS/eip-8183)
- `Project/docs/contracts/SubscriptionEscrow.md` — current state
- `importantInformation-zer0Gig/zer0Gig_Complete_Brief.md` — full project context
