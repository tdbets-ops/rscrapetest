# Daily Analytics Summary — 2026-07-20

**Overall win rate: 97 / 166 closed (58.4%)**

*Corrected version: this run enforces peak-must-precede-target-check causality (a trailing-stop target for bar i can only be computed from the peak established by bars strictly before i, never from bar i's own high) — an initial pass had a look-ahead bug where a bar's own new high could inflate the trailing target used to evaluate that same bar.*

Candidate universe: all 166 symbols from the single successful 9:30am ET scan firing (`under120_down5_20260720T1336Z.csv`). The 12:30pm and 3:30pm firings were blocked by a balance gate before scanning, and the $120-$500 high-price scan didn't exist yet at 9:30am — so there is no afternoon or 120-500 candidate data today.

## Win rate by price bucket

| price_bucket | closed | n | win rate |
|---|---|---|---|
| <1 | 29 | 41 | 70.7% |
| 1-3 | 22 | 43 | 51.2% |
| 3-10 | 19 | 30 | 63.3% |
| 10-120 | 27 | 52 | 51.9% |
| 120-500 | 0 | 0 | n/a (no candidates today) |

Sub-$1 and $3-10 names closed at a noticeably higher rate than $1-3 and $10-120 names, but with only 5 buckets over a single day this is a weak signal, not a trend.

## time_bucket / source_list

Every one of today's 166 candidates has `source_list=under120` and `time_bucket=morning` (all first-seen at 9:36am ET from the one scan that produced a list). There is no afternoon bucket and no alternate source_list to compare against today, so any morning-vs-afternoon or under120-vs-other split would be fabricated from a single group — that comparison is degenerate today and is intentionally omitted.

## Guardrail-metric correlation notes (n<10 — too small to draw conclusions)

Cross-referencing the 12 actual buys in `latest_trades.csv` against this simulation (7 closed, 5 not closed):

- **volume_spike_ratio**: closed trades averaged 55.5x vs 11.8x for non-closed (n=7 vs n=4 with data) — directionally suggests higher volume spikes associate with hitting target, but is driven by two high outliers (GLXG 209x, MSS 168x) and the sample is far too small to generalize.
- **atr_14_pct**: closed vs non-closed averages were nearly identical (0.193 vs 0.196, n=7 vs n=5) — no visible relationship in today's data.
- **spread_pct**: also nearly identical between groups (0.021 vs 0.022) — no visible relationship.

Across the 24 skipped candidates (treating them as a hypothetical "what if bought" cross-check), 14/24 (58.3%) would have closed under this simulation, roughly in line with the overall 58.4% rate — consistent with skip guardrails (stale-close, wide-spread, ATR, prior-spike) not being strongly predictive of outcome in this single day's sample, but again n=24 is not enough to draw a real conclusion. (EOSER — skipped for `wide_spread_guardrail` — is the one candidate whose outcome flipped from the initial buggy pass: it was previously recorded as not-closed but actually closes at 14:10:00Z via the plain decay target once causality is fixed.)

## Data quality note

18 of 166 symbols (10.8%) had fewer than 2 usable (non-interpolated) 5-minute bars in the session — mostly zero-volume/illiquid tickers (ACMM, CCPX, WEBX, XEML, GEW, MWG, CHNU, SRHR, CNCG, PMMR had 0 usable bars; ONTX, AUGT, MYND, HBNB, MGRT, DLAG, AMKX, SMRX had 1). These are recorded as closed=false with best-effort peak/target values but the underlying data is too thin to trust individually.
