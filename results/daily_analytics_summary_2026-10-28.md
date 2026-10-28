# Daily Analytics — 2026-10-28

## Headline: zero-scan day

**No Phase A scan ran at any firing today.** `get_portfolio` buying_power was **$47.13** at both the 9:30am and 10:30am ET firings (confirmed flat across both — no fills in between), below the Step 0 $50 gate, so Phase A (scan) and Phase B (buy) were skipped entirely at both buy firings. No `under120_down5_*`, `over120_under500_down5_*`, `trades_*`, or `skipped_candidates_*` files exist for 2026-10-28. **No 11:30am or 3:30pm ET firings are recorded in git history today either** — only 9:30 and 10:30 firings ran (position-mgmt only) — so this is a thinner day than usual even by zero-scan-day standards: the normal four scan snapshots and two additional position-mgmt cycles are entirely absent.

Consequently:
- **D1–D5 (per-symbol simulation):** `total_candidates_seen = 0`, `simulated_count = 0`. `results/daily_analytics_2026-10-28.csv` is header-only. No win-rate, time-bucket, decline-bucket, or per-firing in-bracket breakdown is possible today.
- **D6b (guardrail saturation check):** not applicable — both buy firings never reached Phase B at all (blocked at Step 0, before any bracket candidate could be evaluated), so there is no per-guardrail skip-count data to check for saturation. **No malfunction suspected** — straightforward capital gate, same situation as 2026-10-27.

## Exit-rule ledger (D6a) — inferred, not from this cloud routine's own logs

This cloud routine's own `latest_position_mgmt.csv` / `position_mgmt_2026102*.csv` logs (9:45am and 10:36am ET firings, the only two today) show **no step 16a3 firing** — all 10 positions logged `no_change_decay_floor_holds` at both checks, with no drawdown ≤ -25% and no sessions_held ≥ 4 at a 3:30pm firing observed *by this routine* (because no 3:30pm cloud firing ran today). One flag was already present in the 9:45am log: **QTEX** was noted `sessions_held=4_watch_for_3:30pm_time_stop`.

Separately, **three positions sold today, all at a loss** (not take-profit, unlike 10/27's three ordinary wins): **QTEX** (1 sh, limit 1.34, filled 1.3415, 15:34 ET, -4.11% vs. $1.399 cost), **USAS** (1 sh, market, filled 3.1637, 15:44 ET, -2.65% vs. $3.2499 cost), and **YRD** (2 sh, limit 0.4479, filled 0.4571, 13:24 ET, -28.55% vs. $0.6397 cost). None of these appear in this routine's own position-mgmt logs as an explicit liquidation — they were almost certainly executed by the **local every-10-minute Phase C loop**, since no cloud firing ran between 10:36am and now. The pattern strongly matches the documented rules even though this routine cannot confirm it from its own records:
- **QTEX and USAS** both reached **sessions_held=4** today (bought 10/22, 4th trading session) and both sold within minutes of 3:30–3:44pm ET — consistent with `time_stop_4_sessions`.
- **YRD** (bought 10/26, sessions_held=2) sold at a **-28.55% drawdown** — past the -25% threshold — mid-day (1:24pm ET), consistent with `drawdown_stop_25pct` firing at any time of day.

**Would holding have beaten exiting?** Comparing each exit to the 4:00pm ET closing print: QTEX close ≈1.355 (hold roi -3.14% vs. exit -4.11%, holding better by ~1pt); USAS close ≈3.195 (hold roi -1.69% vs. exit -2.65%, holding better by ~1pt); YRD close ≈0.4706 (hold roi -26.43% vs. exit -28.55%, holding better by ~2pts but still a large loss). In all three cases holding to the close would have been marginally better, but all three were real losers either way — consistent with the exit rules cutting losing positions, just with some give-back from the exact price captured (QTEX's limit-style fill, USAS's market fill).

## EOD liquidation suggestions (D7)

No buys were filled today (confirmed via `get_equity_orders`, 0 buy orders since 00:00 UTC), so `results/eod_liquidation_suggestions_2026-10-28.csv` is header-only — advisory-only, nothing to flag.

## Cohort tracking (D8)

No new candidates to log today (no scan ran) — `decliner_cohort_log.csv` is unchanged at 18,350 rows (2,517 distinct symbols). Recovery tracking (D8c–e) still ran independently of today's scan: took the 150 most-recently-added distinct symbols (of 2,517 total, capped per spec), quoted them, and appended 149 rows to `decliner_recovery_tracking.csv` (now 9,150 data rows). **1 symbol skipped**: `ATII` returned no quote in its batch call (consistent with its prior-flagged inactive/delisted status).

## Guardrail paper-trade ledger (D10)

No new paper positions opened today — no `skipped_candidates_*` files exist for 2026-10-28 (Phase B never ran). D10 instead carried forward the 6 pre-existing open paper positions using daily bars (today's official daily bar had not yet posted at the 4:30pm ET firing, so today's OHLC was synthesized from non-interpolated 5-minute bars for the regular session — `ILLU` had zero real trades today, thin/stale, carried forward unchanged per the existing a7 pattern).

**2 closed today:**

| Symbol | Entry | Exit | Reason | ROI |
|---|---|---|---|---|
| MASK | 0.7345 (a6, 10/26) | 0.755 | paper_target | +2.79% |
| RUBI | 0.1552 (a6, 10/26) | 0.1164 | paper_drawdown_stop_25pct | -25.00% |

**4 still open:** ILLU, NIVF, CIRC, BBDO.

### Lifetime per-guardrail results (closed paper trades, all dates, normalized to guardrail a1–a8)

| Guardrail | n closed | Win rate | Mean ROI | Median ROI | Sum ROI | n open |
|---|---|---|---|---|---|---|
| a2 (ATR) | 131 | 61.1% | +111.31% | +4.17% | +14,582.0% | 1 |
| a3 (prior-spike) | 277 | 72.9% | +38.46% | +5.00% | +10,652.4% | 0 |
| a5 (compliance) | 212 | 72.2% | +26.47% | +4.44% | +5,611.7% | 1 |
| a1 (spread) | 297 | 81.5% | +18.12% | +5.06% | +5,380.4% | 0 |
| a8 (leveraged/inverse ETF) | 86 | 64.0% | +2.48% | +3.16% | +213.2% | 0 |
| a7 (thin liquidity / stale) | 70 | 70.0% | +0.02% | +4.54% | +1.3% | 2 |
| **a6 (reverse-split proxy)** | 246 | 65.4% | **-0.33%** | +5.00% | **-81.0%** | 0 |
| a4 (earnings recency) | 35 | 51.4% | -0.82% | +2.05% | -28.8% | 0 |

(1 additional closed row, YYGH under a3, is excluded from the a3 stats above — it exited `no_data_likely_delisted` with no recoverable price, so it has no roi_pct.)

**a6, under active review since 2026-08-25, remains the only guardrail whose paper book is net negative** (sum ROI -81.0% across 246 closed trades, mean -0.33%, win rate 65.4%). Today's two a6 resolutions (MASK +2.79%, RUBI -25.00%) moved the sum from -58.8% to -81.0% — one more large loser outweighing one modest winner, the exact pattern that has kept this guardrail's book negative since the review began. This remains a reasonably large sample (246 closed, 0 still open as of today) and continues to NOT support loosening the 15x threshold.

**Baseline (account's actual realized results, 2026-08-25 to 2026-10-28, same window as the paper ledger):** ~225 closing trades, **total realized P&L -$27.58 (-2.48% aggregate return)** — both figures slightly worse than yesterday's snapshot (-$26.83, -2.44%), reflecting today's three losing exits (-$0.52 net on 3 trades, per `get_realized_pnl`). Every guardrail's paper book except a6 and a4 continues to beat this baseline on win rate and mean ROI by a wide margin — reinforcing that a1/a2/a3/a5/a8 remain correctly tuned, while a6's blocked population remains roughly in line with (or slightly worse than) what the account actually achieved.

**Caveats (apply to all of the above):**
1. Guardrails short-circuit in a1→a8 order — a paper result for guardrail aX measures aX's *marginal* cost given a1..a(X-1) already passed. `fundamentals_guardrails_failed` records every one of a5/a6/a8 a candidate would also have failed; a7 is never recomputed (needs an extra historicals call) so no guardrail's removal is a clean "would have won" claim.
2. Paper entries fill at the observed price with zero spread/slippage; real buys are market orders, so every paper result is optimistic relative to a real fill — most optimistic for exactly the thin, low-priced names these guardrails target.
3. Target exits assume a limit fills whenever a bar's high touches it, matching both the live resting GTC order and the D3 simulation, but it is still an assumption.

## Scope note

Per the 2026-07-29 scope fix, the per-symbol simulation (D3/D3a/D3b) only ever covers `in_bracket`/`shallow_outside`/`deep_outside` symbols; today that population was empty because no scan ran.
