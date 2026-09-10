# Daily Analytics — 2026-09-09

**Note on firing coverage:** only 3 of the usual 4 non-4:30 firings ran today (9:30, 10:30, 11:30 ET) — the 3:30pm ET firing did not fire (no `under120_down5_20260909T19*.csv` / `over120_under500_down5_20260909T19*.csv` files exist). All figures below are drawn from those 3 firings only. Because 3:30 never ran, `time_bucket` is "morning" for literally everything seen today (the morning cluster is 9:30/10:30/11:30 per the routine's own bucket definition) — no afternoon rows exist to compare against.

**Scope note (per the 2026-07-28 incident):** the per-symbol decay/trailing simulation (D3/D3a/D3b) runs only on `decline_bucket` in (in_bracket, shallow_outside, deep_outside) — i.e. price < $10 with a 5–30% decline. Symbols classified `other_price_or_range` (127 of 380 today, including everything from the $120–500 scan) are excluded from simulation by design and carry `simulated=false` / null scenario columns in the CSV.

## Headline

- **Candidates seen today:** 380 unique symbols (under-$120 scan + $120–500 scan, deduped)
- **Simulated (in-scope):** 248 of 253 in-scope symbols (5 had zero usable bars: DOT and DAMD were not found by the historicals API at all; IRAB, ILAG, IFBD returned only interpolated bars)
- **Overall win rate (decay/trailing target hit by close):** **47.2%** (117/248)

## By time bucket

| bucket | n | win rate |
|---|---|---|
| morning | 248 | 47.2% |
| afternoon | 0 | n/a — 3:30 firing didn't run |

## By price bucket (simulated rows only — 10-120 and 120-500 are never simulated, see scope note)

| bucket | n | win rate |
|---|---|---|
| <1 | 58 | 63.8% |
| 1-3 | 75 | 48.0% |
| 3-10 | 115 | 38.3% |
| 10-120 | 0 | n/a (out of D3 scope) |
| 120-500 | 0 | n/a (out of D3 scope) |

## By decline bucket (bracket calibration)

| bucket | n | win rate |
|---|---|---|
| in_bracket (10-25% decline, <$10 — the live buy bracket) | 27 | **59.3%** |
| shallow_outside (5-10% decline, <$10) | 220 | 45.9% |
| deep_outside (25-30% decline, <$10) | 1 | 0.0% |

The in-bracket population continues to outperform the shallow-decline population outside it (59.3% vs 45.9%), consistent with prior days' calibration reads. The deep_outside bucket has n=1 today — not meaningful.

## In-bracket win rate by firing (the direct evidence for the two live buy windows + whether 11:30 should join)

| firing | n | win rate |
|---|---|---|
| 9:30 (live buy window) | 15 | **73.3%** |
| 10:30 (live buy window) | 9 | **44.4%** |
| 11:30 (not a buy window) | 3 | 33.3% |
| 3:30 (not a buy window) | 0 | n/a — didn't fire today |

Small samples (n=15, n=9, n=3) — one day's read, not a trend. Today the 9:30 window's in-bracket candidates clearly outperformed 10:30's; 11:30's n=3 is too thin to say anything about extending buying to that window.

## Cross-reference with today's real trades / skipped candidates

- 9:30 firing: 0 buys, 16 bracket candidates all skipped by a guardrail (see saturation check below).
- 10:30 firing: 5 buys (OCGN, WYHG, NTHI, FJET, GIXI) out of the bracket-eligible pool that reached step 12.

## Exit-rule firings (D6a)

One step-16a3 time-stop liquidation fired today, executed by the local every-10-minute loop at 2026-09-09T19:33:59Z (3:33:59pm ET) since the cloud's own 3:30pm firing never ran:

| symbol | condition | sessions_held | drawdown_pct at exit | realized P&L | 4:00pm close |
|---|---|---|---|---|---|
| CANF | time_stop_4_sessions | 4 | -12.69% (2.5399 → 2.217) | -$0.323/share (-12.71%) | $2.22 |

Exiting vs. holding made essentially no difference today — the 4:00pm close ($2.22) was within $0.003 of the exit fill ($2.217). This is one data point in the ongoing review of the 25%/4-session time-stop rule.

## Guardrail saturation check (D6b)

The 9:30am firing placed **zero buys**, so per the mandatory diagnostic: 16 bracket-eligible candidates were evaluated, and every guardrail type fired at least once — a1_spread(3), a2_atr(3), a3_prior_spike(1), a4_earnings(1), a5_compliance(3), a6_reverse_split(3, +2 more as a secondary co-failure), a7_thin_liquidity(2). **No single guardrail accounted for more than 3/16 (18.75%) of the candidates that reached it** — this reads as a genuinely thin/bad candidate pool that morning, not a guardrail malfunction. The 10:30 firing bought 5, so there's no two-consecutive-firing saturation pattern either.

## EOD liquidation suggestions (D7, advisory only — NOT executed)

Zero symbols flagged. Four positions bought today remain open (OCGN, WYHG, FJET, GIXI — NTHI already sold via its resting limit); evaluated against all three SUGGEST_LIQUIDATE criteria (≤-5% since buy AND near 60-min-window low AND trending down):
- OCGN: -4.5% since buy (fails the -5% threshold)
- WYHG: +0.4% since buy (positive)
- FJET: -7.7% since buy, but price has bounced well off its post-buy low (2.04 vs low 1.96 — not "near low")
- GIXI: -3.2% since buy (fails the -5% threshold)

`results/eod_liquidation_suggestions_2026-09-09.csv` written header-only.

## Decliner cohort tracking (D8)

- 253 new candidates (price < $10, 5-30% decline) appended to `decliner_cohort_log.csv` today; none were already logged for 2026-09-09.
- 14 symbols got fresh `get_equity_fundamentals` sector/industry lookups (true first-ever appearances); 239 had sector/industry copied from their earliest existing log row. 1 (DOT) came back not_found.
- Full cohort log now spans **2,121 distinct symbols**.
- Recovery tracking: distinct-symbol count exceeded the 150 cap, so the 150 most-recently-added distinct symbols were re-quoted (DOT, ENA, OP skipped — missing/inactive instruments); 147 rows appended to `decliner_recovery_tracking.csv`.

## Guardrail paper-trade ledger (D10)

- **23 new paper positions** opened today from the two skipped_candidates files (dedup'd against real buys OCGN/WYHG/NTHI/FJET/GIXI — WYHG's earlier 9:30 skip was excluded since it was later bought at 10:30). 18 closed same-day, 5 stayed open.
- **28 pre-existing open positions carried forward**; 10 closed today (5 via paper_target incl. MGN +689% and FTFT +149%, 3 via paper_time_stop_4_sessions, 2 via paper_drawdown_stop_25pct).
- **Closed-trade performance by skip_reason** (all 286 closed paper trades in the ledger, n / win% / mean roi% / median roi% / summed roi%):

| skip_reason | n | win% | mean roi% | median roi% | sum roi% |
|---|---|---|---|---|---|
| a1_spread_guardrail | 53 | 84.9% | 15.21% | 5.01% | 806.0 |
| a2_atr_guardrail | 29 | 58.6% | 141.95% | 4.16% | 4116.6 |
| a3_prior_spike_guardrail | 66 | 84.8% | 16.96% | 5.04% | 1119.6 |
| a4_earnings_recency | 10 | 60.0% | 0.15% | 4.00% | 1.5 |
| a5_compliance_guardrail | 47 | 78.7% | 104.74% | 5.00% | 4922.8 |
| **a6_reverse_split_proxy** | **48** | **66.7%** | **4.49%** | **5.00%** | **215.5** |
| a7_thin_liquidity_guardrail | 12 | 75.0% | 1.35% | 5.00% | 16.2 |
| a8_leveraged_inverse_etf | 21 | 90.5% | 2.76% | 2.24% | 57.9 |
| **Overall** | **286** | **77.3%** | 39.36% (skewed by rare huge movers) | **5.00%** | — |

**Baseline (real account, ~Aug 13 – Sep 9, FIFO buy→sell pairing, 7 still-open lots excluded):** 60 round trips, **58.3% win rate**, total P&L **-$3.74**, avg P&L/trade -$0.06, avg ROI/trade -1.17%, median ROI +2.25%.

The paper ledger's 77.3% win rate / +5.00% median ROI sits well above the real account's 58.3% / +2.25% — but see the caveats below before reading that as "guardrails are too strict." a6 specifically (still under active review, 15x split-ratio threshold): 48 closed, 66.7% win rate, +5.00% median ROI — a meaningfully positive first real sample, though still well short of a robust validation.

**Caveats (apply to every number above):**
1. `skip_reason` is only each candidate's *first* guardrail failure (a1→a8 short-circuit order) — these are each guardrail's *marginal* cost given earlier guardrails already passed. The `fundamentals_guardrails_failed` column records every one of a5/a6/a8 a candidate would *also* have failed; a7 is never recomputed here (needs an extra historicals call per candidate), so no guardrail's removal is a clean "we'd have won this."
2. Paper entries fill at the observed price with zero spread/slippage; real buys are market orders. Every paper result is optimistic relative to a real fill — most of all for the wide-spread, thin, low-priced names a1/a6/a7 specifically target.
3. Target exits assume a limit fills whenever a bar's high touches it — matches how the live resting GTC limit behaves and how D3 simulates, but it's still an assumption.

## Files written today

`daily_analytics_2026-09-09.csv`, `daily_analytics_summary_2026-09-09.md`, `eod_liquidation_suggestions_2026-09-09.csv`, `decliner_cohort_log.csv`, `decliner_recovery_tracking.csv`, `guardrail_paper_trades.csv`.
