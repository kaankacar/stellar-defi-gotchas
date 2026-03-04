# PROTOCOL FEEDBACK REPORT

> Extracted from `ULTIMATE_COMPOSABLE_DEFI_REPORT.md` (Runs 21–40).
> Based on 50+ hours of developer time catalogued across 150+ gotchas.
> Original reports are unchanged — this is a standalone copy for protocol teams.

---

### Etherfuse

#### Critical Issues (🔴 Blocking)

**1. Inconsistent Field Naming Between Onramp and Offramp**
- **Impact:** 90+ minutes wasted per developer
- **Issue:** Onramp uses `orderId`, offramp uses `order_id` or `id` depending on endpoint
- **Evidence:** Run 6, Gotcha 6.1
- **Recommended Fix:** Standardize on camelCase throughout API. Provide field name migration guide.

**2. Response Structure Inconsistency**
- **Impact:** 75+ minutes wasted per developer
- **Issue:** Onramp wraps response in `onramp` key, offramp in `offramp` key, sometimes flat
- **Evidence:** Run 6, Gotcha 6.2
- **Recommended Fix:** Consistent response envelope or document structure per endpoint clearly.

**3. No "Bearer" Prefix in Auth Header (Undocumented)**
- **Impact:** 30+ minutes wasted per developer, most common support request
- **Issue:** `Authorization: <api-key>` vs standard `Authorization: Bearer <token>`
- **Evidence:** Run 1, Gotcha 1.2; Run 13, Gotcha 13.1
- **Recommended Fix:** Add to docs prominently. Consider supporting both formats for compatibility.

**4. API Key Requires Manual Discord Contact**
- **Impact:** 120+ minutes wasted per developer
- **Issue:** No self-service API key generation
- **Evidence:** Run 4, Gotcha 4.1
- **Recommended Fix:** Implement automated API key portal. Discord should be for support only.

**5. ID Binding Complexity Not Clear**
- **Impact:** 60+ minutes wasted per developer
- **Issue:** customer_id + bank_account_id binding is permanent but not emphasized
- **Evidence:** Run 1, Gotcha 1.1
- **Recommended Fix:** Add prominent warning in docs. Add `/validate-ids` endpoint to check binding status.

#### API Design Issues (🟡 Friction)

**6. Indexing Delay Not Documented**
- New orders have 3-10 second indexing delay before queryable
- **Fix:** Document clearly. Add `X-Indexing-Delay-Expected: true` header.

**7. Nested Response Wrapper Inconsistent**
- Sometimes `response.onramp`, sometimes flat
- **Fix:** Consistent envelope or provide `?format=flat` query param.

**8. Sandbox Fiat Simulation Endpoints Different**
- Different flows for crypto vs fiat simulation
- **Fix:** Unified simulation endpoint or clearer documentation.

#### Documentation Gaps
- Quote expiration (2 minutes) not prominent
- Trustline requirements before receiving not mentioned
- CLABE format validation requirements missing
- Withdrawal timing (1-4 business days) not clear

---

### Blend Protocol

#### Critical Issues (🔴 Blocking)

**1. Testnet USDC Fragmentation**
- **Impact:** 90+ minutes wasted per developer
- **Issue:** Different USDC issuers on testnet (Soroswap USDC ≠ Blend USDC)
- **Evidence:** Run 3, Gotcha 3.1
- **Recommended Fix:** Use canonical testnet USDC or document issuer addresses prominently.

**2. Soroban Simulation Required But Not Emphasized**
- **Impact:** 60+ minutes wasted per developer
- **Issue:** MUST call `simulateTransaction` before `sendTransaction`
- **Evidence:** Run 3, Gotcha 3.2
- **Recommended Fix:** Add "Simulation Required" banner to all SDK docs.

**3. Health Factor Calculation Complex**
- **Impact:** Significant confusion
- **Issue:** Formula uses both collateralFactor AND liabilityFactor, easy to confuse
- **Evidence:** Run 8, multiple gotchas
- **Recommended Fix:** Provide interactive calculator in docs. Clarify formula with examples.

#### API Design Issues (🟡 Friction)

**4. Stroop Conversions**
- 7 decimals not standard (most chains use 18)
- **Fix:** Provide conversion utilities in official SDK.

**5. Interest Rate Model Three-Leg Curve**
- Complex formula not well explained
- **Fix:** Interactive visualization of rate curve.

**6. Liquidation Mechanics**
- Dutch auction not well documented
- **Fix:** Step-by-step liquidation flow diagram.

#### Documentation Gaps
- Resource limits (40 read / 25 write entries) not prominent
- Event truncation (8KB limit) not mentioned
- Re-leveraging compounding risks not explained

---

### DeFindex

#### Critical Issues (🔴 Blocking)

**1. API Key No Self-Service**
- **Impact:** 120+ minutes wasted per developer
- **Issue:** Must contact team on Discord for API key
- **Evidence:** Run 4, Gotcha 4.1
- **Recommended Fix:** Implement automated API key portal.

**2. Endpoint Singular vs Plural Inconsistency**
- **Impact:** 45+ minutes wasted per developer
- **Issue:** `/vault/` not `/vaults/` (404 if wrong)
- **Evidence:** Run 4, Gotcha 4.2
- **Recommended Fix:** Support both or redirect. Document clearly.

**3. Param Name `from` Not `user`**
- **Impact:** 30+ minutes wasted per developer
- **Issue:** `?from=<address>` not `?user=<address>`
- **Evidence:** Run 4, Gotcha 4.3
- **Recommended Fix:** Standardize on `user` or `address`. Support both during transition.

**4. Amounts Array Format**
- **Impact:** 35+ minutes wasted per developer
- **Issue:** `{"amounts": [N]}` not `{"amount": N}` - array even for single deposit
- **Evidence:** Run 4, Gotcha 4.4
- **Recommended Fix:** Accept both formats. Document array requirement clearly.

**5. HTTP 201 vs 200**
- **Impact:** 25+ minutes wasted per developer
- **Issue:** Returns 201 Created, not 200 OK
- **Evidence:** Run 4, Gotcha 4.5
- **Recommended Fix:** Document status codes or accept both.

#### API Design Issues (🟡 Friction)

**6. XLM Requires SAC Wrapping**
- Extra contract call needed for native XLM
- **Fix:** Provide helper function or auto-wrap in SDK.

**7. TVL Location Non-Intuitive**
- TVL at `totalManagedFunds[0].total_amount` not `tvl`
- **Fix:** Add `tvl` convenience field or document path clearly.

#### Documentation Gaps
- API key acquisition process not documented
- Rate limits not specified
- Webhook retry policies not documented

---

### DEXs (Soroswap, Aqua, Phoenix)

#### Critical Issues (🔴 Blocking)

**1. Different Price Formats Across DEXs**
- **Impact:** 40+ minutes wasted per developer
- **Issue:** Each DEX returns prices in different formats
- **Evidence:** Run 7, Gotcha 7.1
- **Recommended Fix:** Standardize on single format or provide normalization utilities.

**2. Severe Rate Limiting (Phoenix)**
- **Impact:** 50+ minutes wasted per developer
- **Issue:** Phoenix returns 403 when rate limited (10 req/min)
- **Evidence:** Run 7, Gotcha 7.3
- **Recommended Fix:** Return 429 with Retry-After header. Document limits clearly.

**3. Inconsistent Pair Ordering**
- **Impact:** 30+ minutes wasted per developer
- **Issue:** CETES/USDC vs USDC/CETES - different DEXs order differently
- **Evidence:** Run 7, Gotcha 7.2
- **Recommended Fix:** Standardize on base/quote convention.

**4. Pathfinding API Incompatibility**
- **Impact:** 60+ minutes wasted per developer
- **Issue:** Different pathfinding endpoints and response formats
- **Evidence:** Run 7, Gotcha 7.5
- **Recommended Fix:** Standard pathfinding API across all DEXs.

#### API Design Issues (🟡 Friction)

**5. Slippage Calculation Differences**
- Each DEX calculates differently
- **Fix:** Standardize or provide conversion utilities.

**6. Fee Structures Vary**
- Aqua: 0.05%, Phoenix: 0.15%, Soroswap: 0.3%
- **Fix:** Fee registry or standardize.

#### Documentation Gaps
- Rate limits not prominently documented
- Price staleness guarantees not specified
- Liquidity depth endpoints missing

---

### Stellar Core (Horizon/Soroban RPC)

#### Critical Issues (🔴 Blocking)

**1. Testnet Asset Fragmentation**
- **Impact:** 90+ minutes wasted per developer
- **Issue:** No canonical testnet tokens. Each protocol mints own.
- **Evidence:** Run 3, Gotcha 3.1; FAQ from briwylde08
- **Recommended Fix:** Create official testnet asset registry or canonical tokens.

**2. Horizon Rate Limiting Not Clear**
- **Impact:** 20+ minutes wasted per developer
- **Issue:** 3,600 req/hour limit not prominent
- **Evidence:** Run 2, Gotcha 2.9
- **Recommended Fix:** Add to docs prominently. Return rate limit headers.

**3. Soroban Resource Limits Complex**
- **Impact:** 45+ minutes wasted per developer
- **Issue:** 40 read / 25 write entries, 100M instructions
- **Evidence:** Run 10, Gotcha 10.3
- **Recommended Fix:** Interactive resource calculator in docs.

#### Testnet Limitations (🟡 Major Friction)

**4. Testnet Resets**
- Contracts and data lost on resets
- **Impact:** Significant developer friction
- **Fix:** Better communication of reset schedule. Preserve critical contracts.

**5. No MEV Protection**
- Transactions can be front-run
- **Impact:** Arbitrage and other strategies difficult
- **Fix:** Consider MEV protection mechanisms.

#### Documentation Gaps
- Simulation vs reality differences not well explained
- Event ordering guarantees not specified
- Transaction replacement mechanics not clear

---

## CROSS-PROTOCOL ISSUES

### 1. Asset Fragmentation (Testnet)
**Severity:** 🔴 Critical
**Time Wasted:** 90+ minutes per developer
**Description:** Each protocol has own USDC/CETES on testnet. No interoperability.
**Recommendation:** Create canonical testnet assets or asset registry.

### 2. Auth Standardization
**Severity:** 🔴 Critical
**Time Wasted:** 40+ minutes per developer
**Description:** Etherfuse: no Bearer, DeFindex: Bearer, Soroban RPC: varies
**Recommendation:** Standardize on OAuth 2.0 or API key with consistent format.

### 3. Ecosystem Gaps
**Severity:** 🟡 High
**Description:**
- No unified DEX aggregator API
- No standard pathfinding across DEXs
- No canonical testnet asset registry
- No unified event indexing (Mercury good but protocol-specific)

**Recommendation:**
- Create Stellar DeFi standards working group
- Develop unified APIs for common operations
- Establish canonical testnet assets

---

## SUMMARY STATISTICS

### By Severity
- 🔴 Critical: 42 gotchas
- 🟡 High: 56 gotchas
- 🟢 Medium: 52 gotchas

### By Category
1. **API Design:** 35 gotchas
2. **Authentication:** 15 gotchas
3. **Asset Management:** 18 gotchas
4. **Error Handling:** 22 gotchas
5. **Transaction Flow:** 28 gotchas
6. **Testing/Mocks:** 12 gotchas
7. **State Management:** 10 gotchas
8. **Security:** 20+ gotchas
9. **Monitoring:** 8 gotchas
10. **Documentation:** 12 gotchas

### Total Developer Time Wasted
**50+ hours** documented across all runs

### With Parallel Agents (5 agents)
**Estimated time with single agent:** 200+ hours
**Actual time with 5 parallel agents:** 50+ hours
**Speedup:** 4x
