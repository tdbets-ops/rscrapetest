# Daily Analytics — 2026-10-06

**Headline:** 182 of 291 candidates seen today were in-scope for simulation (in_bracket / shallow_outside / deep_outside). Overall simulated win rate **47.8%** (87/182). The standout finding is a large gap **within the live-buy bracket by firing**: 9:30 candidates won 73.9% (17/23) vs 10:30 candidates at just 20.0% (2/10) — small samples, but directionally consistent with today's real fills (9:30: 2/2 winners; 10:30: 1 winner, 1 loss, 1 still open).

## Scope note
Per the 2026-07-29 scope fix, D3/D3a/D3b simulation runs only on decline_bucket in (in_bracket, shallow_outside, deep_outside) — 182 of 291 symbols. The remaining 109 (`other_price_or_range`, mostly the $120-$500 high-price list and bigger/smaller declines) are logged in the daily_analytics CSV with `simulated=false` and null scenario columns, not dropped silently.

## Win rate by time_bucket
| time_bucket | wins | n | win% |
|---|---|---|---|
| morning | 87 | 182 | 47.8% |
| afternoon | 0 | 0 | n/a (11:30 and 3:30 both hit the balance gate today — no Phase A scan data) |

## Win rate by price_bucket (simulated rows only)
| price_bucket | wins | n | win% |
|---|---|---|---|
| <1 | 28 | 54 | 51.9% |
| 1-3 | 31 | 69 | 44.9% |
| 3-10 | 28 | 59 | 47.5% |
| 10-120 / 120-500 | — | 0 | n/a (out of D3 scope by design) |

## Win rate by source_list
| source_list | wins | n | win% |
|---|---|---|---|
| under120 | 87 | 182 | 47.8% |
| 120to500 | 0 | 0 | n/a (all 11 rows fell outside D3 scope — price ≥120) |

## Win rate by decline_bucket (bracket calibration)
| decline_bucket | wins | n | win% |
|---|---|---|---|
| in_bracket (10-25% decline, <$10 — the live buy bracket) | 19 | 33 | 57.6% |
| shallow_outside (5-10% decline, <$10) | 65 | 145 | 44.8% |
| deep_outside (25-30% decline, <$10) | 3 | 4 | 75.0% (n=4, noise) |

## in_experiment_bracket=true, by time_bucket
| time_bucket | wins | n | win% |
|---|---|---|---|
| morning | 19 | 33 | 57.6% |
| afternoon | 0 | 0 | n/a |

## in_experiment_bracket=true, by firing (the direct evidence on the two buy windows)
| firing | wins | n | win% |
|---|---|---|---|
| 9:30 | 17 | 23 | **73.9%** |
| 10:30 | 2 | 10 | **20.0%** |
| 11:30 | 0 | 0 | n/a (balance gate skipped Phase A) |
| 3:30 | 0 | 0 | n/a (balance gate skipped Phase A) |

Caveat: n=23 and n=10 respectively — one session's data. Today's actual fills point the same direction (9:30: LCFY +5.28%, NSTR +5.13%, both hit target; 10:30: YDES +2.09% target hit, SSM -2.05% via a2 60-min liquidation, AGMH still open at -4.4%), but that's only 5 real trades. Not enough on its own to justify narrowing back to a single window — flagging for the account owner to track over more sessions.

candidates seen: 291 total, 182 simulated (62.5%).

## Exit-rule firings (16a3 time-stop / drawdown-stop)
Four positions were liquidated today under step 16a3. All four were pre-existing positions (not bought today); sessions_held counts the trading days since original fill.

| symbol | condition(s) fired | sessions_held | drawdown_pct at exit | realized P&L | exit price | 4:00pm close | holding vs exiting |
|---|---|---|---|---|---|---|---|
| GRML | drawdown_stop_25pct | 3 | -25.93% | -$2.41 (1 sh, $9.2899→$6.88) | $6.88 (market) | $6.33 | exiting beat holding by $0.55/share |
| BXBL | time_stop_4_sessions | 4 | -0.91% | -$0.03 (1 sh, $3.29→$3.26) | $3.26 (market) | $3.30 | holding beat exiting by $0.04/share |
| BSEM | time_stop_4_sessions | 4 | -24.05% | -$0.79 (1 sh, $3.2785→$2.4901) | $2.4901 (market) | $2.51 | holding beat exiting by $0.02/share |
| GLAS | time_stop_4_sessions | 4 | -13.01% | -$0.77 (1 sh, $5.8974→$5.1301) | $5.1301 (market) | $5.15 | holding beat exiting by $0.02/share |

Net: -$4.00 realized across the four. GRML's drawdown stop clearly helped (it kept falling after being cut loose intraday, then only partially recovered). The three time-stop exits were all within a few cents of what holding to the close would have given — consistent with the rule's own stated purpose (preventing indefinite drift, not capturing a specific day's move) rather than a precision timing tool.

Separately, SSM (bought today at 10:30, $1.409) was liquidated via step 16a2 (60-minute never-touched-breakeven, limit-at-bid) at $1.3801, -2.05%. That's outside this section's scope (16a3 only) but noted for completeness.

## Guardrail saturation check (D6b)
Not applicable today — both buy firings (9:30 and 10:30) placed trades (2 and 3 respectively), so there is no zero-buy firing to diagnose.

## EOD liquidation suggestions (advisory only)
No symbols flagged. The only today-bought position still open at close is AGMH (8 sh, avg $0.6116): pct_change_since_buy -4.4% (misses the -5% trigger), not near its session low, though it is trending down over the second half of the session — 2 of 3 required conditions, not flagged. See `eod_liquidation_suggestions_2026-10-06.csv` (header-only).

## Decliner cohort tracking (D8)
182 candidates logged today (5-30% decline, <$10 band — same filter as the D3 scope coincidentally). 6 new sector-tagged (ANTX, GILT, NSTR, PALD, SAIQ, VJET); the rest copied sector/industry from earlier log entries (176 symbols), several of which are still blank because they were first logged before 2026-08-22. Distinct symbol universe is now 2,341.

Recovery tracking: refreshed 145 of the 150 most-recently-added symbols. 5 skipped (no quote returned): PSKY, UNI (not found/delisted), plus the recurring HBAR/ONDO/SYRUP crypto-ticker-leak (these tickers resolve to crypto on the equity quote endpoint and have never worked in this tracker).

## Guardrail paper-trade ledger (D10)
+33 new paper positions opened today (from the 9:30/10:30 skipped_candidates files, deduped against symbols already open and against today's 5 real buys). 17 of the 33 closed same-day (mostly via target hit; GCDT and AUID hit the 25% drawdown stop). 2 pre-existing open positions also closed today on the day-1+ carry-forward pass: MSOX (time_stop_4_sessions, -9.53%) and PWCM (target hit, +7.29%). 37 paper positions remain open across the full ledger (811 closed total).

By guardrail (closed trades, all-time, canonical a1-a8 grouping — medians are the reliable number per the known reverse-split entry-price mean-distortion issue):

| guardrail | n closed | win% | median roi% | open |
|---|---|---|---|---|
| a1 (spread) | 158 | 84.2% | 5.07% | 5 |
| a2 (ATR) | 70 | 61.4% | 4.24% | 1 |
| a3 (prior spike) | 179 | 77.7% | 5.00% | 12 |
| a4 (earnings recency) | 27 | 51.9% | 2.05% | 0 |
| a5 (compliance) | 129 | 76.7% | 4.37% | 5 |
| **a6 (reverse-split proxy)** | **146** | **69.9%** | **4.97%** | **9** |
| a7 (thin liquidity / stale quote) | 46 | 69.6% | 4.54% | 1 |
| a8 (leveraged/inverse ETF) | 55 | 78.2% | 5.00% | 4 |

a6 specifically: 69.9% win rate / +4.97% median on 146 closed paper trades — materially positive, in line with the other guardrails, same conclusion as the 2026-10-05 reading (69.4%/+4.88% on n=144). Still no evidence this guardrail's 15x threshold is miscalibrated; it is not an outlier among the eight.

**Baseline comparison:** every guardrail's paper book is solidly positive (median roi 2-5%, win rates 52-84%), while the account's own realized P&L (get_realized_pnl, span=all) is **-$151.60 across 315 closing trades (-3.66%)**. This gap is the headline caveat below, not a case for loosening any guardrail.

Caveats (apply to every number in this section):
1. Guardrails short-circuit in a1→a8 order; `skip_reason` is only a candidate's *first* failure. A paper winner blocked by a6 might also have failed a7 or a8 had a6 not existed — check `fundamentals_guardrails_failed` (covers a5/a6/a8; a7 is never recomputed here, it needs an extra historicals call per candidate).
2. Paper entries fill at the observed price with no spread or slippage; real buys are market orders. Every paper result is optimistic relative to a real fill, most of all for the thin, wide-spread, sub-$1 names several guardrails specifically target.
3. Target exits assume a limit fills whenever a bar's high touches it — matches how the real resting GTC limit behaves and how D3 simulates, but it's still an assumption.
