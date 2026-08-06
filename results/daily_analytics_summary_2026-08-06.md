# Daily Analytics — 2026-08-06

## Scope note
547 unique candidates seen today across all 4 firings (9:30/10:30/11:30/3:30 ET). Per the 2026-07-29 scope fix, the per-symbol decay/trailing simulation runs only on the `in_bracket`/`shallow_outside`/`deep_outside` population (under $10, 5–30% decline) — **336 of 547 candidates** fell in that scope. Of those, **6 symbols had zero real (non-interpolated) trades** in their simulation window (DSS, MOGU, AHG, ZSTK, CHNR, BBLG) and were excluded, leaving **330 simulated**. The remaining 211 candidates (price ≥ $10, decline > 30%, or the $120–500 list) are logged with their classification only (`simulated=false`).

## Headline win rate
**168 / 330 = 50.9%** of simulated candidates would have closed at a decay/trailing profit target by market close.

## Win rate by time bucket (key number for the 10:30/11:30 expansion decision)
| Time bucket | Wins | N | Win rate |
|---|---|---|---|
| Morning (9:30/10:30/11:30) | 136 | 239 | **56.9%** |
| Afternoon (3:30) | 32 | 91 | **35.2%** |

Morning continues to outperform afternoon by a wide margin, consistent with prior days — supports keeping the buy experiment morning-only for now.

## Win rate by decline bucket (core bracket-calibration number)
| Decline bucket | Wins | N | Win rate |
|---|---|---|---|
| in_bracket (10–25%, live buy bracket) | 33 | 51 | **64.7%** |
| shallow_outside (5–10%) | 131 | 273 | 48.0% |
| deep_outside (25–30%) | 4 | 6 | 66.7% (n=6, too small to act on) |

The live 10–25% bracket continues to outperform the shallower 5–10% band by a wide margin, supporting the current bracket choice. deep_outside's 66.7% is on only 6 candidates — noise, not signal.

## in_experiment_bracket=True, split by time (the exact population the buy-expansion decision hinges on)
| Time bucket | Wins | N | Win rate |
|---|---|---|---|
| Morning | 28 | 45 | 62.2% |
| Afternoon | 5 | 6 | 83.3% (n=6 — do not act on this) |

Afternoon's bracket win rate looks higher but the sample is tiny (6 candidates); the broader shallow/deep buckets above (n=91 afternoon overall) are the more reliable signal, and those still favor morning.

## Win rate by price bucket
| Price bucket | Wins | N | Win rate |
|---|---|---|---|
| <$1 | 50 | 103 | 48.5% |
| $1–3 | 58 | 112 | 51.8% |
| $3–10 | 60 | 115 | 52.2% |
| $10–120 | — | 0 | n/a — out of simulation scope by construction (decline_bucket requires price < $10) |
| $120–500 | — | 0 | n/a — out of simulation scope by construction |

## Win rate by source list
under120: 168/330 = 50.9%. 120to500: n=0 in the simulated population (that list is data-collection-only and always falls in `other_price_or_range`, never in the scoped decline buckets).

## Today's actual trades (bracket, 9:30am firing)
Both of today's buys — **TJGC** and **BJDX** — fell in `in_bracket` and are simulated winners (decay ROI 5.08% and 5.40%). BJDX actually closed today at its 1.16 limit sell (confirmed via order history). TJGC remains open as of this run; note the simulation's "closed_at 13:35" precedes TJGC's actual fill time (13:43) because the simulation enters at the scan's first-seen price/time, not the live market-order fill price — a known limitation when comparing simulated vs. actual outcomes for the same symbol.

## Guardrail cross-reference (skipped candidates vs. simulated outcome)
23 of today's 33 skipped candidates fell in the simulated population; **23/33 = 69.7%** would have closed as winners had the guardrails not skipped them (small sample, all guardrail types pooled — do not read this as "guardrails are too strict," several of the misses were meaningful: RCON -12.3% EOD, YYAI -13.7% EOD, MGN -12.4% EOD).

## Cohort tracking (D8)
336 new symbols logged to `decliner_cohort_log.csv` today (5–30% decline, under $10 band — same population as the simulated set by construction). Distinct symbols in the full log: **1,530**. This cycle quoted the **150 most-recently-added** distinct symbols for `decliner_recovery_tracking.csv` (capped per the routine's 150-symbol-per-cycle limit); all 150 quotes succeeded with no delisted/errored symbols.

## Suggested EOD liquidations (advisory, not executed)
None flagged. TJGC (today's only still-open buy) is down only 3.7% from cost and not near its intraday low (current $3.66 vs. low-since-buy $3.31) — does not meet the -5%/near-low/trend-down threshold. See `eod_liquidation_suggestions_2026-08-06.csv`.
