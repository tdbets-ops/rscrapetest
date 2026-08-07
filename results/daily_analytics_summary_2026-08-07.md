# Daily Analytics — 2026-08-07

**Scope note:** Per the 2026-07-29 scope fix, only candidates in the `in_bracket` / `shallow_outside` / `deep_outside` decline buckets (under $10, 5-30% decline) are simulated. `other_price_or_range` candidates (price ≥ $10, decline > 30%, or the $120-500 list) are excluded from simulation to keep runtime bounded — **272 of 436** candidates seen today were simulated (10 additional in-scope symbols had zero real trade bars in the window and were excluded: ABTS, ARBB, AURE, BONK, FXHO, IMRN, IOTR, MF, MTEX, NIPG).

## Headline win rate

**56.99%** (155/272) of simulated candidates would have closed via the decay/trailing algorithm by market close.

## Win rate by time bucket (KEY number for 10:30/11:30 expansion decision)

| Time bucket | Win rate | n |
|---|---|---|
| Morning (9:30/10:30/11:30) | **61.06%** | 208 |
| Afternoon (3:30) | **43.75%** | 64 |

Morning continues to outperform afternoon, consistent with prior days.

## Win rate by decline bucket (bracket-calibration number)

| Decline bucket | Win rate | n |
|---|---|---|
| in_bracket (10-25%, live buy bracket) | **64.71%** | 51 |
| shallow_outside (5-10%) | 54.59% | 218 |
| deep_outside (>25-30%) | 100.00% | 3 (too small to draw conclusions) |

## Win rate by price bucket

| Price bucket | Win rate | n |
|---|---|---|
| <$1 | 67.65% | 68 |
| $1-3 | 54.44% | 90 |
| $3-10 | 52.63% | 114 |
| $10-120 | n/a | 0 (out of scope) |
| $120-500 | n/a | 0 (out of scope) |

## in_experiment_bracket=true, split by time (the exact Phase B population)

| Time bucket | Win rate | n |
|---|---|---|
| Morning | **71.11%** | 45 |
| Afternoon | 16.67% | 6 (very small sample — one afternoon firing, use with caution) |

This is the strongest same-direction evidence yet for the morning-only decision: within the live 10-25%/<$10 buy bracket, morning candidates closed 71% of the time vs. 17% for the lone afternoon batch (n=6). Consistent with the pattern seen 8/5 and 8/6.

## Cross-reference: today's guardrail-skipped candidates (Phase B, 9:30am firing)

0 trades were placed today (all 36 bracket-eligible candidates screened at 9:30am were skipped by guardrails a1-a7). Of those 36, 35 fell inside today's simulated population (1, BONK, had no tradable instrument). Of the 35, **23 (65.7%) would have closed as winners** under the decay/trailing algorithm despite tripping a guardrail — roughly in line with the overall in_bracket win rate (64.7%). Sample too small per individual guardrail (a1-a7) to draw guardrail-specific conclusions; noting for the ongoing dataset.

## Suggested end-of-day liquidations (advisory, not executed)

No symbols were bought today (0 fills), so there is nothing to evaluate for D7 — `results/eod_liquidation_suggestions_2026-08-07.csv` is header-only.

## Cohort tracking

Logged 282 new candidates to `decliner_cohort_log.csv` today (5-30% decline, under $10 band). Full log now covers 1,591 distinct symbols; 150 most-recently-added were quoted this cycle for `decliner_recovery_tracking.csv` (spanning first-seen dates 2026-08-05 through 2026-08-07).
