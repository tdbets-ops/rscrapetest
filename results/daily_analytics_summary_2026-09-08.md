# Daily Analytics — 2026-09-08 (Phase D, EOD)

Run after the 9:30/10:30/11:30 ET firings (commits c363ccf, 3db6839, 0b5d68b). **The 3:30pm ET firing did not run today** — no commit, no `results/*_20260908T19*Z.csv` / `T20*Z.csv` files exist. This is a scheduler/data-gap, not something this run attempted to backfill; all stats below reflect only the three morning firings that actually happened.

## Headline

- **Candidates seen today:** 449 unique symbols (425 under-$120, 24 $120–$500), across the 3 morning firings only
- **In-scope for simulation (decline_bucket ∈ {in_bracket, shallow_outside, deep_outside}):** 275
- **Actually simulated:** 274 (ACLZ excluded — every historicals bar returned was `interpolated=true`, i.e. no real trades in the window; likely a thinly-traded leveraged-ETF-proxy ticker, not delisted)
- **Overall win rate (simulated=true, win = decay/trailing target reached):** **54.4%** (149/274)

## Scope note (per D3 spec)

Simulation was restricted to price<$10 with 5–30% decline. The realized in-scope count (275) landed above the "well under 200" estimate in the spec — the `shallow_outside` band (230 rows, 5–10% decline) drove most of it, same pattern as prior runs. The $120–$500 list is always `other_price_or_range` by construction and never enters simulation.

## Win rate by time bucket

| Bucket | Wins | N | Win rate |
|---|---|---|---|
| Morning (9:30/10:30/11:30) | 149 | 274 | 54.4% |
| Afternoon (3:30) | — | 0 | n/a — firing missing today |

## Win rate by decline bucket

| Bucket | Wins | N | Win rate |
|---|---|---|---|
| in_bracket (price<$10, decline 10–25%) | 24 | 41 | 58.5% |
| shallow_outside (decline 5–<10%) | 122 | 230 | 53.0% |
| deep_outside (decline >25–30%) | 3 | 3 | 100.0% (n=3, not meaningful) |

## Win rate by price bucket / source list

| Price bucket | Wins | N | Win rate |
|---|---|---|---|
| <$1 | 48 | 76 | 63.2% |
| $1–3 | 51 | 87 | 58.6% |
| $3–10 | 50 | 111 | 45.0% |
| $10–120 / $120–500 | — | 0 | n/a (out of scope by design) |

Source list: under120 = 54.4% (149/274, all simulated rows); 120to500 had 0 in-scope rows.

## In-experiment-bracket (price<$10, decline 10–25%) breakdown — key evidence for buy-window decisions

By time bucket:

| Bucket | Wins | N | Win rate |
|---|---|---|---|
| Morning | 24 | 41 | 58.5% |
| Afternoon | — | 0 | n/a — firing missing today |

By exact firing:

| Firing | Wins | N | Win rate |
|---|---|---|---|
| 9:30 | 19 | 31 | 61.3% |
| 10:30 | 4 | 8 | 50.0% |
| 11:30 | 1 | 2 | 50.0% (n=2) |
| 3:30 | — | 0 | n/a — firing missing today |

**Read with caution:** 11:30's n=2 is far too small to support adding it as a buy window on its own. 9:30 (n=31) remains the only firing with a sample size approaching usefulness; its 61.3% is modestly above the overall in-bracket rate (58.5%). 10:30's 50.0% (n=8) is thin but broadly in line. **Recommendation: keep collecting data — this run neither strengthens nor weakens the case for adding an 11:30 buy window.**

## Cross-reference with today's real trades

All 10 of today's real buys landed in-scope (9 in_bracket, 2 shallow_outside — NABL/AXG). Comparing the actual outcome to what the D3 simulation (entered at first-seen scan price, not the real fill) would have predicted for the same symbol:

| Symbol | Firing | Real fill | Real exit / status | Sim: closed? | Sim decay-target ROI |
|---|---|---|---|---|---|
| LASE | 9:30 | $1.0577 | still open (D7 flags SUGGEST_LIQUIDATE) | No | — (68.9% peak-toward-target reached... below) |
| CYCN | 9:30 | $3.49 | 60-min auto-liquidate @ $3.1297 (-10.3%) | **Yes** (+5.0%) | Sim called it correctly; real exit failed on timing |
| OPTT | 9:30 | $0.1631 | limit target hit @ $0.165 (+1.2%) | No (114% of target reached, close) | — |
| BANL | 9:30 | $7.1481 | still open | No, sim's own liquidation-rule also triggers (-10.6%) | — |
| TV | 9:30 | $2.3287 | limit target hit @ $2.45 (+5.2%) | **Yes** (+5.0%) | Matches — model called it correctly |
| XFOR | 10:30 | $3.5287 | limit target hit @ $3.4413 (-2.5%, i.e. sold on a rule other than target*) | No, sim liquidation-rule triggers (-2.9%) | Consistent direction |
| CD | 10:30 | $3.595 | still open | No (42% of target reached) | — |
| NABL | 10:30 | $3.6299 | limit target hit @ $3.7801 (+4.1%) | **Yes** (+2.0%) | Matches |
| AXG | 10:30 | $2.0087 | limit target hit @ $1.9413 (-3.4%) | No, sim liquidation-rule triggers (-8.5%) | Directionally consistent (both negative) |
| MODD | 10:30 | $3.3057 | limit target hit @ $3.09 (-6.5%) | No (0% of target reached) | Directionally consistent |

*XFOR's real exit price is below its fill despite being a "limit" order — likely the trailing target itself had decayed downward intraday; treat as a rule-driven exit, not a straightforward loss.

Real trades: 4 winners / 6 losers among the 10 opened today (before end-of-day mark; 3 remain open per D7). Small-sample, but directionally the simulation and the live algorithm agree on most names.

## Exit-rule firings today (D6a)

Three real step-16a3-style liquidations fired today:

| Symbol | Condition | Sessions held | Drawdown at exit | Realized round-trip P&L | 4pm close (approx, from live quote) |
|---|---|---|---|---|---|
| CYCN | 60-min-never-touched-breakeven auto-liquidate | 0 | -10.3% at exit (low -19.2% intraday, $2.82 vs fill $3.49) | -$0.36 (1 sh, -10.32%) | $3.42 |
| CVKD | Session time-stop (sessions_held=4), market sell | 4 | -19.9% (low $1.1903 vs entry $1.4858) | -$0.86 (3 sh, -19.23%) | $1.20 |
| CNXU | Session time-stop (sessions_held=4), market sell | 4 | -24.1% (low $4.65 vs entry $6.13) | -$1.48 (1 sh, -24.14%) | $4.66 |

CVKD and CNXU were both entered 2026-09-01; the Labor Day holiday (9/7) meant the fourth trading session landed today, so both time-stops fired on the same day. Net exit-rule P&L today: **-$2.70** across 3 positions.

## Guardrail saturation check (D6b)

**Not triggered.** Both buy-eligible firings today (9:30 and 10:30) recorded real buys (5 each, confirmed from `trades_20260908T1344Z.csv` and `trades_20260908T1442Z.csv`), so the "zero-buy firing" scenario this section targets does not apply. For reference:

| Firing | Bracket-candidate skips | Top guardrail | Share |
|---|---|---|---|
| 9:30 | 26 | a6_reverse_split_proxy | 38.5% |
| 10:30 | 21 | a3_prior_spike | 33.3% |

No single guardrail claimed 100% of either firing's skip volume, and no guardrail saturated on both consecutive firings.

## EOD manual liquidation suggestions (D7) — advisory only, not executed

**1 flagged: LASE.** Of today's 10 real buys, 3 remain open (LASE, BANL, CD):

| Symbol | Qty | Avg buy | Current | Pct chg | Low since buy | Resting target | Flag |
|---|---|---|---|---|---|---|---|
| LASE | 4 | $1.06 | $0.9511 | -10.3% | $0.95 | $1.09 | **SUGGEST_LIQUIDATE** |
| BANL | 1 | $7.15 | $6.63 | -7.3% | $6.40 | $7.30 | no (not near_low: $6.63 > low×1.02=$6.53) |
| CD | 1 | $3.60 | $3.51 | -2.5% | $3.26 | $3.68 | no (decline <5%) |

LASE clears all three conditions: down 10.3% since buy, current price ($0.9511) within 2% of its post-buy low ($0.95, threshold $0.969), and trailing 5-min closes declining (first-half avg $1.0558 → second-half avg $1.0118). Written to `results/eod_liquidation_suggestions_2026-09-08.csv`. **This is advisory only — no order was placed, cancelled, or reviewed by this run.**

## Decliner-bracket cohort tracking (D8)

- 275 symbols from today's under-$120 scans matched the tracking band (price<$10, |decline| 5–30%) and were appended to `decliner_cohort_log.csv` with `date_seen=2026-09-08`.
- Of those, 22 were new to the log (ACLZ, BCRX, BLSG, BMEA, BRBR, CCC, CRMG, CWH, DAVA, FLNA, IBO, JRSH, MSTW, RSVR, RTB, SER, SGRX, SLDB, SMWB, TLSI, TRBG, WIMI) and got fresh sector/industry via `get_equity_fundamentals`; the remaining 253 copied sector/industry from their existing earliest log entry.
- Full cohort log now holds **2,107 distinct symbols** (earliest date_seen/price per symbol). This cycle's recovery-tracking pass was **capped to the 150 most-recently-added distinct symbols** (per spec) rather than covering the full history.
- 146 of the 150 capped symbols returned live quotes and got a new row in `decliner_recovery_tracking.csv` (`pct_change_from_original` written as a decimal fraction). 4 symbols (ENA, HOOZ, OP, ZORA) returned no quote data and were skipped/noted — likely delisted or non-equity tickers.

## Guardrail paper-trade ledger (D10)

- **Opened today:** 33 new paper positions from the 47 rows across today's two skipped-candidates files, after dedup (11 within-day duplicate symbols across the 9:30/10:30 skip lists collapsed to their 9:30 occurrence; JAGX and CMND excluded because they already had open paper positions from a prior day; no symbol bought for real today needed exclusion).
- **Day-0 result:** 24 of the 33 new positions closed same-day (all via `paper_target`); 9 remain open.
- **Carry-forward (33 prior-day open positions):** peak_pct/max_drawdown_pct updated on all 33 (daily-bar basis); sessions_held incremented by 1 (only one trading session — today — has elapsed since the last update, due to the Labor Day gap). 14 more closed today: SMJF, RPGL, TAOP, FINV via `paper_time_stop_4_sessions` (sessions_held reached 4); DGZ, BRTX, CIEX, TXXD, CBRZ, GIXI, SDST, ABTC via `paper_target`; CMND and JAGX via `paper_drawdown_stop_25pct` (-25.0%). YYGH, PFAI, and MGN returned no data from historicals (YYGH/PFAI: empty daily bars; MGN: `not_found`) — carried forward unchanged and flagged not evaluable (likely delisted or inactive). 19 remain open.
- **Total ledger:** 286 rows (258 closed, 28 open) after today's activity.

### Closed paper trades by skip_reason (all-time, n / win rate / mean roi% / median roi% / summed roi% / still open)

| skip_reason | n closed | win rate | mean ROI% | median ROI% | sum ROI% | still open |
|---|---|---|---|---|---|---|
| a1_spread | 34 | 94.1% | 3.48 | 5.03 | 118.3 | 7 |
| a1_spread_guardrail | 5 | 80.0% | 1.50 | 7.06 | 7.5 | 0 |
| a1_spread_pct | 6 | 50.0% | -1.00 | 0.27 | -6.0 | 2 |
| a2_atr | 16 | 62.5% | 263.29 † | 5.00 | 4212.7 | 1 |
| a2_atr_guardrail | 9 | 55.6% | -8.52 | 3.44 | -76.6 | 0 |
| a3_prior_spike | 63 | 85.7% | 18.04 † | 5.05 | 1136.5 | 5 |
| a4_earnings_recency | 8 | 50.0% | -0.81 | 2.44 | -6.5 | 1 |
| a5_compliance | 38 | 76.3% | 85.04 † | 5.00 | 3231.3 | 2 |
| a5_compliance_guardrail | 6 | 100.0% (n<10) | 282.40 † | 5.11 | 1694.4 | 1 |
| **a6_reverse_split_proxy** | **43** | **67.4%** | **2.14** | **5.00** | **92.1** | **5** |
| a7_stale_quote | 3 | 66.7% (n<10) | -1.15 | 5.00 | -3.4 | 0 |
| a7_stale_quote_gap | 1 | 0.0% (n<10) | -7.49 | — | -7.5 | 0 |
| a7_thin_liquidity | 5 | 80.0% (n<10) | 2.77 | 5.24 | 13.8 | 2 |
| a7_thin_liquidity_stale_quote | 1 | 100.0% (n<10) | 5.26 | — | 5.3 | 0 |
| a8_leveraged_etf | 10 | 100.0% | 4.16 | 2.09 | 41.6 | 1 |
| a8_leveraged_inverse_etf | 10 | 90.0% | 2.92 | 5.30 | 29.2 | 1 |

† `a2_atr`, `a3_prior_spike`, `a5_compliance`, and `a5_compliance_guardrail` means are skewed by a handful of rows with entry prices that look like stale/pre-adjustment artifacts — most notably today's carry-forward closes on **BRTX** (entry $0.1787 → exit ~$3.17, +1671%) and **TXXD** (entry $4.10 → exit ~$45.13, +1001%). TXXD's own quote data shows `adjusted_previous_close`≈$40.53 vs raw `previous_close`≈$4.05 — a ~10x adjustment factor consistent with a reverse split hitting the historicals feed as a raw price jump rather than a split-adjustment. **Use the median, not the mean, for any of these four rows.** This is a pre-existing data-quality issue in the ledger (same pattern flagged in prior runs' OMH/VBIO/CALC/CHGA rows), not introduced by this run.

**a6 (reverse-split proxy) explicitly requested:** n=43 closed, 67.4% win rate, mean ROI +2.14%, median +5.00%, summed ROI +92.1%, 5 still open. No outlier skew in this group.

**Caveats (apply to every number in this section):**
1. Guardrails short-circuit in a1→a8 order; `skip_reason` is only the *first* failure. Cross-check `fundamentals_guardrails_failed` for the full a5/a6/a8 picture. a7 is never recomputed here.
2. Paper entries fill at the observed price with no spread/slippage modeled — every paper result is optimistic vs. a real market-order fill, most optimistic for thin/low-priced names.
3. Target exits assume a limit fills whenever a bar's high touches it (matches how the real GTC limit behaves and how D3 simulates, but is still an assumption).

### Baseline comparison — real vs. paper (D10g)

| | Real trades (today, realized closes) | Paper ledger (all-time, closed) |
|---|---|---|
| N | 9 | 258 |
| Win rate | 33.3% (3/9) | 78.3% (202/258) |
| Mean ROI | -6.17% (equal-weighted) | +40.6% mean / **+5.0% median** (mean is outlier-skewed, see † above — median is the honest read) |
| Net $ P&L | -$2.68 | n/a (paper, no position sizing) |

Real-trade sample (n=9, includes CYCN/TV/OPTT/NABL/AXG/MODD/XFOR from today's buys plus the two time-stop exits CVKD/CNXU on older positions) skews negative today, driven by the two -20%+ time-stop exits and the CYCN auto-liquidate. This is a small, single-day sample — not evidence the guardrails are miscalibrated, but worth watching if the pattern repeats.

---
*All D7/D10 content in this report is advisory/simulation only. No brokerage orders were placed, cancelled, or reviewed by this run.*
