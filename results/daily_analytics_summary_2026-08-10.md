# Daily Analytics — 2026-08-10

## Headline

- **539** unique candidates seen today across 4 firings (9:30/10:30/11:30/3:30 ET).
- **357 of 539 simulated** (in_bracket/shallow_outside/deep_outside population only, per the 2026-07-29 scope fix). The remaining 182 are `other_price_or_range` (price ≥ $10, decline > 30%, or the $120–500 list) and are not simulated.
- **Overall win rate (simulated population): 49.3%** (176/357 closed via the decay/trailing algorithm by market close).

## Win rate by time bucket (key number for the 10:30/11:30 expansion decision)

| Bucket | Win rate | n |
|---|---|---|
| Morning (9:30/10:30/11:30) | **56.2%** | 274 |
| Afternoon (3:30) | **26.5%** | 83 |

Morning candidates continue to outperform afternoon by a wide margin, consistent with prior days — supports keeping the buy experiment morning-only for now.

## Win rate by decline bucket (bracket calibration)

| Bucket | Win rate | n |
|---|---|---|
| in_bracket (10–25% decline, <$10) | **66.7%** | 42 |
| shallow_outside (5–10% decline, <$10) | 47.0% | 313 |
| deep_outside (>25–30% decline, <$10) | 50.0% | 2 (too small to read anything into) |

The live 10–25% bracket continues to outperform the shallow-decline population by a wide margin (66.7% vs 47.0%), supporting the current bracket choice over widening back toward 5%.

## in_experiment_bracket, morning vs afternoon (the exact population being decided on)

| Bucket | Win rate | n |
|---|---|---|
| Morning | **72.2%** | 36 |
| Afternoon | 33.3% | 6 |

Small afternoon sample (n=6), but directionally consistent with the broader morning/afternoon split — reinforces keeping Phase B morning-only.

## Win rate by price bucket

| Bucket | Win rate | n |
|---|---|---|
| <$1 | 57.7% | 97 |
| $1–3 | 56.8% | 118 |
| $3–10 | 37.3% | 142 |
| $10–120 | n/a (not simulated, per scope fix) | 0 |
| $120–500 | n/a (not simulated, per scope fix) | 0 |

Note: the $10–120 and $120–500 rows show n=0 by design — the 2026-07-29 scope fix restricts simulation to the under-$10, 5–30%-decline population. This is not a data gap.

## Source list

All 357 simulated rows are from the under-$120 scan (357/357); the $120–500 list contributed 10 candidates today but none fall in the simulated scope (all classified `other_price_or_range`).

## Cross-reference: today's actual Phase B trades

| Symbol | Decay/trailing outcome | EOD-sell ROI |
|---|---|---|
| SSM | **Closed** (+11.5% via decay target) | +12.8% |
| STIM | **Closed** (+3.3% via decay target) | +1.2% |
| COHH | Not closed | **-15.8%** |
| VIVS | Auto-liquidated at 60min (never touched breakeven), realized ~-6.6% | -10.6% (would-be, hypothetically) |

2 of 4 today's buys closed profitably via the take-profit ladder; COHH is currently well underwater and flagged below for advisory liquidation. Sample size (n=4) is too small to draw guardrail-specific conclusions.

## Suggested end-of-day liquidations (advisory, not executed)

- **COHH**: down 15.0% since buy, trading at/near today's low ($8.02 vs low $8.02), second-half-of-day average close ($8.48) below first-half average ($8.73) — actively continuing to decline, not just sitting flat. Account owner should review and decide whether to manually close.
- STIM: down only 1.1% since buy, not near its low, second half trending up slightly — no liquidation suggestion (normal GTC hold).

## Scope note

Per the 2026-07-29 scope fix, only the in_bracket/shallow_outside/deep_outside population (357 of 539 candidates seen today) is simulated — this avoids the 2026-07-28 incident where an unscoped full-population simulation failed to complete in one firing. The `other_price_or_range` rows (182 today) still appear in `daily_analytics_2026-08-10.csv` with their classification fields but `simulated=false` and null scenario columns.

## Cohort tracking

- 361 new candidates (5–30% decline, <$10) logged to `decliner_cohort_log.csv` today.
- 1,664 distinct symbols now tracked across the full cohort log; this cycle quoted the **150 most-recently-added** (capped) and appended to `decliner_recovery_tracking.csv`. 1 symbol (BONK) had no equity quote (likely delisted/unlisted) and was skipped.
