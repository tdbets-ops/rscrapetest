# Daily Analytics — 2026-08-28

**No buys today.** Both buy firings (9:30am, 10:30am ET) placed zero trades. See "Guardrail saturation check" below — this was diversified guardrail rejection across a genuinely unfavorable candidate pool, not a single-guardrail malfunction like the 2026-08-21/08-24/08-25 incident.

## Headline win rate

Simulated population: **400 of 406 in-scope candidates** (406 of 703 total candidates seen today were in-scope per the D3 scope restriction — price<$10 and decline 5–30%; the other 297 were outside that price/decline range and were not simulated at all, per the 2026-07-28 scope-restriction fix). 6 in-scope symbols could not be simulated: 1 (OP) had no historicals data; 5 (VAI, IMTE, AUST, VGAS, STRR), all first-seen at the 3:30 firing, had zero non-interpolated bars afterward (effectively halted).

| Cut | n | Win rate |
|---|---|---|
| **Overall** | 400 | **27.8%** (111 wins) |
| Morning (9:30/10:30/11:30) | 239 | 36.0% |
| Afternoon (3:30) | 161 | 15.5% |
| Price <$1 | 99 | 45.5% |
| Price $1–3 | 126 | 21.4% |
| Price $3–10 | 175 | 22.3% |
| Price $10+ | 0 | n/a — scope requires price<$10 |
| source_list = 120to500 | 0 | n/a — scope requires price<$10, so this list never qualifies |

## Bracket calibration (core signal)

| decline_bucket | n | Win rate |
|---|---|---|
| **in_bracket (10–25% decline, the live buy bracket)** | 45 | **53.3%** |
| shallow_outside (5–<10%) | 353 | 24.6% |
| deep_outside (>25–30%) | 2 | 0.0% |

The 10–25% bracket continues to strongly outperform the wider decliner population (53.3% vs 24.6%), reinforcing that the bracket itself is well-targeted.

## Per-firing breakdown, in_experiment_bracket=true only (n=45)

By time bucket: morning 62.5% (20/32), afternoon 30.8% (4/13).

| Firing | n | Win rate |
|---|---|---|
| 9:30 | 22 | 68.2% |
| 10:30 | 8 | 50.0% |
| 11:30 | 2 | 50.0% (n too thin to act on) |
| 3:30 | 13 | 30.8% |

Both live buy windows (9:30, 10:30) simulate well above the 3:30 no-buy window. 11:30's n=2 is not conclusive either way but is consistent with the existing morning>afternoon evidence rather than contradicting it.

## Guardrail saturation check

Both buy firings placed zero trades, so per D6b this is checked explicitly:

**9:30 firing** — 22 bracket-eligible candidates reached step 12, all skipped. Per-guardrail counts: a3_prior_spike 7, a2_atr 5, a6_reverse_split_proxy 4, a1_spread 3, a5_compliance 2, a8_leveraged_etf 1. No single guardrail exceeded 32% of candidates — diversified, not a malfunction pattern.

**10:30 firing** — 29 bracket-eligible candidates reached step 12, all skipped. Per-guardrail counts: a3_prior_spike 12, a2_atr 5, a6_reverse_split_proxy 5, a1_spread 5, a4_earnings_recency 1, a7_thin_liquidity 1. Largest single guardrail (a3_prior_spike) accounted for 41% of candidates, still far from saturation.

No guardrail hit 100% of candidates at either firing, and no guardrail repeated as the dominant blocker across both firings in a way that suggests a bug. This looks like a genuinely thin, spike-heavy candidate pool today, not the "one guardrail matching everything" failure mode from August.

## Guardrail cross-reference (are guardrails blocking winners?)

Simulated outcomes for today's bracket-eligible skipped candidates, by first-failing guardrail (unique symbols, merged across the two firings' slightly different reason labels):

| skip_reason | n sim'd | won | lost | note |
|---|---|---|---|---|
| a3_prior_spike | 11 | 8 | 3 | 73% would have won — worth a closer look |
| a1_spread | 7 | 5 | 2 | 71% would have won — worth a closer look |
| a2_atr | 6 | 1 | 5 | correctly filtering mostly losers (17% win) |
| a6_reverse_split_proxy | 5 | 4 | 1 | consistent with its ledger-wide 83% win rate (see paper ledger below) |
| a5_compliance | 2 | 2 | 0 | too small to read into |
| a8_leveraged_etf | 1 | 0 | 1 | too small to read into |
| a7_thin_liquidity | 1 | 1 | 0 | too small to read into |

Sample sizes are small (single-day), but a3_prior_spike and a1_spread both show a majority-winning blocked population today. This is a one-day signal, not a basis for changing either guardrail — flagging for the account owner's awareness only; no guardrail change is made here.

## Exit-rule (16a3) firings

None today. The only position closed today (WEN, bought on a prior day) filled against its ordinary resting decay/trailing limit sell — not a 16a2 auto-liquidate or 16a3 time/drawdown-stop event.

## Manual EOD liquidation suggestions (advisory only)

Zero — no symbols were bought today (results/trades_20260828T1337Z.csv and results/trades_20260828T1440UTC.csv are both header-only), so there were no todays-fills to evaluate. results/eod_liquidation_suggestions_2026-08-28.csv was written header-only. These suggestions are advisory only and require manual action — nothing here was or will be executed automatically.

## Cohort tracking (D8)

406 new candidates logged today (price<$10, decline 5–30%, all previously unseen for today's date). Cohort log now covers 2036 distinct symbols all-time (up from 2019). 17 of today's additions were new to the log's entire history; 16/17 got sector/industry via fundamentals lookup (1, OP, came back not_found). Recovery tracking re-quoted 146 of the 150 most-recently-added distinct symbols (HOOZ and BBCQ are inactive/likely delisted; ZORA and CRV came back not found).

**Data anomaly flagged, not corrected:** LIT shows a +2114% pct_change_from_original in the recovery tracking file (original_price $3.4355 vs current $76.07). This looks like an upstream scan data glitch on LIT's original scan date — the real Global X Lithium ETF trades near $76, consistent with its current fundamentals — not a real 21x move. Logged as-is per the pure-data-logging mandate; flagging here for awareness since it will otherwise look like an extreme outlier in any recovery-rate analysis.

## Guardrail paper-trade ledger (D10)

Opened 37 new paper positions today (from both skip files, deduped by symbol). 21 closed same-day; 16 remain open. Of 27 pre-existing open positions, 7 closed today via carry-forward logic (CDT and FBDT hit the -25% drawdown stop; XPON, CHGA, VEEA, NVDQ, NVD hit their decay/trailing target). Ledger now: 36 open, 104 closed, all-time.

Closed-trade performance by guardrail (all history):

| Guardrail | n closed | Win rate | Mean ROI% | Median ROI% | Sum ROI% | Still open |
|---|---|---|---|---|---|---|
| a1_spread | 23 | 95.7% | 5.39 | 5.14 | 123.99 | 5 |
| a2_atr | 6 | 50.0% | -9.48 | -9.98 | -56.91 | 5 |
| a3_prior_spike | 24 | 91.7% | 4.12 | 5.10 | 98.80 | 7 |
| a4_earnings_recency | 3 | 33.3% | -2.11 | -3.57 | -6.33 | 2 |
| a5_compliance | 24 | 87.5% | 4.23 | 5.11 | 101.58 | 6 |
| **a6_reverse_split_proxy** | **18** | **83.3%** | **0.95** | **5.09** | **17.03** | **6** |
| a7_thin/stale_liquidity | 0 | — | — | — | — | 3 |
| a8_leveraged/inverse_etf | 6 | 100.0% | 4.24 | 2.09 | 25.45 | 2 |

**a6 (under active review):** 83.3% win rate over 18 closed trades, but mean/sum ROI trail every guardrail except a2/a4 — a healthy 5.09% median dragged down by a couple of large losers. Several a6-skip rows also carry a5 or a8 in `fundamentals_guardrails_failed`, meaning a6 was not the only red flag on those names. Not yet enough data to revisit the 15x threshold.

**Caveats (apply to all of the above):**
1. Guardrails short-circuit a1→a8, so `skip_reason` is only a candidate's *first* failure — paper results for guardrail aX measure aX's marginal cost given a1..a(X-1) already passed (the decision-relevant quantity), but a winner blocked by a6 might also have failed a7 or a8 had a6 not existed. a7 is never recomputed in the ledger (needs an extra API call per candidate), so no guardrail-removal claim here is ever "clean."
2. Paper entries fill at the observed price with no spread or slippage; real buys are market orders, so paper results are optimistic — most of all for the thin, wide-spread, low-priced names these guardrails target.
3. Target exits assume a limit fills whenever a bar's high touches it, matching how the real GTC limit behaves (and how D3 above simulates), but it is still an assumption.

**Baseline for comparison:** real-account realized results over the same window (2026-08-25 to 2026-08-28): 6 closed round trips, 83.3% win rate, +$0.57 total realized P&L. Several guardrail-blocked populations (a1, a3, a5, a8 especially) show materially higher mean/median ROI% than what was actually realized — directionally interesting, but the real-trade sample here is far too small (n=6) to call any guardrail definitively worth loosening.
