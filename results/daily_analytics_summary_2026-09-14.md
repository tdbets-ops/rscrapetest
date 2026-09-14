# Daily Analytics — 2026-09-14

**Headline:** 477 unique decliner candidates seen today across 3 firings (9:30, 10:30, 11:30 — the 3:30 firing skipped Phase A because starting buying power was $11.57, below the $50 floor). 240 in-scope for simulation (in_bracket/shallow_outside/deep_outside; the remaining 237 are `other_price_or_range` and out of scope per the 2026-07-28 cost-control rule). Overall simulated win rate: **57.5%** (n=240). In-bracket (the live buy population) win rate: **74.2%** (n=31).

## Win rate by time bucket
| bucket | win rate | n |
|---|---|---|
| morning | 57.5% | 240 |
| afternoon | — | 0 (3:30 firing had no scan data today) |

## Win rate by price bucket
| bucket | win rate | n |
|---|---|---|
| <1 | 63.8% | 69 |
| 1-3 | 61.6% | 86 |
| 3-10 | 48.2% | 85 |
| 10-120 / 120-500 | out of simulation scope | 0 |

## Win rate by decline bucket (bracket calibration)
| bucket | win rate | n |
|---|---|---|
| in_bracket (10-25% decline, <$10 — the live buy population) | **74.2%** | 31 |
| shallow_outside (5-10% decline, <$10) | 55.0% | 209 |
| deep_outside (>25-30% decline, <$10) | — | 0 |

## In-bracket win rate by firing (both 9:30 and 10:30 are live buy windows; 11:30 is not)
| firing | win rate | n |
|---|---|---|
| 9:30 | 70.0% | 20 |
| 10:30 | **88.9%** | 9 |
| 11:30 | 50.0% | 2 |
| 3:30 | — | 0 (no scan data) |

Small samples throughout (n=2 to n=20) — directional only. Today's numbers continue to favor both live buy windows over 11:30, consistent with the pattern that motivated adding 10:30 as a second buy window on 2026-08-25.

## Alternate exit scenarios (same 240-symbol population)
- **60-min auto-liquidate proxy** (D3a): triggered for 23/237 evaluable (9.7%), mean ROI if triggered −3.49%. In-bracket: 3/31 triggered, mean −4.31%.
- **EOD market-sell** (D3b): overall win rate 59.1%, mean ROI +1.09% (n=237). In-bracket: win rate 74.2%, mean ROI +5.70% (n=31) — very close to the primary decay/trailing result, suggesting in-bracket names that close, close early and cleanly.

**Scope note:** decline_bucket=`other_price_or_range` (237 of 477 symbols, mostly the $120-$500 data-collection list and shallow/deep declines outside the 5-30% tracking band) is excluded from simulation entirely per the standing cost-control rule — no historicals were fetched for these.

## Exit-rule firings (16a3 time-stop / drawdown-stop)
None fired today — no rows in any of today's `position_mgmt_*.csv` files carried a `time_stop` or `drawdown_stop` reason.

## Guardrail saturation check (D6b)
Both buy firings placed trades today (9:30: 5 buys, 10:30: 5 buys) — the zero-buy condition this check targets did not occur. For reference, skip-reason distribution was well spread at both firings (9:30: 15 skipped across 7 guardrail types, max single guardrail 4/15; 10:30: 23 skipped across 7 types, max single guardrail 6/23) — no single guardrail rejected anywhere close to 100% of candidates reaching it.

## EOD manual liquidation suggestions (D7, advisory only — NOT executed automatically)
Checked the 2 positions bought today still open at 4:30pm: **MKDW** (avg $6.8888, last $6.69, −2.9% since fill, low-since-buy $6.1619 — not near low) and **PCLA** (avg $7.75, last $7.70, −0.6% since fill, low-since-buy $7.340 — not near low). Neither met the −5%-decline threshold, so neither is flagged; `results/eod_liquidation_suggestions_2026-09-14.csv` is header-only.

## Decliner cohort tracking (D8)
241 new symbols logged to the 5-30%/<$10 tracking cohort today (13 required a fresh sector/industry fetch, the rest copied from prior history). The full log now holds 2,168 distinct symbols. Recovery-tracking update appended 146 rows today (capped to the 150 most-recently-added distinct symbols since the full distinct-symbol count exceeds 150; 4 of the 150 — WLFI, DOT, ENA, OP — returned no equity quote, consistent with crypto tickers rather than delisted equities).

**Data-quality flag (not fixed, per append-only rule):** 5 of today's new tracking rows (AERO, ARB, TXXD, LIT, AVNT) show implausible >1000% swings versus their original scan price — most consistent with the current quote resolving to a different/reissued security under the same ticker, a pre-existing data-quality issue in the historical log rather than a computation error introduced today.

## Guardrail paper-trade ledger (D10)
+29 new paper positions opened today (from 47 total skip rows across both buy firings, after excluding PCLA — bought for real at 10:30 despite being skipped at 9:30 — and de-duplicating 8 symbols skipped at both firings). 22 closed same-day; 8 pre-existing open positions were also carried forward, of which 8 closed today. 15 positions remain open. Ledger now totals 403 rows (388 closed / 15 open).

**Per-guardrail closed-trade stats** (win rate / mean ROI / n):
| guardrail | n closed | win rate | mean ROI | median ROI |
|---|---|---|---|---|
| a1 spread | 78 | 82.1% | +10.86% | +5.00% |
| a2 ATR | 36 | 52.8% | +112.84%† | +2.43% |
| a3 prior-spike | 85 | 84.7% | +113.50%† | +5.01% |
| a4 earnings-recency | 16 | 50.0% | −0.35% | +1.44% |
| a5 compliance | 61 | 73.8% | +80.45%† | +5.00% |
| **a6 reverse-split proxy** | 62 | 69.4% | +3.83% | +5.00% |
| a7 thin-liquidity | 23 | 65.2% | −0.57% | +5.00% |
| a8 leveraged/inverse ETF | 27 | 88.9% | +3.08% | +5.00% |

† fat-tail-skewed mean (a few extreme winners); median is the more representative figure for a2/a3/a5.

**a6 spotlight (under active review):** unlike a2/a3/a5, a6's paper-trade cohort shows **no fat-tail winner inflating the mean** — a modest, consistent +3.83% mean / +5.00% median / +237.4% summed ROI across 62 closed trades, i.e. a real but small foregone edge. Today also produced a concrete real-world validation of a6's risk rationale: **NXXT** (flagged a5;a6, blocked on reverse-split proxy) was confirmed to have undergone a genuine 1-for-10 reverse split between 2026-09-11 and today. Two data issues were handled in the ledger: NXXT's entry_price was restated ×10 to stay on the split-adjusted basis (original value preserved in the run notes), and **YYGH** (a pre-existing open paper position) is now delisted/inactive and was left untouched, flagged for manual review.

**Caveats** (per the routine's standing D10 caveats): (1) guardrails short-circuit in a1→a8 order, so skip_reason reflects only a candidate's first failure — the `fundamentals_guardrails_failed` column records every one of a5/a6/a8 a candidate would also have failed; a7 is not recomputed. (2) paper entries fill at the observed price with no spread/slippage, so every paper result is optimistic relative to a real market-order fill — most optimistic for exactly the thin, low-priced names these guardrails target. (3) target exits assume a limit fills whenever a bar's high touches it, matching both the live GTC behavior and the D3 simulation's assumption.

**Baseline for comparison:** today's real filled buys (10 trades across both windows) — see `trades_20260914T1337Z.csv` and `trades_20260914T1439Z.csv` for realized fills; a full realized-P&L comparison against the paper ledger's per-guardrail numbers is left to the account owner's review, per the routine's read-only mandate for this section.
