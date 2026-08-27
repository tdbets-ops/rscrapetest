# Daily Analytics — 2026-08-27

## Headline

- **363** unique candidates seen today across 4 firings (9:30/10:30/11:30/3:30), **243** simulated (in_bracket/shallow_outside/deep_outside scope only; 113 other_price_or_range skipped by design, 7 in-scope symbols had zero non-interpolated bars after first-seen and are unsimulable).
- **Overall win rate (simulated): 45.7% (111/243)**

## By time bucket

| Bucket | Win rate | n |
|---|---|---|
| Morning (9:30/10:30/11:30) | 51.1% | 174 |
| Afternoon (3:30) | 31.9% | 69 |

## By price bucket (simulated rows only — 10-120 / 120-500 excluded by scope)

| Bucket | Win rate | n |
|---|---|---|
| <$1 | 61.2% | 85 |
| $1-3 | 44.3% | 70 |
| $3-10 | 31.8% | 88 |

## By decline bucket

| Bucket | Win rate | n |
|---|---|---|
| in_bracket (10-25%, <$10) | 47.4% | 38 |
| shallow_outside (5-10%, <$10) | 44.0% | 200 |
| deep_outside (25-30%, <$10) | 100.0% | 5 |

## In-bracket by firing (the direct evidence for the 10:30 window and whether 11:30 should follow)

| Firing | Win rate | n |
|---|---|---|
| 9:30 | 52.6% | 19 |
| 10:30 | 33.3% | 12 |
| 11:30 | 33.3% | 3 |
| 3:30 | 75.0% | 4 |

Small samples throughout (n=3-19 per firing) — no firing-level conclusion should be drawn yet. 9:30 continues to outperform 10:30 on this single day (52.6% vs 33.3%), consistent with the account owner's original morning-outperforms-afternoon read, but n=12 at 10:30 is too thin to judge the second window. 11:30 (n=3, 33.3%) does not yet make a case for opening a third buy window.

## Cross-reference with today's real trades

Two buys placed today: **WEN** (9:30, still open at close, matches the decay/trailing simulation which also shows it un-closed with peak 6.2% of the way to target) and **HCWC** (10:30, filled 0.2775, decay/trailing sim shows it *would* have closed profitably at +5.34% — but the REAL position was auto-liquidated at a loss (-7.6%, exit 0.2565) by the 16a2 60-minute-never-touched-breakeven rule before the resting limit could fill). This is a reminder that D3's decay/trailing simulation does not model the a2 auto-liquidate override, so it will systematically overstate outcomes for names that get auto-liquidated before reaching target.

## Guardrail saturation check (D6b)

Not triggered — both buy firings placed at least one trade (9:30: 1 buy; 10:30: 1 buy), so the zero-buy diagnostic does not apply. For completeness, per-guardrail skip counts:

- **9:30** (18 skipped, 19 bracket candidates): a3_prior_spike 5, a5_compliance 4, a1_spread 3, a2_atr 2, a8_leveraged_etf 2, a4_earnings_recency 1, a6_reverse_split_proxy 1. No single guardrail exceeded 28% of candidates reaching it.
- **10:30** (29 skipped, 30 bracket candidates): a5_compliance 8, a6_reverse_split_proxy 7, a1_spread_pct 5, a3_prior_spike 4, a8_leveraged_inverse_etf 3, a2_atr_guardrail 1, a7_stale_quote 1. No single guardrail exceeded 28% of candidates reaching it.

No saturation pattern and no repeat-saturation across the two windows or across days — nothing to flag.

## Exit-rule firings (D6a)

None — step 16a3 (time-stop / drawdown-stop) did not fire today. (HCWC's liquidation today was step 16a2's 60-minute auto-liquidate, a different rule, not 16a3.)

## EOD manual liquidation suggestions (advisory only — NOT executed automatically)

Only WEN was open and bought today (HCWC already closed via auto-liquidate before this analysis ran). WEN: pct_change_since_buy -1.57%, near_low=true, trend_down=true, but the -5% decline threshold was not met, so **not flagged**. See `results/eod_liquidation_suggestions_2026-08-27.csv`.

## Cohort tracking (D8)

- Appended 250 new candidates to `decliner_cohort_log.csv` (5-30% decline, <$10 tracking band) — cohort log now covers 2,019 distinct symbols since 2026-07-24.
- 16 symbols were new-to-the-log and got a fresh fundamentals fetch for sector/industry; the other 234 today's candidates copied their sector/industry from prior log entries.
- Recovery tracking: quoted the 150 most-recently-added distinct symbols (of 2,019 total, capped as designed) — 148/150 re-quoted successfully; CRV and ZORA came back not_found (consistent with prior days, likely delisted).

## Guardrail paper-trade ledger (D10)

Opened **37** new day-0 paper positions today (10 symbols were skipped by multiple firings today and deduplicated to their first occurrence; none overlapped with an already-open ledger position or with today's 2 real buys). **19 closed same-day**, 18 carried to open. Carrying forward the 15 pre-existing open positions (9 from 2026-08-25, 6 from 2026-08-26): **6 closed today** (AMOD via drawdown-stop; NXTT, ALBG, PFSA, EXOD, AEHG via target), 9 remain open. Ledger total: 103 rows, 76 closed, 27 open.

### By skip_reason (closed trades only)

| skip_reason | n closed | win rate | mean roi% | median roi% | sum roi% | still open |
|---|---|---|---|---|---|---|
| a5_compliance | 20 | 85.0% | +1.15% | +5.06% | +23.07% | 8 |
| a1_spread (+a1_spread_pct) | 19 | 94.7% | +4.72% | +5.14% | +89.64% | 2 |
| a3_prior_spike | 15 | 93.3% | +4.29% | +5.16% | +64.40% | 3 |
| **a6_reverse_split_proxy** | **12** | **83.3%** | **+1.27%** | **+5.07%** | **+15.28%** | **5** |
| a8_leveraged_etf | 4 | 100.0% | +5.35% | +3.60% | +21.41% | 2 |
| a2_atr | 3 | 66.7% | -3.98% | +6.21% | -11.94% | 1 |
| a4_earnings_recency | 3 | 33.3% | -2.11% | -3.57% | -6.33% | 1 |

a6 (the guardrail under active review) shows an 83.3% win rate and positive sum ROI, pulled down in mean-terms by one -25% drawdown-stop (SMJF, which cratered from a previous-close of 11.33 to under $1.50 intraday — an extreme single-name event, not representative). Still a small sample (n=12 closed); no threshold change is warranted from one day's data.

**Caveats (apply to every row above):**
1. Guardrails short-circuit in a1→a8 order, so skip_reason reflects only each candidate's *first* failure — paper results measure that guardrail's *marginal* cost given earlier guardrails already passed. The `fundamentals_guardrails_failed` column records every one of a5/a6/a8 a candidate would also have failed; a7 is not recomputed (needs an extra call per candidate), so removing any guardrail is never a clean "we'd have won this" claim.
2. Paper entries fill at the observed price with no spread and no slippage; real buys are market orders, so every paper result is optimistic relative to what a real fill would achieve — most optimistic for exactly the thin, wide-spread, low-priced names these guardrails target.
3. Target exits assume a limit fills whenever a bar's high touches it, matching how the real resting GTC limit behaves (and how D3 already simulates), but it remains an assumption.

**Baseline — actual realized results, same window (2026-08-25 through today):** 5 closed real round trips — BAK +$0.09, AARD +$0.13, ADBT +$0.21, NTRP +$0.27, HCWC -$0.38 → **80.0% win rate, +$0.32 net realized P&L**. The paper-blocked populations above (a5: 85.0%, a1: 94.7%, a3: 93.3%, a6: 83.3%) all show win rates in the same range as — not clearly superior to — what the account actually achieved over the same days, before accounting for caveats 1-3 above (all of which bias the paper numbers upward). No guardrail's blocked population makes a strong case for loosening on this data.
