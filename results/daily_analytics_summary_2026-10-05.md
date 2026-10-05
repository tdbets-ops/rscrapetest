# Daily Analytics — 2026-10-05

## Headline

- **295** unique candidates seen today (under-$120 + $120-$500 lists, across the 9:30 and 10:30 firings only — 11:30 and 3:30 ran Phase A/C but the balance gate skipped Phase A both times, so no scan data exists for those windows today).
- **219** in scope for simulation (in_bracket / shallow_outside / deep_outside; 76 `other_price_or_range` skipped per the 2026-07-29 scope fix).
- **Overall simulated win rate: 54.8%** (120/219).

## By time bucket

| bucket | n | wins | win rate |
|---|---|---|---|
| morning (9:30+10:30 scans; 11:30/3:30 had none) | 219 | 120 | 54.8% |
| afternoon | 0 | — | n/a (no scan data) |

## By price bucket (simulated rows only)

| bucket | n | win rate |
|---|---|---|
| <1 | 72 | 59.7% |
| 1-3 | 74 | 52.7% |
| 3-10 | 73 | 52.1% |
| 10-120 / 120-500 | 0 | n/a — not simulated (out of scope; all 76 such candidates today were `other_price_or_range`) |

## By decline bucket

| bucket | n | win rate |
|---|---|---|
| in_bracket (10-25%, <$10) | 27 | **70.4%** |
| shallow_outside (5-10%) | 190 | 52.6% |
| deep_outside (25-30%) | 2 | 50.0% |

## In-bracket performance by firing (core bracket-calibration number)

| firing | n | win rate |
|---|---|---|
| 9:30 | 22 | 72.7% |
| 10:30 | 5 | 60.0% |
| 11:30 | 0 | n/a — balance gate skipped Phase A |
| 3:30 | 0 | n/a — balance gate skipped Phase A |

Both live buy windows continue to outperform the 54.8% overall pool average; 9:30 remains the stronger window on today's small sample.

**Caveat:** 2 of today's 5 real buys (RADX, CWH) are *not* in the 27-symbol in_bracket population above. Both were first seen at 9:30 as `shallow_outside` (-6.3%, -7.8%) and only deepened into the 10-25% bracket by the time the 10:30 firing's fresh re-evaluation bought them. D2/D5 classify by first-seen value; Phase B re-evaluates the bracket fresh each firing. This is expected, not a bug, but means the per-firing bracket table above slightly undercounts what Phase B actually considered in-bracket at buy time.

Cross-reference with today's trades/skipped files: of the 27 in_bracket symbols, 5 were bought (SSM, NXH, VELO at first-seen, plus RADX/CWH which deepened in) and 22 were skipped by a guardrail (a1:4, a2:3, a3:9, a5:3, a6:9, a7:2 — note some symbols hit more than the first-listed reason across the two firings' independent evaluations).

## Exit-rule firings (16a3 time-stop / drawdown-stop)

| symbol | condition | sessions_held | drawdown_pct at exit | realized P&L | 4:00pm close |
|---|---|---|---|---|---|
| SSM | drawdown_stop_25pct | 0 | -26.80% (at decision) | -$1.32 (bought 2@$2.22, sold 2@$1.5601, realized -29.7%) | $1.595 |

Holding to the 4:00pm close ($1.595) instead of the 10:51am ET exit ($1.5601) would have been marginally better (+$0.07 total) but still a loss — the stop fired on a real, continuing decline, not noise.

## Guardrail saturation check

Both buy firings placed trades today (3 at 9:30, 2 at 10:30) — no zero-buy diagnostic required.

## EOD liquidation suggestions (advisory only — not executed)

| symbol | pct_change_since_buy | near_low | trend_down | flag |
|---|---|---|---|---|
| NXH | -10.0% | yes (2.06 vs low 2.02) | yes | **SUGGEST_LIQUIDATE** |

NXH is the only symbol bought today still open. All three criteria are met. This is advisory only; no order was placed. (VELO/SSM/RADX/CWH, the other four buys, all closed intraday before this analysis ran.)

## Cohort tracking (D8)

- 219 new rows appended to `decliner_cohort_log.csv` for today's 5-30%-decline-under-$10 tracking band.
- 7 new symbols required a fundamentals lookup for sector/industry (AIRI, ETRA, GFSG, LGL, LYEL, MFA, MPLT); 212 copied from existing log history.
- Distinct symbol universe now **2,335**. Recovery tracking refreshed 147/150 of the most-recently-added symbols (3 skipped: HBAR, ONDO, SYRUP — the known crypto-ticker-leak issue, where a crypto symbol transiently resolves through the equity quote endpoint then fails).

## Guardrail paper-trade ledger (D10)

- **+29** new paper positions opened today from the two skipped_candidates files (31 skipped candidates minus 2 — LGHL and XHLD — deduped against already-open rows from 2026-10-01). **21 closed same-day**, 8 carried forward.
- **25** pre-existing open positions carried forward one day: **10 closed today** (3 from the 2026-09-29 cohort hit sessions_held=4: ONCO drawdown-stopped, MNOV time-stopped, EHGO hit target; others closed via target or drawdown stop), 15 remain open.
- Ledger now holds **816** total rows: 793 closed, 23 open.

### By skip_reason (closed trades, all-time)

| guardrail | n | win rate | median roi | mean roi | summed roi | still open |
|---|---|---|---|---|---|---|
| a1 (spread) | 154 | 83.8% | +5.07% | +33.0%† | +5088%† | 4 |
| a2 (ATR) | 67 | 59.7% | +4.06% | +220.4%† | +14770%† | 0 |
| a3 (prior-spike) | 173 | 77.5% | +5.00% | +56.6%† | +9783%† | 7 |
| a4 (earnings-recency) | 27 | 51.9% | +2.05% | +0.2% | +6% | 0 |
| a5 (compliance) | 126 | 77.0% | +4.32% | +46.0%† | +5796%† | 3 |
| **a6 (reverse-split proxy)** | **144** | **69.4%** | **+4.88%** | +0.9% | +135% | 5 |
| a7 (thin-liquidity/stale-quote) | 46 | 69.6% | +4.54% | -0.8% | -37% | 0 |
| a8 (leveraged/inverse ETF) | 54 | 79.6% | +5.00% | +6.9% | +370% | 4 |
| Overall | 792 | 74.5% | +5.00% | +45.4%† | — | 23 |

† **New finding today, flagged for the account owner, not acted on:** several of these mean figures are badly distorted by apparent reverse-split entry-price mismatches. Today's two new a6/a5/a1-tagged positions **GUTS** (+853%) and **CMND** (+801%) both show an entry price from the morning scan that is roughly 1/10th of their actual same-day trading range once fetched via split-adjusted historicals — i.e., Robinhood's split-adjustment appears to retroactively restate the whole day's bars after an intraday reverse split, while the logged entry_price (captured live, pre-adjustment) is never restated. The ledger's history has at least 13 older rows with the same signature (CTNT +10,779%, GOSS +8,500%, OMH +4,297%, MGN +3,215%/+689%, VBIO +2,812%, BRTX +1,671%, TXXD +1,001%, CALC +396%, LITZ +248%, FTFT +198%/+149%, AEHL +101%), spread across a1/a2/a3/a5/a6/a8. **Median is unaffected** (one bad row can't move a median of 100+) and should be treated as the primary comparison metric per guardrail until this is investigated; mean/summed roi columns above are included for completeness but are not reliable as-is. This is a data-quality observation only — no guardrail logic was changed.

a6 remains in line with the other guardrails on win rate and median (69.4% / +4.88%, consistent with the 2026-10-02 reading of 70.1%/+5.00%) — still not an outlier case for loosening the 15x threshold.

### Baseline comparison

Actual realized P&L since account inception (via `get_realized_pnl`, span=all): **-$148.04 on 307 closing trades (-3.61%)**.

Every guardrail's paper win rate and median roi is positive, while the account's real trades are net negative. Per the standing caveats: paper fills have no spread/slippage (optimistic vs. real market-order fills, especially for the thin/wide-spread names these guardrails specifically target) and target exits assume any bar touching the limit fills, matching live behavior but still an assumption. The gap is informative but should not be read as "the guardrails are too strict" without accounting for those caveats — and, as of today, without first correcting for the reverse-split mean-distortion noted above.

**Standing caveats (restated every day per D10f):**
1. Guardrails short-circuit in a1→a8 order; skip_reason is only a candidate's *first* failure. `fundamentals_guardrails_failed` records every one of a5/a6/a8 that would also have failed — a7 is never recomputed, so no guardrail removal claim is clean.
2. Paper entries fill at the observed price with no spread or slippage; real buys are market orders. Every paper result is optimistic, most of all for the wide-spread/thin names these guardrails target.
3. Target exits assume a limit fills whenever a bar's high touches it — matches the real resting GTC limit and D3's own methodology, but is still an assumption.
