# Daily Analytics — 2026-10-09

## Headline

- **433 candidates seen** today across 4 firings (9:30/10:30/11:30/3:30 ET), **300 simulated** (13 dropped for zero non-interpolated bars after first-seen; 120 out of scope as `other_price_or_range`).
- **Overall decay/trailing win rate: 46.0%** (138/300).
- Both buy firings placed real trades — no zero-buy diagnostic needed today.

## D5 breakdowns (simulated=true rows only)

| Breakdown | Win rate | n |
|---|---|---|
| Overall | 46.0% | 300 |
| Morning (9:30/10:30/11:30) | 51.7% | 201 |
| Afternoon (3:30) | 34.3% | 99 |

**By price bucket** (only `<1`/`1-3`/`3-10` have simulated rows — every in-scope symbol was under $10; `10-120` and `120-500` are empty of *simulated* rows, not omitted from the data):

| Price bucket | Win rate | n |
|---|---|---|
| <1 | 54.0% | 100 |
| 1-3 | 43.6% | 94 |
| 3-10 | 40.6% | 106 |
| 10-120 | — | 0 |
| 120-500 | — | 0 |

**By source list**: `under120` 46.0% (n=300); `120to500` had no simulated rows (none fell in the decline-bucket scope).

**By decline bucket** (core bracket-calibration number):

| decline_bucket | Win rate | n |
|---|---|---|
| in_bracket | 53.7% | 54 |
| shallow_outside | 44.1% | 245 |
| deep_outside | 100.0% | 1 (n=1, not meaningful) |

**Within in_experiment_bracket=true (n=54):**

By time bucket: morning 51.2% (n=41), afternoon 61.5% (n=13).

By firing (direct evidence on the two live buy windows + whether 11:30 should join them):

| Firing | Win rate | n |
|---|---|---|
| 9:30 | 57.1% | 28 |
| 10:30 | 44.4% | 9 (noisy) |
| 11:30 | 25.0% | 4 (very noisy) |
| 3:30 | 61.5% | 13 (noisy) |

Only the 9:30 firing has a sample size worth reading anything into. 10:30/11:30/3:30 are each under 15 observations and should not move any buy-window decision on their own.

## Scope note

D3's per-symbol simulation is restricted to `decline_bucket` in (in_bracket, shallow_outside, deep_outside) — 313 of 433 symbols. The other 120 (`other_price_or_range`: priced ≥$10, or declines outside the 5-30% tracking band) are skipped entirely (no historicals fetched), carrying `simulated=false` and null scenario columns in `daily_analytics_2026-10-09.csv`. This is the standing cap adopted 2026-07-28 to avoid an unfinished/unlanded simulation.

## Cross-reference with live trading

Of today's 6 real buys, 5 were in the D3 simulation scope (MF was out of scope). 2 of those 5 closed as decay-target wins in simulation (ALHC +6.8%, PCSA +6.7% — both match their actual real-trade outcomes), while 3 showed mixed-to-negative marks in simulation (IAE +0.9%, MOBX -11.6%, ZENA -5.9%) — broadly consistent with the ~46% overall win rate at this tiny n.

Of the 40 guardrail-skipped bracket-eligible candidates today, 20 (half) would have closed as decay-target wins in simulation, spanning several guardrail reasons (a1_spread, a3_prior_spike, a5_compliance, a6_reverse_split_proxy, a7_thin_liquidity, a8_leveraged_etf). This is a shallow same-day pass, not a rigorous guardrail audit — see the D10 paper-trade ledger below for the real validation exercise (full exit-rule simulation against the whole historical ledger, not just today).

## Data-quality caveats

- 13 in-scope symbols had zero non-interpolated bars at/after first-seen (effectively no trading for the rest of the session): ATGL, FCHL, MB, ADVB, NDRA, TAOP, AACG, LGHT, LBGJ, UONE, THCH, HTCR, INLX. `simulated=false` for these.
- All 313 in-scope symbols were otherwise fetched successfully; no gaps versus the scope list.

## Exit-rule (16a3) firings

None today — no time-stop or drawdown-stop (step 16a3) liquidations fired at any firing. (Step 16a2's 60-minute auto-liquidate did fire once, on MOBX at the 10:30 firing, -9.25% realized — that's a different rule and is reflected in the position-management logs, not this section, which is reserved for 16a3 only.)

## Guardrail saturation check (D6b)

Both buy firings (9:30 and 10:30) placed real trades (4 and 2 respectively), so there is no zero-buy firing today and no saturation diagnostic is required.

## EOD manual liquidation suggestions (advisory only — nothing executed automatically)

Of today's 6 real buys, only IAE and ZENA are still open (ALHC/PCSA closed on their take-profit targets, MOBX was auto-liquidated via step 16a2, MF closed via an a2-style limit-at-bid exit from the local Phase C loop between cloud firings). Neither IAE (-1.11%) nor ZENA (-4.88%) clears the -5% pct_change gate required before the near-low/trend-down checks even apply, so **0 liquidation suggestions** — see `results/eod_liquidation_suggestions_2026-10-09.csv`. These are advisory only; no order was placed or cancelled based on this section.

## Guardrail paper-trade ledger (D10)

Per-skip-reason performance, **closed rows across the whole ledger** (not just today):

| Guardrail | n closed | Win % | Mean % | Median % | Sum % | Still open |
|---|---|---|---|---|---|---|
| a1 spread | 183 | 81.4 | 27.78 | 5.06 | 5083.2 | 4 |
| a2 ATR | 83 | 61.4 | 177.32 | 4.51 | 14717.4 | 5 |
| a3 prior spike | 210 | 75.7 | 47.25 | 5.00 | 9922.2 | 11 |
| a4 earnings recency | 28 | 53.6 | 0.31 | 2.53 | 8.8 | 2 |
| a5 compliance | 145 | 75.2 | 39.53 | 4.27 | 5732.5 | 8 |
| **a6 reverse-split proxy** | **160** | **68.8** | **0.80** | **4.97** | **127.4** | **12** |
| a7 thin/stale quote | 49 | 71.4 | -0.32 | 5.00 | -15.9 | 1 |
| a8 leveraged ETF | 64 | 70.3 | 5.11 | 5.00 | 327.0 | 3 |

**a6_reverse_split_proxy** (the guardrail under active review, exact literal skip_reason): n=158 closed, 69.0% win rate, mean +0.93%, median +4.97%, summed +147.4%, 12 still open. Median is solidly positive; mean is dragged near flat by a handful of outlier losers — consistent with "occasionally implodes" being exactly why the guardrail exists. Still unremarkable relative to the other guardrails' medians (~4.3-5.1%) — no evidence yet that a6 is uniquely over-blocking relative to its peers.

Note: one pre-existing closed row (YYGH, a3) has no usable roi_pct (`no_data_likely_delisted`, pre-dates this run) and is excluded from all stats above.

**Today's activity**: 36 new paper positions opened (22 from the 9:30 skip file, 14 from 10:30; 4 rows correctly skipped as duplicates of today's real buys). Of those, 26 closed same-day (17 target, 6 auto-liquidate-60min, 3 drawdown-stop-25%); 10 still open. Of the 52 pre-existing open positions, 16 closed on carry-forward (8 drawdown-stop, 6 target, 2 time-stop-4-sessions); 35 remain open; LESL (entry 2026-10-05) is flagged, not guessed at — Robinhood reports it as an inactive/delisted instrument with no price data. Ledger totals now: **970 rows, 46 open, 924 closed**.

**Baseline comparison** (this is the required side-by-side, not an endorsement): the account's actual realized P&L since inception (`get_realized_pnl`, span=all, pulled 2026-10-09) is **-$151.37 total return (-3.58%) over 331 closed real trades**. The guardrail-blocked cohort's aggregate paper performance (all eight guardrails' win rates run 54-81%, with strongly positive summed ROI) looks far better than that — but read the caveats below before treating this as "the guardrails are costing money":

1. `skip_reason` is only each candidate's **first** guardrail failure (a1→a8 short-circuit in order). Cross-check `fundamentals_guardrails_failed` (lists every a5/a6/a8 the candidate would also have failed) before concluding that removing one guardrail alone would have produced these results. a7 (thin-liquidity) is never retroactively recomputed here, so no number above is a clean "we'd have won this without the guardrail."
2. Paper entries fill at the observed price with **no spread or slippage**, unlike the real strategy's market-order buys. Every paper result is optimistic, most of all for the wide-spread/thin/low-priced names these guardrails specifically target.
3. Target exits assume a resting limit fills whenever a bar's high touches it — matching how the real GTC limit and the D3 simulation above both behave, but still an assumption, not an observed fill.

No order was placed, cancelled, or modified by this step or by any part of Phase D today — D7 and D10 are both advisory/simulation-only, and any guardrail-tightening or loosening ideas above are for manual review in a future update, not something this routine acts on itself.
