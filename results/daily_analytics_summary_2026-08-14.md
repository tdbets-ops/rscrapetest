# Daily Analytics — 2026-08-14

**Note on today's firings:** the 9:30am ET firing did not run today (no scan files, no buy orders, and no trades/skipped-candidates files exist for 2026-08-14) — only the 10:30, 11:30, and 3:30 ET firings ran Phase A/C. As a result there is no Phase B (buying) activity to report or cross-reference against simulated outcomes today, and D7's EOD liquidation suggestions file is header-only (no positions bought today).

## Scope

386 unique candidates seen today across all firings (under-$120 and $120-$500 lists combined). Per the 2026-07-29 scope fix, the per-symbol decay/trailing simulation runs only on the in_bracket/shallow_outside/deep_outside population (under $10, 5-30% decline) — **303 of 386 candidates simulated** (78%); the remaining 83 are `other_price_or_range` (price ≥ $10, decline > 30%, or the $120-500 list) and appear in the CSV with `simulated=false` and null scenario columns.

## Headline win rate (decay/trailing algorithm, simulated rows only)

**44.6% overall (n=303)**

| Split | Win rate | n |
|---|---|---|
| Morning (9:30/10:30/11:30 first-seen) | 50.6% | 233 |
| Afternoon (3:30 first-seen) | 24.3% | 70 |

This is the key number for the 10:30/11:30 buying-expansion decision: morning candidates continue to close out roughly 2x as often as afternoon ones.

### By decline bucket (bracket-calibration number)

| Bucket | Win rate | n |
|---|---|---|
| in_bracket (10-25% decline, <$10 — live Phase B bracket) | 48.5% | 66 |
| shallow_outside (5-10% decline, <$10) | 43.3% | 233 |
| deep_outside (25-30% decline, <$10) | 50.0% | 4 (small sample) |

### Price bucket (simulated population only — <$10 by construction)

| Bucket | Win rate | n |
|---|---|---|
| <1 | 40.9% | 88 |
| 1-3 | 53.2% | 111 |
| 3-10 | 38.5% | 104 |
| 10-120 | n/a | 0 (out of simulation scope — see note above) |
| 120-500 | n/a | 0 (out of simulation scope — see note above) |

### in_experiment_bracket morning vs. afternoon (the exact Phase B decision population)

| Time bucket | Win rate | n |
|---|---|---|
| Morning | 47.5% | 61 |
| Afternoon | 60.0% | 5 (very small sample — caveat heavily) |

Small-sample caveat: the afternoon in-bracket win rate is based on only 5 candidates today; not enough to draw conclusions from a single day.

## Guardrail cross-reference

No buys were placed today (Phase B did not run — see note above), so there is no `latest_trades.csv`/`latest_skipped_candidates.csv` data from today to cross-reference against simulated outcomes.

## Suggested end-of-day liquidations (advisory, not executed)

None — no symbols were bought today, so there are no open same-day positions to evaluate. `results/eod_liquidation_suggestions_2026-08-14.csv` is header-only.

## Cohort tracking

Logged 303 new candidates today to `decliner_cohort_log.csv` (5-30% decline, under $10 — same population as the simulated scope). Full cohort log now has 1,827 distinct symbols; this cycle re-quoted the 150 most-recently-added for `decliner_recovery_tracking.csv` (capped per spec).
