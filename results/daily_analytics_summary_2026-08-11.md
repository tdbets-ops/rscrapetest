# Daily Analytics — 2026-08-11

**436** unique candidates seen across today's 4 firings (9:30/10:30/11:30/3:30 ET). **313 of 436 simulated** (in_bracket / shallow_outside / deep_outside only, per the 2026-07-29 scope fix — the other 123 rows are `other_price_or_range`: price ≥ $10, decline > 30%, or the $120-500 list, and carry `simulated=false` with null scenario columns).

## Headline win rate (decay/trailing simulation, simulated rows only)

**58.1% win rate** (182/313 closed before 4:00pm ET close).

## Time-bucket breakdown (key number for the 10:30/11:30 expansion decision)

| Bucket | Win rate | n |
|---|---|---|
| Morning (9:30/10:30/11:30) | 65.7% | 233 |
| Afternoon (3:30) | 36.3% | 80 |

Morning continues to clearly outperform afternoon, consistent with the account owner's original rationale for the morning-only buying experiment.

## Decline-bucket breakdown (bracket-calibration number)

| Bucket | Win rate | n |
|---|---|---|
| in_bracket (10-25% decline, <$10 — the live Phase B bracket) | 69.6% | 56 |
| shallow_outside (5-10% decline, <$10) | 54.8% | 252 |
| deep_outside (25-30% decline, <$10) | 100.0% | 5 (n too small to read into) |

## Price-bucket breakdown

| Bucket | Win rate | n |
|---|---|---|
| <$1 | 65.6% | 93 |
| $1-3 | 59.3% | 91 |
| $3-10 | 51.9% | 129 |
| $10-120 | n=0 (out of D3 scope per 2026-07-29 change — not a data gap) | 0 |
| $120-500 | n=0 (out of D3 scope per 2026-07-29 change — not a data gap) | 0 |

## Source list

All 313 simulated rows are `under120` — the $120-500 list is data-collection-only and structurally excluded from the decline-bucket population (price ≥ $10 fails the D2 bucket test).

## In-experiment-bracket morning vs. afternoon (the exact population the account owner is deciding on)

| Bucket | Win rate | n |
|---|---|---|
| Bracket + morning | 73.6% | 53 |
| Bracket + afternoon | 0.0% | 3 (very small sample — one bad afternoon outcome swings this to 0%, do not over-read) |

This mirrors the pattern seen on prior days (morning bracket win rate has run 70-75% recently vs. afternoon in the 0-33% range) — continues to support keeping Phase B morning-only for now.

## Cross-reference with today's actual trades

All 6 of today's actual bracket buys (CRIS, MGIH, BKYI, GLND, NAII, NIOG) fall in the simulated population and all show `closed=true` in the decay/trailing simulation (5 at ~5.2-5.3% ROI matching the decay-only path, NIOG at 2.1%). In live trading, MGIH/BKYI/GLND/NAII/NIOG have in fact already been sold via their resting take-profit orders (only COHH/PLTD/CRIS remain open per `get_equity_positions`), consistent with the simulation. CRIS is a known discrepancy: the simulation enters at the 13:30 UTC scan price ($2.9951), while the real fill was at 13:54 UTC ($3.0858) — different entry points produce different decay targets, so the simulation's "closed" outcome for CRIS should not be read as contradicting its real still-open status. n=6, too small for guardrail-metric correlation beyond this note.

## Suggested end-of-day liquidations (advisory, not executed)

**1 flagged: CRIS**, down 24.4% since its 13:54 UTC fill ($3.09 → $2.335), sitting at its intraday low (current price within 2% of the day's low of $2.29) and still trending down (second-half-of-day average close $2.362 vs. first-half $2.549). This is advisory only — no order was placed or cancelled. The account owner reviews and decides manually. See `results/eod_liquidation_suggestions_2026-08-11.csv`.

## Cohort tracking

313 new candidates (5-30% decline, <$10) logged to `decliner_cohort_log.csv` today (now 1,720 distinct symbols total across all days). 150 most-recently-added distinct symbols re-quoted and appended to `decliner_recovery_tracking.csv`; all 150 quoted successfully (no delistings/errors).

## Caveats

- Small samples throughout (deep_outside n=5, bracket-afternoon n=3) — treat those cells as noise, not signal.
- The simulation enters at each symbol's first-seen scan price/time, not any actual fill — it is a hypothetical "bought when first spotted" backtest, and will diverge from real trades that filled later/at a different price (see CRIS above).
- 8 symbols (QIS, GRNQ, ATHR, BDRX, BRBI, FEDU, THCH, XTLB) returned zero real (non-interpolated) bars in their simulation window and are recorded with null outcome fields rather than a forced win/loss.
