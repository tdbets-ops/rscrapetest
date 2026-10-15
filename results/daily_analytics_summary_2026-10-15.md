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
