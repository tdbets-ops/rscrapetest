# Daily Analytics — 2026-10-02

## Headline

- 228 unique candidates seen today (224 under-$120, 4 $120-$500) across 2 scan firings (9:30, 10:30 ET). The 11:30 and 3:30 ET firings both hit the balance gate (buying_power $40.46 < $50) and skipped Phase A, so no scan data exists for them today.
- Scope note: per the 2026-07-29 fix, only `in_bracket` / `shallow_outside` / `deep_outside` symbols are simulated. 154 of 228 (67.5%) qualified; 74 `other_price_or_range` (mostly the $120-$500 universe and shallow-priced-but-out-of-range names) were skipped from simulation by design.
- **Overall simulated win rate: 48.7% (75/154).**

## Win rate breakdowns (simulated=true only)

| Breakdown | Win rate | n |
|---|---|---|
| Overall | 48.7% | 75/154 |
| decline_bucket: deep_outside | 100.0% | 3/3 |
| decline_bucket: in_bracket | 60.9% | 14/23 |
| decline_bucket: shallow_outside | 45.3% | 58/128 |
| price_bucket: <1 | 44.9% | 22/49 |
| price_bucket: 1-3 | 47.3% | 26/55 |
| price_bucket: 3-10 | 54.0% | 27/50 |
| price_bucket: 10-120 / 120-500 | n/a | 0/0 (excluded by scope — see note) |
| source_list: under120 | 48.7% | 75/154 |
| source_list: 120to500 | n/a | 0/0 (all 4 rows classed other_price_or_range) |
| time_bucket: morning | 48.7% | 75/154 (only morning firings ran today) |

### In-bracket (`in_experiment_bracket=true`) by firing — the direct evidence for the second buy window

| Firing | Win rate | n |
|---|---|---|
| 9:30 | 64.7% | 11/17 |
| 10:30 | 50.0% | 3/6 |
| 11:30 | n/a | 0/0 (no scan today) |
| 3:30 | n/a | 0/0 (no scan today) |

Small samples (17 and 6). Directionally the 9:30 window still outperforms 10:30 today, consistent with the account owner's original morning-outperforms-afternoon read, but n=6 at 10:30 is too thin to move the needle on its own — cumulative history across multiple days is the more reliable read for the keep/drop decision on the second window.

## Guardrail saturation check (D6b)

- **9:30 firing (0 buys placed):** 17 bracket candidates reached guardrails; none produced a buy. Per-guardrail skip counts: a1_spread=2, a2_atr=1, a3_prior_spike=4, a4_earnings=0, a5_compliance=1, a6_reverse_split_proxy=3, a7_thin_liquidity=2, a8_leveraged_etf=4. **a7 (thin-liquidity/stale-quote) rejected 100% of the candidates that reached it (2/2: CHWM ask-gap 9.41%, SORA ask-gap 4.51%)** — the only guardrail to saturate at 100% this run. No other guardrail exceeded a minority of its reached population, so this reads as a genuinely thin/stale-quote pool rather than a guardrail malfunction. (Diagnosed live in the 9:30 firing's own commit message per the mandatory zero-buy diagnostic.)
- **10:30 firing:** not a zero-buy session (4 trades placed: CHWM, FHTX, SORA, VIOT), so the diagnostic does not apply.
- No guardrail saturated on two consecutive buy firings today or across the 9:30/10:30 pair.

## Exit-rule firings (D6a)

No step 16a3 (time-stop / drawdown-stop) liquidations fired today. All position-management cycles found drawdowns and sessions_held within the -25%/4-5-session thresholds. (Two of today's own buys, FHTX and VIOT, were separately closed intraday by the step 16a2 60-minute-never-touched-breakeven auto-liquidate — not a 16a3 event — realizing -6.9% and -6.1% respectively; CHWM and SORA closed as decay/trailing-target wins at +4.3% and +4.3%.)

## EOD liquidation suggestions (D7, advisory only)

0 flagged. All four of today's buys (CHWM, FHTX, SORA, VIOT) were already closed by the time of this 4:30pm ET analysis (2 via a2 auto-liquidate, 2 via target fills) — none remained open to evaluate. `results/eod_liquidation_suggestions_2026-10-02.csv` written header-only. These are advisory-only and were never executed automatically by this step regardless.

## Cohort tracking (D8)

- 154 new rows appended to `decliner_cohort_log.csv` for today's 5-30%-decline/<$10 tracking band (distinct-symbol universe now 2,328). 7 brand-new symbols got a one-time sector/industry fundamentals lookup (APPC, APPX, BCG, CHWM, GYRO, OPNW, SSG); the rest copied sector/industry from their existing log entry.
- Recovery tracking refreshed against the 150 most-recently-added distinct symbols (range: 2026-09-15 to 2026-10-02). 145/150 quoted successfully; 5 skipped (HBAR, SYRUP, ONDO, UNI, NHIC — the known crypto-ticker-leak-into-equity-scanner issue, consistent with prior days).

## Guardrail paper-trade ledger (D10)

**Today:** +31 new paper positions opened from today's 37 distinct skipped-candidate rows (minus CHWM/SORA, which were actually bought at 10:30, and minus NCI/SCNX/TGE/KNRX, which already had open paper rows from prior days). 27 of the 31 closed same-day (23 target fills, 2 a2-style 60-min auto-liquidates at -9.4%/-3.5%, 1 a6_reverse_split_proxy-tagged SXTC hit the 25% drawdown stop); 4 remain open (SPCQ, SSPC, LOFD, EJH).

Separately, 28 pre-existing open paper positions were carried forward one session: 7 closed today (KITT and SCNX hit the 25% drawdown stop; QNME and ADXN hit the 4-session time stop at a loss [-16.2%, -7.4%]; AUID hit the 4-session time stop at a gain [+6.2%]; OBAI and DKI hit their decay/trailing target [+3.8%, +8.0%]); 21 remain open.

### Cumulative per-guardrail results (all closed paper trades to date, grouped by normalized guardrail a1-a8; medians are the more reliable statistic — several guardrails' means are skewed by a small number of known entry-price-scale outliers flagged in prior runs)

| Guardrail | n closed | win rate | mean ROI | median ROI | sum ROI | n still open |
|---|---|---|---|---|---|---|
| a1 (spread) | 148 | 83.1% | +28.6% | +5.1% | +4238.8% | 5 |
| a2 (ATR) | 64 | 57.8% | +230.5%* | +3.9% | +14753.8%* | 0 |
| a3 (prior-spike) | 167 | 78.4% | +58.8%* | +5.0% | +9825.3%* | 6 |
| a4 (earnings-recency) | 27 | 51.9% | +0.2% | +2.1% | +5.5% | 0 |
| a5 (compliance) | 122 | 76.2% | +40.4%* | +4.3% | +4924.2%* | 2 |
| **a6 (reverse-split proxy)** | **134** | **70.1%** | +1.1% | **+5.0%** | +149.8% | 8 |
| a7 (thin-liquidity/stale-quote) | 44 | 68.2% | -1.1% | +3.9% | -48.7% | 0 |
| a8 (leveraged/inverse ETF) | 54 | 79.6% | +6.9% | +5.0% | +370.3% | 4 |

\* mean/sum distorted by pre-existing entry-price-scale anomaly rows flagged in earlier runs (e.g. CTNT, GOSS, OMH, MGN, VBIO, BRTX, TXXD) — treat median as authoritative for these columns.

**a6 specifically** (under active review since 2026-08-25): n=134 closed, 70.1% win rate, median +5.0% — in line with the other guardrails and still not an outlier case for loosening the 15x threshold. 8 a6-tagged positions remain open.

**Caveats (apply to every number in this section):**
1. Guardrails short-circuit in a1→a8 order, so `skip_reason` is only a candidate's *first* failure; paper results for guardrail aX measure aX's marginal cost given a1..a(X-1) already passed. `fundamentals_guardrails_failed` records every one of a5/a6/a8 a candidate would also have failed; a7 is never re-evaluated (needs an extra historicals call), so no guardrail's removal is a clean "we would have won this" claim.
2. Paper entries fill at the observed price with no spread paid and no slippage, while real buys are market orders — every paper result is optimistic relative to a real fill, most of all for the thin, low-priced names these guardrails target.
3. Target exits assume a limit fills whenever a bar's high touches it, matching how the real resting GTC limit behaves and how D3 already simulates — but it remains an assumption.

### Baseline comparison (the decision-relevant number)

Account's actual realized P&L, all-time: **-$145 / 301 closing trades (-3.56%)** (via `get_realized_pnl`, span=all). Every guardrail's paper-ledger median ROI is solidly positive (+2% to +5%) against this negative real-money baseline — as in every prior run, this gap is explained by caveats 1-3 above (frictionless paper fills vs. real market-order slippage on thin names, and the marginal-cost-only interpretation of each guardrail's population) rather than by the guardrails being miscalibrated. No guardrail's paper population beats the real baseline by enough, once those frictions are accounted for, to justify loosening it on today's data alone.
