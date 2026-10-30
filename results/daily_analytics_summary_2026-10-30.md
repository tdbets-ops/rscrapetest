# Daily Analytics — 2026-10-30

## Headline: zero-scan day

**No Phase A scan ran at any firing today.** `get_portfolio` buying_power was **$36.13** at both the 9:30am and 10:30am ET firings (flat across both — no fills in between), below the Step 0 $50 gate, so Phase A (scan) and Phase B (buy) were skipped entirely at both buy firings. No `under120_down5_*`, `over120_under500_down5_*`, `trades_*`, or `skipped_candidates_*` files exist for 2026-10-30. **No 11:30am or 3:30pm ET firings are recorded in git history today either** — only the 9:30 (14:20 UTC) and 10:30 (15:14 UTC) cloud firings ran, both position-mgmt only.

Consequently:
- **D1–D5 (per-symbol simulation):** `total_candidates_seen = 0`, `simulated_count = 0`. `results/daily_analytics_2026-10-30.csv` is header-only. No win-rate, time-bucket, decline-bucket, or per-firing in-bracket breakdown is possible today.
- **D6b (guardrail saturation check):** not applicable — both buy firings never reached Phase B (blocked at Step 0 before any bracket candidate could be evaluated), so there is no per-guardrail skip-count data to check for saturation. **No malfunction suspected** — straightforward capital gate.

## Exit-rule ledger (D6a) — none from this cloud routine; local-loop activity noted

This routine's own `position_mgmt_20261030T141857Z.csv` / `...T151409Z.csv` (9 positions each, both at the 9:30/10:30 cloud firings) show **no step 16a3 firing** — every position logged `no_change_target_confirmed` at both checks.

Between the 10:30 cloud firing and now, **7 sells filled** (all `type=limit`, matching their resting target price almost exactly — ordinary decay/trailing take-profit fills from the **local every-10-minute Phase C loop**, not a cloud-side 16a2/16a3 liquidation, since every fill was a limit order at/above cost, never a market order): CATX (1.1216 vs 1.12 target), BSBR (4.4001 vs 4.40), VINP (8.6921 vs 8.66 — filled slightly above the posted limit), UGP (5.8001 vs 5.80), SBS (4.4929 vs 4.49), PAGS (7.9216 vs 7.92), plus SID (4 sh @ 0.7503, filled 9:38am ET — a limit sell that predates and is unrelated to today's cloud firings; SID never appeared in this routine's position-mgmt logs because it had already closed before the first 9:30 check). **3 positions remain open**: KPRX, HLN, AZTR, each with a fresh resting GTC limit sell placed at 19:32 UTC.

Account-wide realized P&L today (`get_realized_pnl`, 2026-10-30 bucket): **-$5.74 on 7 closing trades** — the small per-share decay-target gains on CATX/BSBR/VINP/UGP/SBS/PAGS were outweighed by a larger loss on SID, whose cost basis this routine cannot reconstruct (it was never a tracked open position in today's logs).

## EOD liquidation suggestions (D7)

No buys were filled today (confirmed via `get_equity_orders`, 0 buy orders since 00:00 UTC — both buy firings were gated at Step 0), so `results/eod_liquidation_suggestions_2026-10-30.csv` is header-only — advisory-only, nothing to flag.

## Cohort tracking (D8)

No new candidates to log today (no scan ran) — `decliner_cohort_log.csv` is unchanged at 18,493 rows (2,525 distinct symbols). Recovery tracking (D8c–e) still ran independently of today's scan: took the 150 most-recently-added distinct symbols (of 2,525 total, capped per spec, spanning first-seen dates 2026-10-09 through 2026-10-29), quoted them, and appended 148 rows to `decliner_recovery_tracking.csv`. **2 symbols skipped**: `ATII` and `IAE` returned no quote in their batch calls (consistent with their prior-flagged inactive/delisted status).

## Guardrail paper-trade ledger (D10)

No new paper positions opened today — no `skipped_candidates_*` files exist for 2026-10-30 (Phase B never ran). D10 instead carried forward the 10 pre-existing open paper positions using daily bars. **Note:** today's official daily bar had not yet posted at the 4:30pm ET firing (the `get_equity_historicals interval=day` call returned only through 2026-10-29, and `get_equity_quotes`'s settled-close field also still showed 2026-10-29), so today's OHLC for all 10 symbols was synthesized from non-interpolated 5-minute bars across the regular session instead.

**4 closed today:**

| Symbol | Entry (guardrail, date) | Exit | Reason | ROI |
|---|---|---|---|---|
| NIVF | 1.26 (a2_atr, 10/26) | 0.96 | paper_time_stop_4_sessions | -23.81% |
| CIRC | 0.2591 (a5_compliance, 10/26) | 0.301 | paper_target | +16.17% |
| SRTM | 0.19 (a1_spread, 10/29) | 0.196 | paper_target | +3.16% |
| NEXR | 0.3505 (a6_reverse_split_proxy, 10/29) | 0.262875 | paper_drawdown_stop_25pct | -25.00% |

**6 still open:** MEGL, RETO, TWI, HDSN, QUAD, FPI (all entered 10/29, now at sessions_held=1).

### Lifetime per-guardrail results (closed paper trades, all dates, normalized to guardrail a1–a8)

| Guardrail | n closed | Win rate | Mean ROI | Median ROI | Sum ROI | n open |
|---|---|---|---|---|---|---|
| a2 (ATR) | 136 | 61.8% | +107.19% | +4.31% | +14,577.9% | 1 |
| a3 (prior-spike) | 277 | 72.9% | +38.46% | +5.00% | +10,652.4% | 0 |
| a5 (compliance) | 213 | 72.3% | +26.42% | +4.50% | +5,627.9% | 0 |
| a1 (spread) | 302 | 81.8% | +17.89% | +5.06% | +5,403.8% | 1 |
| a8 (leveraged/inverse ETF) | 87 | 64.4% | +2.51% | +4.08% | +218.3% | 0 |
| a7 (thin liquidity / stale) | 72 | 68.1% | -0.33% | +4.09% | -23.7% | 0 |
| **a6 (reverse-split proxy)** | 253 | 66.0% | **-0.33%** | +5.00% | **-82.3%** | 0 |
| a4 (earnings recency) | 37 | 54.1% | -0.56% | +3.00% | -20.7% | 4 |

(1 additional closed row, BGDE, carries a legacy non-guardrail skip_reason `trading_halted` and is excluded from the table above — it closed +5.56% via `paper_target`.)

**a6, under active review since 2026-08-25, remains the only guardrail whose paper book is net negative** alongside a7 (sum ROI -82.3% across 253 closed trades, mean -0.33%, win rate 66.0%). Today's single a6 resolution (NEXR, -25.00% drawdown stop) pushed the sum further negative, from -57.3% to -82.3% — continuing the pattern that has kept this guardrail's book negative since the review began. 253 closed trades with 0 still open remains a reasonably large, now-fully-resolved sample, and continues to **NOT support loosening the 15x threshold**.

**Baseline (account's actual realized results, 2026-08-25 to 2026-10-30, same window as the paper ledger):** `get_realized_pnl` reports **total realized P&L -$32.41 (-2.76% aggregate return)** over the window — both figures worse than the last snapshot, reflecting today's -$5.74 bucket. Every guardrail's paper book except a6, a7, and a4 continues to beat this baseline on win rate and mean ROI by a wide margin — reinforcing that a1/a2/a3/a5/a8 remain correctly tuned, while a6's (and now a7's) blocked population remains roughly in line with (or worse than) what the account actually achieved.

**Caveats (apply to all of the above):**
1. Guardrails short-circuit in a1→a8 order — a paper result for guardrail aX measures aX's *marginal* cost given a1..a(X-1) already passed. `fundamentals_guardrails_failed` records every one of a5/a6/a8 a candidate would also have failed; a7 is never recomputed (needs an extra historicals call) so no guardrail's removal is a clean "would have won" claim.
2. Paper entries fill at the observed price with zero spread/slippage; real buys are market orders, so every paper result is optimistic relative to a real fill — most optimistic for exactly the thin, low-priced names these guardrails target.
3. Target exits assume a limit fills whenever a bar's high touches it, matching both the live resting GTC order and the D3 simulation, but it is still an assumption.

## Scope note

Per the 2026-07-29 scope fix, the per-symbol simulation (D3/D3a/D3b) only ever covers `in_bracket`/`shallow_outside`/`deep_outside` symbols; today that population was empty because no scan ran.
