# Daily Analytics — 2026-10-16

## Headline
- **941** unique candidates seen today across both scan lists, across all 4 firings (9:30/10:30/11:30/3:30 ET).
- **605** in scope for the decay/trailing simulation (64.3% of 941) — **20.0% win rate overall (121/605)**.
- **in_bracket** (the live Phase B buy bracket: price<$10, 10–25% decline): **41.94% win rate, n=62** — clearly separates from shallow_outside (17.53%, n=542) today, a reassuring signal for the bracket's calibration.
- Real buys today: **3**, split across both buy windows — GP (9:30) and EBS, AIBZ (10:30). See per-firing section below for the mandatory zero-buy diagnostic (not needed today — both windows bought).
- Today was a broadly weak day for the strategy's hypothetical close rate versus recent sessions (20.0% vs the ~70-83% seen on 10-13/10-14/10-15) — see time-bucket breakdown below; the afternoon-heavy sample (245 of 605 simulated rows are from the 3:30 firing, which by construction has only ~20 minutes of bars before the 20:00 UTC cutoff) pulls the headline rate down substantially. This is a methodology artifact, not evidence the strategy itself got much worse today — see caveat under "Win rate by firing" below.

## D3 scope note
Per protocol, the per-symbol decay/trailing simulation (D3/D3a/D3b) runs only on `decline_bucket` in (in_bracket, shallow_outside, deep_outside) — i.e. price<$10 candidates with a 5–30% decline. **336 of 941 candidates (35.7%)** fall outside that scope and carry `simulated=false` with null scenario columns — either price≥$10 (the $120-500 list, plus any ≥$10 under-$120 rows) or a decline outside 5–30%.

**deep_outside** (>25–30% decline, <$10) had only **n=1** today (EOCN at the 11:30 firing — which, notably, closed with a huge +28.76% decay-close ROI). n=1 is not a usable sample; no deep_outside-vs-in_bracket comparison is possible.

## Win rate by time bucket (simulated population, n=605)
| Bucket | Win rate | n |
|---|---|---|
| Morning (9:30/10:30/11:30) | 29.44% | 360 |
| Afternoon (3:30 only) | 6.12% | 245 |

**Caveat:** the 3:30 firing's candidates are evaluated from a start_time within ~20 minutes of the 20:00 UTC cutoff, so almost none of them have enough elapsed time for the decay/trailing target to realistically be reached before the simulation window ends — most show `closed=false` not because the setup was bad but because the clock ran out. The 6.12% afternoon rate should **not** be read as "afternoon setups are 5x worse than morning" — it's largely an artifact of evaluation-window length, not setup quality. A fairer same-day comparison restricted to candidates with a full session of runway is the in_bracket-by-firing table below.

## Win rate by price bucket
| Bucket | Win rate | n |
|---|---|---|
| <$1 | 35.16% | 128 |
| $1–3 | 19.82% | 217 |
| $3–10 | 12.69% | 260 |
| $10–120 / $120–500 | n/a | 0 (out of simulation scope by construction) |

## Win rate by decline bucket — the core bracket-calibration number
| Bucket | Win rate | n |
|---|---|---|
| in_bracket (10–25%, <$10) | 41.94% | 62 |
| shallow_outside (5–<10%, <$10) | 17.53% | 542 |
| deep_outside (>25–30%, <$10) | 0.00% | 1 (single observation, EOCN — not a usable sample) |

in_bracket clearly separates from shallow_outside today (41.9% vs 17.5%) — a good day for the bracket's calibration, in contrast to 10-15 where the two were statistically indistinguishable.

## in_experiment_bracket=true, by firing (2026-08-25 addition — direct evidence on the two live buy windows and whether 11:30 should join them)
| Firing | Win rate | n |
|---|---|---|
| 9:30 (buy window #1) | 48.48% | 33 |
| 10:30 (buy window #2) | 50.00% | 8 |
| 11:30 (no buy) | 40.00% | 10 |
| 3:30 (no buy) | 18.18% | 11 |

Both live buy windows (9:30, 10:30) show the strongest in_bracket win rates today, which supports keeping them as the buy windows. 11:30's 40% (n=10) is closer to the buy windows than to 3:30's 18.18% — on this single day's small sample it does not look dramatically worse than 10:30, though n=10 is too small to act on by itself; the 3:30 result is confounded by the short-runway issue noted above (candidates first seen at 3:30 have almost no time left to hit target), so it understates how 3:30 candidates would have performed with a full session. Consistent with the existing note on 11:30/3:30: no change recommended from one day's data.

## Hypothetical exit scenarios (full simulated population, n=605 unless noted)
- **Liquidation-rule scenario (D3a, incl. the 0.999 tolerance factor):** most of today's population could not have the 60-minute check evaluated (candidates first seen too close to the 20:00 UTC cutoff to have 60 minutes of remaining bars, especially the entire 3:30 cohort and later-afternoon 11:30 rows) — liquidation_triggered is blank/not-evaluable for a large share of rows. Among rows where it WAS evaluable, triggers were a minority, consistent with prior days' ~5-10% rate.
- **EOD-market-sell scenario (D3b):** average eod_sell_roi_pct across the 484 symbols that never closed (`closed=false`) is clearly negative (most rows show losses in the -2% to -15% range, with a handful of outliers like RBNE at -53.6% and YFOR at -20.0% — both outside the in_bracket population). The resting take-profit/trailing logic is still doing meaningful work versus a flat EOD dump, as on prior days.

## Trades / skipped-candidates cross-reference
- **Real buys (n=3):** GP (9:30) — simulated from its 9:30 first-seen price, closed in the simulation at +2.04% decay ROI (its actual live round-trip was +4.83%, bought lower at 0.3005 than the 9:30 scan price of 0.3087). EBS and AIBZ (both from the 10:30 window) — EBS's *first* scan appearance was actually at 9:30 (6.6099), so its D3 row is simulated from that earlier/higher price and shows `closed=false`, eod_sell_roi_pct=-3.48%; but the live trade bought EBS later, at 10:30, at a lower price (6.2799) after it declined further, and that live position closed at its real limit target for +4.14% — a good illustration of how buying from the SECOND window at a lower re-entry price can outperform a first-seen-anchored simulation. AIBZ is simulated from its (only) 10:30 first-seen price (2.46), closed=false, eod_sell_roi_pct=-6.50%, consistent with the live AIBZ position which remains open and down roughly 6.8% from fill at the close.
- **Guardrail-skipped bracket candidates (n=49, union of both today's `skipped_candidates_*.csv` files, 17 symbols skipped at both firings deduplicated):** 21/49 (42.9%) closed in the simulation — close to the overall in_bracket win rate of 41.9%, suggesting today's guardrail skips were not disproportionately filtering out either unusually good or unusually bad setups.

---
*(D3–D6 content above. Further sections, if any, are appended below this line by a separate process.)*

## Exit-rule firings (D6a)
One step-16a3 liquidation fired today, at the 3:30pm firing:

| Symbol | Condition | Sessions held | Drawdown at exit | Realized P&L | 4:00pm close |
|---|---|---|---|---|---|
| CRVO | time_stop_4_sessions | 4 | -17.33% | -$0.49 (bought 2026-10-12 @ 2.7446, sold @ 2.2541) | ~$2.25 |

CRVO's exit price (2.2541) and the 4:00pm close (~2.25) are essentially identical — holding instead of exiting would have made no material difference today. This is a neutral outcome for the time-stop rule, not a clear win or loss.

## Guardrail saturation check (D6b)
Both of today's buy firings (9:30 and 10:30) placed at least one buy, so the zero-buy diagnostic does not apply today.

- **9:30** (33 bracket candidates, 1 bought, 32 reached a guardrail): skip counts — a1_spread 6, a2_atr 4, a3_prior_spike 2, a4_earnings_recency 2, a5_compliance 5, a6_reverse_split_proxy 8, a7_thin_liquidity 2, a8_leveraged_etf 3. No single guardrail saturated (max share: a6 at 8/32 = 25%).
- **10:30** (37 bracket candidates, 2 bought, ~34 unique skip rows... some candidates persist from 9:30): skip counts — a1_spread_pct 8, a2_atr_guardrail 7, a3_prior_spike 7, a4_earnings_recency 1, a5_compliance_guardrail 7, a6_reverse_split_proxy 2, a7_thin_liquidity 2. No single guardrail saturated (max share: a1/a2/a3/a5 each ~7-8/34 ≈ 21-24%).

Guardrail skip distribution looks healthy at both firings today — no repeat of the August single-guardrail-saturation failure mode.

## Manual EOD liquidation suggestions (D7, advisory only)
Checked AIBZ (the one symbol bought today, 10:30 window, still open at close). pct_change_since_buy ≈ -6.87% (fails the first of the three required conditions' threshold loosely but the binding test here is near_low), low_since_buy = $2.220 (at 19:25 UTC), current price $2.30 is **not** within 2% of that low (2.30 > 2.220×1.02 = 2.2644), so `near_low` is false and the SUGGEST_LIQUIDATE flag does **not** fire (all three conditions are required). No rows written — `results/eod_liquidation_suggestions_2026-10-16.csv` is header-only.

**These are advisory only — nothing was placed, cancelled, or modified based on this section.**

## Guardrail paper-trade ledger (D10)

Today: **+48 new paper positions opened** (from the 49 unique bracket candidates skipped at today's two buy firings, minus 1 already carrying an open paper position — none of today's new candidates overlapped with an existing open row). **16 of today's new positions closed intraday**; 32 remain open. Of the **21 pre-existing open positions carried forward**, **8 closed today** (2 via `paper_time_stop_4_sessions` at 4 sessions, 2 via `paper_drawdown_stop_25pct`, 4 via `paper_target`) and 13 remain open. Ledger total: **1,192 rows** (1,154 usable closed + 1 excluded data anomaly + 1 pre-existing no-data/delisted row + 36 open).

**Data-quality flag (does not affect today's own rows):** one pre-existing open position, **KNDI** (opened 2026-10-14 at entry_price $0.439), closed today per the simulation at $4.14 — a nominal **+843% roi_pct**. KNDI's fundamentals show its 52-week low ($0.38) was set 2026-10-15 and its 52-week high ($4.15) was set 2026-10-16, the next trading day — the classic signature of a reverse stock split, not a real 9-10x rally. This roi_pct is **excluded from all aggregate stats below** and the ledger row is marked `DATA_ANOMALY` in `exit_reason` rather than silently reported as a win. A similar scan of the full historical ledger found at least two older rows with the same signature (CTNT: entry $0.0398 → exit $4.33, nominally +10,779%; OMH: entry $0.0837 → exit $3.68, nominally +4,297%) which were **not** caught before now — these predate today's firing and were left as-is (not retroactively edited), but they inflate the "a2" bucket's **mean** well below; its **median** is the reliable figure for that bucket. This points to a real gap worth fixing in a future update: the ledger has no reverse-split guard on its own entry/exit prices the way the live strategy's a6 guardrail does for real buys.

### By guardrail (closed trades only, excluding the KNDI anomaly and 1 no-data row; grouped by leading guardrail code since the `skip_reason` string has been renamed several times across the project's history)

| Guardrail | n closed | win rate | mean roi% | median roi% | sum roi% | still open |
|---|---|---|---|---|---|---|
| a3 (prior-spike) | 254 | 73.6% | +38.89 | +5.00 | 9,878.14 | 6 |
| a1 (spread) | 238 | 80.7% | +21.55 | +5.06 | 5,129.89 | 4 |
| a6 (reverse-split proxy) | 201 | 64.7% | **-0.53** | +4.82 | -105.66 | 8 |
| a5 (compliance) | 181 | 72.9% | +31.09 | +4.27 | 5,627.71 | 7 |
| a2 (ATR) | 112 | 62.5% | +131.29* | +4.41 | 14,704.67* | 3 |
| a8 (leveraged/inverse ETF) | 75 | 61.3% | +2.18 | +2.06 | 163.41 | 2 |
| a7 (thin liquidity / stale quote) | 59 | 69.5% | -0.25 | +5.00 | -14.71 | 4 |
| a4 (earnings recency) | 33 | 54.5% | -0.04 | +3.00 | -1.29 | 2 |

*a2's mean/sum are inflated by the CTNT/OMH reverse-split artifacts described above — read a2's median (+4.41%), not its mean, until those two rows are corrected.

**a6 callout (the guardrail under active review):** a6 is the **weakest-performing** guardrail bucket in the paper ledger by mean return — the only one with a negative mean (-0.53%), and its win rate (64.7%) is below every bucket except a2 and a8. Its median (+4.82%) is close to the standard +5% target, meaning most individual a6-blocked trades would have been small winners, but a disproportionate share of large `paper_drawdown_stop_25pct` losses (-25% each) pulls the mean negative. On this data, **a6 does not look like a guardrail that's unfairly blocking good trades** — if anything it's blocking a below-average population, consistent with keeping it rather than loosening the 15x threshold. This is one day's incremental data on top of the existing sample (n=201 closed now vs the n reported on 2026-08-25); the account owner's call on next steps stands.

### Caveats (apply to every number above)
1. Guardrails short-circuit in a1→a8 order, so `skip_reason` is only each candidate's FIRST failure. A guardrail's paper result measures its MARGINAL cost given a1..a(X-1) already passed — the decision-relevant quantity — but a paper winner blocked by a6 might also have failed a7 or a8 had a6 not existed. Cross-check `fundamentals_guardrails_failed` for a5/a6/a8. a7 is never retroactively recomputed (needs an extra historicals call per candidate), so no guardrail's removal is a clean "we would have won this" claim.
2. Paper entries fill at the observed scan price with no spread paid and no slippage; real buys are MARKET orders. Every paper result is optimistic relative to a real fill, most of all for the wide-spread, thin, low-priced names these guardrails specifically target (a1, a7 in particular).
3. Target exits assume a limit fills whenever a bar's high touches it, matching how the real resting GTC limit behaves and how D3 already simulates — still an assumption.

### Baseline — actual realized results, same window (2026-08-25 to 2026-10-16)
From `get_realized_pnl`/`get_pnl_trade_history`: **188 real closing trades, 57.98% win rate, -$22.10 total realized P&L** (-2.3% aggregate return rate). The paper ledger's 70.8% aggregate win rate (ex-anomalies) is nominally higher than the account's actual 57.98% — but per caveat 2 above, that gap is exactly what you'd expect if paper fills systematically avoid the spread/slippage cost that real market orders on these same thin names pay, so **this is not evidence that loosening guardrails would have helped the P&L** — it's at least partly an artifact of the paper ledger's optimistic fill assumption. The real $ P&L and the paper ledger's %-return sum are also not directly comparable (different units: dollars at ~$5 position size vs. unsized percentage returns) — only the win rates are an apples-to-apples comparison, and even that comparison is confounded by caveat 2.
