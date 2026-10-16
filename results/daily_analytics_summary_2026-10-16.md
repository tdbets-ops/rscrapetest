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
