# Daily Analytics — 2026-09-24

**4:30pm ET firing.** Prior firing note: local git history was on a detached HEAD from the 9:30am firing onward (5 commits, never merged into `master`); reattached and fast-forwarded `master` to include today's work before this run.

## Headline

- **574** candidates seen across today's 4 firings (9:30/10:30/11:30/3:30), **391 simulated** (scope: `in_bracket` + `shallow_outside` + `deep_outside` decline buckets only, per the 2026-07-28 scope fix — `other_price_or_range`, including the entire $120-$500 list, is skipped).
- **Overall win rate (simulated): 52.9%** (n=391)

## Win rate breakdowns

| Split | Win rate | n |
|---|---|---|
| Morning (9:30/10:30/11:30) | 56.6% | 309 |
| Afternoon (3:30) | 39.0% | 82 |
| Price <$1 | 56.8% | 125 |
| Price $1-3 | 54.7% | 137 |
| Price $3-10 | 47.3% | 129 |
| decline_bucket: in_bracket | 60.0% | 55 |
| decline_bucket: shallow_outside | 52.0% | 333 |
| decline_bucket: deep_outside | 33.3% | 3 |

**in_experiment_bracket=true (the live buy bracket), by firing (n=55 total):**

| Firing | Win rate | n |
|---|---|---|
| 9:30 (buys live) | 65.6% | 32 |
| 10:30 (buys live) | 33.3% | 6 |
| 11:30 (no buys) | 42.9% | 7 |
| 3:30 (no buys) | 70.0% | 10 |

Small samples throughout, especially 10:30 (n=6) and 11:30 (n=7) — not enough to draw a conclusion on whether 11:30 should become a third buy window, though today's 11:30 in-bracket win rate (42.9%) trailed both live buy windows' historical averages and the 3:30 (non-buy) sample outperformed both live windows. One day is noise; the per-firing table exists precisely to accumulate evidence over time.

## Scope note

price_bucket only has meaningful data for <1/1-3/3-10 since 10-120 and 120-500 rows are all classified `other_price_or_range` (out of D3 scope) — source_list breakdown for simulated rows is 100% under120 (0 simulated 120-500 rows) for the same reason.

## Exit-rule ledger (step 16a3)

No time-stop or drawdown-stop (step 16a3) liquidations fired at any of today's cloud firings. (Two step-16a2 60-minute auto-liquidations did fire — ACON and EO, both bought 9:30, liquidated ~10:30 — but those are 16a2, not 16a3, and are out of this section's scope.)

One likely-16a3-equivalent event was observed via order history but did **not** originate from this cloud session's Phase C: **NUWE**, held since 2026-09-18, reached sessions_held=4 as of the 11:30 firing (dd=-11.8%) and was sold via a MARKET order at 19:31:17 UTC (~3:31pm ET) — consistent with the local every-10-minute loop's own time_stop_4_sessions implementation, not this session's step 16. Buy avg 0.9299 × 5 sh; sold 5 sh @ 0.804 (market) ≈ **-13.5%** realized (-$0.63). Close ~$0.81 at day end — holding would not have recovered the loss.

## Guardrail saturation check (D6b)

Both buy firings (9:30, 10:30) placed trades today (4 and 2 respectively) — no zero-buy firing, so the saturation diagnostic doesn't apply.

## EOD liquidation suggestions (D7, advisory only)

All 6 symbols bought today (RAVE, TAOP, ACON, EO at 9:30; TRT, GP at 10:30) already closed out before this firing — RAVE/TAOP hit their profit target, ACON/EO/TRT/GP were auto-liquidated (a2, 60-min-never-touched-breakeven). None remain open, so `eod_liquidation_suggestions_20260924.csv` is header-only.

## Cohort tracking (D8)

- 398 candidates in today's 5-30%-decline/<$10 tracking band appended to `decliner_cohort_log.csv` (9 brand-new symbols got a fundamentals fetch for sector/industry; the rest copied from prior log entries).
- Cohort log now holds **2,270 distinct symbols** all-time.
- Recovery tracking cycle covered the 150 most-recently-added distinct symbols (capped per spec); 145 quoted successfully, 5 skipped (WLFI has_traded=false; NHIC/UNI/ONDO/SYRUP not_found — likely delisted).

## Guardrail paper-trade ledger (D10)

**Today:** 37 new paper positions opened (from 56 skipped-candidate rows across both buy firings, deduped to 39 distinct symbols, minus 2 already open in the ledger from a prior day — CTNT was excluded for this reason). 23 closed same-day (all `paper_target`), 14 carried open. Of the 28 pre-existing open positions: 6 closed today (2 `paper_target`, 2 `paper_drawdown_stop_25pct`, 2 `paper_time_stop_4_sessions`), 21 carried forward, 1 (YYGH) skipped — not_found/likely delisted, left unchanged and flagged as a data gap.

**Closed-trade stats by guardrail (all-time, grouped by base rule a1-a8; naming has drifted over the routine's history so variants like `a1_spread`/`a1_spread_pct`/`a1_spread_guardrail` are merged):**

| Guardrail | n | Win rate | Mean ROI | Median ROI | Summed ROI | Still open |
|---|---|---|---|---|---|---|
| a1 (spread) | 118 | 83.1% | 35.46% | 5.05% | 4184.5% | 2 |
| a2 (ATR) | 52 | 59.6% | 78.31% | 4.06% | 4071.9% | 3 |
| a3 (prior spike) | 135 | 82.2% | 73.51% | 5.01% | 9923.6% | 8 |
| a4 (earnings recency) | 22 | 50.0% | -0.14% | 3.00% | -3.1% | 2 |
| a5 (compliance) | 84 | 76.2% | 58.74% | 5.00% | 4934.3% | 2 |
| **a6 (reverse-split proxy)** | **86** | **69.8%** | **2.58%** | **5.00%** | **222.3%** | **8** |
| a7 (thin liquidity) | 28 | 67.9% | 0.00% | 5.00% | 0.1% | 6 |
| a8 (leveraged/inverse ETF) | 43 | 79.1% | 8.04% | 5.00% | 345.8% | 2 |

**a6 specifically** (under active review since 2026-08-25): win rate 69.8% on 86 closed paper trades, comfortably above the account's actual realized win rate (below), with a modest mean ROI once outliers are discounted (median 5.00% — most closes are simply hitting the standard +5% target). This is the largest sample a6 has had yet; still recommend leaving the 15x threshold untouched until the account owner reviews.

**Data-quality note:** several guardrails' *mean* ROI is wildly higher than their *median* (e.g. a3: 73.51% mean vs 5.01% median; a2: 78.31% mean vs 4.06% median) — this pattern (a few closed trades with 3- and 4-digit percentage returns) mirrors the previously-flagged MGN 30x price-discontinuity artifact and likely reflects bad prints or corporate-action noise in a handful of underlying bars, not real returns. Treat the median as the more trustworthy summary statistic per guardrail; the mean/summed columns are included for completeness but should not be read as "this guardrail made the account X%."

**Baseline (account's actual realized results, filled buys only):**
- Last 200 realized closing trades (2026-07-27 through 2026-09-24): **54.5% win rate**, **-$82.50** total realized.
- Full trailing 3-month window (since ~2026-06-25): **-$141.44** total realized, **-3.63%** rate of return.

Every guardrail's closed-trade win rate above (49.7-83.1%) is comparable to or above the account's actual 54.5% — but per caveat (2) below, paper fills are optimistic relative to real market fills, so this is not by itself a case for loosening any guardrail.

**Mandatory caveats (per spec, always stated):**
1. Guardrails short-circuit in a1→a8 order; skip_reason is only a candidate's *first* failure, so paper results per guardrail measure its *marginal* cost given earlier guardrails already passed. A paper winner blocked by a6 might also have failed a7 or a8 had a6 not existed — cross-check `fundamentals_guardrails_failed`. a7 is never recomputed for paper trades (needs an extra historicals call per candidate), so "we would have won this" is never a clean claim for any name that would also have hit a7.
2. Paper entries fill at the observed price with no spread paid and no slippage; every paper result is optimistic relative to a real MARKET-order fill, most of all for the wide-spread, thin, low-priced names these guardrails target.
3. Target exits assume a limit fills whenever a bar's high touches it — matches how the real resting GTC limit behaves and how D3 already simulates, but it's still an assumption.

---

*Phase D is read-only analysis except for the files this commit includes. No orders were placed, cancelled, or modified.*
