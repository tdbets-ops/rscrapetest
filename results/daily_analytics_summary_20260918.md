# Daily Analytics — 2026-09-18

**Data gap:** No 3:30pm ET firing ran today (no `*20260918T19*Z` scan files exist). Only 9:30/10:30/11:30 firings scanned today. All stats below reflect 3 of the 4 usual firings.

**Second gap, more serious:** Phase D never ran on 2026-09-17 — no `daily_analytics_2026-09-17.*` files or commit exist, even though all four 2026-09-17 scan/trade firings completed normally and their raw CSVs are intact. This means the guardrail paper ledger's day-1+ carry-forward for the 14 positions open as of 2026-09-16 was never advanced through 09-17. This run performed that catch-up (see D10 below) using 09-17's daily bar before opening today's new paper positions, but the 09-17 `daily_analytics` / cohort-tracking / EOD-suggestion outputs themselves are permanently missing and were not backfilled here (out of scope for a single 4:30pm firing — flagging for the account owner).

## Headline win rate (D3 simulation, decay/trailing rule)

| Population | n | Win rate |
|---|---|---|
| Overall (simulated) | 187 | 58.8% |
| In-bracket (10–25% decline, <$10) | 40 | 70.0% |
| Shallow-outside (5–10% decline, <$10) | 147 | 55.8% |
| Deep-outside (25–30% decline, <$10) | 0 | n/a (none seen today) |

simulated_count=187, total_candidates_seen=309 (119 classified `other_price_or_range`, out of D3 scope by design). 3 in-scope symbols (RMBC, EO, SFWL) returned no historicals bars and were excluded from simulation (simulated=false).

**By time bucket:** morning 58.8% (n=187) — no afternoon data (3:30 firing missing).
**By price bucket:** <$1: 67.2% (n=64); $1–3: 50.9% (n=55); $3–10: 57.4% (n=68).
**By source list:** under120 58.8% (n=187); 120–500: no data (that universe is entirely `other_price_or_range`, excluded from D3 by design).

### In-bracket, by firing (the direct evidence for the two-buy-window question)

| Firing | n | Win rate |
|---|---|---|
| 9:30 | 27 | 70.4% |
| 10:30 | 10 | 70.0% |
| 11:30 | 3 | 66.7% |
| 3:30 | 0 | no data today |

10:30's in-bracket win rate (70.0%, n=10) is essentially identical to 9:30's (70.4%, n=27) and both are well above 11:30's (66.7%, n=3, tiny sample). This is one more day of evidence that the second buy window is performing in line with the first — consistent with the account owner's original morning-outperformance thesis — though 11:30's n is too small to draw a conclusion about extending buying there.

## Exit-rule firings (D6a)

None today — no step 16a3 (time-stop/drawdown-stop) liquidations fired.

## Guardrail saturation check (D6b)

Not applicable — both buy firings placed trades today (9:30: 2/27 bracket candidates bought; 10:30: 3/33 bought), so there was no zero-buy firing to audit.

## EOD liquidation suggestions (D7, advisory only)

Only one today-bought position was still open at 4:30pm: **NUWE** (5 sh @ $0.93). GWAV, AEI, RTB, TRVG (the other four buys from today) all closed via resting sell orders before this firing. NUWE checked: pct_change_since_buy -2.92% (fails the -5% gate), near_low=false, trend_down=true — **not flagged** (fails on the pct_change leg alone). See `results/eod_liquidation_suggestions_20260918.csv`.

## Cohort tracking (D8)

190 candidates matched the 5–30%/<$10 tracking band today, all newly appended to `decliner_cohort_log.csv` (2,203 distinct symbols now logged all-time). 6 needed a fresh sector/industry fetch (LWLX, YFOR, NFLY, NGEN, QNTU, SMSI); the rest copied existing values. `decliner_recovery_tracking.csv` updated for the 150 most-recently-added symbols (capped per the ~150 rule; 2,203 total distinct symbols tracked); 3 symbols (DOT, UNI, WLFI) returned no quote and were skipped as likely delisted.

## Guardrail paper-trade ledger (D10)

- **Catch-up:** because Phase D missed 09-17, the 14 positions open as of 09-16 were advanced through 09-17's daily bar before today's new opens. 8 closed on that catch-up pass (ZKIN, MGN, GTBP, BIAF, WETO, GIPR, SLXN, BLSG); 6 remain open (YYGH — still `not_found`/likely delisted, FEBO, CDT, PLAY, CODX, LITZ).
  - **Data-quality flag: MGN.** Its 09-16 catch-up bar shows a >30x price discontinuity versus its 0.1436 entry price (09-15), producing a nonsensical +3278% "paper_target" exit. This is the same MGN already flagged as a data gap in the 09-17 commit message — treat this row's ROI as an artifact, not a real signal.
- **Today's opens:** 41 new paper positions from today's 55 skip-candidate rows (deduplicated to 43 unique symbols across the 9:30/10:30 firings; 2 already had an open row and were skipped per the dedup rule). 31 closed same-day, 10 still open.
- **Ledger totals:** 483 rows all-time (467 closed, 16 open).

### Results by skip_reason (closed rows, all-time)

| skip_reason | n | win rate | mean ROI | median ROI | sum ROI |
|---|---|---|---|---|---|
| a3_prior_spike | 102 | 80.4% | 96.4% | 5.0% | 9830% |
| **a6_reverse_split_proxy** | **73** | **65.8%** | **2.5%** | **5.0%** | **183%** |
| a1_spread | 59 | 81.4% | 2.2% | 5.0% | 128% |
| a5_compliance | 56 | 73.2% | 57.7% | 4.6% | 3233% |
| a2_atr | 25 | 64.0% | 167.8% | 5.0% | 4194% |
| a4_earnings_recency | 19 | 52.6% | -0.1% | 3.0% | -1% |
| a8_leveraged_etf / a8_leveraged_inverse_etf | 30 | 73.3% | 0.7% | 2.1% | 21% |
| a7_thin_liquidity (+ variants) | 20 | 65.0% | -0.2% | 3.8% | -3% |
| (older-naming variants: a1_spread_guardrail/_pct/_gate, a2_atr_guardrail, a5_compliance_guardrail, a3_prior_spike_guardrail, a7_stale_quote*, a8_*_gap) | ~62 | mixed | mixed | mixed | mixed — see raw ledger |

**a6 is called out per the review request:** n=73, 65.8% win rate, mean +2.5%/median +5.0%/summed +183%. Under review since 08-25 as the single largest blocker some days; this sample says its blocked population has NOT been a clear net negative — if anything it is mildly profitable on paper. This is one more day of data toward that open question, not a verdict (see caveats below).

**Naming-drift note:** skip_reason strings have not been consistent across the routine's history (e.g. `a1_spread` vs `a1_spread_guardrail` vs `a1_spread_pct` vs `a1_spread_gate` all appear to be the same guardrail under different labels from different days). This wasn't normalized in this run — reported as-is.

**Baseline for comparison (D10g):** the account's actual realized trades over the last 30 days (`get_pnl_trade_history`, span=month): 97 closed trades, **58.8% win rate**, but **net realized P&L of -$8.40** (-1.69% total return) — losers are on average larger than the capped +5% winners. a6's paper win rate (65.8%) is higher than the account's actual realized win rate (58.8%), and a6's mean/median paper ROI (+2.5%/+5.0%) is positive and comparable to a real winning trade's capped target. This is suggestive that a6 is not obviously saving the strategy from bad outcomes, but per the caveats below this is not a clean apples-to-apples comparison.

**Mandatory caveats:**
1. Guardrails short-circuit in a1→a8 order; skip_reason is a candidate's *first* failure. `fundamentals_guardrails_failed` records every one of a5/a6/a8 also failed, but a7 is never re-evaluated (needs an extra call), so no result here is a clean "we would have won this."
2. Paper entries fill at the observed price with no spread/slippage; real buys are market orders, so every paper result is optimistic relative to a real fill — most of all for the thin, low-priced names these guardrails target.
3. Target exits assume a limit fills whenever a bar's high touches it, matching how the real resting GTC limit behaves and how D3 already simulates, but it is still an assumption.

## Data-integrity finding worth flagging separately

The 2026-09-18 11:30am ET firing's commit message states: *"GWAV and TRVG both hit their resting sell targets and closed since the 10:30 firing (GWAV +8.1%, TRVG +5.0%)."* Checking the actual fills (`get_equity_orders` / `get_pnl_trade_history`): TRVG did close at +5.0% via its normal GTC decay target (6.23 vs 5.93 cost) as described. **GWAV did not** — it was bought at $2.4642 and sold via a GFD limit order at $2.20 (filled $2.2646), a **realized loss of -8.1%** (confirmed by `get_pnl_trade_history`: realized_gain -$0.20 on that GWAV sell), not a gain. The order shape (GFD, priced below cost) matches an a2-style auto-liquidation, not a decay-target hit — but no `position_mgmt` log entry from any cloud firing shows this order being placed, suggesting it came from the local every-10-minute Phase C loop mentioned throughout this routine's instructions, which isn't visible in this repo's `results/` files. Recommend the account owner double-check that GWAV fill and the 11:30 commit message; the git history currently overstates the day's realized P&L by roughly $0.40 (a swing from the two, +8.1% believed vs -8.1% actual on the same $2.46 cost basis).
