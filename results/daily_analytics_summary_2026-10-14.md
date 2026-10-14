# Daily Analytics — 2026-10-14

## Headline
- **1,151** unique candidates seen today across both scan lists (4 firings: 9:30/10:30/11:30/3:30 ET).
- **791** in scope for decay/trailing simulation (68.7%) — **29.2% win rate** overall.
- **in_bracket** (the live Phase B buy bracket: price<$10, 10–25% decline): **70.4% win rate, n=71**.
- Real buys today: **5** (2 at 9:30, 3 at 10:30) — no zero-buy diagnostic needed.

## Scope note
Per protocol, the per-symbol decay/trailing simulation (D3/D3a/D3b) runs only on `decline_bucket` in (in_bracket, shallow_outside, deep_outside) — i.e. price<$10 candidates with a 5–30% decline. The 360 `other_price_or_range` candidates (mostly the $120–500 high-price list, 70 symbols, plus under-$120 names outside the 5–30% band) are excluded from simulation by design and carry `simulated=false` in the CSV.

## Win rate by time bucket (simulated population, n=791)
| Bucket | Win rate | n |
|---|---|---|
| Morning (9:30/10:30/11:30) | 35.2% | 568 |
| Afternoon (3:30) | 13.9% | 223 |

## Win rate by price bucket
| Bucket | Win rate | n |
|---|---|---|
| <$1 | 46.2% | 221 |
| $1–3 | 27.8% | 284 |
| $3–10 | 17.5% | 286 |
| $10–120 / $120–500 | n/a | 0 (out of simulation scope) |

## Win rate by decline bucket — the core bracket-calibration number
| Bucket | Win rate | n |
|---|---|---|
| in_bracket (10–25%, <$10) | **70.4%** | 71 |
| shallow_outside (5–<10%, <$10) | 25.0% | 716 |
| deep_outside (>25–30%, <$10) | 50.0% | 4 |

The bracket is clearly earning its keep: in_bracket outperforms shallow_outside by 45 points, consistent with prior days.

## in_experiment_bracket=true, by firing (new 2026-08-25 breakdown)
| Firing | Win rate | n |
|---|---|---|
| 9:30 (buy) | 75.0% | 36 |
| 10:30 (buy) | 65.2% | 23 |
| 11:30 (no buy) | 80.0% | 5 |
| 3:30 (no buy) | 57.1% | 7 |

By time bucket: morning (9:30+10:30+11:30 pooled) 71.9% (n=64) vs afternoon 57.1% (n=7, thin sample).

Reading on the open questions: 10:30 (65.2%, n=23) sits between 9:30 and 11:30 and is a reasonable second buy window on this day's data — consistent with keeping it live. 11:30's 80% (n=5) is too small a sample to act on, but shows nothing disqualifying about extending buying there; 3:30's 57.1% (n=7) is also thin. No single day should move the 11:30-extension question — the per-firing split exists precisely so it can be tracked across more sessions.

## Hypothetical exit scenarios (in_bracket population)
- **Liquidation-rule scenario (D3a, mirrors 16a2 incl. the 0.999 tolerance band):** evaluable n=64 (60+ minutes of bars existed), triggered in 8 (12.5%) — i.e. 12.5% of in-bracket buys never got within 0.1% of breakeven in their first 60 minutes.
- **EOD-market-sell scenario (D3b):** n=71, average ROI **-2.04%** — meaningfully worse than the 70.4% decay/trailing win rate, underscoring that the resting take-profit limit (not a flat EOD dump) is doing real work.

## Trades / skipped-candidates cross-reference
- 9:30 firing: 2 buys (SKYD, JLHL); 34 bracket candidates reached a guardrail and were skipped (a1 spread ×7, a2 ATR ×6, a3 prior-spike ×7, a4 earnings ×1, a5 compliance ×7, a6 reverse-split ×3, a7 thin-liquidity ×3).
- 10:30 firing: 3 buys (NWGL, AIBZ, REA); 60 skip rows (a1 ×11, a2 ATR ×7, a3 prior-spike ×11, a4 earnings ×1, a5 compliance ×14, a6 reverse-split ×9, a7 thin-liquidity ×7).
- Across both firings, a5 (compliance) and a6 (reverse-split proxy) remain the two heaviest-weight guardrails on raw count, but neither saturated a firing (see D6b below).

## D6a — Exit-rule (16a3) firings today
One time-stop firing today, but executed by the **local every-10-minute Phase C loop**, not this cloud firing (the 3:30pm cloud firing found CRBU "already closed before this firing started" per its commit message):

| Symbol | Condition | sessions_held | drawdown_pct at exit | Realized P&L | 4:00pm close |
|---|---|---|---|---|---|
| CRBU | time_stop_4_sessions | 4 | -17.69% | **-$0.88** (9 sh, $0.5522→$0.4545) | $0.4615 |

Holding to the 4pm close instead would have realized -16.42% (-$0.81) — about $0.06 better, but still a loss and still not back to cost. One observation, not a trend; CRBU was opened 2026-10-08 and hit the on-schedule 4-session time stop right at the 3:30pm boundary as designed.

No drawdown-stop (-25%) firings today.

## D6b — Guardrail saturation check
Both buy firings (9:30, 10:30) placed trades, so the zero-buy diagnostic does not apply. For completeness: no single guardrail rejected 100% of the candidates reaching it at either firing (a5 and a6 were the largest single buckets, as usual, but neither cleared half the firing's bracket population alone).

## D7 — EOD manual liquidation suggestions (advisory only)
4 positions bought today and still open at day's end: SKYD, JLHL, AIBZ, REA (NWGL already sold at +5.06%). All four are down less than 5% from their buy price (range: -0.4% to -3.5%), so none meets the `pct_change_since_buy <= -5%` gate — **0 flagged**. `results/eod_liquidation_suggestions_2026-10-14.csv` is header-only. These are NOT executed automatically; any action here is the account owner's call.

## D8 — Decliner-bracket cohort tracking
- Cohort log: +791 new rows today (39 symbols never seen before got a fresh `get_equity_fundamentals` sector/industry tag; 752 copied from existing log entries). Distinct universe is now **2,430** symbols.
- Recovery tracking: capped to the 150 most-recently-added distinct symbols (universe exceeds the ~150 threshold), +147 rows appended, 3 skipped for no quote data (IAE, PSKY, HBAR — IAE is the account's own still-held, apparently-inactive instrument; PSKY/HBAR have shown no data on prior days too, consistent with delisting/instrument issues).
- Pure logging — no guardrail or buy-bracket changes made from this data.

## D10 — Guardrail paper-trade ledger
**Day 0 (today's skipped candidates):** 94 skip rows → 75 unique symbols → 73 new paper positions opened (2 symbols, QTEX and HCAI, already had an open paper position from an earlier day and were not duplicated per the dedup rule). Of the 73 new positions, 58 closed same-day (48 hit target, 9 triggered the 60-minute auto-liquidate check, 1 hit the 25% drawdown stop); 15 remain open.

**Carried forward (31 pre-existing open rows):** 17 closed today — 9 hit the on-schedule 4-session time stop (all from the 2026-10-08 cohort: LPCN×2, RGP×2, MVIS×2, WETO, BENF, PACB), 8 hit the 25% drawdown stop (KALA, EOSU, OFAL, AAOX, AAOG, HCAI, XELB, KPTI). 14 remain open. One symbol (LESL) could not be updated — its historicals lookup returned not_found (likely delisted); left as-is.

**Ledger totals:** 1,111 rows (1,082 closed, 1 of which lacks exit data from an earlier delisting and is excluded from stats below; 29 open).

### Closed-trade stats by skip_reason (all-time, n=1,081 valid; groups each guardrail's naming variants together)
| skip_reason | n | win rate | median ROI | still open |
|---|---|---|---|---|
| a1 (spread) | 214 | 79.9% | 5.04% | 3 |
| a2 (ATR) | 104 | 61.5% | 4.24% | 1 |
| a3 (prior-spike) | 241 | 74.7% | 5.00% | 9 |
| **a5 (compliance)** | 169 | 72.8% | 4.26% | 7 |
| **a6 (reverse-split proxy)** | 190 | **64.2%** | **4.31%** | 4 |
| a7 (thin-liquidity/stale-quote) | 57 | 70.2% | 5.00% | 4 |
| a8 (leveraged/inverse ETF) | 73 | 63.0% | 2.12% | 1 |

Mean ROI is omitted from this table deliberately — see the outlier caveat below.

**Mandatory caveat on mean ROI:** several skip_reason buckets contain a handful of historical rows with implausible ROI values (e.g. one row at +10,779%, several others in the hundreds-of-percent range) that are almost certainly data/target-computation artifacts from earlier days rather than real outcomes — the same class of bug documented in the 2026-09-03 "prior_peak_erroneous" fixes to the live Phase C loop. These outliers inflate the mean of every bucket by one to two orders of magnitude, which is why the table above reports median only. **Median is the trustworthy summary statistic here until the ledger's outlier rows are audited** — that audit is out of scope for this firing (D10 is read-only/append-only and this routine does not retroactively edit historical ledger rows). Flagging this for the account owner rather than silently "fixing" old data.

**On a6 specifically** (the guardrail under active review): win rate 64.2% (n=190) vs the account's actual realized win rate of 56.2% over the same window — a6-blocked names would likely have performed *better* than what the account actually achieved, on a simple win-rate basis. Median ROI (+4.31%) is close to the standard +5% target, suggesting most a6-blocked names that moved favorably did so cleanly. This is one day's addition to a growing sample; the account owner's 15x threshold decision should still wait for a larger/cleaner (outlier-audited) dataset.

### Baseline comparison (D10.g)
Account's actual realized results, 2026-08-25 through 2026-10-14 (same window the paper ledger covers): **176 closed trades, 56.2% win rate, -$23.29 total realized P&L (-2.63% rate of return)** (`get_realized_pnl`/`get_pnl_trade_history`, equity only).

Every guardrail bucket's win rate above (62–79%) beats the account's actual 56.2% — on win-rate alone, none of the guardrails look like they're leaving money on the table relative to what actually got bought. But this is an imperfect comparison: the real book's P&L is dollar-weighted and includes real spread/slippage costs that the paper ledger's optimistic fills (caveat below) do not.

**Caveats (mandatory, per protocol):**
1. Guardrails short-circuit in a1→a8 order, so `skip_reason` is only a candidate's *first* failure. A paper winner blocked by a6 might also have failed a7 or a8 had a6 not existed — `fundamentals_guardrails_failed` records a5/a6/a8 co-failures, but a7 is never recomputed (needs an extra historicals call per candidate), so no guardrail's removal is a clean "we'd have won this" claim.
2. Paper entries fill at the observed price with no spread or slippage, while real buys are market orders — every paper result is optimistic, most of all for the thin, low-priced names these guardrails specifically target.
3. Target exits assume a limit fills whenever a bar's high touches it — matches how the real GTC limit behaves and how D3 already simulates, but it's still an assumption.

## Data notes
- The under-$120 scan returned materially more candidates today (1,081 across 4 firings, up to 801 unique per firing) than the ~280 typical of recent days — a broader market decline, not a protocol change.
