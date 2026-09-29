# Daily Analytics — 2026-09-29

**Candidates seen today:** 396 unique symbols (394 under-$120, 2 $120–$500: AMR, HRI)
**Simulated (D3 scope: in_bracket + shallow_outside + deep_outside, price < $10):** 319 of 320 in-scope symbols (HBAR resolved to no equity historicals — see Anomalies)
**Out of scope (other_price_or_range, not simulated):** 76 symbols — simulated=false, null scenario columns, per the D3 scope limit (never re-widened)

## Headline win rate (decay/trailing algorithm, simulated=true only)

**Overall: 154 / 319 closed by market close = 48.3%**

| Breakdown | n | Win rate |
|---|---|---|
| Morning (9:30 + 11:30×2) | 238 | 54.2% |
| Afternoon (3:30) | 81 | 30.9% |
| Price <$1 | 104 | 57.7% |
| Price $1–3 | 123 | 43.1% |
| Price $3–10 | 92 | 44.6% |
| decline_bucket in_bracket (10–25%) | 54 | 61.1% |
| decline_bucket shallow_outside (5–<10%) | 264 | 45.8% |
| decline_bucket deep_outside (>25–30%) | 1 | 0.0% (n=1, not meaningful) |

Note: price_bucket only has meaningful data in <1 / 1–3 / 3–10 — 10–120 and 120–500 are never simulated by construction (in-scope symbols are all under $10), not silently dropped.

## In-bracket (in_experiment_bracket=true, the live buy bracket) by firing — the core 11:30-window evidence

| Firing | n | Win rate |
|---|---|---|
| 9:30 | 27 | 63.0% |
| 11:30 (both today's firings combined) | 19 | 68.4% |
| 3:30 | 8 | 37.5% (n=8, small sample) |
| 10:30 | — | **did not fire today** (no scan/buy window ran — see Known Facts) |

By time bucket: morning 30/46 = 65.2%, afternoon 3/8 = 37.5%.

**Read for the 11:30-buy-window question:** in-bracket candidates first seen at 11:30 today would have won 68.4% of the time under the existing decay/trailing algorithm — the best of the three firings that ran, and comparable to 9:30's 63.0%. This is one day's evidence (n=19) but does not support the idea that 11:30 is a categorically worse buy window than 9:30; if anything today argues for turning it on. Small samples — treat as directional, not conclusive.

## Cross-reference with today's live buy firing (9:30, the only buy window that ran)

trades_20260929T1340Z.csv bought 2 of the 27 in-bracket candidates: **INSE** (closed=true, target hit at 13:40, would-be ROI +5.0%; live trade sold at 3.75 for +18¢/share) and **QNC** (closed=false, still ~1.6% short of target at close; live position still open, currently -4.6% unrealized). skipped_candidates_20260929T1340Z.csv rejected the other 25; of those, the simulation shows 15 would have closed as winners by end of day — i.e. most of today's in-bracket winners were guardrail-skipped, not bought. See the paper-trade ledger below for what buying them would actually have been worth.

## Guardrail saturation check (D6b) — 9:30 buy firing, the only buy firing today

Bracket-candidate count (in-bracket pool reaching guardrails): **27** (2 bought + 25 skipped)

| Guardrail | Rejected | % of candidates that reached it |
|---|---|---|
| a1_spread | 5 | 5/27 = 18.5% |
| a2_atr | 4 | 4/22 = 18.2% |
| a3_prior_spike | 6 | 6/18 = 33.3% |
| a5_compliance | 2 | 2/12 = 16.7% |
| a6_reverse_split_proxy | 5 | 5/10 = 50.0% |
| a7_thin_liquidity | 3 | 3/5 = 60.0% |

Funnel: 27 → (−5 a1) 22 → (−4 a2) 18 → (−6 a3) 12 → (−2 a5) 10 → (−5 a6) 5 → (−3 a7) 2 (bought). No single guardrail rejected 100% of what reached it — **no malfunction flag.** a6 and a7 show high percentages but off small remaining pools (10 and 5 candidates respectively), not saturation.

**10:30 window:** did not fire today (confirmed via file listing and git log — no `under120_down5_20260929T14*Z` / `trades_/skipped_candidates_` file for a 14:xx UTC window exists). This is a missing firing, not a zero-buy diagnostic case.

## Exit-rule (16a3) firings today (D6a)

**INNV** fired the step-16a3 **time-stop** rule: sessions_held reached 4 (entered 2026-09-23, held through 9/24, 9/25, 9/28, 9/29), forced market sell at 19:32:46Z.
- Exit price: $8.9215 (avg fill); entry $9.5799
- sessions_held at exit: 4
- Return at exit vs. entry: −6.87%; max intraday drawdown from peak ($9.74) to the trailing low ($8.40) during the hold: −13.8% peak-to-trough
- Realized round-trip P&L: **−$0.66**
- ~4:00pm close: **$8.96** (last regular-session trade, 19:59:59Z)

INSE's same-day sale was a normal decay/trailing **target hit** (limit sell filled at $3.75), not a 16a3 forced exit.

## Advisory: EOD manual liquidation suggestions (D7) — NOT executed automatically

Only one symbol bought today remains open: **QNC** (2 shares, avg cost $1.83, current resting sell limit $1.87). Evaluated and **not flagged**: pct_change_since_buy = −4.57% (misses the −5% threshold by a hair), so criteria (a) already fails — `results/eod_liquidation_suggestions_2026-09-29.csv` is header-only. INSE (today's other buy) already closed via its own limit sell and is excluded (not "still open"). These suggestions are advisory only; no order was placed or cancelled by this firing.

## Guardrail paper-trade ledger (D10)

**Today:** +24 new paper positions opened from the 9:30 skip file (25 skipped candidates minus RKDA, which already had an open paper position — no duplicate opened, per dedup rule). 17 of the 24 closed same-day (16 via decay/trailing target, 1 via 25% drawdown stop on AIXC); 7 remain open (NCT, PMAX, ONCO, MNOV, BEZ, EHGO, JLHL).

**Carry-forward (34 pre-existing open rows):** 15 closed today — 2 via the 4-session time-stop (RIME, BYND), 3 via target (PFSA +29.6%, GWH +4.0%, FFAI +8.1%), and **10 via the 25% single-day drawdown stop** (SVRE, RKDA, FISN, WETO, WHLR, JAGX, LGHL, NRSN, XXII, HUBC). YYGH (not_found on historicals — likely delisted/renamed) and IRAB (zero bars today, likely halted/illiquid) could not be updated this cycle and were left unchanged, flagged below. 19 carry forward still open.

**Notable pattern:** of the 14 positions opened from the 9/28 skip cohort, 9 already resolved one carry-forward day later — a stark 6-losses-at-exactly−25% vs. 3-wins-of-4–30% split, no middle ground. That is a real, sharp single-day move in this bracket, not a simulation artifact (built from real 5-minute bars); it reads as the guardrails correctly keeping the live account out of a rough day for several of these names, not merely costing profit.

**Ledger totals now:** 702 rows (676 closed, 26 open); 32 positions closed today.

### Closed-trade performance by skip_reason (all-time, this ledger)

| skip_reason | n closed | win rate | mean ROI% | median ROI% | summed ROI% | still open |
|---|---|---|---|---|---|---|
| a1_spread(_guardrail/_pct/_gate combined) | 133 | ~83% | inflated (see caveat 1 below) | ~5.0% | — | 1 |
| a2_atr(_guardrail) | 60 | ~59% | inflated | ~4.0–4.5% | — | 3 |
| a3_prior_spike(_guardrail) | 154 | ~79% | inflated | ~4.9% | — | 6 |
| a4_earnings(_recency) | 24 | ~54% | 0.6% | ~3.0% | 14.2% | 3 |
| a5_compliance(_guardrail) | 103 | ~76% | inflated | ~4.7% | — | 5 |
| **a6_reverse_split_proxy** | **117** | **70.1%** | **1.56%** | **4.93%** | **182.2%** | **5** |
| a7_thin_liquidity (all variants) | 34 | ~68% | mixed, small n | ~2.9% | −49.2% | 3 |
| a8_leveraged/inverse ETF (all variants) | 47 | ~77% | 5.0% | ~4.1% | 337.6% | 0 |

**a6 (under active review):** 70.1% win rate, median +4.93%, 117 closed trades, only 5 still open — this is a clean, non-inflated read (no scale-anomaly rows land in a6 today) and it continues to look like a costly guardrail: most of what it blocks would have won.

### Three caveats (verbatim, per spec)

1. Guardrails short-circuit in a1→a8 order, so skip_reason is only a candidate's FIRST failure — paper results for guardrail aX measure aX's MARGINAL cost given a1..a(X-1) already passed. Cross-check fundamentals_guardrails_failed (records every one of a5/a6/a8 the candidate would have failed). a7 is NOT recomputed, so removing a guardrail is never a clean "we would have won this" claim.
2. Paper entries fill at the observed price with no spread/slippage, unlike real MARKET-order buys — every paper result is optimistic, most of all for the thin low-priced names these guardrails target.
3. Target exits assume a limit fills whenever a bar's high touches it, matching D3's own assumption but still an assumption.

### Baseline: actual account performance, same window (2026-08-25 to 2026-09-29)

Actual realized P&L (get_equity_orders/PnL, equity only): **−$14.79** total realized gain, **−2.50%** rate of return, across ~115 closing trades (per-trade tally, approximate per-trade win rate ~54%). Side by side with the paper ledger's much higher win rates and positive median ROI above, the gap is exactly what caveats 1–2 predict: paper trades skip spread/slippage and only ever measure one guardrail's marginal effect, so they are not a clean "the guardrails are costing us X%" number — but the consistent >50% win rate across nearly every guardrail bucket, a6 especially, is still a real signal worth the account owner's attention.

## Data-quality anomalies flagged this run

1. **Entry-price scale bug is bigger than previously flagged.** Yesterday's summary flagged CTNT alone; today the same ~100x-too-low entry_price pattern also distorts GOSS, OMH, MGN (×2), VBIO, BRTX, TXXD — all show closed-trade ROI between +690% and +10,779%, which is not real. This corrupts every mean/sum in the table above; medians are unaffected and are the reliable read. This is a paper-ledger-only issue (no real money involved) but is growing, not shrinking, and is worth someone looking at the entry-price capture code path.
2. **Crypto tickers keep leaking into the equity scanner.** HBAR (Hedera) appeared on today's under-$120 list and reached the in-bracket simulation set, but has no equity historicals (not_found) — same story for SYRUP, ONDO, UNI, NHIC, WLFI in the recovery-tracking quote batch (also not_found/no data). These are 6 likely-crypto or delisted tickers out of 470 total symbol-lookups this run; none blocked the run (all handled gracefully), but the scanner's input source may need an equity/crypto filter.
3. **get_equity_historicals `interval=day` did not have today's daily bar available** as of this firing (20:3x UTC, after the 20:00 UTC close) — it accepted a range ending yesterday but errored on any range including today. Worked around by building today's synthetic daily OHLC from 5-minute bars for the D10d carry-forward step; results should be equivalent, but noting the workaround for anyone who reruns this.

## Cohort tracking (D8)

- Appended 320 rows to `decliner_cohort_log.csv` for today (5–30% decline, price <$10 — same population as the D3 in-scope set).
- 11 of those symbols were new to the log; sector/industry fetched for 10 (GOW, RILY, CDNG, HOST, CCG, XPEV, FGC, ALIT, NWCL, NMG) — HBAR not_found, left blank. 309 symbols reused sector/industry already on file (some of those are themselves blank, inherited from rows logged before this field existed — not re-fetched, per the "copy, don't refetch" rule).
- Recovery tracking: 2,296 distinct symbols now in the full cohort log; capped this cycle to the 150 most-recently-added. 144 quoted and appended to `decliner_recovery_tracking.csv`; 6 skipped as no-data (HBAR, SYRUP, ONDO, UNI, NHIC, WLFI).
