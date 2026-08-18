# Daily Analytics — 2026-08-18

**Note on today's firings:** the 3:30pm ET firing appears to have never run today — there are no `under120_down5_20260818T19*Z.csv` / `over120_under500_down5_20260818T19*Z.csv` scan files and no corresponding commit in git history between the 11:30am (`da8686b`) and this 4:30pm analytics firing. Only 9:30/10:30/11:30 ET ran Phase A/B/C today. As a result **every candidate seen today falls in the `morning` time bucket** — there is no afternoon comparison data for this date.

Today's 7 morning buys (CBRG, COHH, BIDG, AMKL, VWAV, CIEG, ASTY) were all closed out before this firing: 6 via the 60-minute never-touched-breakeven auto-liquidation at the 11:30am cycle, and VWAV via its take-profit limit sell. No positions bought today remain open, so `results/eod_liquidation_suggestions_2026-08-18.csv` is header-only.

## Scope

599 unique candidates seen today across all firings (under-$120 and $120-$500 lists combined). Per the 2026-07-29 scope fix, the per-symbol decay/trailing simulation runs only on the in_bracket/shallow_outside/deep_outside population (under $10, 5-30% decline) — **330 of 599 candidates simulated** (55%); the remaining 269 are `other_price_or_range` (price ≥ $10, decline > 30%, or the $120-500 list) and appear in the CSV with `simulated=false` and null scenario columns. Of the 330 scoped candidates, 2 (SFWL, SKK) had zero non-interpolated bars in their window and are excluded from the win-rate denominator below.

## Headline win rate (decay/trailing algorithm, simulated rows only)

**44.5% overall (n=328)**

| Split | Win rate | n |
|---|---|---|
| Morning (9:30/10:30/11:30 first-seen) | 44.5% | 328 |
| Afternoon (3:30 first-seen) | n/a | 0 (3:30 firing did not run today) |

No morning-vs-afternoon comparison is possible today since the 3:30pm firing never ran.

### By decline bucket (bracket-calibration number)

| Bucket | Win rate | n |
|---|---|---|
| in_bracket (10-25% decline, <$10 — live Phase B bracket) | 47.2% | 36 |
| shallow_outside (5-10% decline, <$10) | 44.6% | 289 |
| deep_outside (25-30% decline, <$10) | 0.0% | 3 (very small sample) |

### Price bucket (simulated population only — <$10 by construction)

| Bucket | Win rate | n |
|---|---|---|
| <1 | 57.5% | 87 |
| 1-3 | 48.1% | 106 |
| 3-10 | 33.3% | 135 |
| 10-120 | n/a | 0 (out of simulation scope — see note above) |
| 120-500 | n/a | 0 (out of simulation scope — see note above) |

### in_experiment_bracket morning vs. afternoon (the exact Phase B decision population)

| Time bucket | Win rate | n |
|---|---|---|
| Morning | 47.2% | 36 |
| Afternoon | n/a | 0 (3:30 firing did not run today) |

## Guardrail cross-reference

19 of today's 9:30am skipped-candidates (from `latest_skipped_candidates.csv`) overlap with the simulated population:

| Guardrail | Win rate | n |
|---|---|---|
| a1_spread (spread >8%) | 66.7% | 6 |
| a3_prior_spike | 33.3% | 6 |
| a5_compliance (NASDAQ deficiency flag) | 0.0% | 3 |
| a6_reverse_split_proxy | 100.0% | 4 |

Small samples throughout (n≤6 each) — not enough to judge whether any individual guardrail is over- or under-tuned from one day. Notably a6 (reverse-split proxy) shows a small but perfect win rate today, worth continuing to track since the guardrail's rationale is dilution/delisting risk rather than short-term price action, so a positive short-term ROI doesn't invalidate it.

Today's 7 actual buys, cross-referenced against their own decay/trailing simulation: CBRG, COHH, BIDG, AMKL, CIEG, ASTY all show `closed=false` in the simulation (consistent with them not hitting take-profit before the 60-min auto-liquidation fired in real trading); VWAV shows `closed=true` at 5.51% (consistent with its real take-profit fill).

## Suggested end-of-day liquidations (advisory, not executed)

None — all of today's buys were already closed (via auto-liquidation or take-profit) before this firing, so there are no open same-day positions to evaluate. `results/eod_liquidation_suggestions_2026-08-18.csv` is header-only.

## Cohort tracking

Logged 330 new candidates today to `decliner_cohort_log.csv` (5-30% decline, under $10 — same population as the simulated scope; only 20 of these were symbols never seen before, the rest are repeat appearances of already-tracked symbols). Full cohort log now has 1,903 distinct symbols; this cycle re-quoted the 150 most-recently-added for `decliner_recovery_tracking.csv` (capped per spec).
