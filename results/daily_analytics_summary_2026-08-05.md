# Daily Analytics — 2026-08-05

**Scope note (per 2026-07-29 SCOPE FIX):** 525 unique candidates seen today across all 4 firings (9:30/10:30/11:30/3:30 ET). Per-symbol simulation (D3/D3a/D3b) runs only on the under-$10, 5-30%-decline population (`in_bracket`/`shallow_outside`/`deep_outside`) — **337 of 525 candidates simulated** (188 `other_price_or_range` rows, i.e. price ≥$10, decline >30%, or the $120-500 list, appear in the CSV with `simulated=false` and no scenario columns). Of the 337 simulated, 1 (ATGL) had zero non-interpolated bars all session and is excluded from win-rate stats below — **336 usable outcomes**.

## Headline win rate (decay/trailing algo, simulated_count=336)

**52.1% overall win rate.**

## Win rate by time bucket (KEY number for the 10:30/11:30 buying-expansion decision)

| Time bucket | Win rate | n |
|---|---|---|
| Morning (9:30/10:30/11:30) | **59.8%** | 244 |
| Afternoon (3:30) | **31.5%** | 92 |

## Win rate by decline bucket (core bracket-calibration number)

| Decline bucket | Win rate | n |
|---|---|---|
| in_bracket (10-25% decline, <$10 — the live buy bracket) | **76.9%** | 39 |
| shallow_outside (5-<10% decline, <$10) | 48.8% | 295 |
| deep_outside (>25-30% decline, <$10) | 50.0% | 2 (too small to read anything into) |

## in_experiment_bracket morning vs afternoon (exact population the account owner is deciding on)

| Time bucket | Win rate | n |
|---|---|---|
| Morning | **80.6%** | 31 |
| Afternoon | 62.5% | 8 |

Small samples (n=31/n=8), but directionally consistent with the account owner's morning-performs-better thesis and with the overall time-bucket split above.

## Win rate by price bucket

| Price bucket | Win rate | n |
|---|---|---|
| <$1 | 54.1% | 109 |
| $1-3 | 55.4% | 101 |
| $3-10 | 47.6% | 126 |
| $10-120 | n/a — 0 simulated (out of scope per D5 scope note) |
| $120-500 | n/a — 0 simulated (out of scope per D5 scope note) |

## Win rate by source list

under120: 52.1% (336). (120to500 list is entirely `other_price_or_range` — not simulated.)

## Cross-reference: today's actual trades and skipped candidates

Only 2 actual buys today (both 9:30am, both `in_bracket`): **FTHM** closed as a decay-winner (+5.01% decay-close ROI, +4.88% EOD), **BANL** still open at EOD (-0.38% EOD, essentially flat/near-breakeven, never triggered take-profit or the 60-min liquidation rule).

Of the 20 bracket-eligible candidates skipped by Phase B guardrails (a1-a6) today: **16/20 would have closed as decay-winners**, 4/20 (JUNS, SPPL, FUSE, NAGE) never closed. One notable case: **SUJA** (skipped at a4 earnings-recency, reported earnings 1 day prior) *did* close as a fast decay-winner (+5.0%) early in the day but its price then collapsed -31.9% by EOD — a reminder that earnings-adjacent volatility can look like a quick win on the decay algo while still carrying elevated tail risk the guardrail is designed to avoid. n is far too small (1 case) to treat as more than an anecdote.

## Suggested end-of-day liquidations (advisory, not executed)

None flagged today. BANL (the only symbol bought today still open) does not meet the near-low/trend-down criteria (current price is +0.5% above its intraday low band, not sitting at/near session lows) — file `eod_liquidation_suggestions_2026-08-05.csv` written header-only.

## Cohort tracking

Cohort log appended 337 new candidate rows today (2026-08-05). Distinct symbols across the full cohort log now: 1443. Recovery tracking updated for the 150 most-recently-added distinct symbols this cycle (149 quoted successfully, LDO returned no quote / likely delisted and was skipped).
