# Daily Analytics — 2026-09-10

**Headline: 48.0% win rate (71/148 simulated)** over the sampled in-scope population. Morning 55.2% vs afternoon 30.2%. In-bracket 59.2% vs shallow-outside 42.4%.

## Scope note (read before the numbers below)

573 unique symbols were seen today across all four firings (9:30/10:30/11:30/3:30). Per the routine's standing D3 scope rule, only `in_bracket` + `shallow_outside` + `deep_outside` symbols are simulation-eligible (361 of 573; the rest are `other_price_or_range` and get `simulated=false`).

**Additional cap applied today, beyond the standing rule:** 361 in-scope symbols still exceeds the "well under 200" compute-budget guidance. To keep this firing inside a single session (per the 2026-07-28 failure-mode note), I simulated:
- **All 49 `in_bracket` symbols** (the primary bracket-calibration population — not sampled, fully covered).
- **A random sample of 100 of the 312 `shallow_outside` symbols** (seed=42), leaving 212 `shallow_outside` symbols unsimulated this cycle.
- **0 `deep_outside` symbols exist today** (none scanned into that bucket).

Total simulated: **148 of 361 in-scope (41%)**, 148 of 573 total candidates seen (26%). All shallow-outside win-rate and price-bucket numbers below should be read as a 100-symbol sample of a 312-symbol population, not a census. The in-bracket numbers (n=49) are a full census and are the load-bearing numbers for the bracket-calibration question.

## Win rate breakdown (simulated population, n=148)

| Cut | n | Wins | Win rate |
|---|---|---|---|
| Overall | 148 | 71 | 48.0% |
| Morning (9:30/10:30/11:30) | 105 | 58 | 55.2% |
| Afternoon (3:30) | 43 | 13 | 30.2% |
| decline_bucket = in_bracket | 49 | 29 | 59.2% |
| decline_bucket = shallow_outside (sample) | 99 | 42 | 42.4% |
| price <1 | 39 | 22 | 56.4% |
| price 1-3 | 46 | 21 | 45.7% |
| price 3-10 | 63 | 28 | 44.4% |

(10-120 / 120-500 buckets are out of D3 scope by definition — `other_price_or_range` symbols are never simulated, so those price buckets have no simulated rows. Noted per the routine's own instruction rather than silently dropped.)

## in_experiment_bracket=true, by time_bucket and by firing

This is the number that directly bears on "should 11:30 start buying" and "was the 10:30 window worth adding":

| Cut | n | Wins | Win rate |
|---|---|---|---|
| Morning | 36 | 25 | 69.4% |
| Afternoon | 13 | 4 | 30.8% |

| Firing | n | Wins | Win rate |
|---|---|---|---|
| 9:30 (live buy window) | 29 | 23 | **79.3%** |
| 10:30 (live buy window) | 6 | 1 | 16.7% |
| 11:30 (not a buy window) | 1 | 1 | 100.0% |
| 3:30 (not a buy window) | 13 | 4 | 30.8% |

**Reading this with real caution on sample size:** 9:30 in-bracket remains strong (79.3%, n=29, consistent with the 77.1% seen 2026-08-24). 10:30's in-bracket sample is tiny (n=6) and weak (16.7%) today — one bad day on 6 symbols is not a verdict on the second buy window, but it's the kind of number that should keep accumulating scrutiny rather than being read as confirmation. 11:30 (n=1) and 3:30 (n=13, 30.8%) both continue to look weaker than 9:30, consistent with not extending buying to those firings without more data.

## Candidates seen vs simulated vs traded

- Total candidates seen today: 573 (both scan lists, all 4 firings)
- Simulated: 148 (see scope note)
- Real trades placed today: 8 (5 at 9:30, 3 at 10:30) — 7 of the 8 fall inside today's simulated sample
- Skipped candidates (bracket-eligible, failed a guardrail): 53 rows across both buy firings (25 at 9:30, 28 at 10:30) — 32 of those symbols also appear in today's simulated sample

## Exit-rule firings (step 16a3)

Two positions closed today via the time-stop/drawdown-stop rule, both at the 3:30pm firing:

| Symbol | Condition | Sessions held | Drawdown at exit | Realized P&L | Notes |
|---|---|---|---|---|---|
| ANVS | time_stop_4_sessions (on-schedule) | 4 | -17.84% | **-$0.776** (-17.84% on $4.35 cost) | Bought 2026-09-03 @1.4499, sold 2026-09-10 @1.1913. Correctly matches the scheduled 4-session exit, but the 3:30 firing's commit (845c32c) did not log this in position_mgmt or mention it in the commit message — logged here from get_equity_orders instead. Flagging as a documentation gap, not a trading error. |
| SLBT | drawdown_stop_25pct — **flagged ERRONEOUS by the firing itself** | 0 | -14.08% (miscalculated live as -33.5%) | **-$0.68** (-14.3% on $4.76 cost) | The 3:30pm firing's own commit message documents an arithmetic bug: it computed the raw price difference (2.045-2.38) and used that as if it were already the ratio, instead of dividing by 2.38. The true drawdown (-14.08%) never met the -25% threshold — this position should not have been liquidated. Bought same-day at 9:43 ET, sold same-day at 3:38pm ET for a realized loss that would not have occurred under a correct calculation. No re-buy per the routine's rules; flagging for the account owner. |

**Recommend the account owner review the SLBT arithmetic bug** in step 16a3's drawdown calculation before the next firing that could hit this code path again — the routine's own commit self-reported it, so this note is confirmatory, not new information.

## Guardrail saturation check (D6b)

Neither buy firing today (9:30: 5 trades, 10:30: 3 trades) placed zero buys, so the mandatory zero-buy diagnostic does not apply today. No single guardrail saturated (rejected 100% of candidates that reached it) on either firing — skip reasons were spread across a1 (spread), a2 (ATR), a3 (prior-spike), a4 (earnings), a5 (compliance), a6 (reverse-split proxy), a7 (thin-liquidity/stale-quote), and a8 (leveraged-ETF) per the two skipped_candidates files.

## Guardrail paper-trade ledger

See separate section below. New today: 34 day-0 positions opened from today's 2 skipped_candidates files (26 closed intraday, 8 still open). Carried forward: 23 pre-existing open positions re-evaluated on today's daily bar — 10 closed today (3 via time_stop_4_sessions: XTLB, SCNI, CRCD; 5 via decay/trailing target: BRNX, BEZ, CORD, NBIZ, KUST; 2 via drawdown_stop_25pct: UZX, LEXX), 12 remain open with updated peak/drawdown tracking, and YYGH is confirmed inactive/delisted (`inactive_instruments` error on quote lookup) — left untouched this cycle and flagged for manual cleanup since it can no longer be priced.

### Guardrail paper-trade ledger — closed trades by skip_reason (full ledger to date, n=322 closed)

This table covers the *entire accumulated ledger* (since 2026-08-25), not just today — today contributed 34 new day-0 entries plus 13 carry-forward exits out of the 322 closed rows below.

| skip_reason | n closed | win rate | median roi% | mean roi% (outliers excluded) | n outliers >50% | still open |
|---|---|---|---|---|---|---|
| a1 (spread) | 59 | 85% | 5.0 | 2.35 | 1 | 6 |
| a2 (ATR) | 30 | 57% | 3.8 | -6.62 | 1 | 1 |
| a3 (prior-spike) | 75 | 84% | 5.0 | 1.62 | 1 | 5 |
| a4 (earnings-recency) | 13 | 46% | -0.12 | -1.29 | 0 | 1 |
| a5 (compliance) | 52 | 77% | 5.0 | -0.97 | 4 | 3 |
| a6 (reverse-split proxy) | 55 | 67% | 5.0 | -2.36 | 2 | 0 |
| a7 (thin-liquidity/stale-quote) | 14 | 64% | 4.0 | -1.21 | 0 | 3 |
| a8 (leveraged-ETF) | 24 | 92% | 5.0 | 3.04 | 0 | 2 |

**Data-quality note:** raw (non-excluded) means are wildly skewed by a handful of implausible outlier rows (roi_pct in the hundreds or thousands of percent — e.g. OMH +4297%, VBIO +2812%, TXXD +1001%), all logged on **prior** firings (2026-08-27 through 2026-09-04), not today. These look like the same class of data-entry/unit bug documented elsewhere in this routine (e.g. D8f's 100x pct_change bug) rather than real outcomes — a paper position cannot legitimately return 40x under this strategy's capped-target exit logic. I did not alter these historical closed rows (the ledger's own rule restricts in-place updates to open rows only), so they remain in `guardrail_paper_trades.csv` as-is; the table above reports median (robust to this) and a mean that excludes rows with |roi_pct|>50 as a sanity-checked alternative. **Recommend the account owner investigate and, if confirmed erroneous, decide how to handle these historical rows** — they are not something this firing is authorized to correct.

With that caveat: a8 (leveraged-ETF) has the strongest clean record (92% win, n=24) — consistent with a8 being validated against realized outcomes already. a2 (ATR) and a6 (reverse-split proxy) show the weakest clean means, though a6 remains the guardrail under active review per its 2026-08-25 note.

**Caveats (required every time this section appears):**
1. Guardrails short-circuit a1→a8 in order — skip_reason is only a candidate's *first* failure. Paper results for guardrail aX measure aX's marginal cost given a1..a(X-1) already passed. Cross-check `fundamentals_guardrails_failed` (records every one of a5/a6/a8 the candidate would also have failed). a7 is never recomputed retroactively (extra call cost), so no guardrail's removal is a clean "we would have won this" claim.
2. Paper entries fill at the observed price with no spread paid and no slippage — every paper result is optimistic relative to a real market-order fill, most of all for the thin, low-priced names these guardrails specifically target.
3. Target exits assume a limit fills whenever a bar's high touches it, matching how the real resting GTC limit behaves and how D3 already simulates — still an assumption, not a certainty.

**Baseline for comparison:** the account's actual realized trades today: 8 placed, mixed outcomes so far intraday (see position_mgmt logs) — full realized win rate not yet settled since most of today's real positions remain open into tomorrow. A guardrail is only worth loosening if its blocked population would have beaten what the account actually achieved, not merely if it's positive in isolation.

## EOD liquidation suggestions (D7, advisory only)

Two positions bought today remain open (BBOT, BENF). Neither meets the SUGGEST_LIQUIDATE bar (needs pct_change_since_buy ≤ -5% AND near_low AND trend_down, all three):
- **BBOT**: -4.0% (does not meet the -5% threshold)
- **BENF**: -15.8% (meets the decline threshold and is trending down, but is NOT near its intraday low — currently 7.7% above low_since_buy)

These are advisory data points only; no order was placed or modified based on this section.
