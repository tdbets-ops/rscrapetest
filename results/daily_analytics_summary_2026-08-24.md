# Daily Analytics — 2026-08-24

## Scope note: no afternoon firing today

The 3:30pm ET firing did not run today (no scan/position-mgmt files exist at the 19:30Z timestamp). The account's 5 open positions (TPET, PLTD, CRIS, ENVX, SAGT) were manually liquidated via agentic-placed limit sell orders around 15:50 UTC (11:50am ET), apparently coinciding with when the new step 16a3 time-stop/drawdown-stop rule was added to this routine. As a result, **all of today's candidate data comes from the 9:30/10:30/11:30am ET firings only** — there is no afternoon data point today, so every morning-vs-afternoon breakdown below is morning-only by necessity, not because afternoon underperformed.

## Headline

- **469** unique candidates seen today (across under-$120 and $120-500 lists, all 3 morning firings de-duplicated by first sighting).
- **307 of 469** simulated (in_bracket / shallow_outside / deep_outside population only, per the 2026-07-29 scope reduction). 3 additional in-scope symbols (BMGL, ELOG, NICM) had zero real (non-interpolated) trades all session and could not be simulated. The remaining 162 are `other_price_or_range` (price ≥ $10, decline > 30%, or the $120-500 list — 30 symbols) and were not simulated by design.
- **Overall win rate (decay/trailing strategy): 51.5% (158/307)**

## Win rate by time bucket

| Bucket | Win rate | n |
|---|---|---|
| Morning | 51.5% | 307 |
| Afternoon | n/a — no 3:30pm firing today | 0 |

## Win rate by decline bucket (bracket calibration)

| Bucket | Win rate | n |
|---|---|---|
| in_bracket (10-25% decline, <$10 — live Phase B bracket) | **77.1%** | 35 |
| shallow_outside (5-10% decline, <$10) | 48.2% | 272 |
| deep_outside (25-30% decline, <$10) | n/a — 0 candidates today | 0 |

The in_bracket population continues to outperform shallow_outside by a wide margin (77.1% vs 48.2%), consistent with prior days.

## In-experiment-bracket win rate, morning vs afternoon

| Bucket | Win rate | n |
|---|---|---|
| Morning | 77.1% | 35 |
| Afternoon | n/a — no 3:30pm firing today | 0 |

No new evidence either way today on the morning-vs-afternoon question, purely because there was no afternoon firing.

## Win rate by price bucket

| Bucket | Win rate | n |
|---|---|---|
| <$1 | 63.6% | 88 |
| $1-3 | 47.5% | 99 |
| $3-10 | 45.8% | 120 |
| $10-120 | n/a — not simulated (out of scope per 2026-07-29 D3 change) | 0 |
| $120-500 | n/a — not simulated (out of scope per 2026-07-29 D3 change) | 0 |

## Win rate by source list

| Bucket | Win rate | n |
|---|---|---|
| under120 | 51.5% | 307 |
| 120to500 | n/a — data-collection only, never simulated | 0 |

## ROI figures

- Average decay-close ROI (closed positions only): **+5.15%**
- Average EOD-sell ROI (all simulated candidates): **+1.75%**
- 60-minute liquidation-rule scenario: triggered for 33/307 simulated candidates, average ROI on those **-2.56%** (consistent with the rule catching genuine non-starters, at the cost of foreclosing any later recovery).

## Cross-reference with today's real trades/guardrails

Zero buys were placed today (0 rows in `latest_trades.csv`) — every in-bracket candidate that reached Phase B's guardrail chain at the 9:30am firing was skipped. Of the 23 bracket-eligible candidates logged in `skipped_candidates_20260824T1338Z.csv` (guardrails a1/a2/a3/a4/a5), **19 would have closed as winners and 4 as losers (SUGP, AMCI, SKK, RGNX) under the decay/trailing simulation** — an 82.6% simulated win rate on this guardrail-skipped subset, somewhat above the in_bracket population's overall 77.1%. **Caveat: n=23, and this only tells us the guardrails didn't obviously improve average win rate this particular morning — several of these guardrails (a5 compliance, a3 prior-spike) exist to cap tail risk rather than to raise the average, so a small same-day sample can't validate or invalidate them.**

## Cohort tracking

- 310 candidates appended to `decliner_cohort_log.csv` today (5-30% decline, <$10 band). Distinct cohort pool now **1,954** symbols.
- Sector/industry captured for **18** genuinely-new symbols this run — this is the first Phase D firing since the sector/industry columns were added on 2026-08-22 (no Phase D ran 2026-08-22/23, both non-trading days); all pre-existing rows have blank sector/industry as expected.
- 150 most-recently-added of 1,954 distinct symbols re-quoted for `decliner_recovery_tracking.csv` (capped per the ~150/cycle rule).

## Suggested end-of-day liquidations (advisory, not executed)

None — no buys were filled today, so there are no open same-day positions to evaluate. `eod_liquidation_suggestions_2026-08-24.csv` is header-only.

## Operational note

Today's four prior firings (9:30/10:30/11:30am + this 4:30pm run) had been committing to a detached HEAD that was never merged into `master` or pushed to `origin` — 6 commits going back to 2026-08-21 3:30pm were at risk of being lost on container recycle. This was found and fixed during this firing: `master` has been fast-forwarded and pushed to `origin/master`. Worth checking that future firings are committing on a proper branch.
