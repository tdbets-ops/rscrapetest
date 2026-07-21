# Daily Analytics — 2026-07-21

338 unique down-5%+ candidates appeared across today's three scan firings (9:30/12:30/3:30 ET): 326 from the under-$120 universe, 12 from the new $120-$500 data-collection universe. 21 symbols (all under-$120, mostly illiquid/halted names) had zero non-interpolated bars in their post-first-seen window and are excluded from the win-rate math below. All stats are over the remaining **317 usable candidates**.

A mock buy is simulated at first-seen price/time; "closed" (win) means the existing decay/trailing sell logic (decay_pct 0.05→0.04→0.03→0.02 through the day, trailing = 98% of peak-since-first-seen once above the decay floor) would have triggered a fill by 4:00pm ET close.

## Headline

**Overall win rate: 46.7% (148/317)**

## By time bucket (first seen before/after 12:30pm ET)

| Bucket | Win rate | n |
|---|---|---|
| Morning | 56.5% | 138 |
| Afternoon | 39.1% | 179 |

Morning entries had noticeably more runway before the 4pm close and a materially higher win rate. Expected direction, but the gap (56.5% vs 39.1%) is large enough to be a real signal, not noise, given n>100 in both buckets.

## By price bucket

| Bucket | Win rate | n |
|---|---|---|
| <$1 | 68.1% | 69 |
| $1–3 | 52.9% | 70 |
| $3–10 | 38.4% | 73 |
| $10–120 | 33.3% | 93 |
| $120–500 | 41.7% | 12 |

Win rate falls steadily as price rises through the under-$120 range — cheap/volatile names bounce back into the decay/trailing target far more easily than higher-priced, lower-volatility names. The $120–500 bucket (n=12, small sample) sits in between the $3–10 and $10–120 buckets rather than continuing the downtrend, but n=12 is too small to draw a real conclusion from.

## By source list (under-$120 vs the new $120-$500 data-collection universe)

| List | Win rate | n |
|---|---|---|
| under120 | 46.9% | 305 |
| 120to500 | 41.7% | 12 |

At n=12, the $120–500 universe's win rate is statistically indistinguishable from the under-$120 universe's — there is not yet enough data to say whether widening the strategy above $120 would help or hurt. Needs many more sessions of data collection before drawing a conclusion.

## Guardrail-metric cross-reference

Not available for today: Phase B (initial buy) has been paused since 2026-07-20, so `results/latest_trades.csv` and `results/latest_skipped_candidates.csv` contain no rows from 2026-07-21 — both files' most recent timestamps are 2026-07-20 (pre-pause). No real guardrail-metric data exists to cross-reference against today's simulated win/loss outcomes.

## Caveats

- Excludes 21 symbols with no usable (non-interpolated) intraday bars — mostly illiquid/leveraged-ETN tickers that didn't actually trade in their post-first-seen window.
- The $120–500 bucket and source-list split are both n=12 — directionally interesting but not statistically reliable yet.
- Simulation uses 5-minute bar highs as the trigger check; it does not model order-fill slippage, partial fills, or liquidity constraints.
