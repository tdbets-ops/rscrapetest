# Daily Analytics — 2026-09-11

**Headline: 43.1% win rate (62/144 simulated)** over the sampled in-scope population. Morning 52.4% vs afternoon 19.5%. In-bracket 53.5% vs shallow-outside 39.0% (sampled).

## Scope note (read before the numbers below)

332 unique symbols were seen today across all four firings (9:30/10:30/11:30/3:30). Per the routine's standing D3 scope rule, only `in_bracket` + `shallow_outside` + `deep_outside` symbols are simulation-eligible (231 of 332; the rest are `other_price_or_range` and get `simulated=false`).

231 in-scope symbols is modestly over the "well under 200" compute-budget guidance, so — consistent with yesterday's approach — I simulated:
- **All 43 `in_bracket` symbols** (the primary bracket-calibration population — not sampled, fully covered).
- **The 1 `deep_outside` symbol** (full census — small population today).
- **A random sample of 100 of the 187 `shallow_outside` symbols** (seed=42), leaving 87 `shallow_outside` symbols unsimulated this cycle.

Total simulated: **144 of 231 in-scope (62%)**, 144 of 332 total candidates seen (43%). All shallow-outside win-rate numbers below should be read as a 100-symbol sample of a 187-symbol population, not a census. The in-bracket numbers (n=43) are a full census and are the load-bearing numbers for the bracket-calibration question.

## Win rate breakdown (simulated population, n=144)

| Cut | n | Wins | Win rate |
|---|---|---|---|
| Overall | 144 | 62 | 43.1% |
| Morning (9:30/10:30/11:30) | 103 | 54 | 52.4% |
| Afternoon (3:30) | 41 | 8 | 19.5% |
| decline_bucket = in_bracket | 43 | 23 | 53.5% |
| decline_bucket = shallow_outside (sample) | 100 | 39 | 39.0% |
| decline_bucket = deep_outside | 1 | 0 | 0.0% |
| price <1 | 46 | 21 | 45.7% |
| price 1-3 | 42 | 18 | 42.9% |
| price 3-10 | 56 | 23 | 41.1% |

(10-120 / 120-500 buckets are out of D3 scope by definition — `other_price_or_range` symbols are never simulated, so those price buckets have no simulated rows, and the 3 `120to500`-source-list symbols today (DOCN, QLYS, LEU) were all `other_price_or_range` and thus unsimulated. Noted per the routine's own instruction rather than silently dropped.)

## in_experiment_bracket=true, by time_bucket and by firing

This is the number that directly bears on "should 11:30 start buying" and "was the 10:30 window worth adding":

| Cut | n | Wins | Win rate |
|---|---|---|---|
| Morning | 34 | 21 | 61.8% |
| Afternoon | 9 | 2 | 22.2% |

| Firing | n | Wins | Win rate |
|---|---|---|---|
| 9:30 (live buy window) | 20 | 13 | **65.0%** |
| 10:30 (live buy window) | 11 | 8 | **72.7%** |
| 11:30 (not a buy window) | 3 | 0 | 0.0% |
| 3:30 (not a buy window) | 9 | 2 | 22.2% |

**Reading this with real caution on sample size:** both live buy windows look strong today — 9:30 at 65.0% (n=20) and 10:30 actually *outperforming* 9:30 at 72.7% (n=11), a reversal from a prior day's weak 10:30 read. With both days' 10:30 samples still under 15, this is not yet a verdict either way — the number needs to keep accumulating before treating 10:30 as confirmed or disconfirmed. 11:30 (n=3, 0.0%) continues to look weak on a still-tiny sample; 3:30 (n=9, 22.2%) is consistent with prior afternoon underperformance. Nothing here argues for extending live buying to 11:30 or 3:30.

## Candidates seen vs simulated vs traded

- Total candidates seen today: 332 (both scan lists, all 4 firings)
- Simulated: 144 (see scope note)
- Real trades placed today: 5 (2 at 9:30: CVV, SGLY; 3 at 10:30: KPTI, SMR, TLS) — CVV, SGLY, SMR, TLS fall inside today's simulated sample.
- Skipped candidates (bracket-eligible, failed a guardrail): 42 rows across both buy firings (17 at 9:30, 25 at 10:30) — 32 of those symbols also appear in today's simulated sample.

## Exit-rule firings (step 16a3)

No exit-rule (16a3) or auto-liquidate (16a2) firings occurred today — grep across all of today's `position_mgmt_20260911T*.csv` files for `liquidat|time_stop|drawdown_stop` returned no matches. Per the routine's standing rule, this section is omitted beyond this one-line confirmation.

## Guardrail saturation check (D6b)

Both buy firings today placed trades (9:30: 2, 10:30: 3), so the mandatory zero-buy diagnostic does not apply today. Skip-reason tally across both skipped_candidates files (n=42): a1_spread 8, a5_compliance 7, a6_reverse_split_proxy 7, a3_prior_spike 6, a7_ask_gap_exceeded 6, a2_atr 4, a8_leveraged_etf 3, a4_earnings_recency 1. No single guardrail saturated (rejected 100% of candidates that reached it, or dominated the list) on either firing.

## Guardrail paper-trade ledger

New today: 31 day-0 positions opened from today's 2 skipped_candidates files (24 closed intraday, 7 still open). Carried forward: 20 pre-existing open positions re-evaluated on today's daily bar (YYGH remains confirmed inactive/delisted — `inactive_instruments` API error on the historicals lookup — left untouched again this cycle) — 12 closed today (7 via `paper_time_stop_4_sessions`: IFBD, GYGY, ADBG, ASAN, RITR, IRAB, VACI; 2 via `paper_drawdown_stop_25pct`: HUBC, FGL; 3 via `paper_target`: UZX, CIFC, GOSS), 9 remain open with updated peak/drawdown tracking.

**Data anomaly flagged — GOSS:** GOSS's ledger `entry_price` (0.1429, recorded 2026-09-10) is inconsistent with today's and yesterday's fetched market data by roughly 100x (yesterday's day-bar and today's day-bar both show GOSS trading in the $10-13 range). This produces a nonsensical simulated `paper_target` exit at +8500% ROI, which was **not** suppressed (the ledger's own rule restricts in-place edits to open-row field updates, and there is no authority here to rewrite `entry_price`), but this is the same class of data-entry/unit bug flagged in prior days' summaries (e.g. OMH/VBIO/TXXD outliers) — not a real outcome. It is excluded from the "clean" mean below and flagged here for the account owner to investigate (most likely: GOSS underwent an actual reverse split between 09-10 and today, and the recorded entry_price was never adjusted).

### Guardrail paper-trade ledger — closed trades by skip_reason (full ledger to date, n=358 closed)

Grouped by guardrail number (a1-a8), consolidating minor skip_reason label variants collected over time (e.g. `a1_spread`, `a1_spread_gate`, `a1_spread_guardrail`, `a1_spread_pct` all roll into `a1`).

| guardrail | n closed | win rate | median roi% | mean roi% (outliers >\|50%\| excluded) | n outliers | still open |
|---|---|---|---|---|---|---|
| a1 (spread) | 68 | 83.8% | 5.00 | 2.44 | 1 | 4 |
| a2 (ATR) | 33 | 54.5% | 3.44 | -7.00 | 1 | 1 |
| a3 (prior-spike) | 81 | 84.0% | 5.00 | 1.59 | 2 | 3 |
| a4 (earnings-recency) | 14 | 42.9% | -0.57 | -1.27 | 0 | 1 |
| a5 (compliance) | 57 | 73.7% | 5.00 | -0.72 | 4 | 2 |
| a6 (reverse-split proxy) | 58 | 67.2% | 5.00 | -2.49 | 2 | 2 |
| a7 (thin-liquidity/stale-quote/ask-gap) | 20 | 60.0% | 3.54 | -1.54 | 0 | 2 |
| a8 (leveraged-ETF) | 27 | 88.9% | 5.00 | 3.08 | 0 | 1 |

**Data-quality note:** as in prior days, raw (non-excluded) means are wildly skewed by a handful of implausible outlier rows (roi_pct in the hundreds or thousands of percent — e.g. today's new outlier GOSS +8500%, plus pre-existing MGN +689%, OMH +4297%, TXXD +1001%, VBIO +2812%, CALC +396%, BRTX +1671%, FTFT +198%/+149%), spanning firings from 2026-08-27 through today. These look like the same class of data-entry/unit bug documented elsewhere in this routine rather than real outcomes — a paper position cannot legitimately return 40x+ under this strategy's capped-target exit logic. These historical closed rows were not altered (the ledger's own rule restricts in-place updates to open rows only), so they remain in `guardrail_paper_trades.csv` as-is; the table above reports median (robust to this) and a mean that excludes rows with |roi_pct|>50 as a sanity-checked alternative. **Recommend the account owner investigate and, if confirmed erroneous, decide how to handle these historical rows** — they are not something this firing is authorized to correct.

With that caveat: a8 (leveraged-ETF) again has the strongest clean record (88.9% win, n=27), and a3 (prior-spike) and a1 (spread) both show strong win rates (84.0%/83.8%) with modest positive clean means. a2 (ATR) shows the weakest clean mean (-7.00) and is roughly a coin flip on win rate (54.5%). a6 (reverse-split proxy) remains the guardrail under active review per its 2026-08-25 note — its clean mean stays negative (-2.49) though its win rate (67.2%) is middling-positive, consistent with prior days.

**Caveats (required every time this section appears):**
1. Guardrails short-circuit in a1→a8 order, so `skip_reason` is only a candidate's FIRST failure. Cross-check `fundamentals_guardrails_failed` (records every one of a5/a6/a8 it would have failed). a7 is not recomputed retroactively. Removing a guardrail is never a clean "we would have won this" claim.
2. Paper entries fill at the observed price with no spread/slippage — real buys are MARKET orders, so paper results are OPTIMISTIC, most so for wide-spread/thin/low-priced names.
3. Target exits assume a limit fills whenever a bar's high touches it — matches how the real GTC limit and D3 behave, but is still an assumption.

**Baseline for comparison:** the account's actual realized P&L over the trailing 30 days (get_realized_pnl, span=month): **-$7.70 total (-1.86% total rate of return)** across roughly 30 closing-trade-days with a mix of small gains (mostly +$0.20-$2.00 days) and a few larger losing days (-$4.21 on 08-18, -$4.13 on 08-24, -$2.70 on 09-08). Today's own realized slice so far: +$0.45 (2 trades). The guardrail-blocked paper population's clean win rates (54-89% across guardrails) look considerably stronger than this baseline in isolation, but per caveat #2 above the comparison is optimistic for the paper side — a guardrail is only worth loosening if its blocked population would plausibly have beaten the baseline net of realistic slippage, not merely if it's paper-positive.

## EOD liquidation suggestions (D7, advisory only)

Four positions bought today remain open (CVV, SGLY, SMR, TLS). Two meet the SUGGEST_LIQUIDATE bar (needs pct_change_since_buy ≤ -5% AND near_low AND trend_down, all three):
- **SMR**: -5.23% since buy, near its intraday low (8.605 vs low 8.57), and trending down (second-half average close below first-half). **SUGGEST_LIQUIDATE.**
- **TLS**: -5.51% since buy, at its intraday low (3.94), and trending down. **SUGGEST_LIQUIDATE.**
- **CVV**: -3.85% — does not meet the -5% decline threshold.
- **SGLY**: -11.29% and trending down, but NOT near its intraday low (currently 3.3% above low_since_buy) — does not meet all three conditions.

These are advisory data points only; no order was placed or modified based on this section. See `results/eod_liquidation_suggestions_2026-09-11.csv` for full detail including current resting target prices (from the latest position_mgmt log).

## Decliner-bracket cohort tracking (D8, pure data logging)

Appended 231 new rows to `decliner_cohort_log.csv` for today's under-$120, 5-30%-decline candidates (the wider tracking band, independent of the live 10-25% buy bracket). Of these, 221 symbols already existed elsewhere in the log — their sector/industry were copied forward rather than re-fetched. The remaining 10 were genuinely new to the log; fundamentals were fetched for all 10 (one, WLFI, returned `not_found` and was logged with empty sector/industry).

Recovery tracking (`decliner_recovery_tracking.csv`): the full cohort log now holds 2,155 distinct symbols (by earliest date_seen). Per the routine's cap, this cycle covered the 150 most-recently-added symbols (earliest date_seen from 2026-08-27 through 2026-09-11) rather than the full 2,155. Of those 150, 146 returned live quotes and got a recovery row appended; 4 (WLFI, DOT, ENA, OP) returned no quote data and were skipped as likely delisted/invalid.

## Commit summary

- 332 candidates seen, 144 simulated, 43.1% win rate (9:30 65.0%, 10:30 72.7%, 11:30 0.0%, 3:30 22.2% — in_experiment_bracket win rates by firing)
- 2 EOD liquidation suggestions (SMR, TLS)
- No exit-rule firings today
- Cohort tracking: 2,155 distinct symbols in the full log, 10 newly sector-tagged today
- Paper ledger: +31 opened, 36 closed today (24 new day-0 + 12 carried-forward), 16 open at end of day
