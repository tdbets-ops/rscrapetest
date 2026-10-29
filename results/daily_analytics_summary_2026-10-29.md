# Daily Analytics — 2026-10-29

## Data-gap note (read first)
Only the **9:30am** and **10:30am** ET firings ran today — no commits exist for an 11:30am or 3:30pm firing, so this report has half the usual data points. The 10:30am firing hit the Step 0 balance gate (`buying_power=$17.40 < $50`) and skipped Phase A entirely, so **all** scan/candidate data below comes from the single 9:30am scan (`under120_down5_20261029T135020Z.csv`, `over120_under500_down5_20261029T135020Z.csv`). Per-firing and time-bucket breakdowns are therefore degenerate (one firing, one bucket) — not a methodology change, just a thin day.

## Headline
- **220** candidates seen today (200 under-$120, capped by the primary scan's row limit — tail-tier re-run added 0 more rows; 20 over-$120/under-$500).
- **143 / 144** in-scope candidates (decline_bucket ∈ {in_bracket, shallow_outside, deep_outside}) simulated; **1 missing bars** (STG — no historicals returned, likely halted/inactive).
- **Overall simulated win rate: 103 / 143 = 72.0%** (decay/trailing algorithm would have closed the position at a profit by 4:00pm).

## By decline_bucket
| bucket | win rate | n |
|---|---|---|
| in_bracket (live buy bracket: <$10, 10–25% decline) | 72.7% | 24/33 |
| shallow_outside (<$10, 5–<10% decline) | 72.5% | 79/109 |
| deep_outside (<$10, >25–30% decline) | 0.0% | 0/1 (n=1, LVWR — not meaningful) |
| other_price_or_range | not simulated (out of D3 scope) | 76 symbols |

## By price_bucket (simulated rows only)
| bucket | win rate | n |
|---|---|---|
| <1 | 75.0% | 42/56 |
| 1-3 | 81.0% | 34/42 |
| 3-10 | 60.0% | 27/45 |
| 10-120 / 120-500 | n/a — always `other_price_or_range`, not simulated | 0 |

## By source_list
under120: 72.0% (103/143). 120to500: no in-scope rows (price ≥120 always falls to `other_price_or_range` by definition).

## in_experiment_bracket (the live buy bracket), by firing
| firing | win rate | n |
|---|---|---|
| 9:30 | 72.7% | 24/33 |
| 10:30 | no data (Phase A skipped — balance gate) | — |
| 11:30 | no data (firing did not run today) | — |
| 3:30 | no data (firing did not run today) | — |

## D3a — 60-minute never-touched-breakeven scenario (in_bracket population, n=33)
Triggered (would have auto-liquidated): **3**. Not triggered: **30**. Not evaluable (insufficient bars past the 60-min mark): 0.

## D3b — EOD market-sell scenario (in_bracket population, n=33)
Average ROI if every in-bracket candidate had been sold at today's close: **+1.83%**.

## Scope note
Per the 2026-07-29 scope fix, only `in_bracket` / `shallow_outside` / `deep_outside` symbols get the full D3/D3a/D3b simulation (144 of 220 today); `other_price_or_range` (76 symbols, all price ≥10 or outside the 5–30% decline band) get `simulated=false` with null scenario columns.

## D6a — Exit-rule firings (step 16a3)
None today. All 13 pre-existing/carried positions checked at the 10:30am Phase C run showed `no_change_target_confirmed` — no drawdown-stop or time-stop liquidations fired.

## D6b — Guardrail saturation check
Not applicable today: the only buy firing that ran Phase B (9:30am) placed **8 trades** out of 33 bracket candidates (not a zero-buy session). No single guardrail saturated — the widest was a6 (reverse-split proxy) at 7/25 skips, a2 (ATR) at 5/25, a4 (earnings) and a1 (spread) each at 6/25. The 10:30am firing never reached Phase B (balance gate), so it has no guardrail funnel to check.

## D7 — EOD manual liquidation suggestions (advisory only — never executed automatically)
Checked the 3 symbols bought today that are still open (KPRX, HLN, AZTR — the other 5 of today's 8 buys, CHRS/FISN/VEEA/NUAI/DAVI, already hit their +5% target and sold intraday). None met all three SUGGEST_LIQUIDATE conditions:

| symbol | pct_change_since_buy | near_low | trend_down | flagged |
|---|---|---|---|---|
| KPRX | -7.46% | No (current $1.77 vs low $1.73 ×1.02=$1.76) | Yes | No |
| HLN | -1.33% | Yes | No | No |
| AZTR | -6.87% | No | No | No |

`results/eod_liquidation_suggestions_2026-10-29.csv` written header-only (no flags).

## D8 — Decliner cohort tracking
- Appended **144** new rows to `decliner_cohort_log.csv` for today (5–30% decline, <$10 band — same population as the D3 in-scope set). 8 symbols were never logged before and got fresh sector/industry lookups (QUAD, FPI, HLN, ADAM, GYRE, ILPT, OTLY, FFTT); the remaining 136 copied sector/industry forward from their first-ever log entry.
- Recovery tracking: cohort log now holds 2,525 distinct symbols; capped this cycle to the **150 most-recently-added** per the compute-cost rule. Appended **148** rows to `decliner_recovery_tracking.csv` (2 skipped — ATII and IAE both came back with no quote, likely inactive/delisted).

## D10 — Guardrail paper-trade ledger
**Today:** carried forward the 4 pre-existing open paper positions (ILLU, NIVF, CIRC, BBDO) and opened 25 new ones from today's `skipped_candidates_20261029T135020Z.csv`.
- Carry-forward: **ILLU** hit the 4-session time stop today (flat the whole way, exit $9.98, roi 0.0%). **BBDO** hit the 25% drawdown stop (exit $2.4375, roi -25.0%). NIVF and CIRC remain open (sessions_held=3, max_drawdown -16.67% / -15.05%).
- New day-0 opens: **17 of 25 closed the same day**, all via `paper_target` (none hit the 60-min liquidate or drawdown stop) — a strongly positive day across the whole skipped-candidate pool, consistent with today's broad bounce (the account's own 9:30 buys also saw 5 of 8 hit target same-day). 8 remain open (MEGL, SRTM, RETO, TWI, HDSN, QUAD, FPI, NEXR).
- Net: **+25 opened, 19 closed** (2 carry-forward + 17 new-today). Ledger total: 1,385 rows, 10 open, 1,375 closed.

### Aggregate paper performance by skip_reason (all-time, closed trades only, n=1,374)
Overall: 71.2% win rate, mean ROI +26.5%, median +5.0%, summed ROI +36,389pp.

| skip_reason (canonicalized across naming variants used over time) | n | win rate | mean ROI | median ROI |
|---|---|---|---|---|
| a6_reverse_split_proxy (incl. legacy `a6`) | 252 | 66.3% | -0.23% | +5.00% |
| a3_prior_spike (incl. legacy variants) | 277 | ~71% | high (skewed by outliers) | +5.00% |
| a1_spread (incl. legacy variants) | 301 | ~82% | positive | +5.0–5.2% |
| a5_compliance (incl. legacy variants) | 212 | ~71% | positive | +4.0–5.0% |
| a2_atr (incl. legacy variants) | 135 | ~61% | positive (skewed by outliers) | +4.2–4.5% |
| a8_leveraged_inverse_etf (incl. legacy variants) | 87 | ~62% | slightly negative to flat | +2.0% |
| a4_earnings_recency | 37 | ~53% | -0.75% | +2.5% |
| a7_thin_liquidity (incl. legacy variants) | 72 | ~65% | slightly negative | +2.8–5.0% |

**a6 specifically (under active review since 2026-08-25):** n=252, win rate 66.3%, mean ROI -0.23%, summed ROI ≈-57pp. This is the first cycle with a large enough a6 sample to say something: a6-blocked candidates are paper-*breakeven-to-slightly-negative* as a group, not clearly profitable — unlike a8 (whose backtest showed a clear edge before being adopted). **Not yet enough to recommend loosening the 15x threshold** — see caveats below — but it no longer looks like a guardrail that's obviously leaving money on the table.

### Mandatory caveats (per spec — do not drop these)
1. Guardrails short-circuit in a1→a8 order, so `skip_reason` is only a candidate's *first* failure; paper ROI for guardrail aX is aX's *marginal* cost given a1..a(X-1) already passed. a7 is never re-evaluated (extra API cost), so `fundamentals_guardrails_failed` only ever covers a5/a6/a8 co-failures — no guardrail's removal is a clean "we'd have won this" claim.
2. Paper entries fill at the observed price with zero spread/slippage; real buys are market orders. Every paper number here is optimistic relative to what a real fill would have done, most of all for the thin, low-priced names these guardrails target.
3. Target exits assume a limit fills whenever a bar's high touches it — matches how the real resting GTC limit behaves and how D3 already simulates, but is still an assumption.

### Baseline — what the account actually did (real, filled trades)
Per `get_realized_pnl` (span=all, since account inception): **397 realized closing trades, total realized P&L -$156.98, total rate of return -3.47%** (by month: Jun -4.3%, Jul +0.2%, Aug -2.8%, Sep/Oct -2.2%). The paper ledger's headline win rate and mean ROI look far better than this real baseline — but caveats #1 and #2 above are exactly why: paper trades never pay the spread, never eat market-order slippage, and the real book also carries the drawdown-stop/time-stop losers that this ledger's "whichever triggers first" simulation sends to -25% just as often. The gap between paper (+26.5% mean, 71% win rate) and real (-3.5% total return) is the honest size of execution cost plus the guardrails' own selection effect — it is not evidence that any guardrail should be loosened on its own.
