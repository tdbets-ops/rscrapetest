# Daily Analytics — 2026-10-01

**Firings today:** 9:30 (buy), 10:30 (buy), 11:30 (Phase A skipped — balance gate, buying_power < $50), 3:30 (Phase A skipped — balance gate, buying_power $26.60), 4:30 (this report).

## Headline

- Candidates seen today (under-$120 ∪ $120-500 lists, deduped by symbol): **257**
- In scope for simulation (decline_bucket in_bracket/shallow_outside/deep_outside): **208** simulated, 49 classified `other_price_or_range` (skipped, per scope rule)
- Overall simulated win rate: **49.5%** (103/208)
- All candidates today came from the under-$120 list and the morning cluster (9:30/10:30 only had scan data — 11:30/3:30 hit the balance gate before Phase A ran, so no scan rows exist for those firings today). $120-500 scan returned 0 matches at both firings.

## Breakdown by price bucket (simulated population only)

| bucket | n | win rate |
|---|---|---|
| <$1 | 63 | 57.1% |
| $1-3 | 76 | 46.1% |
| $3-10 | 69 | 46.4% |

(10-120 / 120-500 buckets have 0 simulated rows — scope rule excludes `other_price_or_range`, and nothing from the $120-500 list qualified for simulation.)

## Breakdown by decline_bucket

| bucket | n | win rate |
|---|---|---|
| in_bracket (10-25% decline, <$10) | 37 | 62.2% |
| shallow_outside (5-10% decline, <$10) | 169 | 46.7% |
| deep_outside (25-30% decline, <$10) | 2 | 50.0% |

## In-experiment-bracket (live buy bracket) by firing — the bracket-calibration evidence

| firing | n | win rate |
|---|---|---|
| 9:30 | 23 | 52.2% |
| 10:30 | 14 | 78.6% |
| 11:30 | 0 | n/a (no scan data — balance gate) |
| 3:30 | 0 | n/a (no scan data — balance gate) |

This is the second day with a live 10:30 buy window. Today's 10:30 in-bracket simulated win rate (78.6%, n=14) continues to run well above 9:30's (52.2%, n=23), echoing the kind of morning-bracket strength the account owner has been tracking — though both firings' sample sizes are still small, and 11:30/3:30 have no comparison data today because the balance gate kept Phase A from running at either firing (buying_power was below $50 both times).

sample sizes: simulated_count=208, total_candidates_seen=257.

## Cross-reference with real trades

- 9:30: 3 trades placed (HIT, RADX, AHG) — all 3 already hit their take-profit target and sold same-day.
- 10:30: 8 trades placed (PUSA, LUCD, VIVO, TMCR, MCRB, GRML, BKYI, EPRX) — 3 (PUSA, LUCD, TMCR) already hit target and sold same-day; 5 (VIVO, MCRB, GRML, BKYI, EPRX) remain open as of this report.
- Both buy firings placed trades — no zero-buy diagnostic needed today.

## Exit-rule (16a3) firings

None today — no `time_stop_*` or `drawdown_stop_*` reasons appear in any of today's position_mgmt files.

## Guardrail saturation check (D6b)

Both buy firings (9:30, 10:30) placed trades, so no zero-buy firing to diagnose today. For reference, skip-reason spread was healthy (no single guardrail rejected 100% of candidates reaching it):
- 9:30 (20 skipped of 23 bracket candidates, 3 bought): a3_prior_spike 7, a6_reverse_split_proxy 7, a5_compliance 4, a1_spread 1, a7_thin_liquidity 1.
- 10:30 (32 skipped of entrants, 8 bought): a1_wide_spread 11, a6_reverse_split_proxy 11, a5_compliance 6, a7_thin_liquidity 3.

## EOD manual liquidation suggestions (D7, advisory only)

5 positions bought today remain open: VIVO, MCRB, GRML, BKYI, EPRX. None meet all three SUGGEST_LIQUIDATE criteria (pct_change_since_buy ≤ -5% AND near_low AND trend_down):

| symbol | pct_change_since_buy | near_low | trend_down |
|---|---|---|---|
| VIVO | +1.7% | No | Yes |
| MCRB | -0.7% | No | No |
| GRML | -1.1% | No | No |
| BKYI | -7.7% | No | Yes |
| EPRX | -2.6% | No | Yes |

BKYI is down the most (-7.7%) and trending down, but is not near its intraday low, so it does not trigger. No action recommended; these are advisory-only and not executed.

## Decliner cohort tracking (D8)

- Appended 208 new rows to `decliner_cohort_log.csv` for today's 5-30%-decline/<$10 candidates (7 were brand-new symbols never logged before — fundamentals fetched and sector/industry captured for those 7: FFR, MORT, VHC, EOCN, CARS, UNIT, PSKY).
- Recovery tracking (`decliner_recovery_tracking.csv`): distinct-symbol universe is now 2,321; capped this cycle to the **150 most-recently-added** symbols per the cap rule. 145/150 refreshed; 5 skipped (HBAR, ONDO, SYRUP, UNI, NHIC — the known crypto-ticker-leak issue, no equity quote resolves for these).

## Guardrail paper-trade ledger (D10)

Opened **42** new paper positions today from the two buy firings' skipped_candidates files (20 from 9:30, 22 unique from 10:30 after deduping 10 symbols that appeared in both firings' skip lists). Of those, 22 closed same-day (21 at target, 1 — VIVK — hit the 25% drawdown stop). 20 remain open.

Carried forward 18 pre-existing open positions: 10 closed today (8 via the 4-session time stop — NXGL, LGO, PARA, SCNI, LGCY, EZGO, ATGL, VWAV — plus LGCY and JLHL hit target; YYGH returned `not_found` from historicals and was closed as likely-delisted with no P&L data). 8 remain open (KITT, QNME, AUID, ADXN, ONCO, MNOV, EHGO, MSOX).

Ledger now has 727 closed trades (median ROI is the trustworthy summary stat — **means are inflated by a known pre-existing entry_price data-scale anomaly** affecting a handful of symbols across several guardrails, flagged in prior sessions and not yet cleaned up):

| skip_reason | n closed | win rate | median ROI | still open |
|---|---|---|---|---|
| a1_spread | 142 | 83.1% | +5.05% | 5 |
| a2_atr | 63 | 57.1% | +3.65% | 0 |
| a3_prior_spike | 160 | 78.8% | +5.00% | 6 |
| a4_earnings_recency | 26 | 53.8% | +2.53% | 1 |
| a5_compliance | 114 | 76.3% | +4.32% | 6 |
| **a6_reverse_split_proxy** | **128** | **69.5%** | **+4.97%** | **9** |
| a7_thin_liquidity | 44 | 68.2% | +3.92% | 0 |
| a8_leveraged_inverse_etf | 49 | 77.6% | +5.00% | 1 |

Overall: 727 closed, 74.1% win rate, median +5.0%. a6 (under active review) looks no worse than the other guardrails on this cut — 69.5% win rate / +4.97% median is in line with the rest of the book, not an outlier case for loosening yet. n is growing (128 closed, 9 still open) but the threshold itself should still wait on the committed review, not be adjusted from this alone.

**Caveats (apply to every number above):**
1. Guardrails short-circuit in a1→a8 order — a paper result for guardrail aX is aX's *marginal* cost given a1..a(X-1) already passed. A paper winner blocked by a6 might also have failed a7 or a8 had a6 not existed; a7 is never re-checked (extra API cost), so no guardrail removal is a clean "would have won" claim.
2. Paper entries fill at the observed price with no spread or slippage; real buys are market orders, so every paper result is optimistic — most of all for the thin, wide-spread, low-priced names these guardrails specifically target.
3. Target exits assume a limit fills whenever a bar's high touches it (matches live GTC behavior and D3's own method, but is still an assumption).

**Baseline — actual realized P&L since inception:** -$145.07 over 294 closed trades (-3.59% aggregate return), per `get_realized_pnl`. Every guardrail's paper win rate and median ROI above is positive, while the account's real realized result is negative — read this gap through the caveats above (no real slippage/spread in the paper fills) rather than as straightforward evidence the guardrails are too strict.
