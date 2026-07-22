# Daily Analytics — 2026-07-22

542 unique down-5%+ candidates appeared across today's three scan firings (9:36am / 12:37pm / 3:37pm ET): 527 from the under-$120 universe, 15 from the $120–500 data-collection universe. 31 symbols (mostly illiquid/leveraged tickers) had zero non-interpolated bars in their post-first-seen window and are excluded from the win-rate math below. All stats are over the remaining **542 usable candidates** (31 excluded from 573 total appearances).

A mock buy is simulated at first-seen price/time; "closed" (win) means the existing decay/trailing sell logic (decay_pct 0.05→0.04→0.03→0.02 through the day, trailing = 98% of peak-since-first-seen once above the decay floor) would have triggered a fill by 4:00pm ET close.

## Headline

**Overall win rate: 37.1% (201/542)**

## By time bucket (first seen before/after 1:00pm ET)

| Bucket | Win rate | n |
|---|---|---|
| Morning (9:36am + 12:37pm firings) | 47.3% | 364 |
| Afternoon (3:37pm firing) | 16.3% | 178 |

The gap is large and directionally consistent with yesterday's report (56.5% vs 39.1%) — morning entries get far more runway before the 4pm close. Today's gap is even wider, but the afternoon bucket here is entirely the 3:37pm firing (23 minutes of runway), which is a much later/shorter window than yesterday's afternoon bucket — so today's specific magnitude isn't directly comparable to yesterday's.

## By firing (within-day trend)

| Firing (≈ET) | Win rate | n |
|---|---|---|
| 9:36am | 63.8% | 174 |
| 12:37pm | 32.1% | 190 |
| 3:37pm | 16.3% | 178 |

Monotonic decline through the day, as expected — less time for the decay/trailing target to be reached before close.

## By price bucket

| Bucket | Win rate | n |
|---|---|---|
| <$1 | 51.9% | 104 |
| $1–3 | 46.5% | 99 |
| $3–10 | 29.2% | 130 |
| $10–120 | 32.0% | 194 |
| $120–500 | 6.7% | 15 |

Cheap/volatile names again win more often, consistent with yesterday. The $120–500 bucket is notably worse today (6.7% vs yesterday's 41.7%), but n=15 is small — one bad session for the higher-price data-collection universe, not yet a pattern.

## By source list

| List | Win rate | n |
|---|---|---|
| under120 | 38.0% | 527 |
| 120to500 | 6.7% | 15 |

## In-experiment-bracket population (last_price < $10, |pct_change| 15–25%) — the population Phase B would actually buy

This is the key number for the account owner's morning-only buying decision: **13 candidates today** would have qualified for the new bracket had this been a 9:30am firing (12 from morning firings, 1 from the afternoon firing).

| Time bucket | Win rate | n |
|---|---|---|
| Morning | 41.7% | 12 |
| Afternoon | 100.0% | 1 |

n=12 and n=1 are both far too small to draw a conclusion — the afternoon "100%" is a single data point (PAVM). Directionally, the morning bracket win rate (41.7%) sits below the morning bucket's overall rate (47.3%), suggesting the tighter 15–25%/under-$10 bracket isn't obviously outperforming the broader morning population yet, but 12 samples is not enough to say that with confidence. Several more sessions of data are needed before this is a reliable signal.

## Guardrail-metric cross-reference

Not available for today: Phase B has been paused since 2026-07-20 and does not resume (per the account owner's morning-only experiment) until 2026-07-23. `results/latest_trades.csv` and `results/latest_skipped_candidates.csv` still show 2026-07-20 as their most recent date — no real trade or skip data exists for 2026-07-22 to cross-reference against today's simulated outcomes.

## Caveats

- Excludes 31 symbols with no usable (non-interpolated) intraday bars — mostly illiquid/leveraged-ETN tickers that didn't actually trade in their post-first-seen window.
- The $120–500 bucket (n=15) and the in-experiment-bracket splits (n=12, n=1) are all small samples — directionally interesting at most, not statistically reliable.
- Today only had 3 Phase A firings (9:36am/12:37pm/3:37pm ET), not the 4-slot 9:30/10:30/11:30/3:30 cadence described in the current routine — the "morning" bucket here is the 9:36am+12:37pm firings combined, and "afternoon" is the lone 3:37pm firing.
- Simulation uses 5-minute bar highs as the trigger check; it does not model order-fill slippage, partial fills, or liquidity constraints.
