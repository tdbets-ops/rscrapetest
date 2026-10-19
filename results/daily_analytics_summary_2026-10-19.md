# Daily Analytics — 2026-10-19 (4:30pm ET firing)

## Headline

- **527** unique candidates seen today across all 4 firings (525 under-$120, 2 $120–$500).
- **404** in scope for simulation (in_bracket / shallow_outside / deep_outside; 123 `other_price_or_range` skipped per the 2026-07-29 scope fix).
- Overall simulated win rate (decay/trailing target reached by close): **43.8%** (177/404).
- Bracket (the live buy filter) win rate: **57.1%** (36/63) — comfortably ahead of the unscoped population.
- 2 real buy windows ran today (9:30, 10:30); both placed trades. No zero-buy firing.

## Win rate breakdown (simulated rows only)

| Cut | n | Win rate |
|---|---|---|
| Overall | 404 | 43.8% |
| Morning (9:30/10:30/11:30) | 265 | 52.8% |
| Afternoon (3:30) | 139 | 26.6% |
| decline_bucket: in_bracket | 63 | 57.1% |
| decline_bucket: shallow_outside | 338 | 41.1% |
| decline_bucket: deep_outside | 3 | 66.7% (n<10, ignore) |
| price <1 | 162 | 52.5% |
| price 1-3 | 151 | 37.8% |
| price 3-10 | 91 | 38.5% |

### In-bracket, by firing (direct evidence for the two-window buy expansion)

| Firing | n | Win rate |
|---|---|---|
| 9:30 | 31 | 61.3% |
| 10:30 | 11 | 54.5% |
| 11:30 | 7 | 57.1% (n<10, low confidence) |
| 3:30 | 14 | 50.0% |

9:30 and 10:30 (the two live buy windows) both beat 3:30 (the one firing that never buys), consistent with the morning-outperforms-afternoon rationale behind the bracket's time restriction. 11:30's 57.1% looks competitive but rests on only 7 observations — not enough to act on. Nothing here argues for extending buying to 11:30 or 3:30 yet.

*Note: "by firing" here groups a symbol under whichever firing FIRST saw it today (per D2), which differs from how many candidates each firing's live Phase B bracket filter evaluated (that re-scans fresh each firing and can include symbols first seen earlier in the day still sitting in the bracket).*

## Guardrail saturation check (D6b)

Both buy firings today placed trades (9:30: 2 buys from 31 bracket candidates; 10:30: 5 buys from 44 bracket-touches), so there is **no zero-buy firing to flag**. Skip-reason counts for context:

- 9:30 skipped (29 total): a1_wide_spread 11, a6_reverse_split_proxy 6, a5_compliance_guardrail 4, a7_stale_quote 3, a3_prior_spike 3, a2_atr_guardrail 2.
- 10:30 skipped (39 total): a1_spread 13, a6_reverse_split_proxy 11, a5_compliance 5, a3_prior_spike 5, a2_atr 5.

No single guardrail rejected 100% of candidates that reached it at either firing — a1 (spread) is the largest blocker at both, consistent with recent days, not a new pattern.

## Exit-rule firings (D6a)

None today — no position was liquidated by the step 16a3 time-stop/drawdown-stop rule on 2026-10-19.

## EOD manual liquidation suggestions (D7) — advisory only, no orders touched

3 symbols bought today (NYXH, NWCL, ATOS) are still open. None flagged `SUGGEST_LIQUIDATE`:
- NYXH: flat (+0.5%), no decline trigger.
- NWCL: down 8.1% but not near its intraday low (not confirmed breaking down).
- ATOS: near its low but only -0.4%, below the -5% threshold.

These are advisory only — no action was or should be taken automatically.

## Cohort tracking (D8)

- 406 candidates logged today into the 5–30%/<$10 tracking band (wider than the live 10-25% buy bracket).
- 149 recovery-tracking rows appended (capped at 150 most-recently-added per the spec; more distinct symbols exist in the full log).
- Pure data logging — no guardrail or bracket changes made from this.

## Guardrail paper-trade ledger (D10)

43 new day-0 paper positions opened today from the two skipped_candidates files (26 at 9:30, 17 at 10:30); 29 closed intraday (all wins, +2.0% to +20.5%), 14 remain open. Of 36 pre-existing open positions carried forward, 15 closed today (drawdown stops, decay/trailing target hits, 3 time-stop exits); 19 remain open. Ledger now holds 1,235 rows (33 open, 1,202 closed).

**By guardrail (closed paper trades only, normalized to the a1–a8 prefix):**

| Guardrail | n | Win rate | Mean ROI | Median ROI | Summed ROI |
|---|---|---|---|---|---|
| a1 (spread) | 254 | 81.5% | +20.6% | +5.05% | +5242% |
| a2 (ATR) | 115 | 62.6% | +127.9% | +4.32% | +14713% |
| a3 (prior spike) | 259 | 73.4% | +41.2% | +5.00% | +10678% |
| a4 (earnings recency) | 33 | 54.5% | -0.04% | +3.00% | -1% |
| a5 (compliance) | 191 | 71.7% | +29.1% | +4.26% | +5555% |
| **a6 (reverse-split proxy)** | 212 | 65.1% | **-0.42%** | +4.97% | -89% |
| a7 (thin liquidity) | 61 | 68.9% | -0.40% | +5.00% | -24% |
| a8 (leveraged/inverse ETF) | 75 | 61.3% | +2.18% | +2.06% | +163% |

Overall across all closed paper trades: n=1,201, win rate 70.9%, mean ROI +30.2% (mean is pulled up by a handful of large outliers; the median ~4-5% is the more typical outcome and matches the capped +5% limit-sell target).

**Baseline for comparison** (account's actual realized trades, 2026-08-25 through 2026-10-19 — the same window the ledger covers): **193 closed round trips, win rate 57.5%, total realized P&L -$23.29 (-2.4%)**.

Every guardrail's blocked population — including a6, the guardrail under active review — shows a *higher* win rate than the account's own realized 57.5%, and most show solidly positive summed ROI where the live book was net negative over the same stretch. a6 specifically is the one caveat: its mean/summed ROI is slightly negative (-0.42% mean, -89% summed) despite a 65.1% win rate, because its losers are larger than its winners on average — a different shape than a1/a3/a5, whose both win rate and average size are favorable.

**Mandatory caveats (apply to every row above):**
1. Guardrails short-circuit in a1→a8 order, so a trade's `skip_reason` is only its *first* failure. A paper winner blocked by a6 might also have failed a7 or a8 had a6 not existed — `fundamentals_guardrails_failed` (recorded per-trade) captures a5/a6/a8 co-failures, but a7 is never recomputed (it needs an extra historicals call per candidate), so no guardrail's number here is a clean "we would have won this."
2. Paper entries fill at the observed scan price with no spread or slippage; real buys are market orders. Every paper result is optimistic relative to a real fill, most of all for the wide-spread, thin, low-priced names these guardrails specifically target.
3. Target exits assume a limit fills whenever a bar's high touches it — matches how the real resting GTC order and D3 both behave, but it's still an assumption, not a certainty.

No guardrail changes are implemented from this analysis — these numbers are for the account owner's review.
