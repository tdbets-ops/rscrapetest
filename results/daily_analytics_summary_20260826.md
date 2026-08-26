# Daily Analytics — 2026-08-26

No guardrail saturated 100% on a zero-buy firing today (both the 9:30 and 10:30 buy windows placed at least one buy: ADBT at 9:30; NTRP, BAK, AARD at 10:30). D6b detail below.

## Headline

- **Candidates seen today (either list, any firing):** 435 unique symbols (429 under-$120, 6 $120–500 data-collection-only).
- **In D3 scope (in_bracket + shallow_outside + deep_outside):** 295 symbols. **Simulated:** 290 (5 unsimulable — JBDI, MLEC, NUR, PRHI all-interpolated bars all day; ZORA not_found/delisted, no historicals at all).
- **Overall decay/trailing win rate (simulated=true only): 45.5%** (132/290 closed by end of day).
- **In-experiment-bracket (price<$10, 10–25% decline) win rate: 63.9%** (23/36).

## D3 scope note

Per spec, simulation runs only for `decline_bucket` in (in_bracket, shallow_outside, deep_outside) — i.e. price < $10 and a decline of 5–30% at first sight. The 140 `other_price_or_range` candidates (mostly the 6 $120–500 names plus under-$120 names with price ≥ $10) are marked `simulated=false` with null scenario columns and were never sent to `get_equity_historicals`. `price_bucket` among simulated rows is therefore only meaningfully populated for <1 / 1-3 / 3-10 — no simulated row has a 10-120 or 120-500 price_bucket, exactly as expected from the scope restriction.

## Win rate by time_bucket

| time_bucket | n | wins | win rate |
|---|---|---|---|
| morning (9:30/10:30/11:30) | 236 | 114 | 48.3% |
| afternoon (3:30) | 54 | 18 | 33.3% |

## Win rate by price_bucket (simulated rows only)

| price_bucket | n | wins | win rate |
|---|---|---|---|
| <1 | 79 | 41 | 51.9% |
| 1-3 | 96 | 44 | 45.8% |
| 3-10 | 115 | 47 | 40.9% |

## Win rate by decline_bucket (bracket calibration)

| decline_bucket | n | wins | win rate |
|---|---|---|---|
| in_bracket (10–25%, live buy range) | 36 | 23 | 63.9% |
| shallow_outside (5–<10%) | 253 | 108 | 42.7% |
| deep_outside (>25–30%) | 1 | 1 | 100.0% (n=1, not meaningful) |

The 10–25% live bracket clearly outperforms the shallower 5–10% band it's carved from (63.9% vs 42.7%, n=36 vs n=253) — consistent with the bracket being reasonably well-calibrated. deep_outside has only one candidate today, too small to read anything into.

## in_experiment_bracket=true, by time_bucket

| time_bucket | n | wins | win rate |
|---|---|---|---|
| morning | 33 | 20 | 60.6% |
| afternoon | 3 | 3 | 100.0% (n=3) |

## In-bracket win rate by FIRING (the 9:30/10:30 buy-window question)

| firing | n | wins | win rate | live buy window? |
|---|---|---|---|---|
| 9:30 | 21 | 15 | **71.4%** | yes |
| 10:30 | 4 | 2 | 50.0% | yes (added 2026-08-25) |
| 11:30 | 8 | 3 | 37.5% | no |
| 3:30 | 3 | 3 | 100.0% (n=3) | no |

9:30 remains the strongest bracket-candidate pool by a wide margin. 10:30's n=4 today is too small to draw a firm conclusion, but its 50% sits meaningfully below 9:30's 71.4% and roughly in line with cumulative evidence so far — the case for 10:30 as a buy window is mixed, not yet a clear win. 11:30's 37.5% (n=8) is the weakest of the morning cluster and does not support extending buying to that firing. Sample sizes are still small across the board; treat all of these as directional, not conclusive.

## Cross-reference vs today's actual trades/skipped files

- **Trades placed today:** ADBT (9:30), NTRP/BAK/AARD (10:30) — 4 buys. Real fills: ADBT +4.09% (closed, decay target), NTRP +5.54% (closed, decay target), AARD +2.00% (closed, decay target), BAK -0.25% (still open at close). All three closed trades were winners; simulation using each symbol's *first-seen* price (which can differ from the actual later fill price) shows ADBT and NTRP as closed/winners too, but flags BAK and AARD as not-yet-closed under the 9:30 first-seen cost basis — the divergence on AARD is because its actual buy came at the (cheaper, later) 10:30 price, not its 9:30 first-seen price, so the two are not directly comparable 1:1.
- **Skipped candidates today (both firings, 38 rows / 25 unique symbols):** 25 had simulation data; **64.0% (16/25) would have closed as winners** by end of day had they been bought at their skip-time price. This is a modest positive signal for guardrail cost, consistent with the paper-trade ledger below.

## Exit-rule firings (D6a)

No step-16a3 (time_stop_4_sessions / time_stop_overdue / drawdown_stop_25pct) firings today. All of today's real closes (ADBT, NTRP, AARD) were ordinary decay/trailing take-profit fills, not forced liquidations — confirmed via `latest_position_mgmt.csv` reason fields (`no_change_decay_target_unchanged` / `no_change_target_correct` throughout) and via order history (all closes were GTC limit sells at the resting decay target). Section omitted per spec.

## D6b — Guardrail saturation check

Both of today's buy firings placed at least one buy (9:30: 1 buy / 19 skips; 10:30: 3 buys / 19 skips), so the "zero-buy firing" trigger condition does not apply today.

| firing | skipped (bracket candidates that reached a guardrail) | top guardrail | max single-guardrail share |
|---|---|---|---|
| 9:30 | 19 | a5_compliance (8) | 42% |
| 10:30 | 19 | a3_prior_spike (5) | 26% |

No single guardrail rejected 100% of the candidates that reached it on either firing — no malfunction signal today. (For history: 2026-08-25's 10:30 firing had 0 buys; today's 10:30 did not repeat that, so there is no two-consecutive-zero-buy pattern to flag.)

## Advisory: EOD manual liquidation suggestions (D7)

Only one buy from today is still open at market close: **BAK** (3 sh @ $1.4687 avg, 10:30 buy). **Not flagged** — pct_change_since_buy is -0.34% (threshold is ≤ -5%), and current price ($1.465) is above the near-low bound (low $1.43 × 1.02 = $1.4586). trend_down is true but all three conditions are required jointly. See `results/eod_liquidation_suggestions_20260826.csv` (1 data row). **These suggestions are advisory only — nothing was or will be executed automatically from this file.**

## Cohort tracking (D8)

- Filtered today's under-$120 scans to price < $10 and 5–30% decline: 295 candidates, appended to `decliner_cohort_log.csv` under today's date (0 previously logged for today).
- 32 of those were genuinely new to the log (never appeared on any prior date) — sector/industry fetched and written for those 32; the other 263 copied sector/industry from their existing log rows.
- Recovery tracking: cohort log now holds 2,003 distinct symbols across its history. Capped to the 150 most-recently-added (spanning 2026-08-17 through today) for this cycle's re-quote, per spec. 148 quoted successfully; 2 skipped (CRV, ZORA — both not_found/no quote, ZORA also has no historicals data as noted above, likely delisted). Appended 148 rows to `decliner_recovery_tracking.csv`.

## Guardrail paper-trade ledger (D10)

**Caveats (apply to every number below):**
1. Guardrails short-circuit a1→a8, so `skip_reason` is only a candidate's *first* failure; results for guardrail aX measure aX's marginal cost given a1..a(X-1) already passed. Check `fundamentals_guardrails_failed` for the full set of a5/a6/a8 failures a candidate would have hit. a7 is not recomputed, so removing a guardrail is never a clean "we would have won this" claim.
2. Paper entries fill at the observed price with no spread/slippage, while real buys are market orders — every paper result is optimistic relative to a real fill, most optimistic for wide-spread/thin/low-priced names.
3. Target exits assume a resting limit fills whenever a bar's high touches it — matches how the real GTC limit behaves, but is still an assumption.

**Today's activity:** opened 22 new day-0 paper positions from today's 38 skipped-candidate rows (25 unique symbols, minus 3 — FBDT/SHMD/RCON — that already had an open paper position from 2026-08-25, and 0 overlap with symbols actually bought today). Carried forward all 15 pre-existing open rows from 2026-08-25 using today's daily bar: 6 closed (2 via decay/trailing target, 2 via 25% drawdown stop on FBDT and RCON — those are also today's skipped-candidate symbols, now paper-flat for 2026-08-25; 2 via decay/trailing target on CDTG/OLOX; HOOZ/WKSP also hit target), 9 remain open.

**By skip_reason, closed paper trades only (ledger-wide, all history):**

| skip_reason | n closed | win rate | mean roi% | median roi% | summed roi% | still open |
|---|---|---|---|---|---|---|
| a1_spread | 13 | 100.0% | +5.26 | +5.14 | +68.42 | 0 |
| a2_atr | 1 | 0.0% | -25.00 | -25.00 | -25.00 | 1 |
| a3_prior_spike | 11 | 90.9% | +3.56 | +5.08 | +39.16 | 1 |
| a4_earnings_recency | 2 | 0.0% | -5.72 | -5.72 | -11.44 | 1 |
| a5_compliance | 16 | 87.5% | +2.00 | +5.06 | +31.99 | 5 |
| **a6_reverse_split_proxy** | **6** | **66.7%** | **-1.74** | **+5.06** | **-10.46** | **3** |
| a7_stale_quote_gap | 0 | — | — | — | — | 1 |
| a7_thin_liquidity | 0 | — | — | — | — | 1 |
| a8_leveraged_etf | 2 | 100.0% | +3.56 | +3.56 | +7.12 | 2 |
| **Overall** | **51** | **84.3%** | **+1.96** | — | — | **15** |

a6_reverse_split_proxy (flagged for review) is the weakest-performing guardrail bucket with a large enough sample to say something: 66.7% win rate but a *negative* mean ROI (-1.74%), the only guardrail besides a2/a4 (n≤2, too small to read) with negative expectancy — driven by a couple of large adverse moves outweighing several small wins. Given caveat #1, these are candidates that already survived a1–a5, so this is a genuine reflection of what the a6 proxy alone is costing on that pre-filtered pool — worth continued monitoring, not yet strong enough (n=6 closed) to justify loosening a6.

**Baseline — actual account results, same window:** 4 real buys today, 3 closed (ADBT +4.09%, NTRP +5.54%, AARD +2.00% — all winners, decay-target fills) and 1 still open (BAK -0.25% unrealized). Realized win rate 100% (3/3), mean realized ROI +3.88%. The paper ledger's 84.3% closed win rate / +1.96% mean is in the same direction (net positive, high win rate) but lower magnitude than the real book's tiny n=3 sample — both consistent with a favorable environment today, but the real sample is far too small (n=3) to compare rigorously against the paper ledger's n=51.
