# Daily Analytics — 2026-09-04 (Phase D, EOD)

Run at 20:30 UTC (4:30pm ET), after all four intraday firings (9:30/10:30/11:30/3:30 ET) had completed and committed.

## Headline

- **Candidates seen today:** 421 unique symbols (across 4 under-$120 scans + 4 $120–$500 scans)
- **In-scope for simulation (decline_bucket ∈ {in_bracket, shallow_outside, deep_outside}):** 253
- **Actually simulated:** 250 (3 symbols — WIF, TRUMP, PENGU — resolved as `not_found` on `get_equity_historicals`; likely crypto-adjacent tickers misclassified as equities by the scanner, not delisted equities. Excluded from win-rate stats.)
- **Overall win rate (simulated=true, win = decay/trailing target reached):** **44.0%** (110/250)

## Scope note (per D3 spec)

Simulation was restricted to `decline_bucket` in {in_bracket, shallow_outside, deep_outside} — i.e. price < $10 and decline between 5% and 30%. The over-$120 list is always `other_price_or_range` by construction (price ≥ $120), so its 13 rows never entered simulation; this is expected, not a gap. The realized scope (253) came in slightly above the "well under 200" estimate in the spec, driven mostly by the `shallow_outside` band (222 rows, decline 5–10%) — a wide net is deliberate wells within spec.

## Win rate by time bucket

| Bucket | Wins | N | Win rate |
|---|---|---|---|
| Morning (9:30/10:30/11:30) | 102 | 197 | 51.8% |
| Afternoon (3:30) | 8 | 53 | 15.1% |

**Caveat:** the afternoon number is a data-availability artifact, not a real signal. The 3:30pm ET (19:36 UTC) scan leaves only ~24 minutes of regular-session bars before the 20:00 UTC close cutoff used for this run, so most afternoon symbols simply didn't have time to reach a target (94% never triggered the D3a liquidation-rule check either — see below). Do not read 15.1% as "afternoon decliners perform worse."

## Win rate by decline bucket

| Bucket | Wins | N | Win rate |
|---|---|---|---|
| in_bracket (price<$10, decline 10–25%) | 19 | 30 | 63.3% |
| shallow_outside (decline 5–<10%) | 90 | 219 | 41.1% |
| deep_outside (decline >25–30%) | 1 | 1 | 100.0% (n=1, not meaningful) |

## Win rate by price bucket / source list

| Price bucket | Wins | N | Win rate |
|---|---|---|---|
| <$1 | 36 | 77 | 46.8% |
| $1–3 | 31 | 78 | 39.7% |
| $3–10 | 43 | 95 | 45.3% |
| $10–120 / $120–500 | — | 0 | n/a (out of scope by design) |

Source list: under120 = 44.0% (110/250, all simulated rows); 120to500 had 0 in-scope rows.

## In-experiment-bracket (price<$10, decline 10–25%) breakdown — the key evidence for buy-window decisions

By time bucket:

| Bucket | Wins | N | Win rate |
|---|---|---|---|
| Morning | 18 | 28 | 64.3% |
| Afternoon | 1 | 2 | 50.0% |

By exact firing:

| Firing | Wins | N | Win rate |
|---|---|---|---|
| 9:30 | 13 | 21 | 61.9% |
| 10:30 | 4 | 6 | 66.7% |
| 11:30 | 1 | 1 | 100.0% (n=1) |
| 3:30 | 1 | 2 | 50.0% (n=2) |

**Read with caution:** 11:30 and 3:30 sample sizes (n=1, n=2) are far too small to support a decision either way on adding an 11:30 buy window. 10:30's 66.7% (n=6) is directionally encouraging but also thin. 9:30 (n=21) is the only firing with a sample size approaching usefulness, and its 61.9% is in line with the overall in-bracket rate (63.3%). **Recommendation: keep collecting data before changing buy-window policy based on this run alone.**

## Cross-reference with today's real trades

Three real buys fired today, all now closed:

| Symbol | Firing | Fill | Exit | Exit reason | ROI |
|---|---|---|---|---|---|
| CYCN | 9:30 | $4.5399 | $4.2966 | 60-min never-touched-breakeven auto-liquidate | **-5.36%** |
| SGLD | 10:30 (first seen 9:30) | $5.1599 | $5.37 | limit sell target hit | **+4.07%** |
| SNSC | 10:30 (first seen 9:30) | $1.1258 | $1.17 | limit sell target hit | **+3.93%** |

Real win rate: 2/3 (66.7%), net P&L ≈ +$0.14 across the 3 positions. Sample size is too small (n=3) to compare meaningfully against the simulated 44% baseline, but directionally consistent — SGLD/SNSC both landed in the simulated `shallow_outside` bucket and both simulated **closed=True** with positive decay-target ROI (2.08% and 5.56% respectively) confirming the model called them correctly; CYCN was `in_bracket` and simulated **closed=False** (98.9% of target reached, i.e. very close), matching its real-world failure to clear the auto-liquidate window.

## Exit-rule firings today

Two real step-16a3-style liquidations fired today (position-management logs use different reason strings than the literal "time_stop"/"drawdown_stop" tokens, but both are the same underlying rule family):

| Symbol | Condition | Sessions held | Drawdown at exit | Realized P&L | 4pm close |
|---|---|---|---|---|---|
| CYCN | 60-min-never-touched-breakeven auto-liquidate (`auto_liquidate_never_touched_breakeven_60min`) | 0 | ≈ -5.3% (low $4.30 vs fill $4.5399) | -$0.2433 (1 sh, -5.36%) | $4.21 |
| RYET | Session time-stop (`sessions_held=4`), exited via market sell at 19:31:48 UTC | 4 | ≈ -2.6 to -2.9% (position-mgmt log) | -$0.1105 (5 sh, -2.60%) | $0.8484 |

## Guardrail saturation check (D6b)

**Not triggered.** Both buy-eligible firings today (9:30 and 10:30) recorded at least one real buy (CYCN at 9:30; SGLD and SNSC at 10:30), so the "zero-buy firing" analysis this section calls for does not apply today. For reference, the 9:30 firing skipped 21 bracket candidates and the 10:30 firing skipped 27, spread across a1/a2/a3/a4/a5/a6/a7/a8 guardrails with no single guardrail claiming 100% of either firing's skip volume.

## EOD manual liquidation suggestions (D7) — advisory only, not executed

**0 flagged.** None of today's three real buys (CYCN, SGLD, SNSC) remain open — CYCN was auto-liquidated intraday, and SGLD/SNSC both hit their limit-sell targets before close. `results/eod_liquidation_suggestions_20260904.csv` is written header-only. **This entire section is advisory/simulation-only; no orders were placed, cancelled, or reviewed by this run.**

## Decliner-bracket cohort tracking (D8)

- 153 symbols from today's 3:30pm under-$120 scan matched the tracking band (price<$10, |decline| 5–30%) and were appended to `decliner_cohort_log.csv` with `date_seen=2026-09-04`.
- Of those, 7 were new to the log (EGAN, GIXI, NEWP, RSKD, GROW, ARB, SRG) and got fresh sector/industry via `get_equity_fundamentals`; the remaining 146 copied sector/industry from their existing earliest log entry.
- Full cohort log now holds **2,085 distinct symbols** (earliest date_seen/price per symbol). This cycle's recovery-tracking pass was **capped to the 150 most-recently-added distinct symbols** (per spec) rather than covering the full history.
- 145 of the 150 capped symbols returned live quotes and got a new row in `decliner_recovery_tracking.csv` (`pct_change_from_original` written as a decimal fraction, not a percentage — verified). 5 symbols (HOOZ, ZORA, CRV, OP, ENA) returned no quote data and were skipped/noted — likely delisted or non-equity tickers, consistent with the "skip on error" instruction.

## Guardrail paper-trade ledger (D10)

- **Opened today:** 34 new paper positions from the 48 rows across today's two skipped-candidates files, after dedup (11 within-day duplicate symbols across the 9:30/10:30 skip lists collapsed to their first occurrence; ASTN excluded because it already had an open paper position from a prior day; no symbol bought for real today needed exclusion).
- **Day-0 result:** 16 of the 34 new positions closed same-day (all via `paper_target`, except BTAI which hit `paper_drawdown_stop_25pct` at -25.0%); 18 remain open.
- **Carry-forward (19 prior-day open positions):** peak_pct/max_drawdown_pct updated on all 19; sessions_held incremented; 4 more closed today (SSM via drawdown stop at -25.0%; CIEG, BMNZ, LOFD via `paper_target`, +2.24%/+2.06%/+2.03%). YYGH returned no data from either historicals or quotes (`inactive_instruments`) — carried forward unchanged and flagged as not evaluable (likely delisted). None of the 19 reached the 4-session time-stop threshold yet (max is now 3 sessions held).
- **Total ledger:** 253 rows (220 closed, 33 open) after today's activity.

### Closed paper trades by skip_reason (all-time, n / win rate / mean roi% / median roi% / summed roi% / still open)

| skip_reason | n closed | win rate | mean ROI% | median ROI% | sum ROI% | still open |
|---|---|---|---|---|---|---|
| a1_spread | 29 | 93.1% | 3.21 | 5.06 | 92.9 | 5 |
| a1_spread_guardrail | 5 | 80.0% | 1.50 | 7.06 | 7.5 | 0 |
| a1_spread_pct | 6 | 50.0% | -1.00 | 0.27 | -6.0 | 2 |
| a2_atr | 13 | 61.5% | 323.62 † | 5.00 | 4207.1 | 1 |
| a2_atr_guardrail | 8 | 50.0% | -10.01 | -10.42 | -80.1 | 1 |
| a3_prior_spike | 54 | 87.0% | 2.59 | 5.07 | 139.6 | 7 |
| a4_earnings_recency | 7 | 57.1% | -0.91 | 5.00 | -6.3 | 2 |
| a5_compliance | 37 | 75.7% | 87.20 † | 5.00 | 3226.3 | 2 |
| a5_compliance_guardrail | 4 | 100.0% | 4.88 | 5.11 | 19.5 | 3 |
| **a6_reverse_split_proxy** | **34** | **64.7%** | **3.24** | **5.00** | **110.1** | **4** |
| a7_stale_quote / a7_stale_quote_gap | 2 | 0.0% (n<10) | -10.47 | — | -20.9 | 0 |
| a7_thin_liquidity / a7_thin_liquidity_stale_quote | 6 | 83.3% (n<10) | 3.16 | — | 19.1 | 2 |
| a8_leveraged_etf | 7 | 100.0% (n<10) | 4.66 | 2.13 | 32.6 | 1 |
| a8_leveraged_inverse_etf | 8 | 87.5% | 1.26 | 3.70 | 10.1 | 3 |

† `a2_atr` and `a5_compliance` mean ROI is wildly skewed by a handful of pre-existing rows (OMH, VBIO, CALC, CHGA from 2026-08-27) with entry prices that look like stale/pre-split artifacts (e.g. entry $0.08 → exit $3.68). **Use the median (5.0%) for these two rows, not the mean.** This is a pre-existing data-quality issue in the ledger, not introduced by this run.

**a6 (reverse-split proxy) explicitly requested:** n=34 closed, 64.7% win rate, mean ROI +3.24%, median +5.00%, summed ROI +110.1%, 4 still open. No outlier skew in this group.

**Caveats (apply to every number in this section):**
1. Guardrails short-circuit in a1→a8 order; `skip_reason` is only the *first* failure. Results for guardrail aX measure aX's marginal cost given a1..a(X-1) already passed — cross-check `fundamentals_guardrails_failed` for the full a5/a6/a8 picture. a7 is never recomputed here.
2. Paper entries fill at the observed scan price with no spread/slippage modeled — optimistic vs. real market-order fills, especially for thin/wide-spread names (a1/a7 rows).
3. Target exits assume a limit fills whenever a bar's high touches it — same optimistic assumption used in the real D3 simulation.

### Baseline comparison — real vs. paper (D10g)

| | Real trades (today) | Paper ledger (all-time, closed) |
|---|---|---|
| N | 3 | 220 |
| Win rate | 66.7% | 77.3% (170/220) |
| Mean ROI | ~+0.88% (equal-weighted) | +35.2% mean / **+5.0% median** (mean is outlier-skewed, see † above — median is the honest read) |
| Net $ P&L | +$0.14 | n/a (paper, no position sizing) |

Real-trade sample (n=3) is far too small to validate the paper ledger's implied ~5% target-hit rate story, but the one loss (CYCN) and two wins (SGLD, SNSC) are directionally consistent with what the paper/D3 simulations would have predicted for those same names.

---
*All D7/D10 content in this report is advisory/simulation only. No brokerage orders were placed, cancelled, or reviewed by this run.*
