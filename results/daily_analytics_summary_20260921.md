# Daily Analytics — 2026-09-21

## Headline
- 401 unique candidates seen today across the under-$120 and $120-$500 scans (4 firings: 9:30/10:30/11:30/3:30 ET).
- 267 in scope for simulation (decline_bucket in_bracket/shallow_outside/deep_outside per the 2026-07-29 scope fix); 259 actually simulated (8 had no usable real-trade bars in their post-entry window — thin/no-real-trade names).
- Overall simulated win rate: **47.9%** (124/259).

## By time bucket
| bucket | wins | n | win rate |
|---|---|---|---|
| morning (9:30/10:30/11:30) | 103 | 193 | 53.4% |
| afternoon (3:30) | 21 | 66 | 31.8% |

## By decline bucket (bracket calibration)
| bucket | wins | n | win rate |
|---|---|---|---|
| in_bracket (10-25% decline, <$10 — the live buy bracket) | 26 | 36 | 72.2% |
| shallow_outside (5-10% decline, <$10) | 96 | 220 | 43.6% |
| deep_outside (25-30% decline, <$10) | 2 | 3 | 66.7% |

## By price bucket (sim population only)
| bucket | wins | n | win rate |
|---|---|---|---|
| <1 | 47 | 80 | 58.8% |
| 1-3 | 37 | 90 | 41.1% |
| 3-10 | 40 | 89 | 44.9% |
| 10-120 / 120-500 | 0 | 0 | n/a — out of D3 scope by design (price>=10 or the $120-500 list is always decline_bucket=other_price_or_range and never simulated) |

## In-bracket win rate by firing
The direct evidence for whether the 10:30 buy window is pulling its weight and whether 11:30 should also start buying:

| firing | wins | n | win rate |
|---|---|---|---|
| 9:30 (live buy window) | 13 | 16 | 81.2% |
| 10:30 (live buy window) | 8 | 12 | 66.7% |
| 11:30 (no buy) | 2 | 2 | 100.0% (n=2, not meaningful) |
| 3:30 (no buy) | 3 | 6 | 50.0% |

Both live buy windows clear the in-bracket population's overall 72.2% comfortably on the 9:30 side and are in line with it on the 10:30 side; 11:30's sample is too small (n=2) to read anything into extending buying there. 3:30 (never a buy window) is the weakest slice at 50%, consistent with the original morning-only rationale.

## Scope note
Per the 2026-07-29 scope fix, only decline_bucket in (in_bracket, shallow_outside, deep_outside) is simulated. 134 of 401 candidates today were classified other_price_or_range (either priced >=$10 in the under-$120 scan and outside the 5-30% decline band, or from the $120-500 data-collection-only scan) and were skipped entirely — no historicals fetched, simulated=false.

simulated_count=259, total_candidates_seen=401.

## Today's real trades cross-reference
- **9:30 — LVWR** bought at $1.4699 (bracket -10.42%). The simulated decay/trailing algo would have closed this at $1.5201 (+3.4%) by 18:55 UTC. In reality it never touched breakeven within 60 minutes and was auto-liquidated (step 16a2) at $1.3801 — a real loss, illustrating that the simulation (which assumes the position is held through any early dip) doesn't capture the a2 60-minute guardrail's effect on individual real trades even though D3a evaluates that same rule as its own scenario. LVWR's D3a liquidation_triggered/roi is in daily_analytics_20260921.csv.
- **10:30 — FLX** bought at $0.6459 (bracket -20.41%). The simulated algo's target would have closed it at $0.848 (+31.7%). In reality it hit its live resting limit target and filled at $0.6182 for a real win.

## Exit-rule firings (step 16a3 time-stop / drawdown-stop)
None fired today. All four Phase C position-management cycles today (9:30/10:30/11:30/3:30) recorded only `no_change_needed` for the four carried positions (BAK, MRLN, TNON, NUWE) — none crossed the -25% drawdown stop or reached 4 sessions held. The only liquidation event today was LVWR's step 16a2 auto-liquidate (never-touched-breakeven-60min) at the 10:30 firing, which is a distinct rule from 16a3 and not part of this ledger.

## Guardrail saturation check (D6b)
Both buy firings today placed at least one real trade (9:30: LVWR; 10:30: FLX), so the zero-buy diagnostic does not apply. For reference, per-guardrail skip counts among today's bracket-eligible candidates:
- 9:30 (16 candidates reached step 12): a1=2, a2=1, a3=2, a5=2, a6=4, a7=2, a8=3 — no single guardrail exceeded 25% of skips.
- 10:30 (35 candidates reached step 12): a1=7, a2=3, a3=10, a4=1, a5=3, a6=5, a7=2, a8=4 — no single guardrail exceeded 29% of skips.
No saturation pattern observed.

## Manual EOD liquidation suggestions (D7, advisory only — never executed automatically)
Both of today's real buys (LVWR, FLX) had already closed by the time of this 4:30pm ET firing (LVWR via auto-liquidate, FLX via its real target), so there were no still-open same-day positions to evaluate. `results/eod_liquidation_suggestions_20260921.csv` is header-only today.

## Decliner-bracket cohort tracking (D8)
- 267 candidates in the 5-30%-decline/<$10 tracking band were newly logged today (12 of them logged for the very first time ever — fresh sector/industry fundamentals pulled: TULP, EPM, SPCQ, DRK, HIND, MITQ, MAMO, TXMD, MOBI, USGO, LONA, MDXH).
- The full cohort log now spans 2,215 distinct symbols. Recovery tracking quoted the 150 most-recently-added symbols this cycle (uncapped total 2,215) and appended to `decliner_recovery_tracking.csv`; 3 symbols (UNI, WLFI, DOT) could not be quoted (likely delisted).

## Guardrail paper-trade ledger (D10)
38 new paper positions opened today (13 from the 9:30 skip list, 25 from 10:30), after dedup against already-open symbols and same-day duplicates. 21 closed same-day (19 at target, 2 via the 60-min auto-liquidate: SSM -6.05%, FRGT -12.69%); 17 remain open. Separately, 16 pre-existing open positions from earlier days were carried forward on today's daily bar: 5 closed (CDT -25.0% drawdown-stop, WHLR -25.0% drawdown-stop, AEHL -25.0% drawdown-stop, ASTX +8.72% target, FEBO -5.61% time-stop-4-sessions), 10 remain open with updated peak/drawdown, and 1 (YYGH) could not be evaluated — Robinhood reports it `inactive_instruments` with no quote or daily bar over a wide range, likely a delisting/ticker-change; left untouched and flagged for manual review. **28 total paper positions open** across the ledger after today.

**Full-history closed-trade stats by guardrail family** (493 closed rows, rolling up naming variants like `a1_spread`/`a1_spread_gate`/`a1_spread_pct` into 8 families):

| skip_reason | n | win% | mean roi% | median roi% | sum roi% | still open |
|---|---:|---:|---:|---:|---:|---:|
| a1_spread | 103 | 81.6% | 39.961 | 5.045 | 4115.97 | 3 |
| a2_atr | 45 | 57.8% | 90.559 | 4.157 | 4075.17 | 2 |
| a3_prior_spike | 113 | 79.6% | 86.971 | 5.000 | 9827.75 | 4 |
| a4_earnings | 19 | 52.6% | -0.069 | 3.000 | -1.31 | 2 |
| a5_compliance | 73 | 75.3% | 67.285 | 5.000 | 4911.80 | 3 |
| a6_reverse_split_proxy | 77 | 66.2% | 2.404 | 5.000 | 185.11 | 5 |
| a7_thin_liquidity | 26 | 69.2% | 0.029 | 5.000 | 0.75 | 3 |
| a8_leveraged_inverse_etf | 37 | 78.4% | 2.298 | 5.000 | 85.03 | 6 |

On **a6** and **a8** specifically (the two under active review): both show positive average paper outcomes (a6: 66.2% win rate, +2.4% mean roi; a8: 78.4% win rate, +2.3% mean roi), suggesting these guardrails may be excluding trades with a positive average realized result — though see caveat (2) below on optimistic paper fills, and note a6/a8 candidates are disproportionately thin/volatile names where that optimism bias is largest.

**Baseline — actual account performance** (best effort, via `get_realized_pnl`/`get_pnl_trade_history` for account 757884218): total realized P&L since trading began (~2026-06-22) is **-$137.25 across 253 closing trades** (Jun +$63.23/26 trades, Jul -$189.41/136 trades, Aug 22-Sep 21 -$11.07/91 trades). A partial 200-trade sample of that history shows an actual win rate of **~53.0%**, versus paper win rates of 58-82% per guardrail family above. The comparison is directional only — real trades are dollar P&L on small (mostly 1-30) share counts and reflect real spread/slippage/partial-target-fill dynamics the paper ledger doesn't fully replicate.

**Caveats (restated per spec, always apply to the table above):**
1. Guardrails short-circuit in a1->a8 order, so `skip_reason` is only a candidate's FIRST failure. A paper winner blocked by a6 might also have been blocked by a7 or a8 had a6 not existed — cross-check each row's `fundamentals_guardrails_failed` column (a7 is never recomputed here, since it needs its own extra historicals call per candidate).
2. Paper entries fill at the observed price with no spread paid and no slippage, while real buys are MARKET orders — every paper result above is optimistic relative to a real fill, most of all for the thin, low-priced names these guardrails specifically target.
3. Target exits assume a limit fills whenever a bar's high touches it, matching both the live resting GTC limit and D3's own simulation — but it remains an assumption, not an observed fill.
