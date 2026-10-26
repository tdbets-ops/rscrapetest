# Daily Analytics — 2026-10-26

**Scope note:** Only ONE scan firing produced data today — the 9:30am ET firing (timestamp `20261026T1339Z`). The 10:30am firing hit the Step 0 balance gate (buying_power < $50) and skipped Phase A entirely, so it contributed no scan rows. No 11:30am or 3:30pm firing commits exist for today's date, so those firings appear not to have run at all. All candidates below are therefore `time_bucket=morning` and `source_list` is entirely `under120` (the $120-$500 scan returned only MRNA and LIAE, both classified `other_price_or_range` and excluded from simulation scope).

## Headline

- **Candidates seen today:** 202 (200 under-$120 decliners + 2 high-price decliners)
- **Simulated (in-scope: in_bracket / shallow_outside / deep_outside):** 149
- **Overall win rate (simulated population):** **59.7%** (89/149)

## By time bucket

| Bucket | Win rate | n |
|---|---|---|
| morning | 59.7% | 149 |

(No afternoon data today — only the 9:30 firing scanned.)

## By price bucket (simulated rows only)

| Bucket | Win rate | n |
|---|---|---|
| <1 | 73.0% | 63 |
| 1-3 | 60.5% | 43 |
| 3-10 | 39.5% | 43 |

(10-120 / 120-500 buckets excluded from simulation scope per D3 — not reported here.)

## By decline bucket (core bracket-calibration number)

| Bucket | Win rate | n |
|---|---|---|
| in_bracket (live buy bracket: <$10, 10-25% decline) | **60.0%** | 40 |
| shallow_outside (<$10, 5-<10% decline) | 58.9% | 107 |
| deep_outside (<$10, >25-30% decline) | 100.0% | 2 |

## In-experiment-bracket breakdown by firing

Only the 9:30am firing produced a scan today, so there is only one firing's worth of in-bracket data:

| Firing | Win rate | n |
|---|---|---|
| 9:30 | 60.0% | 40 |
| 10:30 | no scan (balance gate) | — |
| 11:30 | no firing recorded today | — |
| 3:30 | no firing recorded today | — |

deep_outside (n=2) and the 1-3/<1 price buckets having the strongest win rates is consistent with prior sessions' pattern that very deep/very cheap decliners recover more often — small sample, treat cautiously.

## Hypothetical exit scenarios (D3a/D3b, same bar series, no extra calls)

- **60-minute auto-liquidate scenario (D3a):** evaluable for 148/149 simulated symbols; would have triggered on 17 of them (peak never came within 0.1% of entry in the first 60 minutes).
- **EOD-market-sell scenario (D3b):** mean ROI across 148 symbols = **+0.57%**, i.e. a flat end-of-day market exit would have been mildly positive on average today, well below the decay/trailing algorithm's realized-style 59.7% win rate — consistent with prior findings that the decay/trailing exit captures upside the EOD-sell scenario gives back.

## Guardrail saturation check (D6b)

- **9:30am firing:** placed 14 buys out of candidates that reached the guardrail chain — guardrails were clearly not saturating (multiple guardrails each rejected a handful of candidates: a1_spread_pct rejected 9, a2_atr_guardrail 2, a3_prior_spike 3, a5_compliance_guardrail 3, a6_reverse_split_proxy 5, a7_thin_liquidity_gap 2). No single guardrail rejected 100% of the candidates that reached it.
- **10:30am firing:** did not reach Phase B at all — Step 0's balance gate skipped Phase A/B outright (buying_power < $50). This is not a guardrail-saturation case; it is a funding constraint, and is reported as such rather than conflated with a zero-buy-via-guardrails outcome.
- No firing today placed zero buys *after* reaching the guardrail chain, so the mandatory zero-buy diagnostic does not apply today.

## Exit-rule firings (D6a)

None today — no `time_stop_*` or `drawdown_stop_*` liquidations appear in either of today's `position_mgmt_*.csv` files (`20261026T1350Z`, `20261026T1436Z`).

## EOD liquidation suggestions (D7)

See `results/eod_liquidation_suggestions_2026-10-26.csv`. Advisory only — nothing here is executed automatically.

## Guardrail paper-trade ledger (D10)

**Today's ledger activity:** 23 new paper positions opened from today's 9:30am `skipped_candidates` (1 symbol, BURU, was already an open paper position and was deduped, not re-opened). Of those, 17 closed intraday and 6 remain open. Separately, 17 pre-existing open rows from 2026-10-20/10-22/10-23 were carried forward one trading session: 14 closed today (8 via `paper_time_stop_4_sessions`, 4 via `paper_drawdown_stop_25pct`, 2 via `paper_target`), 3 remain open (ILLU, WHLR, HRTX). Total ledger: 1360 rows, 9 currently open, 1351 closed.

**Cumulative results by skip_reason** (closed rows, all-time; the ledger has accumulated several historical spellings of the same guardrail from earlier sessions — reported as-is):

| skip_reason | n_closed | win% | mean roi% | median roi% | sum roi% | still open |
|---|---|---|---|---|---|---|
| a1_spread_pct | 46 | 82.6 | 19.49 | 5.18 | 896.73 | 1 |
| a2_atr_guardrail | 60 | 63.3 | 176.97 | 4.15 | 10618.26 | 2 |
| a3_prior_spike | 232 | 74.6 | 42.57 | 5.00 | 9875.73 | 0 |
| a5_compliance_guardrail | 80 | 77.5 | 21.85 | 5.00 | 1748.14 | 2 |
| **a6_reverse_split_proxy** | **242** | **65.7** | **-0.16** | **5.00** | **-38.77** | **2** |
| a7_thin_liquidity_gap | 3 | 66.7 | 1.68 | 5.88 | 5.05 | 1 |
| a8_leveraged_inverse_etf | 35 | 71.4 | 1.77 | 5.01 | 62.08 | 0 |

(Full breakdown including older historical spellings — `a1_spread`, `a2_atr`, `a3_prior_spike_guardrail`, `a4_earnings_recency`, `a7_thin_liquidity`, `a8_leveraged_etf`, etc. — is in the raw ledger; omitted here for brevity since they are the same guardrails under earlier naming conventions.)

**a6 (under active review) in focus:** 242 closed paper trades, 65.7% win rate, but mean ROI essentially flat (-0.16%) and summed ROI negative (-38.77 percentage-points) — a6-blocked candidates win more *often* than they lose, but the losses are disproportionately larger, netting slightly negative. This does not yet make a clean case for loosening the 15x threshold.

**Baseline — account's actual realized results over the same window (2026-08-25 through today):** 215 closed trades, **55.3% win rate**, **-$27.43** total realized P&L (dollar terms; not directly comparable to the ROI-percentage figures above, which is why both are shown rather than netted against each other).

**Mandatory caveats:**
1. Guardrails short-circuit in a1→a8 order, so `skip_reason` is only a candidate's *first* failure — a6-blocked paper winners might also have failed a7/a8 had a6 not existed. The `fundamentals_guardrails_failed` column records every one of a5/a6/a8 a candidate would have failed; a7 is never recomputed here (it needs an extra historicals call per candidate), so no guardrail-removal claim from this ledger is ever clean.
2. Paper entries fill at the observed price with zero spread and zero slippage, while real buys are market orders — every paper result here is optimistic relative to a real fill, most of all for the thin, low-priced names these guardrails target.
3. Target exits assume a limit fills whenever a bar's high touches it, matching both the real resting GTC limit and how D3 already simulates — but it remains an assumption.
