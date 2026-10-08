# Daily Analytics — 2026-10-08

## Headline

- 702 unique candidates seen across today's 4 firings (9:30/10:30/11:30/3:30 ET); 496 simulated (scope excludes `other_price_or_range`, and 1 symbol, ENA, dropped from the nominal 497-symbol scope on a persistent `not_found` from historicals — treated as unsimulated).
- **Overall simulated win rate: 39.3% (195/496 closed by 4:00pm ET).**
- Both buy firings placed trades (3 @9:30, 1 @10:30) — no zero-buy diagnostic needed.

## Scope note

Per the 2026-07-29 scope fix, only `decline_bucket` in (`in_bracket`, `shallow_outside`, `deep_outside`) is simulated — `other_price_or_range` (price ≥ $10, or decline outside 5-30%) is skipped entirely (no historicals fetch), cutting the simulated population from 702 to 496 and avoiding the 2026-07-28 timeout failure mode.

## By time bucket (n=496)

| bucket | win rate | n |
|---|---|---|
| morning (9:30/10:30/11:30) | 43.8% | 372 |
| afternoon (3:30) | 25.8% | 124 |

## By price bucket (n=496; 10-120/120-500 not simulated by design)

| bucket | win rate | n |
|---|---|---|
| <1 | 41.9% | 148 |
| 1-3 | 32.9% | 170 |
| 3-10 | 43.3% | 178 |

## By source list

All 496 simulated symbols are `under120` — no `120to500` symbol fell inside the simulation scope today (120to500 is data-collection only; Phase B never buys from it).

## By decline bucket (core bracket-calibration number)

| bucket | win rate | n |
|---|---|---|
| in_bracket (the live 10-25% buy bracket) | 49.0% | 49 |
| shallow_outside (5-10%) | 38.1% | 446 |
| deep_outside (25-30%) | 100.0% | **1 — not meaningful** |

## In-bracket win rate BY FIRING (the key number for the 10:30/11:30 question)

| firing | win rate | n | flag |
|---|---|---|---|
| 9:30 | 45.2% | 31 | only reasonably-sized sample |
| 10:30 | 40.0% | 5 | n<10 — noise |
| 11:30 | 75.0% | 4 | n<10 — noise |
| 3:30 | 55.6% | 9 | n<10 — noise |

Only the 9:30 bucket has a sample size worth reading anything into. The 10:30/11:30/3:30 in-bracket breakdowns are each single-digit counts and should **not** be used to justify expanding or contracting buy windows yet — more days of data are needed before this number means anything for 10:30 (now live) or 11:30 (still not a buy window).

## D6b — Guardrail saturation check

Both buy firings placed trades, so no zero-buy diagnostic applies. No single guardrail rejected 100% of candidates reaching it at either firing:

**9:30 (28 candidates reached a guardrail):** a3_prior_spike 25.0%, a1_spread 17.9%, a2_atr 17.9%, a5_compliance 17.9%, a8_leveraged_etf 10.7%, a6_reverse_split_proxy 7.1%, a4_earnings_recency 3.6%.

**10:30 (31 candidates reached a guardrail):** a3_prior_spike 29.0%, a5_compliance 22.6%, a1_spread 19.4%, a2_atr 16.1%, a6_reverse_split_proxy 9.7%, a4_earnings_recency 3.2%.

No guardrail repeated a saturation pattern across the two firings either.

## D6a — Exit-rule (16a3) firings

None today. All three position closures (CYPH +2.2%, GP +3.5%, CDLX +5.3%) and the earlier AGMH close were ordinary take-profit target fills, not drawdown-stop or time-stop liquidations.

## D7 — Manual EOD liquidation suggestions (advisory only — nothing executed)

Only one symbol bought today remains open: **CRBU** (entry 0.5522, now 0.4813, -12.8%). It clears the decline threshold but fails `near_low` (current price is ~6.6% above its session low of 0.4514, past the 1.02× near-low gate) — so it is **not flagged**. `results/eod_liquidation_suggestions_2026-10-08.csv` is header-only.

## D8 — Decliner-bracket cohort tracking

- Appended 497 new cohort rows (5-30% band, price<10, under120 list only) — none previously logged today.
- Sector/industry: 21 symbols were first-ever appearances (fresh fundamentals fetch); 476 copied from history.
- Recovery tracking: distinct symbol universe is now 2,365. Refreshed the 150 most-recently-added (capped); 146 successfully quoted, 4 skipped as not-found/delisted (PSKY, HBAR, ONDO, SYRUP). Two legitimate large outliers: GOW +143.8% (9 days), BTLN +710.5% (16 days) — real penny-stock volatility, not computation errors.

## D10 — Guardrail paper-trade ledger

Pure simulation, never a real order. Ledger now has **934 rows: 882 closed, 52 open**.

Today: 54 new paper positions opened (26 @9:30, 28 @10:30; 5 skip-file rows excluded as duplicates of already-open AVAT/DFLI/AMTD). 24 closed same-day (18 target, 5 auto-liquidate-60min, 1 drawdown-stop). Of 33 pre-existing open positions carried forward, 11 closed (4 time-stop-4-sessions, 3 drawdown-stop — AVAT/DFLI/AMTD, 4 target incl. AIXI +53.8%). LESL remains stuck on no-data/delisted quotes and is left open, flagged for manual follow-up.

**Per-guardrail closed-trade stats:**

| code | n | win% | mean% | median% | sum% | still open |
|---|---|---|---|---|---|---|
| a1 | 178 | 80.9% | 28.30 | 5.05 | 5037.7 | 3 |
| a2 | 79 | 64.6% | 187.56 | 5.00 | 14817.4 | 8 |
| a3 | 197 | 78.2% | 50.26 | 5.00 | 9901.0 | 16 |
| a4 | 27 | 51.9% | 0.21 | 2.05 | 5.5 | 2 |
| a5 | 139 | 74.8% | 41.03 | 4.27 | 5703.3 | 10 |
| **a6** | **154** | **68.8%** | **0.78** | **4.97** | **120.6** | **9** |
| a7 | 47 | 70.2% | -0.68 | 5.00 | -31.7 | 1 |
| a8 | 59 | 74.6% | 5.93 | 5.00 | 349.7 | 3 |

**a6 (reverse-split proxy, under live review):** 154 closed, 68.8% win rate, median +4.97% — in line with the other current guardrails, not an outlier worth loosening on today's reading.

Means for a2/a3/a5/a8 are heavily skewed by a handful of extreme-outlier microcap spikes (hundreds to thousands of percent) — **median is the robust comparison metric.**

**Caveats (apply to every number above):**
1. Guardrails short-circuit in a1→a8 order, so `skip_reason` is only a candidate's *first* failure — a paper winner blocked by a6 might also have failed a7 or a8 had a6 not existed (a7 is never cross-checked; `fundamentals_guardrails_failed` covers a5/a6/a8 only).
2. Paper entries fill at the observed price with no spread/slippage — every paper result is optimistic relative to a real market-order fill, most of all for the thin, wide-spread names these guardrails specifically target.
3. Target exits assume a limit fills whenever a bar's high touches it, matching live GTC behavior and D3's own assumption, but it's still an assumption.

**Baseline — actual realized P&L since inception** (`get_realized_pnl`, span=all): **-$151.13 / 327 trades (-3.59%)**, versus every guardrail bucket's positive paper win rate and median above. Every guardrail's blocked population would, on paper, have beaten what the account actually achieved — a reason for caution before loosening any of them, not a reason to tighten further.
