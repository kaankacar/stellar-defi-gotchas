# TL;DR — Biggest Issues Building on Stellar DeFi

> Compiled from 60 runs, 400+ gotchas, 150+ hours of developer time catalogued.
> Full detail lives in the three main reports. This is the "read this first" version.

---

## The #1 Issue Across Everything: Testnet Asset Fragmentation

Every protocol on Stellar testnet minted its own version of USDC. The USDC you get from swapping on the DEX is a **different asset** than the USDC Blend's pool accepts. They're both called "USDC" but they have different issuers, different trustlines, and they don't talk to each other. On mainnet this is fine — everyone uses Circle's real USDC. On testnet, you will hit this wall and lose 90+ minutes figuring out why your deposit keeps failing.

**The fix until the ecosystem sorts this out:** build a mock/demo mode for testnet flows, and document the exact issuer addresses for each protocol you're integrating.

---

## Etherfuse: 3 Things That Will Get You

**1. The ID Binding Trap (60+ min wasted)**
`customer_id` and `bank_account_id` must be generated exactly once, stored permanently, and reused forever. If your app generates new IDs per request, you'll get "Bank account not found" with no helpful error. Etherfuse doesn't generate these — you do, and the binding is permanent from first use.

**2. No "Bearer" in the Auth Header (30+ min wasted)**
The header is `Authorization: <api-key>`, NOT `Authorization: Bearer <api-key>`. Every developer adds Bearer by instinct because that's the standard. It's not documented. You'll get 401s until you figure this out.

**3. Response Fields Are Inconsistent (90+ min wasted)**
`orderId` vs `order_id` vs `id` — different endpoints return different field names for the same thing. The response is also sometimes wrapped (`response.onramp.orderId`) and sometimes flat. Write a normalizer that checks all three field name variants and unwrap the envelope before trusting any value.

---

## Blend: 2 Non-Negotiables

**1. Always simulate before sending (60+ min wasted)**
Soroban requires `simulateTransaction` before `sendTransaction`. This is different from classic Stellar and it's not emphasized enough in the docs. Skip it and your transactions will fail in confusing ways.

**2. Testnet USDC mismatch (see #1 above)**
Blend uses a different USDC issuer contract (`CBIELTK6...`) than what you get from swapping on the DEX (`GBBD47IF...`). This is the single most common blocker for anyone building the Etherfuse → DEX → Blend flow.

---

## DeFindex: API Quirks That Cost Time

These are small but they'll all bite you:
- **API keys are Discord-only** — no self-service portal, you have to DM the team (120+ min wasted just waiting)
- **`/vault/` not `/vaults/`** — wrong plural = 404, not documented
- **`?from=` not `?user=`** — the query param name is nonstandard
- **`{"amounts": [N]}` not `{"amount": N}`** — it's always an array, even for a single deposit
- **Returns 201, not 200** — make sure your response handler accepts the full 2xx range

---

## DEXs (Soroswap / Aqua / Phoenix): Fragmentation Problems

All three DEXs have different rate limits, different price formats, different pair orderings, and different pathfinding APIs. Phoenix will return a 403 (not a 429) when you hit its rate limit (10 req/min), which looks like an auth error. Build a normalizer layer early — don't let DEX-specific formats bleed into your app logic.

---

## Stellar Core: Two Things Worth Knowing Upfront

**Memo limits are 28 bytes, but UUIDs are 36 characters.** If you're passing order IDs as memos, you need to strip the dashes and truncate: `orderId.replace(/-/g, "").slice(0, 28)`.

**`sendTransaction` returns PENDING immediately** — it doesn't mean the transaction succeeded. You must poll for confirmation. Set a max retry limit (60 attempts over 60 seconds is a reasonable default). Without a ceiling, your polling loop will hang forever on failed transactions.

---

## The Agentic Build Finding (Bonus)

Separate from the protocol issues: if you're building any of this with AI agents, 5 parallel agents with clear interface contracts cuts build time by ~6x compared to a single agent working sequentially. Vague prompts ("build a Stellar wallet") cost 3x more time than specific ones. Pre-documenting known blockers before starting a run eliminates most integration failures entirely.

---

## Where to Go From Here

| Document | What's in it |
|---|---|
| `ULTIMATE_REPORT.md` | Wallet experiments, Runs 1–20, agent parallelism findings |
| `ULTIMATE_COMPOSABLE_DEFI_REPORT.md` | 150+ DeFi gotchas, Runs 21–40, full protocol detail |
| `ULTIMATE_REPORT_RUNS_41_60.md` | Advanced DeFi (bridges, flash loans, RWA), 200+ gotchas |
| `PROTOCOL_FEEDBACK_REPORT.md` | Protocol-team-facing feedback, recommended fixes per team |
| `TLDR_BIGGEST_ISSUES.md` | This document |
| Bri's FAQ | https://github.com/briwylde08/stellar-hackathon-faq |
