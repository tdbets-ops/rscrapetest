# Daily Analytics Summary — 2026-09-15

## Headline

- **Total unique candidates seen today:** 260 (253 from `under120_down5`, 7 from `over120_under500_down5`)
- **Simulated (decay/trailing ledger):** 214 of 260 (82.3%)
- **Overall decay-rule win rate (closed=true):** **36.4%** (78/214)

Only one scan firing produced candidate files today: **10:30 ET**. The 9:30 firing did not run, the 11:30 firing produced no new scan/candidate file, and the 3:30 firing was skipped at Phase A (buying_power gate, $23.09 < $50). All `first_seen_timestamp` values are therefore `2026-09-15T14:36:00Z`/`14:37:00Z` (10:30 firing) and `time_bucket = morning` for every row — there is no afternoon comparison possible today.

## D3 scope note

Per spec, the decay/trailing simulation (and the D3a/D3b sub-scenarios) ran **only** for `decline_bucket` in `{in_bracket, shallow_outside, deep_outside}` — i.e., only symbols priced under $10 with a first-seen decline between 5% and 30%. The 7 `over120_under500` rows and the 46 `under120` rows with decline >30% (or otherwise out of range) are `other_price_or_range` and were **not** fetched from historicals; they carry `simulated=false` and null scenario columns in the CSV by design.

## Win rate by decline_bucket

| decline_bucket | n | win rate |
|---|---|---|
| in_bracket | 36 | 38.9% |
| shallow_outside | 176 | 35.8% |
| deep_outside | 2 | 50.0% |

(`deep_outside` n=2 — not statistically meaningful.)

## Win rate by price_bucket

Only `<1`, `1-3`, `3-10` are populated, since only price<$10 symbols are ever in-scope (per spec).

| price_bucket | n | win rate |
|---|---|---|
| <1 | 59 | 33.9% |
| 1-3 | 72 | 43.1% |
| 3-10 | 83 | 32.5% |

## Win rate by time_bucket / source_list

- **time_bucket:** morning 36.4% (n=214); afternoon — no data (no afternoon scan today).
- **source_list:** under120 36.4% (n=214, all in-scope rows); 120to500 — no data (none of the 7 rows were in-scope; all `other_price_or_range`).

## in_experiment_bracket breakdown, by firing

All 36 `in_experiment_bracket=true` symbols today came from the **10:30 ET firing** (the only firing that produced a scan file). There is **no 11:30 or 9:30 or 3:30 in_bracket data** to compare against, despite the standing assumption that 11:30 would have its own bracket population — re-globbing `results/` confirmed only one `under120_down5_*` and one `over120_under500_down5_*` file exist for today, both timestamped to the 10:30 firing.

| Firing | in_bracket n | win rate |
|---|---|---|
| 10:30 ET | 36 | 38.9% |
| 11:30 ET | 0 | n/a — no scan file |
| 9:30 / 3:30 ET | 0 | n/a — did not run / Phase A skipped |

### Cross-reference with today's trades / skipped files

Of the 36 in_bracket symbols, all 36 are accounted for in the 10:30 firing's bracket evaluation: 10 were bought (`trades_20260915T1445Z.csv`) and 26 were skipped by a guardrail (`skipped_candidates_20260915T1445Z.csv`).

| Population | n | decay-rule win rate |
|---|---|---|
| Bought (real fills) | 10 | 50.0% |
| Guardrail-skipped | 26 | 34.6% |

(Small samples — the bought-side n=10 should be read as directional, not conclusive.)

## D6b — Guardrail saturation check (10:30 firing)

Bracket-eligible candidates at 10:30: **36** (10 bought + 26 skipped). Per-guardrail skip counts among the 26 skipped:

| guardrail | skip count |
|---|---|
| a3_prior_spike | 9 |
| a1_spread_guardrail | 4 |
| a5_compliance | 4 |
| a6_reverse_split_proxy | 3 |
| a2_atr_guardrail | 3 |
| a4_earnings_recency | 3 |

No a7/a8 guardrail skips appear in today's data. **No single guardrail rejected 100% of the candidates that reached it** — the largest single-guardrail bucket (a3_prior_spike, n=9) is 25% of the 36 bracket-eligible candidates, well short of saturation. No malfunction flag.

Since the 10:30 firing placed 10 real buys (not a zero-buy firing), there is **no zero-buy firing today** to run the mandatory zero-buy diagnostic against — this section is purely the per-guardrail skip-count breakdown for context, as instructed.

## Guardrail-metric correlation (directional, small-n caveat)

Average `atr14_pct` for bought bracket candidates (n=10) was 0.163 vs. 0.422 for skipped bracket candidates with an atr14_pct field present (n=22) — consistent with a2_atr_guardrail/a3_prior_spike filtering out the more volatile/already-spiked names, as designed. This is largely definitional (those guardrails screen on ATR/spike ratios directly) rather than a novel signal, and n is small either way.

## D7 — Manual EOD liquidation suggestions (ADVISORY ONLY)

Evaluated the three today-bought positions still open: **CNXU, RADX, PRPL**. Fill times/prices confirmed from `get_equity_orders`: CNXU 14:45:34.791Z @ 4.47, RADX 14:45:59.792Z @ 1.8099, PRPL 14:46:13.211Z @ 2.86.

| symbol | pct_change_since_buy | near_low | trend_down | SUGGEST_LIQUIDATE |
|---|---|---|---|---|
| CNXU | -0.22% | No | Yes | No |
| RADX | -16.02% | Yes | Yes | **Yes** |
| PRPL | +1.40% | No | Yes | No |

**RADX flags `SUGGEST_LIQUIDATE`** — down 16% from fill, trading within 2% of its post-fill low, and second-half-of-session average close below first-half average close. Full detail in `results/eod_liquidation_suggestions_20260915.csv`.

**These are advisory suggestions only. No order was placed, cancelled, or modified as part of this task or this suggestion — any action requires separate, explicit execution.**

## Guardrail paper-trade ledger

**Coverage:** 428 ledger rows (406 closed, 22 open) as of 2026-09-15 close, spanning entries from 2026-08-25 through today. Today: 25 new paper positions opened (from `results/skipped_candidates_20260915T1445Z.csv`; `DDC` excluded — already had an open row from 2026-09-10, dedup applied), 18 closed (12 same-day exits: JBDI, PFSA, LHSW, UZX, HYFT, BOXL, DBGI, PLCE, QCLS, ARMP, USDE, BURU; 6 carried-forward exits: MWYN time-stop-4-sessions, NXXT 25%-drawdown-stop, REED/MNTK/RENT/IMMP paper_target).

**Data gap:** `YYGH` (open since 2026-08-28) returned not_found on daily historicals, 5-minute historicals, and tradability — likely delisted. Left unchanged in the ledger; needs manual review/write-off rather than continued carry-forward.

### Results by skip_reason (closed trades only)

| skip_reason | n closed | win rate | mean roi_pct | median roi_pct | summed roi_pct | still open |
|---|---:|---:|---:|---:|---:|---:|
| a1_spread | 49 | 81.6% | 2.05% | 5.01% | 100.3% | 1 |
| a1_spread_gate | 6 | 83.3% | 0.00% | 5.00% | 0.0% | 0 |
| a1_spread_guardrail | 17 | 94.1% | 4.76% | 5.12% | 80.8% | 4 |
| a1_spread_pct | 8 | 62.5% | 85.98% | 4.70% | 687.8% | 0 |
| a2_atr | 22 | 59.1% | 189.85% | 3.90% | 4176.7% | 0 |
| a2_atr_guardrail | 16 | 43.8% | -7.04% | -3.91% | -112.6% | 2 |
| a3_prior_spike | 88 | 81.8% | 109.31% | 5.01% | 9619.7% | 9 |
| a3_prior_spike_guardrail | 1 | 100.0% | 5.00% | 5.00% | 5.0% | 0 |
| a4_earnings_recency | 19 | 52.6% | -0.07% | 3.00% | -1.3% | 1 |
| a5_compliance | 51 | 72.5% | 63.30% | 4.27% | 3228.3% | 2 |
| a5_compliance_guardrail | 14 | 78.6% | 118.82% | 5.00% | 1663.5% | 0 |
| **a6_reverse_split_proxy** | **65** | **69.2%** | **3.80%** | **5.00%** | **246.7%** | **1** |
| a7_ask_gap_exceeded | 5 | 60.0% | -5.81% | 4.09% | -29.0% | 1 |
| a7_stale_quote | 3 | 66.7% | -1.15% | 5.00% | -3.4% | 0 |
| a7_stale_quote_gap | 3 | 66.7% | 0.17% | 3.00% | 0.5% | 0 |
| a7_thin_liquidity | 7 | 57.1% | 0.59% | 2.00% | 4.1% | 0 |
| a7_thin_liquidity_gap | 2 | 50.0% | -0.42% | -0.42% | -0.8% | 0 |
| a7_thin_liquidity_stale_quote | 3 | 100.0% | 5.23% | 5.26% | 15.7% | 0 |
| a8_leveraged_etf | 14 | 92.9% | 3.12% | 3.56% | 43.7% | 1 |
| a8_leveraged_etf_gap | 2 | 100.0% | 11.08% | 11.08% | 22.2% | 0 |
| a8_leveraged_inverse_etf | 11 | 81.8% | 1.59% | 5.15% | 17.4% | 0 |
| **All closed (any reason)** | **406** | **74.1%** | **48.68%** | — | **19,765.1%** | **22** |

*Naming note: several guardrails have logged under more than one `skip_reason` spelling over time (e.g. `a1_spread` vs `a1_spread_guardrail` vs `a1_spread_pct`; `a2_atr` vs `a2_atr_guardrail`; `a5_compliance` vs `a5_compliance_guardrail`). Rows are grouped by the exact `skip_reason` string as recorded, per spec, rather than merged — treat same-numbered rows as one guardrail family when reading results.*

*Several mean_roi figures (a2_atr, a3_prior_spike, a5_compliance, a5_compliance_guardrail) are skewed by a handful of extreme outlier trades (e.g. GOSS +8,500%, OMH +4,297%, VBIO +2,812% — all `paper_target` exits on very low-priced names). The median columns are the more robust read on typical performance; the mean/summed columns are included as specified but should not be read as "typical."*

### a6_reverse_split_proxy — guardrail under active review

**65 closed paper trades, 69.2% win rate, mean ROI +3.80%, median ROI +5.00%, summed ROI +246.7%, 1 still open.** This is the population that a6 blocked and that would have been bought (and largely hit target) had the guardrail not existed. It is a solidly positive, majority-winning population on this ledger's numbers — but see caveats 1 and 2 below before treating that as a case for loosening a6.

### Caveats (mandatory)

1. **Guardrails short-circuit in a1→a8 order**, so `skip_reason` records only a candidate's *first* failure. Paper results for guardrail aX measure aX's *marginal* cost given a1…a(X-1) already passed — decision-relevant, but a paper winner blocked by a6 might also have been blocked by a7 or a8 had a6 not existed. Cross-check the `fundamentals_guardrails_failed` column, which records every one of a5/a6/a8 the candidate would have failed (for a6 rows specifically, 1 also carries `a8` and several carry `a5;a6` when a5 fired first but a6 would also have applied). **a7 is not recomputed here** — checking it would require an extra historicals call per candidate — so this is a known gap in every guardrail's marginal-cost estimate, including a6's.
2. **Paper entries fill at the observed price with no spread paid and no slippage**, while real buys are market orders. Every paper result here is optimistic relative to a real fill, and most optimistic precisely for the wide-spread, thin, low-priced names these guardrails (a1, a5, a6 especially) are designed to catch — the a6 population (reverse-split-proxy penny stocks with sky-high split ratios) is a textbook case of this bias.
3. **Target exits assume a limit fills whenever a bar's high touches it.** This matches how the real resting GTC limit order behaves, but it is still an assumption — real fills at the exact touch price are not guaranteed, particularly on illiquid names.

### Baseline: paper ledger vs. actual account performance

| | Win rate | P&L |
|---|---|---|
| **Paper ledger, all closed guardrail-blocked trades** | 74.1% (406 trades) | +19,765% summed ROI (mean +48.7%/trade) |
| **Account 757884218, actual realized (all-time)** | ~55.5% (sample of the most recent 200 realized closes, 2026-07-23 to today) | **-$136.89** total realized gain, -3.68% total rate of return (all-time, via `get_realized_pnl`, span=all) |

The account's actual win rate is an approximation from the most recent ~200 realized trades (the trade-history endpoint paginates and the account's history runs back to ~2026-07-16); the P&L figure is the exact all-time aggregate. **A guardrail is only worth loosening if its blocked population would have beaten what the account actually achieved** — on raw win rate and ROI, every guardrail bucket above (including a6) clears that bar handily, but per caveats 1-3 this paper ledger is a best-case, frictionless, single-guardrail-marginal estimate, not a live-trading forecast. Any loosening decision should discount these numbers for spread/slippage cost and for the un-tested a7 interaction before acting on them.
