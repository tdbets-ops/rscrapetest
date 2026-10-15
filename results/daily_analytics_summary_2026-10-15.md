# Daily Analytics — 2026-10-15

## Headline
- **284** unique candidates seen today — but all from a **single firing** (9:30am ET / 13:42Z). The 10:30am, 11:30am and 3:30pm firings were all balance-gated out of scanning before they could run, so there is no cross-firing breakdown possible today (unlike most days).
- **185** candidates in scope for the decay/trailing simulation (65.1% of 284) — **82.70% win rate** overall (n=185).
- **in_bracket** (the live Phase B buy bracket: price<$10, 10–25% decline): **82.5% win rate, n=40** — statistically indistinguishable from shallow_outside today (see below).
- Real buys today: **4**, all at the 9:30 firing (GLND, FLNT, SAIQ, AGPU) — 3 of 4 closed in the decay/trailing simulation; small n, no zero-buy diagnostic needed.

## D3 scope note
Per protocol, the per-symbol decay/trailing simulation (D3/D3a/D3b) runs only on `decline_bucket` in (in_bracket, shallow_outside, deep_outside) — i.e. price<$10 candidates with a 5–30% decline. **99 of 284 candidates (34.9%)** fall outside that scope and carry `simulated=false` with null scenario columns:
- 66 under-$120 candidates outside the 5–30% decline band
- 33 candidates from the $120–500 list (price ≥ $10, out of range by construction)

No `deep_outside` (>25–30% decline, <$10) candidates appeared at all today — that bucket is n=0, so no in_bracket-vs-deep_outside comparison is possible this session (only in_bracket vs shallow_outside).

One simulated symbol, **MUZE**, returned zero non-interpolated bars all day (all 76 bars flat at $10.01 / volume 0, i.e. no real trades printed) — consistent with it separately appearing in `skipped_candidates_20261015T1342Z.csv` under `a7_thin_liquidity` / `no_recent_real_trades`. It is kept as `simulated=true` (it does meet the decline_bucket criteria) but all scenario columns (`closed`, `liquidation_triggered`, `eod_sell_price`, etc.) are null for lack of real market data, and it counts as a non-win in every win-rate denominator below (n=185 includes it).

## Win rate by time bucket (simulated population, n=185)
| Bucket | Win rate | n |
|---|---|---|
| Morning (9:30 only — the only firing that ran) | 82.70% | 185 |

## Win rate by price bucket
| Bucket | Win rate | n |
|---|---|---|
| <$1 | 84.21% | 76 |
| $1–3 | 84.48% | 58 |
| $3–10 | 78.43% | 51 |
| $10–120 / $120–500 | n/a | 0 (out of simulation scope by construction) |

## Win rate by decline bucket — the core bracket-calibration number
| Bucket | Win rate | n |
|---|---|---|
| in_bracket (10–25%, <$10) | 82.50% | 40 |
| shallow_outside (5–<10%, <$10) | 82.76% | 145 |
| deep_outside (>25–30%, <$10) | n/a | 0 (none seen today) |

Unlike some prior days, in_bracket does **not** separate from shallow_outside today (82.5% vs 82.8%, basically a coin-flip difference on n=40) — today happened to be a broadly favorable morning for small-cap decliners generally, so the bracket's edge isn't visible in this single-firing sample. No deep_outside comparison is possible (n=0).

## in_experiment_bracket=true, by time bucket
| Bucket | Win rate | n |
|---|---|---|
| Morning (9:30, the only firing) | 82.50% | 40 |

No per-firing breakdown is possible today since only the 9:30am firing scanned — the other three firings never got past the balance gate.

## Hypothetical exit scenarios (full simulated population, n=185 unless noted)
- **Liquidation-rule scenario (D3a, current version incl. the 0.999 tolerance factor):** evaluable n=184 (MUZE has no bars to evaluate). Triggered in **14 (7.6%)** — i.e. 7.6% of in-scope candidates never got within 0.1% of breakeven in their first 60 minutes and would have been auto-liquidated at a loss under this rule. Of those 14, 5 would *also* have eventually hit their decay/trailing target later in the day had they not been liquidated first — a reminder that the 60-minute rule trades a near-term capped loss for giving up some later recoveries.
- **EOD-market-sell scenario (D3b):** among the 31 symbols that never closed (`closed=false`), average EOD ROI is **-4.25%** vs the implicit win on the 153 that did close (average decay_close_roi_pct **+5.25%**) — the resting take-profit/trailing logic is clearly doing the work; a flat EOD dump on the non-closers would have been meaningfully worse than letting the target-based exit run.

## Trades / skipped-candidates cross-reference (brief, small-n caveats noted)
- **Real buys (n=4):** GLND, FLNT, AGPU closed in the simulation (+2.2%, +15.5%, +5.1% decay-close ROI respectively); SAIQ did not close and would have ended the day at -5.3% on an EOD sell. n=4 is too small to read as anything but descriptive.
- **Guardrail-skipped bracket candidates (n=36, from `skipped_candidates_20261015T1342Z.csv`, all overlapping the simulated set):** 30/36 (83.3%) would have closed — essentially identical to the 82.7% overall win rate, suggesting today's guardrail skips weren't disproportionately filtering out either unusually good or unusually bad setups. n=36 is a reasonable sample, not flagged as thin.

---
*(D3–D6 content above. Further sections, if any, are appended below this line by a separate process.)*

## Exit-rule firings (D6a)
One step-16a3 liquidation fired today, executed by the local every-10-minute Phase C loop (not a cloud firing — none of today's cloud position_mgmt_*.csv rows show a time_stop/drawdown_stop reason; identified from the raw order history instead):

| Symbol | Condition | sessions_held | drawdown_pct at exit | Realized P&L | 4:00pm close |
|---|---|---|---|---|---|
| ZENA | time_stop_4_sessions | 4 | -5.31% | -$0.25 (4 sh, 1.1669→1.105) | $1.115 |

ZENA was bought 2026-10-09 (Fri) and hit its scheduled 4-session time stop today (sessions: 10/9=0, 10/12=1, 10/13=2, 10/14=3, 10/15=4), well short of the -25% drawdown stop. Holding to today's 4:00pm close ($1.115) would have been marginally better (-$0.21 vs -$0.25) but still a loss — essentially a wash, not a clear case either way for this single observation.

## Guardrail saturation check (D6b)
Today's only buy firing (9:30am ET) placed 4 trades — not zero — so the zero-buy saturation diagnostic does not apply. The 10:30/11:30/3:30 firings never reached Phase B at all today (10:30 and 3:30 are non-buy windows by design regardless; 11:30 is also a non-buy window; all three additionally never scanned due to the Step-0 balance gate). No firing this session placed zero buys after actually running Phase B, so there is nothing to flag here today.

## EOD liquidation suggestions (D7, advisory only — not executed automatically)
Only one symbol bought today remained open at analysis time: **SAIQ** (1 share, avg buy $3.3799). Not flagged: pct_change_since_buy is -4.73%, just short of the -5% gate (current $3.22 vs low_since_buy $3.11; near_low also fails at current price > low×1.02 = $3.17). Full detail in `results/eod_liquidation_suggestions_2026-10-15.csv`. These are suggestions only — no action is taken on them by this routine.

## Guardrail paper-trade ledger (D10)
Ledger: `results/guardrail_paper_trades.csv`, 1144 total rows (1122 resolved-closed, 21 open, 1 unresolved historical row with no exit data — a pre-existing delisted-symbol case, excluded from the stats below).

**Today's activity:** 33 new day-0 paper positions opened from today's single skipped_candidates file (3 candidates already had an open paper position from a prior day and were deduped). Of those 33: 28 closed via target, 3 via the 60-minute auto-liquidate, 2 still open (FFAI, EDUC). Separately, 29 pre-existing open rows were carried forward on daily bars: 3 closed via target, 6 via the 25% drawdown stop, 1 via the 4-session time stop (XRTX), 19 remain open (2 of those — LESL, MUZE — left untouched for lack of usable data: LESL's historicals call returned not_found, likely delisted; MUZE had zero real trades today, consistent with its own a7 thin-liquidity skip).

**Cumulative stats by guardrail** (closed rows only, grouped by guardrail letter since `skip_reason` strings have varied over the ledger's history — e.g. `a6`/`a6_reverse_split_proxy` are the same guardrail):

| Guardrail | n closed | Win rate | Mean ROI | Median ROI | Still open |
|---|---|---|---|---|---|
| a1 (spread) | 230 | 80.0% | +22.06% | +5.05% | 2 |
| a2 (ATR) | 108 | 62.0% | +135.69% | +4.32% | 0 |
| a3 (prior spike) | 248 | 74.2% | +39.92% | +5.00% | 6 |
| a4 (earnings recency) | 32 | 53.1% | -0.36% | +2.53% | 1 |
| a5 (compliance) | 176 | 72.7% | +31.79% | +4.22% | 4 |
| a6 (reverse-split proxy, under active review) | 197 | 65.0% | -0.47% | +4.82% | 3 |
| a7 (thin liquidity / stale quote) | 57 | 70.2% | -0.03% | +5.00% | 4 |
| a8 (leveraged/inverse ETF) | 73 | 63.0% | +2.59% | +2.12% | 1 |

(Mean ROI is heavily skewed by a handful of extreme-percentage penny-stock movers in a2/a3 — median is the more representative figure for most of these groups.)

**Baseline comparison (the number that matters):** over the same 2026-08-25→2026-10-15 window, the account's actual realized trades won **57.6% (106/184), net -$22.28** (matches `get_realized_pnl`'s -$22.28/-2.42% exactly). Every guardrail group above shows a HIGHER paper win rate than the account's real 57.6% baseline — including a6 at 65.0%. This is consistent with the account's three standing caveats (short-circuit ordering means skip_reason is only a candidate's first failure; paper fills have no spread/slippage vs real market-order fills; target fills assume a touch always fills) rather than evidence any guardrail should be loosened — a6 in particular remains under review pending a larger sample, per the 2026-08-25 note, and this routine does not change guardrail thresholds itself.
