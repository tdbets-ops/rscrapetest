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

<!-- D10_PAPER_LEDGER_SECTION_GOES_HERE -->
