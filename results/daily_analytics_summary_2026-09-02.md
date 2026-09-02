# Daily Analytics — 2026-09-02

**Population:** 344 unique candidates seen today (336 under-$120, 8 $120-$500). Per the 2026-07-29 scope fix, the decay/trailing simulation (D3/D3a/D3b) runs only on `in_bracket`/`shallow_outside`/`deep_outside` symbols (price < $10, decline 5-30%) — **229 in scope**, 221 simulated (8 had no usable non-interpolated bars today: ENA, ZORA not found; DGZ, MOLN, ALA, HTCR, POM, PTLE had zero real trades). The other 115 candidates (all price ≥ $10, mostly the 10-120/120-500 buckets) are `simulated=false` by design — **10-120 and 120-500 price buckets carry no simulated rows and are excluded from the win-rate stats below.**

## Headline win rate

**57.5% (127/221 simulated)** — did the existing decay/trailing algorithm close the position by 4pm ET?

| Breakdown | Win rate | n |
|---|---|---|
| Morning (9:30/10:30/11:30) | 63.4% | 172 |
| Afternoon (3:30) | 36.7% | 49 |
| Price <$1 | 63.2% | 76 |
| Price $1-3 | 48.2% | 85 |
| Price $3-10 | 63.3% | 60 |
| under120 source | 57.5% | 221 |
| 120to500 source | n/a | 0 (always ≥$120, out of D3 scope) |
| decline_bucket: in_bracket (10-25%) | 69.2% | 39 |
| decline_bucket: shallow_outside (5-<10%) | 55.6% | 180 |
| decline_bucket: deep_outside (>25-30%) | 0.0% | 2 |

## In-bracket (the live/candidate buy population) by firing — the key calibration number

| Firing | Win rate | n | Live buy window? |
|---|---|---|---|
| 9:30 | **84.2%** | 19 | Yes (since inception) |
| 10:30 | **71.4%** | 7 | Yes (added 2026-08-25) |
| 11:30 | 66.7% | 3 | No |
| 3:30 | 40.0% | 10 | No (afternoon-only, never buys) |

**Reading:** 9:30 and 10:30 — the two live buy windows — post the two best win rates, both above the 69.2% in-bracket average. 11:30's n=3 is too small to draw a conclusion either way. 3:30's 40% is the weakest slice and consistent with the broader morning-vs-afternoon split (63.4% vs 36.7%) — the algorithm's decay schedule gives afternoon entries a much lower bar to clear (2-3% vs 4-5%) but they still underperform, suggesting time-of-day mean-reversion dynamics rather than a bracket-calibration issue. Nothing here argues against 10:30, and nothing here is strong enough (small n) to justify adding 11:30 as a buy window yet.

Cross-reference with today's real activity: 40 skip rows (18 @ 9:30, 22 @ 10:30) and 2 real buys (XPL @9:30, CANF @10:30) — both real buys were in-bracket. Of the 40 skipped candidates, 39 were classifiable (1 no-data: DGZ); of those, 26 would have won and 13 would have lost/stayed open per the D3 simulation — see D10 below for the actual paper-traded P&L version of this same question.

## Exit-rule firings (D6a)

None today. No position was closed by the 16a2 (never-touched-breakeven auto-liquidate) or 16a3 (time-stop/drawdown-stop) rules. The one real closure today (XPL, bought 9:30 @ $0.6002, sold 9:30 same session @ $0.631, +5.1% / +$0.25) was a normal decay/trailing **target hit**, not an exit-rule firing.

## Guardrail saturation check (D6b)

Both buy firings placed at least one buy today (9:30: 1/19 in-bracket candidates bought; 10:30: 1/8 bought) — the zero-buy saturation check does not apply today. No single guardrail is blocking 100% of a firing's candidates.

## EOD liquidation suggestions (D7) — advisory only, not executed

One symbol flagged: **CANF** (bought 10:30 @ $2.5399, still open). Since fill: -7.1%, near session low (low $2.32, current $2.36), and second-half-vs-first-half average close trending down ($2.396 vs $2.539). All three SUGGEST_LIQUIDATE conditions met. **This is advisory only — no order was placed, cancelled, or modified.** XPL (the other buy today) already closed via its own decay target and is not open, so it's not evaluated here.

## Decliner cohort tracking (D8) — pure logging, no guardrail impact

229 today's candidates (5-30% decline, <$10, under120 source) appended to `decliner_cohort_log.csv`; 13 were new to the log and got sector/industry via fundamentals lookup (1 not_found: ENA). Recovery tracking: cohort log now holds 2,060 distinct symbols; capped to the 150 most-recently-added (2026-08-21 through today) per the >150 cap rule — 144 quoted successfully, 6 skipped as likely delisted/no data (ENA, OP, CRV, ZORA, HOOZ, BBCQ).

## Guardrail paper-trade ledger (D10)

**Today:** opened 27 new paper positions from today's skip lists (19 closed same-day, 8 carried open); dedup correctly skipped CANF (real buy), OLOX and XLAB (already had open paper positions). Carried forward 30 aged open positions from 8/26-8/28 (no D10 carry-forward ran on 9/1 — a one-day gap in the pipeline, noted here rather than backfilled): 17 closed today (12 via 4-session time-stop, 4 via 25% drawdown-stop, 1 via target), 13 remain open, 1 (YYGH) skipped as not-found/likely delisted.

**Cumulative ledger:** 191 rows, 169 closed, 22 open.

| skip_reason | n closed | win% | mean roi% | median roi% | sum roi% | still open |
|---|---:|---:|---:|---:|---:|---:|
| a1_spread | 23 | 100.0% | 5.34 | 5.06 | 122.93 | 6 |
| a1_spread_guardrail | 5 | 80.0% | 1.50 | 7.06 | 7.50 | 0 |
| a1_spread_pct | 5 | 40.0% | -2.21 | -4.51 | -11.04 | 0 |
| a2_atr | 10 | 60.0% | 422.47† | 5.00 | 4224.75 | 1 |
| a2_atr_guardrail | 3 | 33.3% | -14.84 | -25.00 | -44.53 | 1 |
| a3_prior_spike | 39 | 87.2% | 2.62 | 5.05 | 102.33 | 7 |
| a4_earnings_recency | 6 | 50.0% | -1.92 | 0.71 | -11.50 | 1 |
| a5_compliance | 35 | 77.1% | 92.74† | 5.00 | 3246.07 | 2 |
| a5_compliance_guardrail | 2 | 100.0% | 5.11 | 5.11 | 10.22 | 0 |
| **a6_reverse_split_proxy** | **28** | **67.9%** | **5.66** | **5.03** | **158.34** | **3** |
| a7_stale_quote / _gap | 2 | 0.0% | -10.47 | -10.47 | -20.94 | 0 |
| a7_thin_liquidity / _stale_quote | 3 | 66.7% | 3.07 | 5.26 | 9.21 | 0 |
| a8_leveraged_etf / _inverse_etf | 8 | 100.0% | 5.90 | 3.13 | 45.26 | 1 |

† **Data quality flag:** a2_atr and a5_compliance means are heavily skewed by three apparent bad prints in the underlying historicals from 2026-08-27 (OMH $0.0837→$3.68 = +4297%; VBIO $0.1298→$3.78 = +2812%; CALC $0.4902→$2.43 = +396% — almost certainly reverse-split or bad-tick artifacts, not real price action, pre-dating this session and left as-is since those rows are already closed). Use the **median** for these two rows, not the mean.

**a6 (reverse-split proxy) — under active review:** 28 closed paper trades, 67.9% win rate, median +5.03% / mean +5.66%. This is a decent showing and close to the account's actual realized win rate below, i.e. a6 does not look obviously miscalibrated on this sample.

**Overall paper ledger:** 169 closed, 77.5% win rate (131/169).
**Account's actual realized win rate, same 8/25-9/2 window:** 61.5% (8/13 trades), total realized P&L **+$0.51**.

Caveats (apply to all paper-trade numbers above):
1. Guardrails short-circuit a1→a8 in order — `skip_reason` is only the first failure; paper results measure that guardrail's *marginal* cost given a1..a(X-1) already passed. Cross-check `fundamentals_guardrails_failed`. a7 is not recomputed.
2. Paper entries fill at the observed price with no spread/slippage; real buys are market orders, so every paper result is optimistic relative to a real fill.
3. Target exits assume a limit fills whenever a bar's high touches it, matching the real resting GTC limit's behavior.

**Known pre-existing data issue (not introduced today):** 8 symbols (BDRX, PFSA, DDC, TRUG, RPGL, CISS, SMJF, OLOX) each have 2-3 rows in the ledger from before this session, i.e. a second paper position was opened for a symbol that already had one — the dedup-by-symbol rule was violated on one or more prior days. Handled correctly today (each row's own entry_date/entry_price used independently), but worth a look at the pipeline that generates the day-0 opens.
