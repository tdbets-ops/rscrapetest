# Daily Analytics — 2026-10-20 (4:30pm ET firing)

## Headline

- **429** unique candidates seen today across all 4 firings (426 under-$120, 3 $120–$500).
- **312** in scope for simulation (in_bracket / shallow_outside / deep_outside; 117 `other_price_or_range` skipped per the 2026-07-29 scope fix — price ≥$10, or decline ≥30% treated as untrustworthy/out of range).
- Overall simulated win rate (decay/trailing target reached by close): **32.4%** (101/312).
- Bracket (the live buy filter) win rate: **56.1%** (32/57) — well ahead of the unscoped population, consistent with prior days.
- 2 real buy windows ran today (9:30, 10:30); both placed trades (RAIL+AIAI at 9:30, LCU at 10:30). No zero-buy firing.

## Win rate breakdown (simulated rows only)

| Cut | n | Win rate |
|---|---|---|
| Overall | 312 | 32.4% |
| Morning (9:30/10:30/11:30) | 236 | 34.7% |
| Afternoon (3:30) | 76 | 25.0% |
| decline_bucket: in_bracket | 57 | 56.1% |
| decline_bucket: shallow_outside | 253 | 27.3% |
| decline_bucket: deep_outside | 2 | 0.0% (n<10, ignore) |
| price <1 | 105 | 30.5% |
| price 1-3 | 107 | 34.6% |
| price 3-10 | 100 | 32.0% |

### In-bracket, by firing (direct evidence for the two-window buy expansion)

| Firing | n | Win rate |
|---|---|---|
| 9:30 | 34 | 70.6% |
| 10:30 | 16 | 31.2% |
| 11:30 | 2 | 50.0% (n<10, ignore) |
| 3:30 | 5 | 40.0% (n<10, ignore) |

9:30 is the clear standout today (70.6%), while 10:30's in-bracket win rate (31.2%) came in below the overall bracket average — a reminder that the two-buy-window comparison is noisy day to day with these sample sizes. Neither 11:30 nor 3:30 has enough observations (n<10) to draw a conclusion; nothing here argues for extending buying to either.

*Note: "by firing" groups a symbol under whichever firing FIRST saw it today (per D2), which differs from how many candidates each firing's live Phase B bracket filter evaluated (that re-scans fresh each firing and can include symbols first seen earlier in the day still sitting in the bracket).*

## Guardrail saturation check (D6b)

Both buy firings today placed trades (9:30: 2 buys — RAIL, AIAI; 10:30: 1 buy — LCU), so there is **no zero-buy firing to flag**. Skip-reason counts for context:

- 9:30 skipped (32 total): a1_spread 11, a6_reverse_split_proxy 9, a8_leveraged_etf 5, a2_atr 3, a5_compliance 3, a7_stale_quote 1.
- 10:30 skipped (36 total): a1_spread 13, a6_reverse_split_proxy 6, a2_atr 5, a5_compliance 5, a7_stale_quote 3, a3_prior_spike 2, a8_leveraged_etf 2.

No single guardrail rejected 100% of candidates that reached it at either firing — a1 (spread) remains the largest blocker at both firings, consistent with recent days, not a new pattern.

## Exit-rule firings (D6a)

None today. All four of today's position_mgmt files (9:30/10:30/11:30/3:30) were checked for `time_stop_4_sessions`, `time_stop_overdue`, and `drawdown_stop_25pct` reason strings — none appear. RAIL and AIAI (both bought at 9:30) were sold at a loss roughly 63 minutes after entry (RAIL -7.9%, AIAI -5.4%) via the ordinary decayed-limit cancel/replace mechanism, not a tagged step-16a3 exit rule — called out here for transparency but not counted as an exit-rule firing since it doesn't carry one of the three reason strings.

## EOD manual liquidation suggestions (D7) — advisory only, no orders touched

1 symbol bought today (LCU, from the 10:30 buy window) is still open. It **is flagged `SUGGEST_LIQUIDATE`**:
- LCU: down 6.18% since buy (entry $4.8499, current $4.55), near its intraday low ($4.50, within 1.1%), and trending down (first-half avg $4.6114 vs second-half avg $4.5959).

This is advisory only — no action was or should be taken automatically. See `results/eod_liquidation_suggestions_2026-10-20.csv`.

## Cohort tracking (D8)

- 312 candidates logged today into the 5–30%/<$10 tracking band (wider than the live 10-25% buy bracket).
- 10 of those were new symbols to the all-time cohort log (sector/industry captured via fundamentals: LCU, CRWX, AMRO, FIXX, DCM, SMRX, ARMA, CITR, VRC, SRTM — 4 resolved to a real sector/industry, the rest are leveraged-ETF-style instruments with no fundamentals profile).
- 149 recovery-tracking rows appended (capped at the 150 most-recently-added distinct symbols across the whole log's history; IAE skipped — no quote available, likely delisted/inactive, consistent with its appearance in today's position_mgmt as `skipped_inactive_instrument`).
- Pure data logging — no guardrail or bracket changes made from this.

## Guardrail paper-trade ledger (D10)

51 new day-0 paper positions opened today from the two skipped_candidates files (dedup by symbol; LCU excluded as an actual buy; IRE/IREX/IREG/XCH/CMCM excluded as already-open paper positions) — 20 closed intraday (all wins, roughly +4% to +7%), 31 remain open. Of 33 pre-existing open positions carried forward (evaluated against today's daily bar using the same decay/trailing schedule plus the 25%-drawdown and 4-session time-stop rules), 13 closed today (all via decay/trailing target hits), 20 remain open. Ledger now holds 1,286 rows (51 open, 1,235 closed, of which 1 historical row — YYGH, 2026-08-28 — has no ROI due to a no-data/likely-delisted exception carried from a prior day).

**By guardrail (closed paper trades only, normalized to the a1–a8 prefix; excludes the 1 no-data exception):**

| Guardrail | n | Win rate | Mean ROI | Median ROI | Summed ROI |
|---|---|---|---|---|---|
| a1 (spread) | 264 | 81.8% | +20.0% | +5.05% | +5287% |
| a2 (ATR) | 120 | 61.7% | +122.2% | +4.24% | +14662% |
| a3 (prior spike) | 262 | 72.9% | +40.6% | +5.00% | +10643% |
| a4 (earnings recency) | 33 | 54.5% | -0.04% | +3.00% | -1% |
| a5 (compliance) | 196 | 72.4% | +28.5% | +4.44% | +5593% |
| **a6 (reverse-split proxy)** | 215 | 65.6% | **-0.31%** | +5.00% | -66% |
| a7 (thin liquidity) | 66 | 69.7% | -0.15% | +5.00% | -10% |
| a8 (leveraged/inverse ETF) | 77 | 62.3% | +2.25% | +2.12% | +173% |

(One additional closed row predates the a1–a8 guardrail prefix convention and is omitted from this table.)

Overall across all closed paper trades with a valid ROI: n=1,234, win rate 71.1%, mean ROI +29.4% (pulled up by a handful of large outliers; the median ~5% is the more typical outcome and matches the capped +5% limit-sell target).

**Baseline for comparison** (account's actual realized trades, 2026-08-25 through 2026-10-20 — the same window the ledger covers): **196 closed round trips, win rate 57.1%, total realized P&L -$22.65 (-2.3%)**.

Every guardrail's blocked population — including a6, the guardrail under active review — again shows a *higher* win rate than the account's own realized 57.1%, and most show solidly positive summed ROI where the live book was net negative over the same stretch. a6 and a7 are the recurring caveats: both still show a modestly negative mean/summed ROI (-0.31%/-66% and -0.15%/-10% respectively) despite win rates well above the baseline, because their losers run larger than their winners on average — the same pattern noted on prior days, not a new development.

**Mandatory caveats (apply to every row above):**
1. Guardrails short-circuit in a1→a8 order, so a trade's `skip_reason` is only its *first* failure. A paper winner blocked by a6 might also have failed a7 or a8 had a6 not existed — `fundamentals_guardrails_failed` (recorded per-trade) captures a5/a6/a8 co-failures, but a7 is never recomputed (it needs an extra historicals call per candidate), so no guardrail's number here is a clean "we would have won this."
2. Paper entries fill at the observed scan price with no spread or slippage; real buys are market orders. Every paper result is optimistic relative to a real fill, most of all for the wide-spread, thin, low-priced names these guardrails specifically target.
3. Target exits assume a limit fills whenever a bar's high touches it — matches how the real resting GTC order and D3 both behave, but it's still an assumption, not a certainty. For carried-forward positions evaluated on daily (not intraday) bars, the exit tier is approximated from the day's high against the four time-of-day decay levels (5%/4%/3%/2%), which can occasionally pick a more favorable tier than a real intraday fill would have achieved.

No guardrail changes are implemented from this analysis — these numbers are for the account owner's review.

## Methodology note (D3 decay/trailing reconstruction)

This session reconstructed the decay/trailing/liquidation engine from first principles by cross-referencing today's and yesterday's `daily_analytics` output and the account's actual GTC limit-sell order history (cancel/replace timestamps), since the live Phase C engine's source wasn't directly available to this session. The reconstructed model: initial target = entry (or running peak, if higher) × (1 + decay_pct), with decay_pct following a fixed time-of-day schedule (5% before 17:30 UTC, 4% from 17:30–18:30, 3% from 18:30–19:30, 2% from 19:30 to the 20:00 close), evaluated against 5-minute bars. The 60-minute scenario checks whether, at exactly 60 minutes after first-seen (when that falls before close), the price is underwater versus entry; if so it is flagged as a forced-liquidation counterfactual independent of whether the decay target is hit later the same day. This reproduced bracket win rates in the same range as prior days' reported figures (various 40-60%), which was used as a sanity check. One field, `pct_of_target_reached_if_not_closed`, is approximated as the percentage of the trading session elapsed between first-seen and close, since its exact original formula could not be recovered with confidence from the available data; it is a secondary diagnostic field and does not affect any win-rate figure above.
