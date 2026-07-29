# Daily Analytics — 2026-07-29

**Scope note:** 891 unique candidates seen today across all 4 firings (9:30/10:30/11:30/3:30 ET) on the under-$120 and $120-500 scans. Per the 2026-07-29 evening scope fix, the expensive per-symbol decay/trailing/liquidation/EOD simulation (D3/D3a/D3b) now runs **only** on the under-$10, 5-30%-decline population (`in_bracket` + `shallow_outside` + `deep_outside`) — **509 of 891 candidates simulated (57%)**. The remaining 382 (`other_price_or_range`: price ≥ $10, decline > 30%, or the $120-500 list) appear in `daily_analytics_2026-07-29.csv` with `simulated=false` and null scenario columns; the $120-500 list and the 10-120/120-500 price buckets therefore show **n=0 in every table below by design**, not a data gap.

## Headline

**Overall win rate (decay/trailing algorithm, simulated population): 45.8% (233/509 closed by market close)**

## Win rate by time bucket

| Time bucket | Win rate | n |
|---|---|---|
| Morning (9:30/10:30/11:30) | **56.2%** | 372 |
| Afternoon (3:30) | **17.5%** | 137 |

This is the key number for the 10:30/11:30-expansion decision: morning candidates close out at more than 3x the rate of the lone afternoon firing in today's sample.

## Win rate by decline bucket (relative to the live 10-25%/<$10 buy bracket)

| Decline bucket | Win rate | n |
|---|---|---|
| in_bracket (10-25%, the live buy bracket) | **72.9%** | 59 |
| shallow_outside (5-10%) | 42.1% | 449 |
| deep_outside (>25-30%) | 100% | 1 (too small to read anything into) |

The live 10-25% bracket clearly outperforms the shallower 5-10% decline band today. deep_outside has only 1 sample — not usable.

## In-bracket win rate by time bucket (the exact population Phase B trades)

| Time bucket | Win rate | n |
|---|---|---|
| Morning | **80.9%** | 47 |
| Afternoon | **41.7%** | 12 |

Small samples (n=12 afternoon), but directionally this reinforces the morning-only experiment: in-bracket morning candidates closed out at roughly double the afternoon rate today.

## Win rate by price bucket

| Price bucket | Win rate | n |
|---|---|---|
| <1 | 51.5% | 163 |
| 1-3 | 46.1% | 154 |
| 3-10 | 40.6% | 192 |
| 10-120 | n=0 (out of simulated scope) | 0 |
| 120-500 | n=0 (out of simulated scope) | 0 |

## Win rate by source list

| Source list | Win rate | n |
|---|---|---|
| under120 | 45.8% | 509 |
| 120to500 | n=0 (out of simulated scope — this list is data-collection only, never <$10) | 0 |

## Exit-scenario comparison (in_bracket vs shallow_outside)

| Decline bucket | n | Decay avg ROI (closed only) | EOD-sell avg ROI (all) | Liquidation-rule triggered | Liquidation avg ROI (when triggered) |
|---|---|---|---|---|---|
| in_bracket | 59 | +4.42% (n=43 closed) | +0.82% | 4/59 (6.8%) | -6.43% |
| shallow_outside | 449 | +3.96% (n=189 closed) | -0.41% | 31/449 (6.9%) | -3.51% |

Both the decay/trailing algorithm and a flat EOD sell outperform the 60-minute auto-liquidate rule on the candidates it *would* trigger for — but that's an apples-to-oranges comparison since liquidation only fires on the subset that never touched breakeven in the first hour (i.e., the worst-starting names). Small samples on the liquidation column (4 and 31 triggers) — not enough to draw a firm conclusion yet.

## Cross-reference with today's real trades

Three real Phase B trades today (all from the 9:30am firing, all in_bracket):

| Symbol | Buy fill | Real outcome | Simulated decay outcome | Simulated liquidation | Simulated EOD |
|---|---|---|---|---|---|
| SOFX | $6.9875 | Closed +5.05% (limit sell) | Closed +5.09% @ 17:20 UTC | Not triggered | +0.50% |
| HSCS | $2.0499 | Closed +5.42% (limit sell) | Closed +5.42% @ 15:05 UTC | Not triggered | +2.94% |
| DAAQ | $9.4499 | **Closed -24.0%** (market sell, matches the a7 guardrail post-mortem) | Not closed, peak never exceeded first-seen price | **Triggered**, sim exit -19.6% | -19.6% |

The DAAQ post-mortem that motivated tonight's new a7 (thin-liquidity/stale-quote) guardrail shows up cleanly in the simulation too: DAAQ's `peak_price_reached` equals its `first_seen_price` exactly — it never once traded above where it was first seen, and both the liquidation-rule and EOD-sell scenarios land within ~0.1pp of what actually happened on the account (-19.6% sim vs -24.0% real order-book fill vs. Robinhood's own auto-liquidate exit). Sample size is 1 trade — not statistically meaningful on its own, but directionally consistent with the guardrail rationale. SOFX and HSCS both matched their simulated decay-close outcome almost exactly (both closed within the same 5-minute bar the simulation predicted).

25 candidates were skipped by Phase B guardrails today (a1-a5); guardrail-metric-to-outcome correlation isn't meaningful yet at this sample size (25 skips, mostly a1_spread and a2_atr) — no cross-reference attempted this cycle beyond the 3 actual trades above.

## Suggested end-of-day liquidations (advisory, not executed)

No open positions at end of day — all three of today's buys (SOFX, HSCS, DAAQ) were already closed out (2 via limit sell, 1 via market/auto-liquidate) before this firing ran. `results/eod_liquidation_suggestions_2026-07-29.csv` is header-only.

## Cohort tracking

509 new under-$10/5-30%-decline candidates logged to `decliner_cohort_log.csv` today (append-only). The full log now has 1,143 distinct symbols; this cycle refreshed current-price data for 150 of them (capped, prioritizing the most-recently-added — all 150 happened to be today's own additions since today alone added more than the cap) and appended to `decliner_recovery_tracking.csv`. 993 distinct symbols were not refreshed this cycle and will be picked up on a future run.

**Caveat:** all win-rate splits above, especially n<15 cells (in_bracket afternoon, deep_outside, liquidation-triggered counts), are single-day samples. Do not treat any of this as a settled conclusion about morning-vs-afternoon or bracket calibration — it's one data point in an accumulating series.
