# Daily Analytics Summary — 2026-10-23

**Scope note:** Only one Phase A scan fired today, at 9:30am ET (13:33 UTC) — the 10:30am and 11:30am firings were skipped by the low-buying-power gate, and there was no 3:30pm firing. All 205 unique candidates seen today therefore carry `first_seen_timestamp = 2026-10-23T13:33:00Z` and `time_bucket = "morning"`. Of those 205, only the 102 symbols whose `decline_bucket` is `in_bracket`, `shallow_outside`, or `deep_outside` were simulated (`simulated=true`); the remaining 103 (`other_price_or_range` — outside the $/decline window) were skipped entirely, including no historicals fetch, per the routine's cost-control scope rule. `simulated_count = 102`, `total_candidates_seen = 205`.

## Headline win rate (decay/trailing-stop simulation, simulated rows only, n=102)

**Overall win rate: 68 / 102 = 66.7%** (a "win" = the decay/trailing target was hit intraday and the position would have closed).

## By decline_bucket (core bracket-calibration number)

| decline_bucket | n | wins | win rate |
|---|---|---|---|
| in_bracket | 26 | 17 | 65.4% |
| shallow_outside | 72 | 50 | 69.4% |
| deep_outside | 4 | 1 | 25.0% |

The actual trading bracket (`in_bracket`) performs in line with the broader `shallow_outside` population (65.4% vs 69.4%), while the small `deep_outside` sample (n=4) looks much worse — treat that number as noise given the sample size.

## By time_bucket

Only one bucket exists today:

| time_bucket | n | wins | win rate |
|---|---|---|---|
| morning | 102 | 68 | 66.7% |

## in_experiment_bracket=true, by time_bucket

| time_bucket | n | wins | win rate |
|---|---|---|---|
| morning | 26 | 17 | 65.4% |

**Per-firing breakdown of the in_bracket population:** all 26 in_bracket symbols came from the single 9:30am firing (17/26 = 65.4% win rate). The 10:30am, 11:30am, and 3:30pm firings had no Phase A scan today, so there is nothing to compare the 9:30am per-firing number against — this breakdown is a placeholder until a day with multiple firings exists.

## By price_bucket (simulated rows only; all in-scope symbols are priced <$10)

| price_bucket | n | wins | win rate |
|---|---|---|---|
| <1 | 46 | 32 | 69.6% |
| 1-3 | 27 | 20 | 74.1% |
| 3-10 | 29 | 16 | 55.2% |

## By source_list

All 102 simulated symbols came from `under120` (none of the `120to500` list made it into scope today, since in-scope symbols are all priced under $10). `under120`: 68/102 = 66.7%.

## Scenario comparisons (simulated rows)

- **D3 decay/trailing-stop (primary):** 68/102 closed intraday, avg ROI on closers = **+5.9%**; the 34 that never closed averaged **98.8%** of their final target (median outcome: they got close but not quite there).
- **D3a 60-min liquidation rule:** evaluable for 98/102 (4 symbols lacked ≥60 min of bars). Of those, only 11 would have triggered the liquidation exit (peak in the first 60 min stayed below first-seen price). Within `in_bracket`, 3/25 evaluable triggered.
- **D3b EOD market-sell (always evaluated):** avg ROI if held to 4pm with no exit rule at all = **-1.2%** overall (n=99 — 3 symbols, ILLU/ARMA/MFI, had zero non-interpolated bars all day, i.e. no real trades after a7-flagged stale quotes, so no scenario could be computed for them), and **-8.1%** within `in_bracket` (n=25). This confirms the decay/trailing exit rule materially outperforms a naive hold-to-close for the in_bracket population.

## Cross-check against live trades

Three symbols were actually bought at 9:30 (`PHAT`, `NANO`, `MGNX`) and all three were auto-liquidated by the live account's step-16a2 rule within 2 hours, at roughly -8% to -16%. The simulation agrees these were weak picks: none of the three hit the decay/trailing close target intraday (`closed=false` for all three), and the EOD-hold scenario would have been worse than the live outcome for NANO (-28.3%) and MGNX (-31.0%, with the 60-min liquidation rule separately flagging MGNX as a trigger at -19.6%) — i.e. the live 16a2 guardrail protected against a larger loss on those two. For PHAT, EOD-hold (-4.7%) would actually have beaten the live auto-liquidation loss, though PHAT's peak (101.5% of its final decay target) came early in the day when the decay target was still tight at 5% — it simply never closed because the target tightened less than its price had already pulled back by the time the lower afternoon decay rate applied.

## Guardrail correlations (skipped candidates, n=23 — all drawn from the 26 in_bracket population)

All 23 symbols that failed an A-guardrail and were skipped this firing were `in_bracket` (23 skipped + 3 traded = 26 in_bracket total). Simulated win rates by the guardrail that blocked them (small samples — **treat anything n<10 as noise**):

| skip_reason | n | simulated wins | win rate |
|---|---|---|---|
| a1_spread_guardrail | 6 | 5 | 83% |
| a5_compliance_guardrail | 4 | 3 | 75% |
| a6_reverse_split_proxy | 4 | 4 | 100% |
| a3_prior_spike_guardrail | 3 | 2 | 67% |
| a8_leveraged_inverse_etf | 3 | 2 | 67% |
| a2_atr_guardrail | 2 | 1 | 50% |
| a7_thin_liquidity_stale_quote | 1 | 0 | 0% |

Notably, several guardrail-blocked groups (`a6_reverse_split_proxy`, `a1_spread_guardrail`, `a5_compliance_guardrail`) show *higher* simulated win rates than the in_bracket average (65.4%), which on the surface looks like missed opportunity — but several of those "wins" still carried large negative EOD-hold ROI if the decay-close hadn't fired early (e.g. `FGL` closed=true but still -29.4% if held to EOD; `SHFS` never closed and was down -55.3% by EOD). With n≤6 per guardrail, none of this is statistically meaningful yet; it is worth re-checking once more firing days accumulate. The one `a7_thin_liquidity_stale_quote` case (`ILLU`) is a useful confirmation of that guardrail's purpose: the symbol had zero real (non-interpolated) trades all session, so it could not have been exited by any rule — the guardrail correctly avoided a position Robinhood couldn't realistically have traded in or out of.

## Output files

- `results/daily_analytics_2026-10-23.csv` — 205 rows (one per unique symbol seen today; 102 `simulated=true`, 103 `simulated=false`).
- `results/daily_analytics_summary_2026-10-23.md` — this file.
