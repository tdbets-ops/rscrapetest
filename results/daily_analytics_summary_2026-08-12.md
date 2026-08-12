# Daily Analytics — 2026-08-12

## Scope
- **488** unique candidates seen across today's 4 firings (9:30/10:30/11:30 ET morning cluster + 3:30 ET afternoon).
- **353 of 488** simulated (72.3%) — scope limited to `in_bracket` / `shallow_outside` / `deep_outside` (under-$10, 5–30% decline) per the 2026-07-29 scope fix. The remaining 135 rows (`other_price_or_range`: price ≥$10, decline >30%, or the $120–500 list) get classification fields only, no simulated outcome.
- 3 scoped symbols (ARKR, CGTL, HTCR) had zero real (non-interpolated) bars in their fetch window and are marked `simulated=false, note=no_real_bars`.

## Headline win rate
**51.6%** (183/353) of simulated candidates would have hit their decay/trailing take-profit target by market close.

## Win rate by time bucket (key number for the 10:30/11:30 expansion decision)
| Bucket | Win rate | n |
|---|---|---|
| Morning (9:30/10:30/11:30) | **59.8%** | 286 |
| Afternoon (3:30) | **16.4%** | 67 |

Morning candidates outperform afternoon candidates by a wide margin today, consistent with the account owner's premise for the morning-only buying experiment.

## Win rate by decline bucket (bracket-calibration number)
| Bucket | Win rate | n |
|---|---|---|
| in_bracket (10–25% decline, the live Phase B buy bracket) | **61.4%** | 57 |
| shallow_outside (5–10% decline) | 49.1% | 293 |
| deep_outside (25–30% decline) | 100.0% | 3 (too small to read into) |

The live 10–25% bracket outperforms the shallow-outside (5–10%) population today. `deep_outside` has only 3 samples — not meaningful on its own.

## in_experiment_bracket=true, split by time bucket (the exact population the account owner is deciding on)
| Bucket | Win rate | n |
|---|---|---|
| bracket-morning | **66.0%** | 53 |
| bracket-afternoon | 0.0% | 4 |

Small samples (especially afternoon, n=4) — directionally supports morning-only buying but not statistically strong on its own.

## Win rate by price bucket
| Bucket | Win rate | n |
|---|---|---|
| <1 | 51.8% | 112 |
| 1-3 | 57.0% | 128 |
| 3-10 | 45.1% | 113 |
| 10-120 | n/a | 0 (out of scope) |
| 120-500 | n/a | 0 (out of scope) |

10-120 and 120-500 show n=0 because D3's simulation scope excludes `other_price_or_range` — this is the intended scope reduction, not a data gap.

## Win rate by source list
under120: 51.6% (n=353). 120to500 shows n=0 — that list is data-collection-only and out of D3's simulation scope by design.

## Cross-reference: today's real trades
All 5 of today's real Phase B buys (PMA, IPCX, CBRZ, RECT, TAOP) fell in `in_bracket` as expected. Per the simulated decay/trailing outcome: **PMA, IPCX, TAOP closed (win)**; **CBRZ, RECT did not close** by market close (still open, per the resting-order-based simulation — note: real positions are managed live by Phase C, so their actual state may differ from this idealized simulation). Sample too small (n=5) for guardrail-metric correlation beyond this simple match.

## Liquidation-rule scenario (D3a)
Within `in_experiment_bracket=true` rows, 53 of 57 were evaluable (≥60 min of session remained after first-seen); of those, 4 would have triggered the same-day 60-minute auto-liquidate rule (never touched breakeven in the first 60 minutes).

## Suggested end-of-day liquidations (advisory, not executed)
Only one symbol bought today remains open: **RECT** (3 sh, avg buy $1.2304, current $1.20). Not flagged — pct_change_since_buy is only -2.5%, above the -5% flagging threshold (minor decline, not actively falling — same category as the ARI reference case). No liquidation suggested. Full detail in `results/eod_liquidation_suggestions_2026-08-12.csv`. These are advisory only — nothing here is executed automatically; the account owner reviews and decides.

## Caveats
- Afternoon and deep_outside sample sizes are small (n=67 and n=3 respectively) — do not overstate confidence.
- The decay/trailing simulation assumes an idealized fill at target price the instant a bar's high touches it; real fills may differ (slippage, partial fills).
- 3 symbols excluded from simulation due to no real trade data in their fetch window.
