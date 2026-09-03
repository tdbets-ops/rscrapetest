# Daily Analytics — 2026-09-03

**Scope note (per 2026-07-29 fix):** the per-symbol simulation (D3/D3a/D3b) runs only on `decline_bucket` in (in_bracket, shallow_outside, deep_outside) — price < $10 with a 5–30% decline. Of 441 unique candidates seen today (all from the under-$120 list; the $120-$500 list is data-collection only and never simulated), 256 were in scope. All summary statistics below are computed over `simulated=true` rows only, so `price_bucket` only has meaningful <1 / 1-3 / 3-10 buckets — the 10-120 and 120-500 buckets are excluded from simulation by design, not silently dropped.

## Headline

- **simulated_count: 248** vs **total_candidates_seen: 441** (256 in-scope, 8 had zero real trades after their first-seen timestamp and could not be simulated)
- **Overall win rate: 50.8%** (126/248 closed by 4:00pm)

## By time bucket

| Bucket | Win rate | n |
|---|---|---|
| Morning (9:30/10:30/11:30) | 58.3% | 187 |
| Afternoon (3:30) | 27.9% | 61 |

## By price bucket (simulated rows only)

| Bucket | Win rate | n |
|---|---|---|
| <$1 | 53.2% | 62 |
| $1-3 | 54.9% | 82 |
| $3-10 | 46.2% | 104 |

## By decline bucket (bracket calibration)

| Bucket | Win rate | n |
|---|---|---|
| in_bracket (10-25% decline) | 52.8% | 36 |
| shallow_outside (5-10%) | 50.2% | 209 |
| deep_outside (25-30%) | 66.7% | 3 |

## In-bracket win rate by firing (2026-08-25 addition)

Now that 9:30 and 10:30 are both live buy windows and 11:30/3:30 are not, this is the direct evidence for whether the second window is earning its keep and whether 11:30 should follow.

| Firing | Win rate | n |
|---|---|---|
| 9:30 | 45.5% | 22 |
| 10:30 | 100.0% | 5 |
| 11:30 | 50.0% | 4 |
| 3:30 | 40.0% | 5 |

Samples are small (n=4-22 per firing) — the 10:30 100% is 5/5, not a stable estimate. Directionally consistent with 9:30/10:30 outperforming the afternoon, but not enough data yet to make an 11:30 call on its own.

## Cross-reference with today's real trades/skipped candidates

Two buy windows fired today (9:30 and 10:30), both placed at least one trade — SGLD at 9:30 (1 share, later auto-liquidated by the local 60-min loop at -11.5%, see note below), ANVS and VACI at 10:30 (both still open). 22 bracket candidates were skipped at 9:30 and 21 at 10:30 across the 8 guardrails (a1-a8); none reached zero buys, so the mandatory zero-buy diagnostic doesn't apply this cycle.

## Guardrail saturation check (D6b)

Both buy firings (9:30, 10:30) placed at least one trade, so there is no zero-buy firing to diagnose today. Per-guardrail skip counts for reference: 9:30 — a1_spread 5, a8_leveraged_inverse_etf 5, a5_compliance 3, a6_reverse_split_proxy 3, a2_atr 2, a3_prior_spike 2, a7_thin_liquidity 2 (22 total); 10:30 — a3_prior_spike 7, a8_leveraged_inverse_etf 6, a2_atr 2, a5_compliance 2, a6_reverse_split_proxy 2, a7_thin_liquidity 2 (21 total). No single guardrail rejected 100% of candidates reaching it at either firing.

## Exit-rule firings (16a3)

No time-stop or drawdown-stop (16a3) liquidations fired today. (SGLD's exit earlier today was a step-16a2 60-minute auto-liquidate under the pre-tolerance-band test, executed by the local every-10-minute loop, not a 16a3 event — see the day's git history for that firing's own account.)

## EOD liquidation suggestions (D7, advisory only)

Two positions were bought today and remain open: ANVS (3 sh, avg $1.4499) and VACI (1 sh, avg $8.4861). Neither meets all three SUGGEST_LIQUIDATE criteria (≤-5% AND near 60-min low AND trending down) as of 4:00pm — ANVS is down 4.8% and trending down but not near its low; VACI is up 1.6%. **`results/eod_liquidation_suggestions_2026-09-03.csv` is header-only.** These are advisory only — nothing was executed automatically.

## Decliner-cohort tracking (D8)

256 candidates in the 5-30%-decline/under-$10 tracking band appended to `decliner_cohort_log.csv` today (18 of them new-to-log, so 18 fresh sector/industry fundamentals calls; the other 238 copied sector/industry from their existing log entry). Cohort log now covers 2,078 distinct symbols all-time.

Recovery-tracking snapshot: re-quoted the 150 most-recently-added distinct symbols (of 2,078 total) — 145 resolved, 5 not_found (ENA, OP, ZORA, HOOZ, CRV — likely delisted/renamed) and skipped. Pure data logging; sector/industry and this snapshot never filter, skip, rank, or influence any buy.

## Guardrail paper-trade ledger (D10)

Opened 28 new day-0 paper positions today from the 43 rows across both skipped_candidates files (dedup removed candidates already carrying an open row or actually bought today — SGLD, ANVS, VACI excluded). 19 of those 28 closed same-day. Separately, 21 of the 22 pre-existing open positions were carried forward on daily bars (YYGH's quote/bars did not resolve this cycle and was left unchanged); 12 of those closed today. Ledger now holds 219 rows total, 19 still open.

Cumulative results over all CLOSED paper trades in the ledger, by skip_reason (older label variants from before the 2026-08-25 standardization kept separate rather than merged):

| skip_reason | n | win rate | mean ROI | median ROI | summed ROI | still open |
|---|---|---|---|---|---|---|
| a1_spread | 29 | 93.1% | +3.20% | +5.06% | +92.94% | 4 |
| a1_spread_guardrail | 5 | 80.0% | +1.50% | +7.06% | +7.50% | 0 |
| a1_spread_pct | 5 | 40.0% | -2.21% | -4.51% | -11.04% | 0 |
| a2_atr | 10 | 60.0% | +422.47%* | +5.00% | +4224.75%* | 1 |
| a2_atr_guardrail | 7 | 42.9% | -12.18% | -25.00% | -85.23% | 0 |
| a3_prior_spike | 49 | 87.8% | +3.00% | +5.08% | +147.11% | 4 |
| a4_earnings_recency | 6 | 50.0% | -1.92% | +5.00% | -11.50% | 1 |
| a5_compliance | 36 | 75.0% | +89.47%* | +5.00% | +3221.07%* | 1 |
| a5_compliance_guardrail | 2 | 100.0% | +5.11% | +5.16% | +10.22% | 2 |
| **a6_reverse_split_proxy** | **33** | **63.6%** | **+3.25%** | **+5.00%** | **+107.09%** | **1** |
| a7_stale_quote | 1 | 0.0% | -13.45% | -13.45% | -13.45% | 0 |
| a7_stale_quote_gap | 1 | 0.0% | -7.49% | -7.49% | -7.49% | 0 |
| a7_thin_liquidity | 4 | 75.0% | +2.15% | +5.44% | +8.59% | 0 |
| a7_thin_liquidity_stale_quote | 1 | 100.0% | +5.26% | +5.26% | +5.26% | 0 |
| a8_leveraged_etf | 7 | 100.0% | +4.66% | +2.12% | +32.64% | 1 |
| a8_leveraged_inverse_etf | 4 | 75.0% | -0.35% | +5.52% | -1.40% | 4 |

\* a2_atr and a5_compliance means are skewed by single pre-existing outlier rows (OMH +4297%, VBIO +2812%, CALC +396% — all dated 2026-08-27, before this session) whose entry prices look anomalous; medians are the more representative figure for those two rows.

**a6_reverse_split_proxy (under active review since 2026-08-25):** 33 closed paper trades, 63.6% win rate, +3.25% mean / +5.00% median / +107.09% summed. Positive and broadly in line with the other guardrails' paper performance — nothing here yet argues for loosening the 15x threshold, but the sample is still well short of "meaningful."

**Baseline comparison:** over the same window (2026-08-25 to today), the account's actual filled-and-closed trades: **15 trades, 60.0% win rate, +$0.05 realized P&L** (get_realized_pnl total_returns for 2026-08-25..2026-09-03: +$0.05, +0.06%). Every guardrail's paper win rate here (40-100%, mostly 60-90%+) is at or above that real baseline — a signal worth reading skeptically given the caveats below, not as "these guardrails are all costing us money."

**Caveats (apply to every row above):**
1. Guardrails short-circuit in a1→a8 order, so `skip_reason` is only a candidate's *first* failure. A paper winner blocked by a6 might also have failed a7 or a8 had a6 not existed — cross-check `fundamentals_guardrails_failed` in the ledger, which records every one of a5/a6/a8 the candidate would have failed. a7 is never recomputed (needs an extra historicals call per candidate), so removing any guardrail is never a clean "we would have won this."
2. Paper entries fill at the observed price with no spread paid and no slippage; real buys are market orders. Every paper result is optimistic relative to a real fill, most of all for the thin/wide-spread names these guardrails target.
3. Target exits assume a limit fills whenever a bar's high touches it — matches how the real resting GTC limit behaves and how D3 simulates, but is still an assumption.
