# Daily Analytics — 2026-10-13

**Headline:** 378 candidates seen today (9:30 + 10:30 scans only — 11:30/3:30 were balance-gated out of Phase A before scanning). D3 scope (in_bracket/shallow_outside/deep_outside) = 282 symbols; 280 simulated (2 zero-bars: EQS, RYOJ). **Overall win rate: 50.0% (140/280).**

## Win rate breakdowns (simulated=true rows only)

| Dimension | Bucket | Win rate | n |
|---|---|---|---|
| decline_bucket | in_bracket | 64.4% | 45 |
| decline_bucket | shallow_outside | 46.8% | 231 |
| decline_bucket | deep_outside | 75.0% | 4 |
| price_bucket | <1 | 43.8% | 89 |
| price_bucket | 1-3 | 54.1% | 109 |
| price_bucket | 3-10 | 51.2% | 82 |
| time_bucket | morning (all) | 50.0% | 280 |

Scope note: only symbols with first_seen_price < 10 fall into in_bracket/shallow_outside/deep_outside, so price_bucket 10-120/120-500 have no simulated rows (178 other_price_or_range were skipped entirely, no historicals fetched).

**In-bracket by firing** (the direct evidence on the two live buy windows):

| Firing | Win rate | n |
|---|---|---|
| 9:30 | 58.1% | 31 |
| 10:30 | 78.6% | 14 |

Both windows outperform the shallow_outside control band; 10:30's small sample (n=14) means this is suggestive, not conclusive. 11:30/3:30 produced no scan data today (balance-gated), so no cross-firing comparison beyond the two buy windows is possible.

**Cross-reference with real activity:** of today's 7 real buys, 6 closed at the +5% decay target same-day; ENHA (9:30) is still open at -3.89% (vs -4.05% at the real fill price — the gap is just scan-price vs fill-price). Of 70 bracket-eligible candidates a guardrail skipped, 39 would have closed as winners in simulation vs 31 still open — this is a shallow pass, not a guardrail-by-guardrail audit (see D10 below for that).

## D6a — Exit-rule (16a3) ledger
No time-stop or drawdown-stop (16a3) liquidations fired today. Section omitted.

## D6b — Guardrail saturation check
Not applicable: both buy firings (9:30, 10:30) placed trades (1 and 6 respectively), so no zero-buy diagnostic was triggered.

## D7 — EOD liquidation suggestions (advisory only)
1 position evaluated (ENHA, the only symbol bought today still open): **not flagged.** pct_change_since_buy = -4.05%, which does not clear the -5% gate, so near_low/trend_down were never evaluated. See `results/eod_liquidation_suggestions_2026-10-13.csv`.

## D8 — Decliner cohort / recovery tracking
- Cohort log: +282 new rows today (7 freshly fundamentals-tagged: BDAI, IREX, FURY, ECC, ONEG, ADSE, ALMU; 275 copied sector/industry from history). Distinct symbol universe now **2,391**.
- Recovery tracking: +148 rows, capped to the 150 most-recently-added symbols (2 skipped: PSKY/HBAR, no data — consistent with prior sessions).

## D10 — Guardrail paper-trade ledger

**Data-quality note:** the `day`-interval historicals endpoint does not yet publish today's bar as of the 4:30pm ET firing (verified: it returns nothing newer than yesterday for every symbol tested, even though all but one symbol had real trades today per 5-minute bars). A background subagent's first attempt at the day-1+ carry-forward step used this broken endpoint and produced unreliable results (it fabricated exit data for 7 symbols that doesn't match real intraday prices, and wrongly marked 25 genuinely-traded symbols as "no data"). That output was discarded and the day-1+ step was redone directly using verified 5-minute bars aggregated to today's OHLC (high/low/close), cross-checked against live quotes before trusting it. The day-0 new-position simulation (a separate subagent, using 5-minute bars correctly per spec) was spot-checked and is sound.

**Today's ledger activity:**
- Day-0 (51 new positions opened from today's skipped_candidates, deduped against existing open rows and today's real buys): 26 closed via decay/trailing target, 12 via 60-min auto-liquidate, 1 via drawdown stop (CIFC, -25%), 12 still open.
- Day-1+ carry-forward (33 pre-existing open rows): 14 closed (4 time-stop at 4 sessions: LUD/SCAG/BRNX/JAGX; 6 drawdown-stop: CIFG/CIFU/RETO/NXTS/BIAF/RUBI; 4 target: SSM/VEEA/VJET/GP), 18 still open, 1 no-data (LESL, consistent with its prior delisted/no-data flag).
- Ledger total: **1,038 rows (31 open, 1,007 closed)**, +51 opened today.

**Per-guardrail results (closed trades only, grouped by normalized guardrail), with 31 still open:**

| Guardrail | n closed | Win rate | Mean ROI | Median ROI | Sum ROI | Still open |
|---|---|---|---|---|---|---|
| a1 (spread) | 199 | 79.9% | +25.27% | +5.03% | +5,029.0 | 3 |
| a2 (ATR) | 94 | 59.6% | +155.69% | +4.15% | +14,635.0 | 1 |
| a3 (prior spike) | 230 | 75.2% | +43.17% | +5.00% | +9,930.0 | 9 |
| a4 (earnings recency) | 28 | 53.6% | +0.31% | +2.53% | +8.8 | 2 |
| a5 (compliance) | 156 | 73.1% | +36.18% | +4.22% | +5,644.5 | 5 |
| **a6 (reverse-split proxy)** | **176** | **65.9%** | **-0.26%** | **+4.49%** | **-45.9** | **7** |
| a7 (thin liquidity) | 51 | 70.6% | -0.49% | +4.09% | -24.9 | 1 |
| a8 (leveraged/inverse ETF) | 71 | 64.8% | +3.37% | +2.24% | +239.2 | 3 |

**a6, the guardrail under review since 2026-08-25:** at n=176, win rate 65.9% and median +4.49% remain unremarkable versus the other guardrails (a4 and a7 are actually lower on win rate and mean). a6's majority-positive median with a near-zero mean indicates a handful of large losers (several of today's drawdown-stops were a6-flagged names) pulling the average down despite most trades closing near the +5% target. Still no evidence a6 is an outlier worth loosening; the sample has grown but the conclusion from the last several sessions holds.

**Caveats (apply to every row above):**
1. Guardrails short-circuit in a1→a8 order, so `skip_reason` is only a candidate's *first* failure. Per-guardrail results measure that guardrail's *marginal* cost given earlier guardrails already passed — decision-relevant, but not a clean "we'd have won this without the guardrail" claim (a winner blocked by a6 might also have failed a7 or a8, which we don't re-check — a7 needs an extra call per candidate and is deliberately excluded).
2. Paper entries fill at the observed scan price with no spread or slippage; real buys are market orders. Every paper result is optimistic relative to what a real fill would have done, most of all for the wide-spread/thin/low-priced names these guardrails target.
3. Target exits assume a limit fills whenever a bar's high touches it — matching how the real resting GTC limit behaves, but still an assumption.

**Baseline — actual realized P&L (get_realized_pnl, span=all):** **-$152.95 / 341 trades (-3.58%)**. Every guardrail's paper win rate and median above is positive, while the account's actual realized result over the same history is negative — consistent with every prior session's finding. This gap is largely explained by caveat #2 (paper fills are frictionless) plus the asymmetric exit structure (capped +5% winners vs uncapped-at-market losers), and does not by itself argue for loosening any guardrail.
