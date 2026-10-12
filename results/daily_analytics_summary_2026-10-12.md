# Daily analytics — 2026-10-12

**Note on today's data:** only the 9:30am ET firing produced scan data. `starting_buying_power` was below the $50 Step-0 gate at the 10:30/11:30/3:30 firings, so Phase A (scan) never ran at any of them — there is nothing to merge across firings today, and no afternoon bucket exists.

## Headline

- 205 unique candidates seen (200 under-$120 scan rows (capped) + 5 $120-500 data-collection rows), 142 simulated (scope: `in_bracket`/`shallow_outside`/`deep_outside` only — 63 `other_price_or_range` rows skipped per the 2026-07-29 scope fix).
- **Overall win rate: 48.6% (69/142)**

## Win rate by decline_bucket

| bucket | n | win rate |
|---|---|---|
| in_bracket | 32 | 53.1% (17W) |
| shallow_outside | 110 | 47.3% (52W) |
| deep_outside | 0 | n/a (no rows today) |

`in_experiment_bracket=true` win rate: 53.1% (n=32) — identical to in_bracket, as expected with only one firing today.

## In-bracket by firing

| firing | n | win rate |
|---|---|---|
| 9:30 | 32 | 53.1% |
| 10:30 | 0 | balance gate skipped Phase A |
| 11:30 | 0 | balance gate skipped Phase A |
| 3:30 | 0 | balance gate skipped Phase A |

No cross-firing comparison is possible today — only 9:30 has any data.

## Win rate by price_bucket (simulated rows only)

| bucket | n | win rate |
|---|---|---|
| <1 | 56 | 53.6% |
| 1-3 | 46 | 45.7% |
| 3-10 | 40 | 45.0% |
| 10-120 / 120-500 | 0 | not in scope (all in-scope rows are price<10 by definition) |

## Win rate by source_list

under120: 48.6% (n=142, 100% of simulated rows). All 5 `120to500` rows (SOXL, FORM, CIEN, SMTC, ENTG) classified `other_price_or_range` (price ≥120) and were not simulated — confirms that scan is data-collection-only as designed.

## D3a — 60-minute liquidation-rule scenario

15/142 (10.6%) triggered. ROI: mean -4.54%, median -3.09%, range -14.44% to -1.87% (all losses, as the rule only fires on names that never touched breakeven). 19/142 came back `liquidation_triggered=null` — not from insufficient elapsed time (6+ hours were available) but from data sparsity: several thin micro-caps (MWG, JF, MFI, BTLN, etc.) had fewer than 12 non-interpolated 5-min bars in the first 60 minutes. Flagged as a data-sparsity caveat, not a true "not evaluable" case.

## D3b — EOD market-sell scenario

Mean eod_sell_roi_pct = -0.80%, median = -1.22% (n=142) — a modestly negative day for the candidate pool as a whole.

## Cross-reference: today's 3 real buys

| symbol | sim verdict | sim detail | real outcome |
|---|---|---|---|
| QUCY | not closed | peak reached ~99.0% of target; 60-min sim peak (1.0599) was a hair above fsp (1.05) under a plain threshold | real system auto-liquidated at 0.945 (-8.26%) via the live 0.999-tolerance band — the plain-vs-tolerance-banded comparison straddles a razor-thin threshold here, explaining the divergence |
| CRVO | not closed | peak ~102% of a later, lower target, but never re-touched it on the triggering bar | still open at close, real mark ~-8.5% vs sim eod_sell -4.21% (simulation uses last regular-session 5-min close, not live quote) |
| SAIQ | not closed | peak ~101.5% of target, same bar-level-check effect | still open at close, real mark ~-4.7% vs sim eod_sell -6.42% |

All three would have stayed open through today's window under the simulated algorithm — broadly consistent with reality for CRVO/SAIQ, but diverging from QUCY's real outcome because the live 16a2 auto-liquidate rule uses the 0.999 tolerance band (added 2026-09-03) while this D3a scenario spec uses a plain threshold.

## Cross-reference: today's 20 guardrail-skipped candidates

10/20 would have closed as decay-wins in simulation: AIOK (+5.0%), WORX (+5.0%), NTCL (+5.0%), FFR (+5.0%), GCDT (+5.3%), LBGJ (+5.0%), MLEC (+5.0%), DCX (+5.0%), FLYE (+6.6%), ENTL (+5.0%). The other 10 (OFAL, VEEA, RETO, VJET, QTEX, CD, AAOX, AAOG, VCIG, GP) remained open, ranging from +0.15% (GP) to -9.13% (OFAL) on an EOD mark. Shallow pass only, not a guardrail audit — see the paper-trade ledger below for the real guardrail-cost measurement.

simulated_count=142, total_candidates_seen=205, 0 dropped to data errors (all 142 in-scope symbols got usable historicals).

## D6a — Exit-rule (16a3) ledger

No time-stop or drawdown-stop (16a3) liquidations fired at any of today's 4 position-mgmt checkpoints — section omitted per spec.

## D6b — Guardrail saturation check

The only buy firing today (9:30am) placed 3 trades (non-zero), so no zero-buy diagnostic is required. 10:30am never reached Phase B at all (balance gate skipped Phase A+B before scanning), so it isn't a "zero-buy firing" in the diagnostic's sense either. No guardrail saturation to flag.

## EOD liquidation suggestions (advisory only — nothing executed)

Only CRVO and SAIQ (today's real buys still open; QUCY was already auto-liquidated intraday by the local 60-minute loop at -8.26%, before the 4:30pm firing). Neither flags SUGGEST_LIQUIDATE: CRVO clears the -5% pct_change gate (-8.54%) but fails near_low (recovered off its 17:15Z low of 2.42 to 2.51 at close); SAIQ is near its low but its -4.74% pct_change falls just short of -5%. See `eod_liquidation_suggestions_2026-10-12.csv`.

## D8 — Cohort tracking

- 142 of today's 200 under-$120 scan rows fell in the wider 5–30%/<$10 tracking band and were appended to `decliner_cohort_log.csv` (15,050 → 15,192 rows).
- 53 got sector/industry copied from earlier appearances; 89 were brand-new symbols freshly fetched via `get_equity_fundamentals` — all resolved, none blank.
- Distinct symbol universe now **2,384**.
- Recovery tracking: +148 rows appended (capped to the 150 most-recently-added distinct symbols); 2 skipped (PSKY, HBAR — no quote data, likely delisted). All `pct_change_from_original` values verified as decimal fractions (no 100x-scale bug).

## D10 — Guardrail paper-trade ledger

**New paper positions (today, 17 opened after dedup against 3 already-open GCDT/RETO/VCIG rows):** 9 closed via `paper_target` (AIOK +2.21%, WORX +6.76%, NTCL +5.00%, FFR +5.00%, LBGJ +13.22%, MLEC +6.20%, DCX +5.00%, FLYE +5.00%, CD +5.00%), 8 still open at EOD (OFAL, VEEA, VJET, QTEX, ENTL, AAOX, AAOG, GP). None hit the 60-min auto-liquidate or the 25% drawdown stop today.

**Carried-forward positions (46 pre-existing open rows):** 21 closed today — 13 via `paper_drawdown_stop_25pct`, 5 via `paper_time_stop_4_sessions` (SGLY, BOXL, FNGR, YYAI, VRAX, all entered 2026-10-06 and reaching session 4 today), 3 via `paper_target` (BTTC, MPU, GRML). 25 remain open. LESL (entry 2026-10-05) is still unavailable on both daily and 5-min historicals — confirmed still delisted/no-data, left unchanged.

**Ledger totals:** 987 rows (970 + 17 new), 33 open / 954 closed.

**Per-guardrail stats, all CLOSED rows (n / win rate / median roi_pct):**

| guardrail | n | win rate | median roi_pct |
|---|---|---|---|
| a1 | 188 | 81.4% | +5.06% |
| a2 | 87 | 59.8% | +4.32% |
| a3 | 218 | 75.2% | +5.00% |
| a4 | 28 | 53.6% | +2.53% |
| a5 | 149 | 73.8% | +4.19% |
| **a6** | **167** | **66.5%** | **+4.53%** |
| a7 | 50 | 70.0% | +4.55% |
| a8 | 65 | 69.2% | +5.00% |

(plus 1 row tagged `trading_halted`, not an aN guardrail, excluded above.)

**a6, under live review:** 66.5% win rate / +4.53% median ROI over 167 closed trades — lower than a1/a3/a5/a7/a8 but still clearly net-positive and above a2/a4. No evidence today that a6 is an outlier worth loosening; this is simply an updated reading, not a decision — the threshold stays at 15x per the standing hold.

**Baseline comparison:** real account realized P&L since inception (`get_realized_pnl`, span=all, equities): **-$151.71 / 332 trades (-3.58%)**. Every guardrail's paper win rate (54–81%) and median ROI (all positive) stands in sharp contrast to the account's actual net-negative realized result — consistent with guardrails screening out candidates that, on this simulated exit logic, would mostly have been winners.

**Caveats (apply to every number above):**
1. Guardrails short-circuit in a1→a8 order, so `skip_reason` is only a candidate's *first* failure — a paper winner blocked by a6 might also have failed a7 or a8 had a6 not existed. Cross-check `fundamentals_guardrails_failed` for a5/a6/a8 overlaps; a7 is never recomputed here (needs an extra call per candidate), so removing a guardrail is never a clean "we'd have won this" claim.
2. Paper entries fill at the observed price with no spread or slippage; real buys are market orders, so every paper result is optimistic relative to a real fill — most optimistic for the thin, wide-spread, low-priced names these guardrails target.
3. Target exits assume a limit fills whenever a bar's high touches it, matching both the real resting GTC limit and D3's own simulation — but it remains an assumption.
