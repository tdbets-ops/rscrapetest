# Daily Analytics — 2026-10-27

## Headline: zero-scan day

**No Phase A scan ran at any firing today.** `get_portfolio` buying_power was **$14.05** at both the 9:30am and 10:30am ET firings (confirmed flat across both — no fills in between), below the Step 0 $50 gate, so Phase A (scan) and Phase B (buy) were skipped entirely at both buy firings. No `under120_down5_*`, `over120_under500_down5_*`, `trades_*`, or `skipped_candidates_*` files exist for 2026-10-27. No 11:30am or 3:30pm ET position-mgmt commits are present in git history either — only 9:30 and 10:30 firings are recorded today — so the usual four scan snapshots a normal day would produce are entirely absent.

Consequently:
- **D1–D5 (per-symbol simulation):** `total_candidates_seen = 0`, `simulated_count = 0`. `results/daily_analytics_2026-10-27.csv` is header-only. No win-rate, time-bucket, decline-bucket, or per-firing in-bracket breakdown is possible today.
- **D6b (guardrail saturation check):** not applicable in the usual sense — both buy firings never reached Phase B at all (blocked at Step 0, before any bracket candidate could be evaluated), so there is no per-guardrail skip-count data to check for saturation. This is a different failure mode than the "one guardrail blocks 100% of candidates" pattern from August: there was no candidate pool to filter in the first place. **No malfunction suspected** — it's a straightforward capital gate.

## Exit-rule ledger (D6a)

No step 16a3 (time-stop/drawdown-stop) liquidations fired today — `latest_position_mgmt.csv` logs show `no_change_decay_floor_holds` for all 13 positions at both the 9:30 and 10:30 firings, no drawdown ≤ -25% and no sessions_held ≥ 4 at a 3:30pm firing (GTBP was the watch item at sessions_held=4, flagged for the 3:30pm on-schedule check).

Separately, three positions **sold today via their normal resting take-profit limits** (not step 16a3, not advisory D7 — ordinary GTC/decaying targets being hit): **NAUT** filled 1 share at $2.74 (gtc limit, 14:07 ET), **CEPL** filled 1 share at $2.98 (gtc limit, 12:17 ET), and **GTBP** filled 1 share at $5.56 (gfd limit, 15:34 ET — this was GTBP's decay-adjusted target after the 1:30pm/2:30pm/3:30pm decay brackets stepped down from the morning's 6.15 target; presumably managed by the local intraday Phase C loop since no 11:30am/3:30pm cloud firing ran). These were ordinary wins, not flagged exits — no exit-rule ledger section is warranted.

## EOD liquidation suggestions (D7)

No buys were filled today (0 buy orders since 00:00 UTC), so `results/eod_liquidation_suggestions_2026-10-27.csv` is header-only — advisory-only, nothing to flag.

## Cohort tracking (D8)

No new candidates to log today (no scan ran) — `decliner_cohort_log.csv` is unchanged at 18,350 rows (2,517 distinct symbols). Recovery tracking (D8c–e) still ran independently of today's scan: took the 150 most-recently-added distinct symbols (of 2,517 total, capped per spec), quoted them, and appended 148 rows to `decliner_recovery_tracking.csv` (now 9,000 data rows). **2 symbols skipped**: `ATII` and `IAE` returned no quote in their batch call (consistent with ATII's prior-flagged inactive/delisted status; IAE now shows the same symptom).

## Guardrail paper-trade ledger (D10)

No new paper positions opened today — no `skipped_candidates_*` files exist for 2026-10-27 (Phase B never ran). D10 instead carried forward the 9 pre-existing open paper positions using daily bars (one, `ILLU`, had zero real trades today — thin/stale, carried forward unchanged per the existing a7 "no_recent_real_trades" pattern that originally flagged it).

**3 closed today:**

| Symbol | Entry | Exit | Reason | ROI |
|---|---|---|---|---|
| HRTX | 0.304 (a5, 10/22) | 0.314 | paper_target | +3.29% |
| WHLR | 0.3836 (a2, 10/23) | 0.2877 | paper_drawdown_stop_25pct | -25.00% |
| YIBO | 0.98 (a1, 10/26) | 1.08 | paper_target | +10.20% |

**6 still open:** ILLU, NIVF, CIRC, MASK, RUBI, BBDO.

### Lifetime per-guardrail results (closed paper trades, all dates, normalized to guardrail a1–a8)

| Guardrail | n closed | Win rate | Mean ROI | Median ROI | Sum ROI | n open |
|---|---|---|---|---|---|---|
| a2 (ATR) | 131 | 61.1% | +111.31% | +4.17% | +14,582.0% | 1 |
| a3 (prior-spike) | 277 | 72.9% | +38.46% | +5.00% | +10,652.4% | 0 |
| a1 (spread) | 297 | 81.5% | +18.12% | +5.06% | +5,380.4% | 0 |
| a5 (compliance) | 212 | 72.2% | +26.47% | +4.44% | +5,611.8% | 1 |
| a8 (leveraged/inverse ETF) | 86 | 64.0% | +2.48% | +3.16% | +213.2% | 0 |
| a7 (thin liquidity / stale) | 70 | 70.0% | +0.02% | +4.54% | +1.3% | 2 |
| **a6 (reverse-split proxy)** | 244 | 65.6% | **-0.24%** | +5.00% | **-58.8%** | 2 |
| a4 (earnings recency) | 35 | 51.4% | -0.82% | +2.05% | -28.8% | 0 |

**a6, under active review since 2026-08-25, is the only guardrail whose paper book is net negative** (sum ROI -58.8% across 244 closed trades, mean -0.24%) despite a majority win rate (65.6%) — a handful of large losers are dragging the mean below zero while the median stays positive. This is now a reasonably large sample (244 closed, 2 still open) and continues to NOT support loosening the 15x threshold: a6-blocked candidates as a group would have been a drag on the book, not an improvement, consistent with the original backtest rationale for a8 (leveraged/inverse ETFs often masquerade as reverse-split names) even though a6 and a8 are nominally separate signals.

**Baseline (account's actual realized results, 2026-08-25 to 2026-10-27, same window as the paper ledger):** 215 closing trades, **total realized P&L -$26.83 (-2.44% aggregate return)**. Every guardrail's paper book except a6 and a4 beat this baseline on win rate and mean ROI by a wide margin — reinforcing that a1/a2/a3/a5/a8 remain correctly tuned (the stocks they block would likely have underperformed what was actually bought), while a6's blocked population is roughly in line with (slightly worse than) what the account actually achieved.

**Caveats (apply to all of the above):**
1. Guardrails short-circuit in a1→a8 order — a paper result for guardrail aX measures aX's *marginal* cost given a1..a(X-1) already passed. `fundamentals_guardrails_failed` records every one of a5/a6/a8 a candidate would also have failed; a7 is never recomputed (needs an extra historicals call) so no guardrail's removal is a clean "would have won" claim.
2. Paper entries fill at the observed price with zero spread/slippage; real buys are market orders, so every paper result is optimistic relative to a real fill — most optimistic for exactly the thin, low-priced names these guardrails target.
3. Target exits assume a limit fills whenever a bar's high touches it, matching both the live resting GTC order and the D3 simulation, but it is still an assumption.

## Scope note

Per the 2026-07-29 scope fix, the per-symbol simulation (D3/D3a/D3b) only ever covers `in_bracket`/`shallow_outside`/`deep_outside` symbols; today that population was empty because no scan ran.
