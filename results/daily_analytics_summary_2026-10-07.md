# Daily Analytics — 2026-10-07

**Headline:** 428 candidates seen today across 2 scan rounds (9:30 and 10:30 ET only — the 11:30 and 3:30 firings both skipped Phase A on the balance gate, buying_power under $50 both times, so there is no afternoon scan data today). 264 candidates were in scope for simulation (in_bracket/shallow_outside/deep_outside, per the 2026-07-29 scope fix); the other 164 (162 `other_price_or_range` + 2 with zero usable bars: ARBB, GIXI) were not simulated. **Overall simulated win rate: 59.1% (n=264)**, mean EOD-sell return +1.53%.

## Scope note
Simulated population (264) = in_bracket (33) + shallow_outside (229) + deep_outside (2), minus 2 symbols with no usable non-interpolated bars (ARBB, GIXI, both simulated=false). All 28 of today's $120-500 high-price candidates, plus every under-$120 name priced ≥$10 or outside the 5–30% decline band, fall in `other_price_or_range` and are out of scope by design.

## Win rate breakdowns (simulated=true rows only)

| Breakdown | Win rate | n |
|---|---|---|
| Overall | 59.1% | 264 |
| Time bucket: morning | 59.1% | 264 |
| Time bucket: afternoon | — | 0 (no 11:30/3:30 scan data today) |
| Price <1 | 68.9% | 74 |
| Price 1-3 | 62.1% | 95 |
| Price 3-10 | 48.4% | 95 |
| Price 10-120 / 120-500 | — | 0 (out of scope, not simulated) |
| Source: under120 | 59.1% | 264 |
| Source: 120to500 | — | 0 (always `other_price_or_range`, never in scope) |
| Decline bucket: in_bracket | 72.7% | 33 |
| Decline bucket: shallow_outside | 57.2% | 229 |
| Decline bucket: deep_outside | 50.0% | 2 |

### In-bracket (live buy bracket), by firing first seen
| Firing | Win rate | n |
|---|---|---|
| 9:30 | 74.1% | 27 |
| 10:30 | 66.7% | 6 |
| 11:30 | — | 0 (no scan data — balance gate) |
| 3:30 | — | 0 (no scan data — balance gate) |

Both live buy windows' in-bracket population continues to beat the overall simulated win rate (59.1%), consistent with prior days. Small samples (n=27, n=6) — directional evidence only.

## Other scenarios (D3a / D3b)
- **60-minute auto-liquidation proxy:** would have triggered on 23/264 (8.7%) of simulated candidates; those 23 realized a mean -4.38% under that rule, vs the overall simulated mean. Consistent with the rule's intent (catching names that never approach breakeven).
- **EOD market-sell:** 61.7% win rate (n=264), mean +1.53%, slightly better than the decay/trailing-close win rate (59.1%) — comparable to recent days.

## Candidate cross-reference
- 8 real trades placed today (6 at 9:30: BULL, TAOX, SWRD, HTCR, FWDI, XLAB; 2 at 10:30: SQFT, MKDW) — all 8 found in today's candidate population (100% overlap, as expected).
- 33 skipped_candidates rows today (21 at 9:30, 24 at 10:30... wait 21+24=45, dedup to 33 distinct symbols across both firings) — all 33 found in today's candidate population (100% overlap).

## Guardrail saturation check (D6b)
Both buy firings placed real trades today (6 at 9:30, 2 at 10:30), so the zero-buy diagnostic does not apply. For reference, per-guardrail skip counts:
- **9:30** (27 bracket-eligible candidates, 6 bought, 21 skipped): a1_spread 5, a2_atr 4, a3_prior_spike 5, a5_compliance 5, a6_reverse_split_proxy 2.
- **10:30** (26 bracket-eligible candidates, 2 bought, 24 skipped): a1_spread 3, a2_atr 4, a3_prior_spike 8, a5_compliance 7, a6_reverse_split_proxy 1, a7_thin_liquidity 1.

No single guardrail rejected 100% of the candidates reaching it at either firing — no saturation flag.

## Exit-rule firings (D6a)
None today — all four position_mgmt logs show only `no_change_*` reasons; step 16a3 (time-stop/drawdown-stop) did not fire on any open position.

## EOD manual liquidation suggestions (D7, advisory only — no orders placed)
0 flags. Three positions bought today remain open (TAOX, BULL, XLAB), but none crossed the -5% pct_change_since_buy gate: TAOX -3.16%, BULL -0.59%, XLAB -2.93%. File written header-only.

## Decliner cohort tracking (D8)
- 266 new rows appended to `decliner_cohort_log.csv` for today (5-30% decline band, price <$10). 3 symbols were brand-new to the log and required fresh fundamentals lookups (SVM, MAKO, DFH — all Non-Energy Minerals/Precious Metals or Consumer Durables/Homebuilding); the other 263 copied sector/industry from prior history. Distinct-symbol universe is now 2,344 (up from 2,341 yesterday).
- `decliner_recovery_tracking.csv` refreshed 146/150 symbols (capped from the 2,344 distinct universe, most-recently-added 150). 4 skipped as not-found: PSKY, HBAR, ONDO, SYRUP (the HBAR/ONDO/SYRUP crypto-ticker-leak issue is a recurring, already-known artifact).

## Guardrail paper-trade ledger (D10)
- **Today:** 31 new paper positions opened (20 from 9:30, 11 from 10:30; dedup removed cross-firing repeats and 2 symbols already open from earlier dates). 18 closed same-day, all via `paper_target` (none hit the 60-min auto-liquidate or 25% drawdown stop). 13 remain open.
- **Carried forward:** 37 pre-existing open positions (entry dates 10/01–10/06) evaluated; 17 closed today (8 × `paper_drawdown_stop_25pct` at exactly -25.0% each: TGE, SMJF, KNRX, ENLV, HLSQ, AMOD, SHFS, HTCO; 4 × `paper_time_stop_4_sessions` losses averaging -12.0%: SEV, PAVM, ALZN, GYGY; 5 × `paper_target` wins including BIYA +72.7%, plus NIVF/ICU/AAOZ/SDST). 20 remain open.
- **Ledger-wide:** 846 closed rows with a valid roi_pct (1 excluded — YYGH, a pre-existing delisted/no-exit-price row from 2026-08-28), 33 open rows.
- **a6_reverse_split_proxy (guardrail under active review):** now n=150 closed, 68.7% win rate, mean +0.69%, median +4.88%. Still in line with the other current-convention guardrails (a1_spread 83.3%/median 5.02%, a2_atr 70.5%/median 5.00%, a3_prior_spike 80.5%/median 5.00%, a5_compliance 74.2%/median 4.37%, a7_thin_liquidity 65.2%/median 2.83%, a8_leveraged_inverse_etf 78.6%/median 5.02%) — not an outlier justifying a threshold change, consistent with prior readings.
- **Data-quality note:** the ledger carries a number of legacy skip_reason naming variants from earlier sessions (e.g. `a1_spread_guardrail`, `a2_atr_guardrail`, `a3_prior_spike_guardrail`, `a7_thin_liquidity_stale_quote`, `a8_leveraged_etf`) that appear to be older spellings of the same current-convention guardrails. These were left as-is (no historical rows rewritten) but mean a literal groupby undercounts each guardrail's true sample size. Flagging for a future cleanup rather than fixing now.
- **Baseline:** actual realized P&L since inception (get_realized_pnl, span=all) is **-$150.78 / 321 trades (-3.61%)**, versus every current-convention guardrail's paper win rate and median roi being positive. As in prior summaries: paper entries fill at the observed price with no spread/slippage (optimistic vs. real market-order fills), target exits assume any bar touching the limit fills, and skip_reason reflects only each candidate's FIRST guardrail failure in a1→a8 order (see `fundamentals_guardrails_failed` for the fuller picture on a5/a6/a8 overlap). None of this is a case for loosening any guardrail on today's data.

## Caveats
- All of today's data is from the morning cluster (9:30/10:30 ET); the balance gate suppressed both Phase A and any analytics signal for 11:30/3:30, so no afternoon comparison is possible today.
- Small samples throughout (in-bracket n=33 total, n=6 at 10:30) — treat as directional, not conclusive.
- Price-bucket and source-list breakdowns both collapse to a single meaningful category by construction (see Scope note) — this is expected, not a data loss.
