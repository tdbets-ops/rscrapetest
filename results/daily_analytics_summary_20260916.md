# Daily Analytics Summary — 2026-09-16

> **BACKFILL NOTICE:** This report was generated on the morning of **2026-09-17** as a backfill of the **4:30pm ET / 20:30 UTC firing on 2026-09-16**, whose original run was lost when the container running it was restarted mid-task before any commit happened. All of 2026-09-16's raw Phase A/B/C data and 2026-09-16's market data are final, so D1–D6 (the decay/trailing/liquidation/EOD-sell simulation) and D8's cohort-log append are computed exactly as if run same-day. **D7 (manual EOD liquidation suggestions)** and **D8's recovery-price check** instead reflect account/market state **as of the 2026-09-17 backfill time**, not end-of-day 2026-09-16 — see their sections below for what that means in practice. No live orders were placed, cancelled, or modified at any point in this run.

## Headline

- **Total unique candidates seen 2026-09-16:** 228 (225 from `under120_down5`, 3 from `over120_under500_down5`)
- **Simulated (decay/trailing ledger):** 148 of 228 (64.9%)
- **Overall decay-rule win rate (closed=true):** **54.1%** (80/148)

Two scan firings produced candidate files: **9:30 ET** (106 under120 rows, 2 over120 rows) and **10:30 ET** (193 under120 rows, 3 over120 rows). The 11:30 and 3:30 firings produced no scan/candidate files (buying_power gate skipped Phase A both times — expected, not a bug). All `first_seen_timestamp` values are therefore `2026-09-16T13:36:00Z` (9:30 firing) or `2026-09-16T14:36:00Z` (10:30 firing), and `time_bucket = morning` for every row — there is no afternoon comparison possible today.

## D3 scope note

Per spec, the decay/trailing simulation (and the D3a/D3b sub-scenarios) ran **only** for `decline_bucket` in `{in_bracket, shallow_outside, deep_outside}` — i.e., only symbols priced under $10 with a first-seen decline between 5% and 30%. The 80 `other_price_or_range` rows (3 `over120_under500` rows plus 77 `under120` rows with decline >30% or price ≥$10) are not simulated by design; they carry `simulated=false` and null scenario columns in the CSV.

**Data gaps within scope:** 4 of the 148 in-scope symbols (EO, YI, NCT, UNI) returned **zero non-interpolated 5-minute bars** for the entire session — effectively no real trades printed that day (likely halted/illiquid). All four are counted as `simulated=true, closed=false` with null peak/liquidation/EOD-sell fields, per the engine's handling of an empty bar series; they contribute 0 wins and 0 losses to the win-rate denominator's "closed" count but are included in the `n=148` simulated total (consistent with how "simulated" is scoped, not "has usable data").

## Win rate by decline_bucket

| decline_bucket | n | win rate |
|---|---|---|
| in_bracket | 38 | 76.3% |
| shallow_outside | 110 | 46.4% |
| deep_outside | 0 | n/a — no candidates in range today |

The in-bracket population (10–25% decline, price <$10) closed out far more reliably than the shallow-decline (5–<10%) population today — a notably stronger showing than 2026-09-15's 38.9% in-bracket rate.

## Win rate by price_bucket

Only `<1`, `1-3`, `3-10` are populated, since only price<$10 symbols are ever in-scope (per spec).

| price_bucket | n | win rate |
|---|---|---|
| <1 | 45 | 73.3% |
| 1-3 | 43 | 46.5% |
| 3-10 | 60 | 45.0% |

## Win rate by time_bucket / source_list

- **time_bucket:** morning 54.1% (n=148); afternoon — no data (no afternoon scan today).
- **source_list:** under120 54.1% (n=148, all in-scope rows); 120to500 — no data (all 3 rows were `other_price_or_range`, priced well above $120).

## in_experiment_bracket breakdown, by firing

| Firing | in_bracket n | win rate |
|---|---|---|
| 9:30 ET | 26 | 84.6% |
| 10:30 ET | 12 | 58.3% |
| 11:30 / 3:30 ET | 0 | n/a — no scan file |

The 9:30 firing's in-bracket population closed out substantially more reliably than 10:30's today. This is directional evidence (n=26/12) that the in-bracket experiment continues to perform well in the morning window and supports extending buying to the 11:30 firing when it does produce scan data, though sample sizes remain small enough that this should not be treated as conclusive on its own.

### Cross-reference with today's trades / skipped files

Of the 38 in_bracket symbols, 37 are accounted for in today's bracket evaluations: 8 were bought (across the 9:30 and 10:30 `trades_*` files — note FBDT was skipped at 9:30 on `a3_prior_spike_guardrail` but bought at 10:30, so it's counted under "bought") and 29 were skipped by a guardrail. One symbol, **MKDW**, appears in the 10:30 scan as in-bracket but is neither in `trades_*` nor `skipped_candidates_*` — it was already an open position carried from 9/10–9/15 (per the 11:30 firing's Phase C notes), so Phase B correctly did not re-evaluate or re-buy it; this is expected behavior, not a data gap.

| Population | n | decay-rule win rate |
|---|---|---|
| Bought (real fills) | 8 | 62.5% |
| Guardrail-skipped | 29 | 79.3% |
| Already-held (MKDW, not re-evaluated) | 1 | — |

(Small samples — read directionally.)

## D6a — Exit-rule ledger

Re-grepped all 4 `position_mgmt_20260916T*.csv` files (`1345Z`, `1445Z`, `1539Z`, `1938Z`) for `time_stop`, `drawdown_stop`, and `auto_liquidate` reason strings — **no matches in any file**. Per spec, this section is skipped entirely; no exit-rule firings occurred on 2026-09-16.

## D6b — Guardrail saturation check

Both buy firings placed real trades today (5 at 9:30, 4 at 10:30 — confirmed by directly reading `trades_20260916T1336Z.csv` and `trades_20260916T1436Z.csv`, not just trusting the paraphrase), so there is **no zero-buy firing** to run the mandatory zero-buy diagnostic against. Per-guardrail skip counts are provided below for context.

**9:30 ET** (5 bought, 21 skipped):

| guardrail | skip count |
|---|---:|
| a3_prior_spike_guardrail | 6 |
| a1_spread_guardrail | 5 |
| a5_compliance_guardrail | 4 |
| a2_atr_guardrail | 3 |
| a6_reverse_split_proxy | 2 |
| a7_thin_liquidity_guardrail | 1 |

**10:30 ET** (4 bought, 22 skipped):

| guardrail | skip count |
|---|---:|
| a6_reverse_split_proxy | 10 |
| a5_compliance_guardrail | 5 |
| a8_leveraged_inverse_etf_guardrail | 4 |
| a3_prior_spike_guardrail | 3 |

No single guardrail rejected 100% of the candidates reaching it at either firing (largest share: a3_prior_spike_guardrail at 6/26 ≈ 23% of 9:30's bracket-eligible pool; a6_reverse_split_proxy at 10/26 ≈ 38% of 10:30's). **No malfunction flag.**

## Guardrail-metric correlation (directional, small-n caveat)

Average `atr14_pct` for bought in-bracket candidates (n=8, only a2/a3-tagged rows carry this field) was 0.071 vs. 3.796 for guardrail-skipped in-bracket candidates with an `atr14_pct` field present (n=3). This is consistent with the a2_atr/a3_prior_spike guardrails filtering out already-volatile/already-spiked names as designed, but n is very small on the skipped side today (most skips were a1/a5/a6/a8, whose detail strings don't carry `atr14_pct`) — read this as weakly directional only.

## D7 — Manual EOD liquidation suggestions (ADVISORY ONLY, next-morning snapshot)

**This section reflects account state at backfill time (2026-09-17 morning), not end-of-day 2026-09-16.** Of the 9 symbols bought on 2026-09-16 (MYSE, PECE, BAK, TOPS, AIXC, MRLN, FBDT, GWAV, TNON), 5 were already sold same-day (MYSE, PECE, TOPS, AIXC, FBDT — all closed profitably at their resting limit-sell targets per `get_pnl_trade_history`) and are naturally excluded from this advisory check. The remaining 4 — **BAK, MRLN, GWAV, TNON** — are still open as of this morning:

| symbol | pct_change_since_buy | near_low | trend_down | SUGGEST_LIQUIDATE |
|---|---|---|---|---|
| BAK | -0.93% | Yes | Yes | No (pct threshold not met) |
| MRLN | -5.44% | No | Yes | No (not near low) |
| GWAV | -1.27% | No | Yes | No |
| TNON | -6.15% | No | Yes | No (not near low) |

None flag `SUGGEST_LIQUIDATE` — no symbol satisfies all three conditions (≤-5% since buy AND within 2% of its post-fill low AND second-half-of-session-average below first-half). `results/eod_liquidation_suggestions_20260916.csv` is header-only (no rows).

**These are advisory suggestions only. No order was placed, cancelled, or modified as part of this task or this suggestion — any action requires separate, explicit execution.**

## D8 — Decliner-bracket recovery/decline cohort tracking (pure data logging)

**a/b.** From 2026-09-16's under-$120 scans (both firings merged/deduped), 148 candidates matched the filter (price<$10, |decline| in [0.05, 0.30]) — identical population to the D3 in-scope set. All 148 were new `date_seen=2026-09-16` rows (confirmed no 2026-09-16 rows existed in the log before this run) and were appended to `results/decliner_cohort_log.csv` (10,359 → 10,507 lines).

**b2.** 16 of the 148 were brand-new symbols never logged before (PECE, TPVG, SOUX, ELTX, TCPC, NUG, SSPC, BNGO, ALHC, BULL, RMBC, RBLU, RLGT, GRNT, UECG, UNI); fetched fundamentals for sector/industry (UNI returned `not_found` — blank fields, noted). The other 132 copied sector/industry from their existing log entries.

**c–e.** The full cohort log now spans **2,197 distinct symbols**. Per spec, capped to the **150 most-recently-added** (by earliest `date_seen`, spanning 2026-09-02 through 2026-09-16) for the recovery-price check — 2,047 older symbols were not re-checked this run. Quotes were fetched for all 150; **4 could not be resolved** (UNI and WLFI returned no usable quote/not_found; DOT and ENA returned no quote data at all in their batch, likely delisted or otherwise untradable) and were skipped. **146 rows** were appended to `results/decliner_recovery_tracking.csv` (4,897 → 5,043 lines) with `check_date=2026-09-17` (today's actual date, per the log's own convention), `pct_change_from_original` recorded as a decimal fraction (not ×100, consistent with the post-2026-08-22 fix). No 2026-09-17 rows existed for any of these symbols before this run, so no duplicates were skipped.

## D10 — Guardrail paper-trade ledger

**New positions opened for 2026-09-16:** 14, from the 43 rows across both `skipped_candidates_20260916T*.csv` files. 29 of the 43 were excluded as pre-existing duplicates already in the ledger from prior days (ADBT, HCAI, HKPD, TRUG, BOXL, AIXI, BMGL, IRAB, BURU, WETO, BDRX ×2, VWAV, SUGP, ILLR, SWVL, SMTK, NIVF, LOFD, GTBP, ADBT ×2, HLSQ, OPENW), and FBDT was excluded because it was actually bought on 2026-09-16 (10:30 firing) despite being skipped at 9:30.

**Day-0 (2026-09-16) intraday simulation** on the 14 new entries: 11 closed same-day (10 `paper_target`, 2 `paper_auto_liquidate_60min` — IPW and ARTL), 3 remain open (CODX, LITZ, BLSG).

**Carry-forward** for pre-existing open positions (22 as of 2026-09-15) through the now-completed 2026-09-16 session: 11 closed (2 `paper_time_stop_4sessions` at sessions_held=4 — DDC, LSTA; 4 `paper_drawdown_stop_25pct` — SMUP, SMU, RIBB, BMGL, ILLR [5 total]; 4 `paper_target` — TNMG, FTFT, NXTT, AEHL), 9 remain open with updated `sessions_held`/`peak_pct`/`max_drawdown_pct`. **Did not carry forward into 2026-09-17** — as of this backfill (09:30 UTC on 2026-09-17), the market has not yet opened for that session, so no 2026-09-17 daily bar exists to simulate against; those positions keep 2026-09-16 as their latest completed session.

**Data gaps:** YYGH (open since 2026-08-28, previously flagged as likely delisted) again returned `not_found` on daily historicals — left unchanged. MGN (opened 2026-09-15) also returned `not_found` on 2026-09-16 daily historicals — new data gap, left unchanged; needs manual review.

**Ledger totals after this run:** 442 rows (428 closed, 14 open).

### Results by skip_reason (closed trades only, full ledger to date)

| skip_reason | n closed | win rate | mean roi_pct | median roi_pct | summed roi_pct | still open |
|---|---:|---:|---:|---:|---:|---:|
| a1_spread | 50 | 80.0% | 1.70% | 5.00% | 85.2% | 0 |
| a1_spread_gate | 6 | 83.3% | 0.00% | 5.00% | 0.0% | 0 |
| a1_spread_guardrail | 23 | 91.3% | 3.89% | 5.12% | 89.5% | 2 |
| a1_spread_pct | 8 | 62.5% | 85.98% | 4.70% | 687.8% | 0 |
| a2_atr | 22 | 59.1% | 189.85% | 3.90% | 4176.7% | 0 |
| a2_atr_guardrail | 17 | 47.1% | -6.31% | -3.34% | -107.3% | 2 |
| a3_prior_spike | 93 | 80.6% | 104.87% | 5.01% | 9752.8% | 4 |
| a3_prior_spike_guardrail | 2 | 50.0% | -3.78% | -3.78% | -7.6% | 0 |
| a4_earnings_recency | 19 | 52.6% | -0.07% | 3.00% | -1.3% | 1 |
| a5_compliance | 52 | 71.2% | 61.60% | 4.23% | 3203.3% | 1 |
| a5_compliance_guardrail | 16 | 81.2% | 104.64% | 5.03% | 1674.2% | 0 |
| **a6_reverse_split_proxy** | **68** | **67.6%** | **3.13%** | **5.00%** | **212.6%** | **1** |
| a7_ask_gap_exceeded | 5 | 60.0% | -5.81% | 4.09% | -29.0% | 1 |
| a7_stale_quote | 3 | 66.7% | -1.15% | 5.00% | -3.4% | 0 |
| a7_stale_quote_gap | 3 | 66.7% | 0.17% | 3.00% | 0.5% | 0 |
| a7_thin_liquidity | 7 | 57.1% | 0.59% | 2.00% | 4.1% | 0 |
| a7_thin_liquidity_gap | 2 | 50.0% | -0.42% | -0.42% | -0.8% | 0 |
| a7_thin_liquidity_stale_quote | 3 | 100.0% | 5.23% | 5.26% | 15.7% | 0 |
| a8_leveraged_etf | 15 | 86.7% | 1.25% | 2.12% | 18.7% | 0 |
| a8_leveraged_etf_gap | 2 | 100.0% | 11.08% | 11.08% | 22.2% | 0 |
| a8_leveraged_inverse_etf | 11 | 81.8% | 1.59% | 5.15% | 17.4% | 0 |
| a8_leveraged_inverse_etf_guardrail | 1 | 100.0% | 2.01% | 2.01% | 2.0% | 2 |
| **All closed (any reason)** | **428** | **73.4%** | **46.3%** | — | **19,813.2%** | **14** |

*Naming note: several guardrails have logged under more than one `skip_reason` spelling over time (e.g. `a1_spread` vs `a1_spread_guardrail` vs `a1_spread_pct`; `a2_atr` vs `a2_atr_guardrail`; `a5_compliance` vs `a5_compliance_guardrail`; a new `a8_leveraged_inverse_etf_guardrail` spelling appears for the first time today). Rows are grouped by the exact `skip_reason` string as recorded, per spec, rather than merged — treat same-numbered rows as one guardrail family when reading results.*

*Several mean_roi figures (a2_atr, a3_prior_spike, a5_compliance, a5_compliance_guardrail) remain skewed by a handful of extreme outlier trades carried over from prior days. The median columns are the more robust read on typical performance; the mean/summed columns are included as specified but should not be read as "typical."*

### a6_reverse_split_proxy — guardrail under active review

**68 closed paper trades, 67.6% win rate, mean ROI +3.13%, median ROI +5.00%, summed ROI +212.6%, 1 still open.** Today added 2 new closed a6 trades (ARTL: -14.5% via `paper_auto_liquidate_60min`; BYAH: +5.33% via `paper_target`) plus 1 still-open (CODX). The population remains solidly positive on win rate and median ROI, consistent with prior days — but see caveats below before treating that as a case for loosening a6.

### Caveats (mandatory)

1. **Guardrails short-circuit in a1→a8 order**, so `skip_reason` records only a candidate's *first* failure. Paper results for guardrail aX measure aX's *marginal* cost given a1…a(X-1) already passed — decision-relevant, but a paper winner blocked by a6 might also have been blocked by a7 or a8 had a6 not existed. Cross-check the `fundamentals_guardrails_failed` column, which records every one of a5/a6/a8 the candidate would have failed. **a7 is not recomputed here** — checking it would require an extra historicals call per candidate — so this is a known gap in every guardrail's marginal-cost estimate, including a6's.
2. **Paper entries fill at the observed price with no spread paid and no slippage**, while real buys are market orders. Every paper result here is optimistic relative to a real fill, and most optimistic precisely for the wide-spread, thin, low-priced names these guardrails (a1, a5, a6 especially) are designed to catch.
3. **Target exits assume a limit fills whenever a bar's high touches it.** This matches how the real resting GTC limit order behaves, but it is still an assumption — real fills at the exact touch price are not guaranteed, particularly on illiquid names.

### Baseline: paper ledger vs. actual account performance

| | Win rate | P&L |
|---|---|---|
| **Paper ledger, all closed guardrail-blocked trades** | 73.4% (428 trades) | +19,813% summed ROI (mean +46.3%/trade) |
| **Account 757884218, actual realized (all-time)** | ~55.3% (sample of the most recent 199 realized closes, 2026-07-24 to 2026-09-16) | **-$136.97** total realized gain, -3.63% total rate of return (all-time, via `get_realized_pnl`, span=all) |

The account's actual win rate is an approximation from the most recent ~199 realized trades (the trade-history endpoint paginates and the account's history runs back to ~2026-07-16); the P&L figure is the exact all-time aggregate. **A guardrail is only worth loosening if its blocked population would have beaten what the account actually achieved** — on raw win rate and ROI, every guardrail bucket above (including a6) clears that bar handily, but per caveats 1-3 this paper ledger is a best-case, frictionless, single-guardrail-marginal estimate, not a live-trading forecast. Any loosening decision should discount these numbers for spread/slippage cost and for the un-tested a7 interaction before acting on them.
