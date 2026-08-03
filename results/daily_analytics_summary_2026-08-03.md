# Daily Analytics — 2026-08-03

**Scope note:** 405 unique candidates seen across today's 4 firings (9:30/10:30/11:30/3:30 ET); only the under-$10, 5–30%-decline population (`in_bracket`/`shallow_outside`/`deep_outside`) is simulated per the 2026-07-29 scope fix — **220 of 405 simulated** (54%). The remaining 185 (`other_price_or_range`: price ≥$10, decline >30%, or the $120–500 list) appear in the CSV with `simulated=false` and null scenario columns.

## Headline win rate (decay/trailing algorithm, simulated population only)

| Segment | Win rate | Wins/N |
|---|---|---|
| **Overall** | 61.8% | 136/220 |
| Morning (9:30/10:30/11:30) | **67.7%** | 126/186 |
| Afternoon (3:30) | **29.4%** | 10/34 |

Morning candidates continue to sharply outperform the lone afternoon firing — consistent with prior days and the rationale for the morning-only Phase B experiment.

## Win rate by decline bucket (bracket calibration)

| Bucket | Win rate | Wins/N |
|---|---|---|
| in_bracket (10–25% decline, <$10 — live buy bracket) | 69.6% | 39/56 |
| shallow_outside (5–10% decline, <$10) | 58.9% | 96/163 |
| deep_outside (>25–30% decline, <$10) | 100.0% | 1/1 (n=1, not meaningful) |

The live 10–25% bracket continues to outperform the shallower 5–10% band by ~11pp, though `deep_outside` has essentially no sample this cycle.

## in_experiment_bracket morning vs. afternoon (the exact decision population)

| | Win rate | Wins/N |
|---|---|---|
| Morning | **73.5%** | 36/49 |
| Afternoon | 42.9% | 3/7 (small sample) |

This is the key number for the 10:30/11:30 expansion decision: in-bracket morning candidates won nearly 3-in-4 today; the afternoon in-bracket sample is thin (n=7) and shouldn't move the decision much on its own, but it's directionally consistent with morning outperforming.

## Win rate by price bucket

| Bucket | Win rate | Wins/N |
|---|---|---|
| <$1 | 63.6% | 42/66 |
| $1–3 | 66.7% | 44/66 |
| $3–10 | 56.8% | 50/88 |
| $10–120 | n/a | n=0 (out of scope this cycle) |
| $120–500 | n/a | n=0 (out of scope this cycle) |

## Today's actual trades (5 morning-bracket buys, all cross-referenced)

All 5 buys (AXTL, AXTX, FOMG, MIC, SKYQ) simulated as **winners** in the decay/trailing model and, per the position-management log, all 5 actually take-profit-filled today. Average simulated ROI at close: **+5.1%**.

## Guardrail correlation (skipped candidates, small samples — do not over-read)

Of today's 30 skipped bracket-eligible candidates, 30 had simulate-able outcomes:

| Guardrail | N | Win rate |
|---|---|---|
| a1_spread | 12 | 83.3% |
| a2_atr | 4 | 100.0% |
| a3_prior_spike | 1 | 100.0% |
| a4_earnings_recency | 1 | 0.0% |
| a5_compliance | 5 | 100.0% |
| a6_reverse_split_proxy | 6 | 100.0% |
| a7_thin_liquidity | 1 | 0.0% |

Most guardrail-skipped candidates would have "won" under the decay/trailing sim anyway — expected, since these guardrails target tail risk (compliance flags, reverse splits, stale quotes) rather than typical-case ROI. Sample sizes are all under 15; no conclusions should be drawn about loosening any guardrail from this alone.

## Alternate exit scenarios (new 2026-07-29 scenarios, informational)

- **60-minute auto-liquidate rule**: triggered on 14 of 173 evaluable candidates (8.1%) — i.e., most candidates touched breakeven within their first 60 minutes today.
- **Flat EOD market sell**: average ROI across 212 candidates with a valid EOD price was **+3.8%**, below the decay/trailing algorithm's realized ROI on winners — consistent with the decay/trailing approach capturing upside that a flat EOD sell would leave on the table.

## Suggested end-of-day liquidations (advisory, not executed)

None. No symbols bought today remain open — all 5 morning-bracket buys (AXTL, AXTX, FOMG, MIC, SKYQ) already take-profit-filled earlier today. The only open position (AMIX) was bought 2026-07-30 and is out of scope for same-day EOD advisory review.

## Cohort tracking

Decliner cohort log: 220 new candidates logged today (5–30% decline, <$10 band), bringing the cumulative log to 2,505 rows across 1,311 distinct symbols. Recovery tracking refreshed today for the 150 most-recently-added distinct symbols (full history now spans back to prior weeks); all 150 quoted successfully with no delisted/error symbols.
