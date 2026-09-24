# Daily Analytics — 2026-09-23

**Operational note (read first):** only the 9:30am ET buy window ran today. The 10:30am firing did not occur at all (no scan/trade files exist for it — under120 files exist only for 13:41Z, 15:41Z and 19:40Z, corresponding to 9:30/11:30/3:30). This is a gap in the schedule, not a guardrail or market effect — the second buy window contributed zero data today. 1 real trade was placed (INNV, 9:30 firing).

## Headline

- **785** unique candidates seen across all firings (under-$120 list only today; the $120-500 list had matches at every firing but none fall in scope for D3/D8, see below).
- **502** simulated (in_bracket + shallow_outside + deep_outside population); **271** skipped as `other_price_or_range` (scope fix from 2026-07-29) and **12** further excluded as `no_data` (illiquid/halted, zero real trades in their simulation window).
- **Overall simulated win rate: 28.7%** (n=502) — dragged down by the very large shallow_outside population (see below).
- **In-bracket (the live buy filter, 10-25% decline / <$10) win rate: 62.2%** (n=45).

## Breakdowns (simulated rows only)

| Cut | Win rate | n |
|---|---|---|
| Overall | 28.7% | 502 |
| Morning (9:30/10:30/11:30) | 32.8% | 332 |
| Afternoon (3:30) | 20.6% | 170 |
| Price <1 | 42.7% | 110 |
| Price 1-3 | 32.4% | 179 |
| Price 3-10 | 18.3% | 213 |
| decline_bucket: in_bracket | **62.2%** | 45 |
| decline_bucket: shallow_outside (5-10%) | 25.1% | 455 |
| decline_bucket: deep_outside (25-30%) | 100.0% | 2 (small sample) |

**In-bracket by firing** (the direct evidence for whether 11:30 should also become a buy window):

| Firing | Win rate | n |
|---|---|---|
| 9:30 (live buy) | 65.0% | 20 |
| 10:30 (live buy) | — | 0 (firing did not run today) |
| 11:30 (not a buy window) | 60.0% | 15 |
| 3:30 (not a buy window) | 60.0% | 10 |

11:30's in-bracket win rate (60.0%, n=15) is close to 9:30's (65.0%, n=20) and comfortably ahead of the overall shallow_outside population — consistent with prior days' pattern that all three morning-cluster firings perform similarly. Sample size is still small; no schedule change is being made from this data alone.

The `shallow_outside` bucket (5-10% decline, just below the live bracket's 10% floor) is over 10x larger than `in_bracket` today and performs far worse (25.1% vs 62.2%), reinforcing that the 10% floor is doing real work, not just narrowing volume.

## Cross-reference with real trades/skipped candidates

Only one buy firing (9:30) ran today: 1 real trade (INNV, bracket price=9.5799, decline=10.46%) and 19 bracket-eligible candidates were skipped by guardrails (see `skipped_candidates_20260923T1341Z.csv`). No 10:30 firing occurred, so there is only one set of these files for today (contrary to the routine's usual expectation of up to two).

## Exit-rule firings (D6a)

None today — no step 16a3 (drawdown-stop/time-stop) liquidations fired on any open position.

## Guardrail saturation check (D6b)

Not applicable today in the strict sense (the one buy firing that ran, 9:30, placed 1 trade, so there was no zero-buy firing to diagnose). For completeness, guardrail hits at the 9:30 firing among the 19 skipped candidates: a1 (spread) 3, a2 (ATR) 1, a3 (prior-spike) 7, a5 (compliance) 1, a6 (reverse-split proxy) 3, a7 (thin-liquidity) 2, a8 (leveraged ETF) 1 — no single guardrail accounted for 100% of skips.

## Guardrail paper-trade ledger (D10)

19 new paper positions opened today from the 9:30 firing's skipped candidates (no 10:30 skips to open). 12 of those closed same-day, all winners (`paper_target`, 2.1%-5.4% ROI); 7 remain open. Separately, 8 of the 26 pre-existing open paper positions were updated from daily bars: 4 exited today (NXXT +3.9% target, DCX -25.0% drawdown-stop, IPW +2.3% target, SNDQ +4.9% target), 4 remain open with updated peak/drawdown stats (TCRT, BTCZ, PECE, DAMD). 17 pre-existing positions had no real trade today (interpolated-only bars) and were left unchanged; YYGH remains flagged inactive/delisted for manual review (its historicals call errors with `inactive_instruments`).

By skip_reason (closed trades only, cumulative since 2026-08-25 ledger inception — **caveats**: (1) skip_reason is only a candidate's first guardrail failure in a1→a8 order, so this measures each guardrail's marginal cost given earlier guardrails already passed, and a7 is never cross-checked so a "would have won" claim is never clean; (2) paper entries fill at the observed price with no slippage, which is optimistic relative to a real market-order fill, most of all for the thin/low-priced names these guardrails target; (3) target exits assume a limit fills whenever a bar's high touches it, matching the live GTC behavior but still an assumption; (4) mean ROI is heavily skewed by at least one outlier — OMH (a2, entry 2026-08-27) shows +4297% roi_pct on this ledger, almost certainly a data anomaly (bad print, corporate action, or an earlier-session computation error) rather than a real 43x paper gain, so **median is the more trustworthy summary stat** here):

| skip_reason | n | win rate | mean ROI | median ROI | sum ROI |
|---|---|---|---|---|---|
| a1 (spread, various labels) | 109 | 82.6% | +38.02% | +5.02% | +4144.31% |
| a2 (ATR, various labels) | 49 | 59.2% | +83.45%† | +4.16% | +4088.82% |
| a3 (prior-spike, various labels) | 129 | 81.4% | +76.70% | +5.00% | +9894.39% |
| a4 (earnings-recency, various labels) | 22 | 50.0% | -0.14% | +1.44% | -3.11% |
| a5 (compliance, various labels) | 76 | 75.0% | +64.58% | +5.00% | +4908.05% |
| a6 (reverse-split proxy) | 82 | 68.3% | +2.53% | +5.00% | +207.08% |
| a7 (thin-liquidity/stale-quote, various labels) | 28 | 67.9% | +0.00% | +5.00% | +0.07% |
| a8 (leveraged/inverse ETF, various labels) | 41 | 80.5% | +8.55% | +5.00% | +350.66% |

† a2's mean is the group most distorted by the OMH outlier above; its median (+4.16%) is far more representative.

(Historical skip_reason label conventions changed over the ledger's life — e.g. `a1_spread` vs `a1_spread_pct` vs `a1_spread_guardrail` — these are the same guardrail logged under different names at different times and have been grouped above; exact per-label breakdown is in the CSV.)

**Baseline for comparison:** the account's actual realized results over the same window (since 2026-08-25, the paper ledger's inception) were **56.5% win rate** (52/92 closed trades) and **-$8.73 total realized P&L** (`get_realized_pnl`/`get_pnl_trade_history`, equity trades). Every guardrail's paper population above shows a higher win rate than the account's actual 56.5% — consistent with a3/a8's prior validation, and now extending that pattern to a1/a2/a5/a6/a7 as well, though the caveats above (especially the optimistic-fill caveat) mean this is directional evidence, not proof any guardrail should be loosened.

## EOD liquidation suggestions (D7, advisory only)

1 symbol flagged: **INNV** — down 6.04% since the 9:30 buy fill, sitting within 2% of its intraday low, and trending down over the second half of the session. All three SUGGEST_LIQUIDATE conditions met. No order was placed; this is advisory only for the account owner's manual review.

## Cohort tracking (D8)

514 candidates (5-30% decline band, <$10) logged to `decliner_cohort_log.csv` today; 26 were brand-new symbols and got sector/industry looked up fresh (of which BABU, LASC and AEHG-family names showed leveraged-ETF descriptions, consistent with a8's basis). `decliner_recovery_tracking.csv` got 144 new rows from the 150 most-recently-added distinct symbols in the cohort log (2261 distinct symbols total); 6 symbols (ONDO, SYRUP, UNI, NHIC, WLFI, DOT) returned no quote and were skipped as likely delisted/inactive.

## Scope note

Per the 2026-07-29 scope fix, only `in_bracket`/`shallow_outside`/`deep_outside` candidates get the full D3/D3a/D3b bar-by-bar simulation (502 of 785 today); `other_price_or_range` (271 candidates, mostly stocks ≥$10 or declines >30%/<5%) get `simulated=false` and no historicals fetch, keeping the compute-cost bounded per the 2026-07-28 incident note.
