# Daily analytics — 2026-08-31

## Headline

- **413** unique candidates seen today across all 4 firings (9:30/10:30/11:30/3:30 ET); **285** in scope for simulation (in_bracket/shallow_outside/deep_outside), 9 had zero real bars (unsimulated).
- Overall simulated win rate (decay/trailing target hit by close): **53.3%** (152/285).
- **In-experiment bracket (live buy bracket, price<$10, 10-25% decline): 74.1% win rate (20/27 simulated)** — the core bracket-calibration number.

## Win rate by time bucket

| Bucket | n | wins | win rate |
|---|---|---|---|
| morning (9:30/10:30/11:30) | 218 | 122 | 56.0% |
| afternoon (3:30) | 67 | 30 | 44.8% |

## Win rate by price bucket (simulated only — 10-120/120-500 have 0 simulated rows, as expected since the bracket requires price<$10)

| Bucket | n | wins | win rate |
|---|---|---|---|
| <1 | 92 | 52 | 56.5% |
| 1-3 | 94 | 52 | 55.3% |
| 3-10 | 99 | 48 | 48.5% |

## Win rate by decline_bucket

| Bucket | n | wins | win rate |
|---|---|---|---|
| in_bracket (10-25%, live buy bracket) | 27 | 20 | 74.1% |
| shallow_outside (5-10%) | 258 | 132 | 51.2% |
| deep_outside (25-30%) | 0 | 0 | — (none seen today) |

## In-bracket win rate by firing (the direct evidence for the 10:30 window and whether 11:30 should follow)

| Firing | n | wins | win rate |
|---|---|---|---|
| 9:30 | 19 | 15 | 78.9% |
| 10:30 | 4 | 3 | 75.0% |
| 11:30 | 0 | 0 | — (no in-bracket candidates first-seen at 11:30 today) |
| 3:30 | 4 | 2 | 50.0% |

Sample sizes are small (esp. 10:30 n=4, 3:30 n=4) — directional only. Both live buy windows (9:30, 10:30) continue to outperform the 3:30 slot in-bracket, consistent with the morning-outperforms-afternoon pattern that motivated the original narrowing. 11:30 had zero in-bracket candidates first-seen there today, so today's data is silent on whether 11:30 should be added as a third buy window.

## Scope note

Per the 2026-07-29 scope fix, only decline_bucket in (in_bracket, shallow_outside, deep_outside) — i.e. price<$10 with a 5-30% decline — is simulated (285 of 413 candidates). The other_price_or_range population (119 candidates, mostly the $120-$500 data-collection scan and out-of-range under-$120 names) is intentionally not simulated to keep compute bounded.

## Today's trades cross-reference

6 real buys today (1 at 9:30: NCNA; 5 at 10:30: VACI, MPU, GP, DSC, RYET). Of these, NCNA/MPU/DSC were classified in_bracket in today's D2 pass (their *first-seen* price/decline differs from their actual fill, since first-seen is earliest-across-firings, not fill time); VACI/RYET/GP were shallow_outside at first-seen (they deepened into the live bracket by the time they were actually bought at a later firing). All 6 were liquidated or hit target intraday except RYET (still open, see D7).

## Exit-rule ledger (D6a)

No step 16a3 (time-stop/drawdown-stop) liquidations fired today. All of today's forced closes (NCNA, MPU, GP, DSC) were step 16a2 60-minute never-touched-breakeven auto-liquidations, executed by the local 10-minute Phase C loop shortly after each position's 60-minute mark; VACI closed via a normal resting-limit target fill, not a liquidation.

## Guardrail saturation check (D6b)

Both buy firings placed trades today (9:30: 1 trade; 10:30: 5 trades) — the mandatory zero-buy diagnostic does not apply. For completeness: a3_prior_spike was the largest single skip reason at both firings (9/18 at 9:30, 8/18 at 10:30) but did not approach 100% saturation at either, so no guardrail malfunction is flagged.

## EOD liquidation suggestions (D7, advisory only — NOT executed)

Only RYET is open (bought 10:30 window, 5 sh @ $0.85). pct_change_since_buy is **+1.04%** (not a decline), so SUGGEST_LIQUIDATE is **not** flagged — none of the three required conditions (≤-5% decline, near 60-min low, downtrend) are met. See results/eod_liquidation_suggestions_2026-08-31.csv.

## Cohort tracking (D8)

294 new candidates appended to results/decliner_cohort_log.csv for today (0 already logged). 11 symbols were genuinely new to the log ever and got fresh sector/industry fundamentals lookups; the other 283 copied sector/industry forward from their earliest prior appearance (most of which predate sector/industry capture and are blank). 150 of 2,047 total distinct cohort symbols (most-recently-added, capped) were re-quoted for recovery tracking — all 150 succeeded with no delistings/errors.

## Guardrail paper-trade ledger (D10)

**⚠️ Data-quality flag before reading the table**: OMH (skip_reason a2_atr), and VBIO/CALC (skip_reason a5_compliance) closed today with implausible ROIs of +4297%, +2812%, and +396% — traced to apparent reverse stock splits over the weekend (confirmed via 60+ consistent intraday bars, reproduced under both split-adjusted and raw pricing) that created a price discontinuity between the stored entry_price and today's daily bar. FTFT and KALA (both skip_reason **a6_reverse_split_proxy** — the guardrail this ledger exists to validate) show the same pattern but are still open, so they haven't polluted a6's numbers *yet*; expect a similar distortion when they close. **These 3 closed outliers are excluded from the "clean" columns below**; raw (as-recorded) aggregates are shown alongside for transparency. This is likely to recur — a reverse split is structurally exactly the failure mode a6 is trying to flag, so paper trades that survive one will keep generating unusable ROI outliers.

| skip_reason | n (closed) | win% | mean ROI% (raw) | mean ROI% (clean) | median ROI% | sum ROI% (clean) | still open |
|---|---|---|---|---|---|---|---|
| a3_prior_spike | 33 | 93.9% | 4.24 | 4.24 | 5.08 | 139.89 | 9 |
| a5_compliance | 29 | 82.8% | 113.69 | 3.30 (n=27, excl. VBIO/CALC) | 5.09 | 89.23 | 3 |
| **a6_reverse_split_proxy** | **22** | **72.7%** | **0.11** | **0.11** | **5.07** | **2.32** | **5 (incl. FTFT/KALA, likely split-distorted on close)** |
| a1_spread (all variants) | 25 | 96.0% | 5.30 | 5.30 | 5.14 | 132.49 | 6 |
| a2_atr (all variants) | 9 | 55.6% | 476.42 | -9.48 (n=6, excl. OMH; a2_atr_guardrail n=2 kept as-is) | 5.03 | -47.85 | 3 |
| a8_leveraged_inverse_etf | 6 | 100.0% | 4.24 | 4.24 | 2.09 | 25.45 | 1 |
| a4_earnings_recency | 5 | 40.0% | -3.30 | -3.30 | -3.57 | -16.50 | 1 |
| a7 (thin_liquidity/stale_quote, all variants) | 2 | 50.0% | 0.34 | 0.34 | 0.34 | 0.67 | 0 |

Totals: 133 closed, 31 open (was 104 closed / 36 open before today). Today: 24 new paper positions opened from today's skipped_candidates (20 closed same-day: 18 target hits, 2 auto-liquidates; 4 still open); of the 36 carried-forward open positions, 9 closed (5 time-stops at session 4, 3 target hits, 1 drawdown stop), 27 remain open.

**a6 specifically (the guardrail under review)**: 72.7% win rate, mean +0.11%, median +5.07%, summed +2.32% ROI on its 22 closed paper trades — a solidly positive, unremarkable-looking population, *not* the kind of result that would argue for loosening the 15x threshold. Sample is still small (22 closed, 5 open) and about to get noisier from the FTFT/KALA split issue above.

**Baseline for comparison** (account's actual realized results, 2026-08-25 through today, matching the ledger's window): **11 real closed round trips, 54.5% win rate (6W/5L), +$0.03 total realized P&L** (source: get_realized_pnl / get_pnl_trade_history). Every guardrail's closed-paper win rate above (55.6%–100%) is at or above this baseline, and most guardrails' clean mean/median ROI is comparable to or better than the real book's near-breakeven result — but see the caveats below before reading that as "loosen the guardrails."

**Mandatory caveats** (bound what these numbers mean):
1. Guardrails short-circuit in a1→a8 order, so skip_reason is only a candidate's *first* failure. A paper winner blocked by a6 might still have failed a7 or a8 had a6 not existed (a7 is never recomputed here — it needs an extra historicals call per candidate — so removing a6 or a8 is never a clean "we would have won this" claim). Cross-check the fundamentals_guardrails_failed column, which records every one of a5/a6/a8 a candidate would have failed.
2. Paper entries fill at the observed price with no spread paid and no slippage, while real buys are MARKET orders — every paper result is optimistic relative to a real fill, most of all for the wide-spread, thin, low-priced names these guardrails target.
3. Target exits assume a limit fills whenever a bar's high touches it, matching how the real resting GTC limit behaves (and how D3 already simulates), but it's still an assumption.
