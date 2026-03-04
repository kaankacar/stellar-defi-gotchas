# Stellar DeFi Gotchas

A comprehensive reference of 400+ gotchas, blockers, and hard-learned lessons from 60 experimental runs building on Stellar DeFi — covering self-custodial wallets, Blend, Etherfuse, DeFindex, Soroswap, Aqua, and Phoenix.

**Estimated developer time documented:** 150+ hours of pain, so you don't have to repeat it.

---

## What's in Here

| File | Description |
|------|-------------|
| `MEGA_GOTCHAS.md` | The main reference — all 400+ gotchas categorized across 60 runs |
| `TLDR_BIGGEST_ISSUES.md` | Start here — the most expensive issues at a glance |
| `MASTER_INDEX_ALL_60_RUNS.md` | Index of all 60 experiment runs and their outcomes |
| `ULTIMATE_REPORT.md` | Deep dive on runs 1–20 (Stellar wallet experiments) |
| `ULTIMATE_REPORT_RUNS_41_60.md` | Deep dive on runs 41–60 |
| `ULTIMATE_COMPOSABLE_DEFI_REPORT.md` | 20-run composable DeFi experiment (Etherfuse, Blend, DEXs) |
| `PROTOCOL_FEEDBACK_REPORT.md` | Per-protocol feedback and recommendations |

---

## Top Issues (Quick Reference)

- **Testnet asset fragmentation** — every protocol mints its own USDC with different issuers; they don't interoperate
- **Etherfuse auth** — header is `Authorization: <api-key>`, not `Authorization: Bearer <api-key>`
- **Etherfuse ID binding** — `customer_id` and `bank_account_id` must be generated once and reused forever
- **Blend trustlines** — must be established before any deposit or the transaction silently fails
- **Soroban simulation** — always simulate before submitting; fee estimation from static values will get you

Read `TLDR_BIGGEST_ISSUES.md` for the full rundown before starting any Stellar DeFi integration.

---

## Experiment Overview

| Runs | Focus | Key Finding |
|------|-------|-------------|
| 1–20 | Self-custodial Stellar wallets | 6.2x speedup with 5 parallel agents vs 1 |
| 21–40 | Composable DeFi protocols | 50+ hours of wasted dev time catalogued |
| 41–60 | Protocol integration & edge cases | 400+ total gotchas documented |

---

## Who This Is For

Developers building on Stellar — especially anyone integrating Soroban smart contracts, Blend lending, Etherfuse on/off-ramp, or Stellar DEXs. The gotchas here are real failure modes hit repeatedly across independent runs.
