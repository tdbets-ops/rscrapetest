# Daily Analytics — 2026-07-31

**Scope note:** 617 unique candidates seen across today's 4 scan firings (9:30/10:30/11:30/3:30 ET). Per the 2026-07-29 scope fix, only the under-$10, 5-30%-decline population (`in_bracket` + `shallow_outside` + `deep_outside`) is simulated — **379 of 617 (61.4%) candidates simulated**. The remaining 238 (`other_price_or_range`: price ≥$10, decline >30%, or the $120-500 list) have classification fields only, no simulated outcome.

## Headline win rate

**54.1% (205/379)** of simulated candidates would have hit their decay/trailing take-profit target by market close under the live Phase C algorithm.

## Time-of-day breakdown (key number for the 10:30/11:30 expansion decision)

| Bucket | Win rate | n |
|---|---|---|
| Morning (9:30/10:30/11:30 first-seen) | **59.3%** | 305 |
| Afternoon (3:30 first-seen) | **32.4%** | 74 |

Morning candidates won ~27 points more often than afternoon ones — consistent with the account owner's morning-only buying thesis and with the 7/29 report (56.2% vs 17.5%).

## Decline-bucket breakdown (bracket-calibration number)

| Bucket | Win rate | n |
|---|---|---|
| in_bracket (10-25% decline, live Phase B bracket) | **59.1%** | 44 |
| shallow_outside (5-10% decline) | 53.6% | 334 |
| deep_outside (25-30% decline) | 0.0% | 1 (single data point — not meaningful yet) |

The live 10-25% bracket outperforms the shallow 5-10% band by ~5.5 points on this single day's sample; deep_outside has only one observation today (not enough to draw a conclusion — the cumulative decliner_cohort_log/recovery-tracking dataset is the better source for that band over time).

## in_experiment_bracket=true, morning vs. afternoon (the exact population the account owner is deciding on)

| Bucket | Win rate | n |
|---|---|---|
| Morning | **62.2%** | 37 |
| Afternoon | 42.9% | 7 |
| All | 59.1% | 44 |

Small samples (n=37/7), but directionally the same story: morning-bracket buys look meaningfully stronger than afternoon-bracket would.

## Price-bucket breakdown

| Bucket | Win rate | n |
|---|---|---|
| <$1 | 59.8% | 117 |
| $1-3 | 50.4% | 123 |
| $3-10 | 52.5% | 139 |
| $10-120 | n/a | 0 (out of scope — not a data gap, see scope note) |
| $120-500 | n/a | 0 (out of scope — not a data gap, see scope note) |

## Other exit scenarios (data foundation, not yet a report)

- **Liquidation rule (D3a):** evaluable for 298/379 candidates (the 3:30-firing cohort's 74 candidates arrived too late in the day for a 60-min window); triggered for **27** (9.1% of evaluable).
- **EOD flat sell (D3b):** average ROI +1.13% across all 379; 219/379 (57.8%) would have closed the day above their first-seen price.

## Cross-reference with today's real activity

- **7 actual morning-bracket buys** (all `in_bracket`): 5 closed via real take-profit fills (CONX/CONL/COIG/COIA/COIW, +3.0% to +4.2% roi), 2 still open (RBLY, AMZD) at day end.
- **15 skipped candidates** (guardrail a1-a7, all `in_bracket`): simulation says 9/15 would have closed as winners (decay ROI +2.4% to +29.2%), 6 would still be open — small sample, but no single guardrail looks obviously miscalibrated from this alone.

## Suggested EOD liquidations (advisory, not executed)

None — all 7 of today's morning-bracket buys were already closed via take-profit fills before this firing; no buy-today positions remain open to evaluate.

## Cohort tracking

379 new candidates logged to `decliner_cohort_log.csv` (5-30% decline / under $10 band). 150 of 1253 distinct tracked symbols re-quoted this cycle (capped at 150 most-recently-added; prioritized over older/complete symbols) and appended to `decliner_recovery_tracking.csv`.

## Data gap note

`daily_analytics_2026-07-30.csv` was never produced — the 4:30pm ET Phase D firing that day appears to have not run or not committed (no cohort-log rows for 7/30, no analytics files). This is the second such gap after 7/28. Worth the account owner's attention if it recurs.
