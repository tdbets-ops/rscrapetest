# Daily Analytics — 2026-09-30

**Firings today:** 9:30, 11:30, 3:30 ET (scan+buy / scan+mgmt / scan+mgmt). **10:30 ET did not fire** — no scan/trades/skipped-candidates files or git commit exist for that window, yet 5 real buys (LSE, BXBL, BSEM, GLAS, OXBR) were placed on the live account at 14:48 UTC (~10:48am ET), each with a resting sell already in place by the 11:30 firing. See **Operational note** below — this is a data-integrity gap, not a performance issue.

## Headline

- 374 unique candidates seen across today's under-$120 and $120–$500 scans; **251 simulated** (in_bracket/shallow_outside/deep_outside scope), 123 out of scope (`other_price_or_range`) or no-bar-data (4 symbols: MPU, WTF, ELWT, BMHL).
- **Overall win rate (simulated): 49.4%** (124/251).

## Win rate by time bucket

| Bucket | n | Win rate |
|---|---|---|
| Morning (9:30/10:30/11:30) | 158 | 58.2% |
| Afternoon (3:30) | 93 | 34.4% |

## Win rate by decline bucket (bracket calibration)

| Bucket | n | Win rate |
|---|---|---|
| in_bracket (10–25% decline, <$10) | 42 | **61.9%** |
| shallow_outside (5–10%) | 208 | 47.1% |
| deep_outside (25–30%) | 1 | 0.0% (n=1, not meaningful) |

## In-bracket win rate by firing (core evidence for the 10:30/11:30 buy-window question)

| Firing | n | Win rate |
|---|---|---|
| 9:30 (buy window) | 18 | **77.8%** |
| 10:30 (buy window) | — | did not fire today |
| 11:30 (no buy) | 11 | 54.5% |
| 3:30 (no buy) | 13 | 46.2% |

9:30 continues to outperform the later firings by a wide margin on a small sample. 11:30 sits roughly midway between 9:30 and 3:30 — still directionally weaker than either buy window would want, consistent with prior days' pattern of morning outperforming afternoon. No new evidence today on the 10:30 window specifically (it never fired), so yesterday's and today's per-firing history remains the only read on that question.

## D3a (60-min liquidation-rule scenario) and D3b (EOD market-sell scenario)

- D3a: 157/251 evaluable (94 not evaluable — mostly the 3:30 cohort, whose first-seen + 60min exceeds the 20:00 UTC cutoff). Of evaluable, **9 triggered (5.7%)**.
- D3b: mean EOD-sell ROI +0.51%, median +0.15% (n=251).

## Scope note

Per the 2026-07-29 scope fix, only decline_bucket ∈ {in_bracket, shallow_outside, deep_outside} is simulated (251 of 374 today). The other 123 (`other_price_or_range`) get `simulated=false` with null scenario columns — this is expected, not a data gap.

## Operational note — missing 10:30 audit trail

At 14:48 UTC (~10:48am ET) the account bought LSE, BXBL, BSEM, GLAS and OXBR ($5-cap-sized market buys, each immediately followed by a resting limit sell — the standard Phase B step 12 pattern), consistent with a 10:30am ET buy-window firing. However **no scan CSVs, trades_/skipped_candidates_ files, or git commit exist for a 10:30 firing today** — the first commit after 9:30 is the 11:30 firing at 15:46 UTC. This means the guardrail evaluations, bracket values, and skip log for that window are unrecoverable; the only record is the orders themselves (get_equity_orders) and the resting-sell state picked up by the 11:30 firing. Two of the five (BXBL, LSE) had actually been *skipped* at 9:30 (a1/a7 respectively) — not an error, since 11b explicitly re-evaluates fresh each window, but it does mean this window's own skip log, which would show what else it passed, is gone. This looks like a firing that executed trades but was cut off before writing its results files or committing — worth checking whatever runs the 10:30 schedule slot.

## Exit-rule (16a3) firings — none by this routine today

No drawdown-stop or time-stop liquidation appears in today's own position_mgmt logs. One pre-existing position, **OPTT** (bought 2026-09-28 @ $1.7585, 2 sh), was closed via a market sell at 15:20 UTC today at $1.2901 (realized **-$0.937**, -26.6%) — past this routine's own -25% drawdown-stop threshold. This wasn't triggered by a cloud firing (our 11:30 firing ran later, at 15:46 UTC); it was almost certainly the local every-10-minute companion loop's mirrored drawdown-stop catching it first, consistent with the pattern noted on prior days.

## D6b — guardrail saturation check

Today's only buy firing (9:30) placed 3 trades (AIB, PYXS, LNAI) out of 18 in-bracket candidates — not a zero-buy firing, so the saturation check doesn't apply. 14 bracket-eligible candidates were logged to skipped_candidates, split a1 (BGIN, EGG, BXBL — BXBL later bought anyway) = 3, a5 (SLXN, SLND, EJH) = 3, a6 (SDEV, TDTH, LESL, PFSA) = 4, a7 (LSE — later bought anyway) = 1, a8 (MSOX, CBRG, CBRX) = 3. No single guardrail rejected 100% of what reached it. (10:30 never ran, so its own guardrail breakdown is unknown — see Operational note above.)

## EOD liquidation suggestions (D7, advisory only)

Of 5 positions still open from today's real buys (BXBL, LNAI, BSEM, GLAS, OXBR — AIB/PYXS/LSE already closed as winners):

| Symbol | pct_change_since_buy | Flag |
|---|---|---|
| OXBR | **-12.81%** | **SUGGEST_LIQUIDATE** — near its post-buy low, still trending down |
| BSEM | -6.05% | not flagged (not near low) |
| GLAS | -5.04% | not flagged (not near low) |
| LNAI | -1.25% | not flagged |
| BXBL | -0.61% | not flagged |

Advisory only — no orders placed or suggested for automatic execution.

## Cohort tracking (D8)

- 255 candidates (5–30% decline band, <$10) appended to `decliner_cohort_log.csv` for today; 18 newly sector-tagged (2314 distinct symbols all-time).
- Recovery tracking refreshed for the 150 most-recently-added distinct symbols (capped from 2314 total); 145 quoted successfully, 5 skipped (HBAR, SYRUP, ONDO, UNI, NHIC — the known crypto-ticker-leak issue flagged on 2026-09-29, still unresolved).

## Guardrail paper-trade ledger (D10)

Today: **+12 opened** (all from the single 9:30 skipped-candidates file — 10:30 never fired so contributed nothing), **11 closed same-day**, 1 (MSOX) still open. Carrying forward the 26 pre-existing open rows: **9 closed** (IRAB/MSS/NEOV/MTC via 4-session time-stop, GCTK/INDP/NCT/PMAX via -25% drawdown-stop, BEZ via target), 16 remain open, YYGH unchanged (not_found on both fundamentals and quotes — likely delisted, left as-is per the "no-data/likely-delisted" precedent).

Per-guardrail win rate (closed trades only, grouped by guardrail prefix — skip_reason strings are inconsistently named across days, e.g. `a1_spread` vs `a1_spread_guardrail` vs `a1_spread_pct`; grouped by prefix here):

| Guardrail | n closed | Win rate | Median ROI |
|---|---|---|---|
| a1 (spread) | 135 | 83.0% | +5.05% |
| a2 (ATR) | 63 | 57.1% | +3.65% |
| a3 (prior-spike) | 156 | 78.2% | +5.00% |
| a4 (earnings-recency) | 25 | 52.0% | +3.00% |
| a5 (compliance) | 107 | 76.6% | +4.50% |
| **a6 (reverse-split proxy)** | **122** | **71.3%** | **+5.00%** |
| a7 (thin-liquidity) | 38 | 65.8% | +3.92% |
| a8 (leveraged/inverse ETF) | 49 | 77.6% | +5.00% |

**Baseline — actual realized P&L (all filled buys, since strategy inception 2026-07):** **-$145.38 realized** over 286 closing trades (-3.63% aggregate rate of return) — July -$133.03/109 trades, August +$2.75/69 trades, September (partial) -$15.10/108 trades.

Every guardrail's paper-ledger win rate and median ROI is positive and well above what the account has actually realized. That gap is the headline number to watch, but it does **not** by itself mean any guardrail should be loosened — see the caveats below, all of which point the same direction (paper results are systematically more favorable than real fills).

**Mandatory caveats:**
1. Guardrails short-circuit a1→a8 in order, so skip_reason is only a candidate's *first* failure. Paper results for guardrail aX measure aX's *marginal* cost given a1..a(X-1) already passed — decision-relevant, but a paper winner blocked by a6 might also have failed a7 or a8 had a6 not existed. Cross-check `fundamentals_guardrails_failed`. a7 is never re-evaluated (needs an extra call per candidate), so removing a guardrail is never a clean "we would have won this."
2. Paper entries fill at the observed price with no spread paid and no slippage; real buys are market orders. Every paper result is optimistic relative to a real fill, most of all for the wide-spread, thin, low-priced names these guardrails target.
3. Target exits assume a limit fills whenever a bar's high touches it — matches live GTC behavior and D3's method, but is still an assumption.

**Known pre-existing data-quality issue (unchanged today):** a historical entry_price data-scale anomaly (previously CTNT-only, since spread to several other symbols per the 2026-09-29 note) still badly skews *mean/sum* ROI stats for some guardrail cohorts in the full (non-prefix-grouped) breakdown — medians above are unaffected and remain the reliable figures.
