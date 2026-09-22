# Daily Analytics — 2026-09-22

## Headline
- 382 unique candidates seen today across the under-$120 and $120-$500 scans (4 firings: 9:30/10:30/11:30/3:30 ET).
- 247 in scope for simulation (decline_bucket in_bracket/shallow_outside/deep_outside); 240 actually simulated (7 had no usable real-trade bars in their post-entry window — ONDO/SYRUP/LDO not_found on Robinhood historicals, plus GRAN/GMHS/VSA/KFFB which had zero real trades all session, all-interpolated bars).
- Overall simulated win rate: **56.25%** (135/240).

## By time bucket
| bucket | wins | n | win rate |
|---|---|---|---|
| morning (9:30/10:30/11:30) | 124 | 191 | 64.9% |
| afternoon (3:30) | 11 | 49 | 22.4% |

## By decline bucket (bracket calibration)
| bucket | wins | n | win rate |
|---|---|---|---|
| in_bracket (10-25% decline, <$10 — the live buy bracket) | 21 | 29 | 72.4% |
| shallow_outside (5-10% decline, <$10) | 112 | 209 | 53.6% |
| deep_outside (25-30% decline, <$10) | 2 | 2 | 100.0% |

## By price bucket (sim population only)
| bucket | wins | n | win rate |
|---|---|---|---|
| <1 | 55 | 80 | 68.8% |
| 1-3 | 36 | 72 | 50.0% |
| 3-10 | 44 | 88 | 50.0% |
| 10-120 / 120-500 | 0 | 0 | n/a — out of D3 scope by design |

## In-bracket win rate by firing
The direct evidence for whether the 10:30 buy window (live since 2026-08-25) is pulling its weight and whether 11:30 should follow:

| firing | wins | n | win rate |
|---|---|---|---|
| 9:30 (live buy window) | 13 | 17 | 76.5% |
| 10:30 (live buy window) | 5 | 6 | 83.3% |
| 11:30 (no buy) | 1 | 1 | 100.0% (n=1, not meaningful) |
| 3:30 (no buy) | 2 | 5 | 40.0% (n=5, small sample) |

Both live buy windows clear the overall in-bracket rate (72.4%) comfortably. 10:30's small sample (n=6) is running hot (83.3%) but on too few observations to be conclusive on its own — combined with 2026-09-21's 66.7% (n=12) 10:30 reading, the two days together (11/18 = 61.1%) still support 10:30 pulling its weight. 11:30's n=1 today (and n=2 yesterday) remains far too small to justify extending live buying there. 3:30 stays the weakest slice (40% today, 50% yesterday), consistent with the morning-only buy-window rationale.

## Scope note
Only decline_bucket in (in_bracket, shallow_outside, deep_outside) is simulated. 135 of 382 candidates today were classified other_price_or_range (priced >=$10 in the under-$120 scan outside the 5-30% band, or from the $120-500 data-collection-only scan) and were skipped entirely — no historicals fetched, simulated=false.

simulated_count=240, total_candidates_seen=382.

## Today's real trades cross-reference
Both buy firings placed real trades today: **9:30 — DAVA** (bought $2.1199, bracket -23.7%); **10:30 — XNDU, SUPX, CELZ, NUR, AXG**.
- SUPX and CELZ already closed today via their real GTC target limit sells (7.75 and 0.886) — ordinary target hits, not exit-rule events.
- DAVA, XNDU, NUR, AXG remain open at the 4:30pm mark; see D7 below (none flagged for liquidation).
- CELZ is classified in_bracket in D4 and its simulated decay/trailing algo closed it at +5.0% by 13:45 UTC — consistent with its real target hit at $0.886 (+2.65% from the 9:30 skip-list entry_price, +8.1% from the eventual 10:30 buy fill).
- AXG's simulated D3 run shows it never rallied above its first-seen price all session (peak_price_reached == first_seen_price), matching its still-underwater real position.

## Exit-rule firings (D6a — step 16a3 time-stop / drawdown-stop)
Three step-16a3 exits fired today, all via the local every-10-minute Phase C loop (not visible in the cloud-committed position_mgmt CSVs — confirmed from Robinhood's own order history, cross-checked against get_equity_quotes for the ~4pm close):

| symbol | condition | sessions_held | drawdown_pct at exit | realized P&L | 4:00pm close | holding-vs-exiting |
|---|---|---|---|---|---|---|
| MRLN | drawdown_stop_25pct | 4 | -26.27% | -$1.2558 (-26.27%) | $1.925 | Exit was WORSE than holding (~-19.4% if held) — MRLN partially recovered after being stopped out |
| BAK | time_stop_4_sessions | 4 | -6.51% | -$0.234 (-6.51%) | $1.67 | Exit slightly BEAT holding (~-7.15% if held) |
| TNON | time_stop_4_sessions | 4 | -23.85% | -$1.0834 (-23.85%) | $3.47 | Roughly a wash — exit marginally worse (~-23.6% if held) |

MRLN is the notable case: the -25% drawdown stop fired against a same-day partial recovery, locking in a deeper loss (-26.3%) than where the stock ended the day (-19.4% from cost). This is a real cost of the hard drawdown-stop rule and worth tracking if it recurs.

## Guardrail saturation check (D6b)
Both buy firings today placed real trades (9:30: DAVA; 10:30: XNDU/SUPX/CELZ/NUR/AXG), so the zero-buy diagnostic does not apply. Per-guardrail skip counts among today's bracket-candidates reaching step 12:
- 9:30 (16 candidates reached step 12: 15 skipped + 1 bought): a1_spread=4 (26.7%), a3_prior_spike=3 (20.0%), a6_reverse_split_proxy=3 (20.0%), a7_thin_liquidity=2 (13.3%), a2_atr=1 (6.7%), a4_earnings_recency=1 (6.7%), a5_compliance=1 (6.7%).
- 10:30 (23 candidates reached step 12: 18 skipped + 5 bought): a3_prior_spike=5 (27.8%), a6_reverse_split_proxy=4 (22.2%), a2_atr=3 (16.7%), a5_compliance=3 (16.7%), a1_spread=1 (5.6%), a7_thin_liquidity=1 (5.6%), a8_leveraged_inverse_etf=1 (5.6%).

No single guardrail exceeded 28% of skips at either firing — **no saturation pattern observed**, no malfunction flag.

## Manual EOD liquidation suggestions (D7, advisory only — never executed automatically)
Evaluated the 4 same-day buys still open (DAVA, XNDU, NUR, AXG; SUPX and CELZ already closed via real target hits, excluded). None met all three SUGGEST_LIQUIDATE criteria (pct_change_since_buy <= -5% AND near_low AND trend_down):

| symbol | pct_change_since_buy | near_low | trend_down | flagged |
|---|---|---|---|---|
| DAVA | +0.24% | No | Yes | No |
| XNDU | -3.15% | Yes | Yes | No (pct threshold not met) |
| NUR | -4.13% | No | Yes | No (pct threshold not met) |
| AXG | +0.71% | No | No | No |

`results/eod_liquidation_suggestions_20260922.csv` written with all 4 rows (no_flag rationale for each). **These are advisory only — no orders were placed, cancelled or modified.**

## Decliner-bracket cohort tracking (D8)
- 251 candidates in the 5-30%-decline/<$10 tracking band were newly logged today (20 logged for the very first time ever — fresh sector/industry fundamentals pulled: SNDQ, SBC, ERIC, HERE, AIFU, SMX, BTLN, SKHN, USAX, USGG, MAX, NRDS, KRKR, NNVC, NXDR, CLRB, PXS, OWLS, plus ONDO/SYRUP which returned not_found and were logged with blank sector/industry).
- The full cohort log now spans 2,235 distinct symbols. Recovery tracking quoted the 150 most-recently-added symbols this cycle (uncapped total 2,235) and appended to `decliner_recovery_tracking.csv`; 6 symbols (DOT, NHIC, ONDO, SYRUP, UNI, WLFI) could not be quoted (likely delisted — DOT/UNI/WLFI persist from 2026-09-21's unquotable list).

## Guardrail paper-trade ledger (D10)
24 new paper positions opened today (12 from the 9:30 skip list, 12 from 10:30), after dedup against already-open symbols (ENLV, RUBI, PAAI already had open rows — skipped) and against symbols actually bought today (CELZ excluded even though it appeared in the 9:30 skip list before being bought at 10:30). 18 closed same-day (14 at target, 4 via the 60-min auto-liquidate: TULP -2.28%, FTFT -5.88%, NFE -10.67%, AIFU -6.11%); 6 remain open (RIME, IRAB, DCX, IPW, GIPR, SNDQ). Separately, 27 pre-existing open positions from earlier days were carried forward on today's daily bar: 8 closed at target or time-stop (CODX +2.04%, LITZ +248.21%, ASTY +7.39%, MGLD +6.50%, VNCE +8.82%, PAAI +31.65%, BIAF +5.75% all via paper_target; PLAY -6.02% via paper_time_stop_4_sessions), 19 remain open with updated peak/drawdown, and YYGH (entry 2026-08-28) again returned `not_found` on today's daily-bar fetch — still inactive/delisted, left untouched and flagged for manual review as in prior sessions. **26 total paper positions open** across the ledger after today. Note: LITZ's +248% is a genuine outlier (real large price move from its 09-16 entry, not a data error) that meaningfully skews the a8 family's mean below.

**Full-history closed-trade stats by guardrail family** (519 closed rows, rolling up naming variants like `a1_spread`/`a1_spread_gate` and `a8_leveraged_inverse_etf`/`a8_leveraged_inverse_etf_guardrail` into 8 families):

| skip_reason | n | win% | mean roi% | median roi% | sum roi% | still open |
|---|---:|---:|---:|---:|---:|---:|
| a1_spread | 107 | 82.2% | 38.654 | 5.023 | 4135.97 | 3 |
| a2_atr | 48 | 60.4% | 85.705 | 4.338 | 4113.82 | 2 |
| a3_prior_spike | 120 | 80.0% | 82.120 | 5.000 | 9854.44 | 4 |
| a4_earnings | 22 | 50.0% | -0.141 | 1.438 | -3.11 | 0 |
| a5_compliance | 74 | 74.3% | 66.232 | 5.000 | 4901.14 | 4 |
| a6_reverse_split_proxy | 81 | 67.9% | 2.496 | 5.000 | 202.15 | 5 |
| a7_thin_liquidity | 27 | 66.7% | -0.198 | 5.000 | -5.35 | 4 |
| a8_leveraged_inverse_etf | 40 | 80.0% | 8.641 | 5.000 | 345.64 | 4 |

On **a6** and **a8** (the two under active review): a6 remains modestly positive (67.9% win rate, +2.5% mean roi, in line with 2026-09-21's 66.2%/+2.4%), and a8 continues to look strong (80.0% win rate, +8.6% mean roi, up from 78.4%/+2.3% yesterday) — both still suggest these guardrails may be excluding trades with a positive average realized result, though see caveat (2) below on optimistic paper fills, and note a6/a8 candidates are disproportionately thin/volatile names where that optimism bias is largest.

**Baseline — actual account performance** (via `get_realized_pnl`/`get_pnl_trade_history` for account 757884218): total realized P&L since trading began (~2026-06-22) is **-$139.34 across 258 closing trades** (Jun +$52.80/32 trades, Jul -$178.98/130 trades, Aug 23-Sep 22 -$13.16/96 trades). A 200-trade sample of that history shows an actual win rate of **53.0%** (identical to 2026-09-21's read), versus paper win rates of 50-82% per guardrail family above. The comparison is directional only — real trades are dollar P&L on small (mostly 1-30) share counts and reflect real spread/slippage/partial-target-fill dynamics the paper ledger doesn't fully replicate.

**Caveats (restated per spec, always apply to the table above):**
1. Guardrails short-circuit in a1->a8 order, so `skip_reason` is only a candidate's FIRST failure. A paper winner blocked by a6 might also have been blocked by a7 or a8 had a6 not existed — cross-check each row's `fundamentals_guardrails_failed` column (a7 is never recomputed here, since it needs its own extra historicals call per candidate).
2. Paper entries fill at the observed price with no spread paid and no slippage, while real buys are MARKET orders — every paper result above is optimistic relative to a real fill, most of all for the thin, low-priced names these guardrails specifically target.
3. Target exits assume a limit fills whenever a bar's high touches it, matching both the live resting GTC limit and D3's own simulation — but it remains an assumption, not an observed fill.
