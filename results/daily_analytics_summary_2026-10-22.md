# Daily Analytics — 2026-10-22

## Overview

- Firings today: 9:30, 10:30, 11:30, 3:30 ET. **Only 9:30 and 10:30 ran Phase A** (scan) — the 11:30 and 3:30 firings hit the balance gate (`buying_power=$18.87 < $50`) and skipped Phase A per instructions, so there are only 2 under-$120 scan files and 2 $120-$500 scan files for today, not 4.
- **Filename bug note (pre-existing, not from today's run):** `under120_down5_20261022T0934Z.csv` / `over120_under500_down5_20261022T0934Z.csv` carry a mislabeled UTC timestamp — the actual 9:30am ET firing scanned/evaluated at ~13:41 UTC (confirmed via that firing's `trades_`/`skipped_candidates_` files, both stamped `evaluated_at=2026-10-22T13:41:00Z`), not 09:34 UTC. The `...T1433Z` pair from the 10:30 firing is correctly UTC-stamped. This analysis uses the correct firing-derived timestamps; both interpretations fall in the "morning" time bucket regardless, so it does not distort today's numbers, but the filename itself should be fixed at the source in a future update.
- Candidates seen today (both lists, deduped by symbol): **641**. In-scope for simulation (decline_bucket in `in_bracket`/`shallow_outside`/`deep_outside`): **325**. Out of scope (`other_price_or_range` — includes the entire $120-500 list and any under-$120 row outside the 5-30% decline band): **316**.

## D5 — Win-rate breakdown (simulated rows only)

| Cut | Win rate | n |
|---|---|---|
| **Overall** | 46.8% | 325 |
| By time bucket: morning | 46.8% | 325 |
| By time bucket: afternoon | n/a | 0 |
| By price bucket: <1 | 58.8% | 119 |
| By price bucket: 1-3 | 46.8% | 94 |
| By price bucket: 3-10 | 33.9% | 112 |
| By price bucket: 10-120 / 120-500 | n/a (out of scope, not simulated) | 0 |
| By source list: under120 | 46.8% | 325 |
| By source list: 120to500 | n/a (out of scope) | 0 |
| By decline bucket: in_bracket | 68.6% | 35 |
| By decline bucket: shallow_outside | 43.8% | 288 |
| By decline bucket: deep_outside | 100.0% | 2 |

### in_experiment_bracket=true, by firing (new 2026-08-25 breakdown)

| Firing | Win rate | n |
|---|---|---|
| 9:30 | 64.0% | 25 |
| 10:30 | 80.0% | 10 |
| 11:30 | n/a | 0 (no Phase A today) |
| 3:30 | n/a | 0 (no Phase A today) |

**Scope note:** per the 2026-07-29 scope fix, only `in_bracket`/`shallow_outside`/`deep_outside` rows are simulated (325 of 641 candidates, 50.7%). This keeps the per-firing simulation tractable but means the 120-500 list and most of the under-$120 list contribute no win-rate signal here — they still feed the raw CSV for other uses.

**Cross-reference with real trades/skipped candidates:** Today's two buy windows placed 11 real trades (1 at 9:30: AUNA; 10 at 10:30: USDE, NUAI, DRIO, CEPL, SHMD, QTEX, PPSI, USAS, SCZM, ROLR) and logged 52 skipped-candidate rows (24 at 9:30, 28 at 10:30, 15 of which were the same symbol re-skipped at both firings). Only 4 of the 11 real trades (AUNA, DRIO, PPSI, ROLR) fall inside the **first-seen** `in_experiment_bracket=true` set used for this table — the other 7 (USDE, NUAI, CEPL, SHMD, QTEX, USAS, SCZM) were seen at 9:30 outside the 10-25% bracket (5-10% declines) and only deepened into the bracket by the 10:30 rescan, which Phase B correctly re-evaluates live per firing (as documented in the routine). This is expected behavior, not a bug — it just means the per-firing `in_experiment_bracket` counts above (25 @ 9:30, 10 @ 10:30) undercount Phase B's own live bracket counts that firing (25 and 40 respectively, per the buy-window commit messages), because D2's `in_experiment_bracket` is fixed at first-seen values rather than re-evaluated per firing.

## D6a — Exit-rule firings (step 16a3)

None today. No position was liquidated via the time-stop or drawdown-stop rule this session (per `results/latest_position_mgmt.csv` from the 3:30pm firing — all 10 tradable positions were `no_change`).

## D6b — Guardrail saturation check

Both buy firings placed at least one real trade today (9:30: 1 trade; 10:30: 10 trades), so the zero-buy-firing trigger condition for this check **did not occur** — nothing to flag.

## D7 — EOD liquidation suggestions (advisory only)

Evaluated the 5 positions bought today that are still open (QTEX, USAS, USDE, NUAI, CEPL — AUNA, DRIO, PPSI, ROLR, SCZM, SHMD already hit their take-profit limits and closed earlier today, all for gains). None met all three SUGGEST_LIQUIDATE criteria (pct_change_since_buy ≤ -5% AND near_low AND trend_down) — worst was CEPL at -4.6%, just short of the -5% threshold. `results/eod_liquidation_suggestions_2026-10-22.csv` is header-only. **These are advisory only — no action taken.**

## D8 — Decliner-bracket cohort tracking

- Appended 325 new rows to `decliner_cohort_log.csv` (5-30% decline band, <$10, today's two scans). 5 symbols (AUNA, OKYO, FSM, RES, TLX) were never logged before and got fresh sector/industry from `get_equity_fundamentals`; the rest reused their existing sector/industry values.
- Recovery-tracking pass: 2,500 distinct symbols in the full cohort log; capped to the 150 most-recently-added per the cap rule. Of those, 148 quoted successfully and got a new row in `decliner_recovery_tracking.csv`; 2 (IAE, ATII) returned no quote (likely delisted/untradeable) and were skipped and noted here rather than logged with bad data.

## D10 — Guardrail paper-trade ledger

**Today:** opened 30 new paper positions from today's 52 skipped-candidate rows (7 excluded as already-open symbols, 15 excluded as same-symbol duplicates across the two firings, 0 excluded as already-bought-for-real). Day-0 simulation closed 23 of the 30 same day (mostly `paper_target`, three via 60-minute auto-liquidate); 7 carried into tomorrow still open. Carry-forward pass on the 51 pre-existing open rows (oldest from 2026-10-14) closed 40 more (18 via `paper_target`, 11 via the 4-session time stop, 11 via the -25% drawdown stop) and left 11 still open. Net: **+30 opened, +63 closed today (23 day-0, 40 carried-forward), 18 still open.**

**Data-quality note:** DKI (entry 2026-10-20, entry_price $0.045, skip_reason a2_atr) underwent what the data strongly indicates was an unannounced reverse split (~1-for-50) effective 2026-10-22 — split-adjusted historicals retroactively restated its prior-day prices from ~$0.04-0.05 to ~$2-4.6, while raw unadjusted bars confirm the pre-split $0.04-0.05 range held through 10-21 and the post-split ~$2.00 range began today. This produced a nonsensical +4833% raw result that was corrected by hand to the mechanically-correct outcome implied by the (scale-invariant) -25% drawdown rule: `paper_drawdown_stop_25pct`, exit -25.0%, 1 session held. No other row in today's batch showed a comparable anomaly (next-largest move was well under ±60%).

### Per-skip-reason results (closed paper trades, full ledger history to date)

Guardrail-tag naming has drifted over time (e.g. `a6` / `a6_reverse_split_proxy` are the same guardrail logged under different tags before/after the 2026-08-25 standardization) — not corrected retroactively here, shown as logged:

| skip_reason | n | win% | mean roi% | median roi% | sum roi% |
|---|---|---|---|---|---|
| a3_prior_spike | 229 | 74.7% | +43.2% | +5.0% | +9886.9 |
| **a6_reverse_split_proxy** | **227** | **65.6%** | **-0.1%** | **+5.0%** | **-20.7** |
| a1_spread | 139 | 82.0% | +2.3% | +5.0% | +319.8 |
| a5_compliance | 126 | 69.0% | +30.9% | +4.1% | +3898.2 |
| a1_spread_guardrail | 74 | 79.7% | +44.8% | +5.1% | +3313.6 |
| a5_compliance_guardrail | 74 | 77.0% | +23.5% | +5.0% | +1739.0 |
| a2_atr | 69 | 59.4% | +57.7% | +4.5% | +3983.1 |
| a2_atr_guardrail | 58 | 65.5% | +183.9% | +4.2% | +10668.4 |
| a8_leveraged_etf | 41 | 56.1% | -2.8% | +2.0% | -115.7 |
| a3_prior_spike_guardrail | 39 | 64.1% | +20.5% | +5.0% | +799.6 |
| a1_spread_pct | 38 | 84.2% | +23.2% | +5.2% | +880.4 |
| a4_earnings_recency | 34 | 50.0% | -1.0% | +1.0% | -35.3 |
| a8_leveraged_inverse_etf | 32 | 71.9% | +1.9% | +5.0% | +59.1 |
| a7_thin_liquidity | 27 | 66.7% | -1.7% | +2.8% | -46.3 |
| a1_wide_spread | 21 | 85.7% | +39.4% | +5.0% | +828.0 |
| (remaining smaller-n tags, n≤15 each) | 48 combined | — | — | — | — |

**a6 (reverse-split proxy), explicitly:** combining `a6_reverse_split_proxy` + `a6`, n=229 closed paper trades, win rate 65.4%, mean roi ≈ -0.2%, summed roi ≈ -40.7. This is the guardrail currently under active review (it was the single largest blocker at the 10:30 firing on 2026-08-25). The paper ledger shows it is **roughly breakeven on average** — markedly worse than a3 (+43% mean), a2_atr_guardrail (+184% mean), a1-family (+2% to +45% mean), or a5-family (+24-31% mean) — i.e., the paper evidence so far does **not** support loosening the 15x threshold; names it blocks have performed like a coin flip rather than like the strong-but-missed opportunities a looser guardrail would need to show.

**Required caveats (apply to all of the above):**
1. Guardrails short-circuit in a1→a8 order, so `skip_reason` is only a candidate's *first* failure — a paper winner tagged a6 might also have failed a7 or a8 had a6 not existed (a7 is never re-checked, by design, since it needs an extra API call per candidate; the `fundamentals_guardrails_failed` column does capture co-failures among a5/a6/a8).
2. Paper entries fill at the observed price with no spread and no slippage; real buys are market orders, so every paper result is optimistic relative to what a real fill would have achieved — most optimistic for exactly the thin, wide-spread, low-priced names these guardrails target.
3. Target exits assume a limit fills whenever a bar's high touches it, matching how the real resting GTC limit behaves (and how D3 already simulates) — but it is still an assumption, not an observed fill.

**Baseline comparison (account's actual realized results, per `get_realized_pnl` / `get_pnl_trade_history`):**
- Trailing 30 days (2026-09-23 → today): 114 realized round trips, **57.0% win rate**, **-$13.71 realized** (-2.49%).
- Since inception (2026-06-23 → today): **-$153.05 realized** (-3.47%), with a strong-positive first month (+$52.80, 32 trades) followed by three successive losing months (-$178.98, -$13.16, -$13.71).
- Side-by-side: a6's paper population (n=229, win 65.4%, mean ~breakeven) shows a *higher win rate but near-zero average return* versus the account's real trades (57.0% win rate but also near-breakeven over the trailing month). Neither population is clearly strong — this does not make a case that loosening a6 would have beaten what the account actually achieved.
