# Daily Analytics — 2026-08-13

## Scope
- **448** unique candidates seen across today's 4 firings (9:30/10:30/11:30 ET morning cluster + 3:30 ET afternoon).
- **290 of 448** simulated (64.7%) — scope limited to `in_bracket` / `shallow_outside` / `deep_outside` (under-$10, 5–30% decline) per the 2026-07-29 scope fix. The remaining 158 rows get classification fields only, no simulated outcome: 148 are `other_price_or_range` (price ≥$10, decline >30%, or the $120–500 list — out of scope by design), and 10 scoped symbols (PTLE, ANTA, ZSTK, BKYI, BNR, HTOO, LITB, RPGL, THCH, WBX) had zero real (non-interpolated) bars in their fetch window and are marked `simulated=false, note=no_real_bars`.

## Headline win rate
**59.3%** (172/290) of simulated candidates would have hit their decay/trailing take-profit target by market close.

## Win rate by time bucket (key number for the 10:30/11:30 expansion decision)
| Bucket | Win rate | n |
|---|---|---|
| Morning (9:30/10:30/11:30) | **67.7%** | 232 |
| Afternoon (3:30) | **25.9%** | 58 |

Morning candidates outperform afternoon candidates by a wide margin today — consistent with the account owner's premise for the morning-only buying experiment, and one of the strongest morning/afternoon splits seen in recent daily runs.

## Win rate by decline bucket (bracket-calibration number)
| Bucket | Win rate | n |
|---|---|---|
| in_bracket (10–25% decline, the live Phase B buy bracket) | **76.8%** | 56 |
| shallow_outside (5–10% decline) | 54.5% | 231 |
| deep_outside (25–30% decline) | 100.0% | 3 (too small to read into) |

The live 10–25% bracket clearly outperforms shallow_outside today — supports the current bracket calibration. `deep_outside` n=3 is not meaningful on its own.

## in_experiment_bracket=true, split by time bucket (the exact population the account owner is deciding on)
| Bucket | Win rate | n |
|---|---|---|
| bracket-morning | **82.0%** | 50 |
| bracket-afternoon | 33.3% | 6 |

Strongly supports morning-only buying today, though bracket-afternoon (n=6) is a small sample.

## Win rate by price bucket
| Bucket | Win rate | n |
|---|---|---|
| <1 | 65.1% | 86 |
| 1-3 | 63.4% | 112 |
| 3-10 | 48.9% | 92 |
| 10-120 | n/a | 0 (out of scope) |
| 120-500 | n/a | 0 (out of scope) |

10-120 and 120-500 show n=0 because D3's simulation scope excludes `other_price_or_range` — this is the intended scope reduction, not a data gap.

## Win rate by source list
under120: 59.3% (n=290). 120to500 shows n=0 — that list is data-collection-only and out of D3's simulation scope by design.

## Cross-reference: today's real trades
All 6 of today's real Phase B buys (LUNL, OPEG, ONDL, ONDU, ONDG, IREZ) fell in `in_bracket` as expected, and per the simulated decay/trailing outcome, **all 6 closed (win)** — matching reality exactly: all 6 real positions already hit their take-profit limit and sold same-day (no positions from today's buys remain open). Sample too small (n=6) for guardrail-metric correlation beyond this simple match, but a clean 6/6 real-vs-simulated agreement is a good sanity check on the simulation methodology.

## Liquidation-rule scenario (D3a)
Within `in_experiment_bracket=true` rows, 50 of 56 were evaluable (≥60 min of session remained after first-seen); of those, only 1 would have triggered the same-day 60-minute auto-liquidate rule (never touched breakeven in the first 60 minutes) — a notably low trigger rate today.

## Suggested end-of-day liquidations (advisory, not executed)
No symbols bought today remain open — all 6 of today's Phase B buys (LUNL, OPEG, ONDL, ONDU, ONDG, IREZ) already sold at their take-profit limit earlier today. `results/eod_liquidation_suggestions_2026-08-13.csv` is header-only. Nothing to review.

## Cohort tracking (D8)
1799 distinct symbols now in `decliner_cohort_log.csv` (300 new today, all from the under-$120 5–30%-decline population). `decliner_recovery_tracking.csv` updated with 150 rows this cycle — capped from 1799 distinct symbols, prioritizing the 150 most-recently-added (date range of this cycle's cohort: 2026-08-10 to 2026-08-13).

## Caveats
- Afternoon and deep_outside sample sizes are small (n=58 and n=3 respectively) — do not overstate confidence.
- The decay/trailing simulation assumes an idealized fill at target price the instant a bar's high touches it; real fills may differ (slippage, partial fills).
- 10 symbols excluded from simulation due to no real trade data in their fetch window (7 of these were the 3:30pm-firing group, where thin/halted names are more likely to lack real trades in a short post-first-seen window).
- This report was assembled from 4 background subagents (one per firing's first-seen timestamp) running the identical decay/trailing/liquidation/EOD-sell algorithm in parallel to keep the 4:30pm firing from timing out, per the 2026-07-29 scope-fix note about the 2026-07-28 incident. Final results were spot-checked against 6 real trades (100% match) before publishing.
