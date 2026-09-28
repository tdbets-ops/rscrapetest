# Daily Analytics — 2026-09-28

## Headline

- **344** unique candidates seen today (under-$120 + $120-500 lists, across the 9:30 and 10:30 ET firings only — 11:30 and 3:30 both skipped Phase A on the buying-power gate, so there is no midday/afternoon scan data today).
- **235 of 344 simulated** (in_bracket + shallow_outside + deep_outside populations; 109 classified `other_price_or_range` and skipped per the D3 scope rule). 4 additional in-scope symbols (FEDU, BONK, WXM, SFWL) had zero non-interpolated bars all session and are excluded from the simulated denominator.
- **Overall decay/trailing win rate: 44.3%** (104/235).
- **In-bracket (the live buy population) win rate: 52.6%** (20/38).
- Both buy firings placed trades today: 9:30 → 2 trades, 10:30 → 6 trades (8 total). No zero-buy diagnostic needed.

## Win rate breakdowns (simulated population only)

| Cut | n | Win rate |
|---|---|---|
| Overall | 235 | 44.3% |
| decline_bucket = in_bracket | 38 | 52.6% |
| decline_bucket = shallow_outside | 196 | 42.9% |
| decline_bucket = deep_outside | 1 | 0.0% |
| price_bucket < 1 | 68 | 54.4% |
| price_bucket 1–3 | 87 | 32.2% |
| price_bucket 3–10 | 80 | 48.8% |
| source_list = under120 | 235 | 44.3% |

price_bucket 10-120 and 120-500 are not reported here — nothing in those buckets survives the D3 scope filter (in-bracket/shallow/deep-outside are all defined on price < 10), so those rows are simulated=false by design, not silently dropped.

time_bucket: 100% of today's simulated rows are "morning" — there is no afternoon comparison today since 11:30 and 3:30 never got a scan.

## In-bracket win rate by firing (the direct buy-window evidence)

| Firing | n | Win rate |
|---|---|---|
| 9:30 | 26 | 61.5% |
| 10:30 | 12 | 33.3% |

Small samples (n=26, n=12) — one bad afternoon-adjacent stretch at 10:30 easily swings this. Not enough data yet to conclude the second window underperforms structurally; keep watching this split across more sessions before revisiting the two-window buy schedule.

## Cross-reference with real trades/skips

All 8 real buys today matched a simulated row: ACET, QTEX, PTN, MTEK closed via the decay/trailing rule (matches their real GTC-limit fills); OCUL, GLXG, OPTT, GTEC did not reach their simulated target and were still open/being managed (GTEC and GLXG were in fact closed today by the account's automated liquidation logic — see below). 58 skip rows (25 at 9:30, 33 at 10:30) were fed into the D10 paper ledger below.

## Guardrail saturation check (D6b)

Not triggered — both buy firings placed at least one trade today, so there's no zero-buy session to audit for a single-guardrail-eats-everything failure mode.

## Exit-rule ledger (D6a)

No step-16a3 (time-stop/drawdown-stop) liquidations fired on the cloud side today — all four position_mgmt files logged `target_unchanged` for every position. Note: GTEC and GLXG were still closed out today (GTEC via what looks like the a2 60-minute-never-touched-breakeven rule, ~66 min after fill, limit-at-bid exit at 0.9303 vs 0.9703 cost; GLXG's exit and the DAVA/XNDU/NUR time-stop-looking exits at 15:34pm ET all happened via the **local** every-10-minute Phase C loop between this routine's scheduled firings, not this cloud session — consistent with the routine's documented division of labor.

## EOD liquidation suggestions (D7) — advisory only, no orders placed

| Symbol | Qty | Avg buy | Current | Pct chg | Low since buy | Resting target | Flag |
|---|---|---|---|---|---|---|---|
| OPTT | 2 | 1.7585 | 1.675 | -4.75% | 1.670 | 1.80 | Not flagged (pct_change threshold is -5%, this is -4.75%) |

Only one symbol bought today (OPTT) is still open; the other seven were already closed out (six via profit target, GTEC via the 60-min liquidation rule).

## Cohort tracking (D8)

- 239 new decliner-cohort rows appended for today (5–30% decline, <$10 band).
- 191 of those got a fresh sector/industry fundamentals lookup (new-to-the-log symbols or symbols whose only prior rows predate 2026-08-22 sector tagging); 47 copied sector/industry from an existing log entry; BONK returned not_found (likely delisted) and was logged with blank sector/industry.
- Recovery tracking: refreshed the 150 most-recently-added distinct symbols (2,285 distinct symbols total in the cohort log; capped per spec). 145 quoted successfully and appended; 5 (SYRUP, ONDO, UNI, NHIC, WLFI) returned no quote data and were skipped as likely delisted.

## Guardrail paper-trade ledger (D10)

**Day 0:** opened 45 new paper positions from today's 58 skip rows (46 distinct symbols after within-day dedup, minus VWAV which already had an open ledger row from 2026-09-25 and was correctly *not* re-opened). 31 of the 45 closed same-day (mostly `paper_target`; four hit the 60-minute auto-liquidate; one hit the 25% drawdown stop).

**Carry-forward:** of the 37 pre-existing open paper positions, 36 had today's session data (YYGH returned not_found on both fundamentals and quotes — likely delisted/renamed — and was left unchanged pending investigation). 17 of those 36 closed today (mostly `paper_time_stop_4_sessions` on the 2026-09-21 cohort reaching session 4, plus several `paper_target` hits and one `paper_drawdown_stop_25pct` on ENLV and FRGT).

**Ledger now:** 678 total rows, 34 open, 644 closed.

### Per-guardrail results (closed rows only, grouped by canonical guardrail — the ledger's skip_reason spelling has drifted over months, so exact-string grouping undercounts; this groups by the `aN` prefix)

| Guardrail | n | Win rate | Mean ROI | Median ROI | Summed ROI |
|---|---|---|---|---|---|
| a1 (spread) | 127 | 82.7% | +32.93% | +5.04% | +4181.95% |
| a2 (ATR) | 56 | 60.7% | +265.26%* | +4.16% | +14854.41%* |
| a3 (prior-spike) | 149 | 80.5% | +66.39%* | +5.00% | +9892.36%* |
| a4 (earnings-recency) | 24 | 54.2% | +0.59% | +4.46% | +14.19% |
| a5 (compliance) | 98 | 77.6% | +50.94% | +5.00% | +4991.89% |
| **a6 (reverse-split proxy)** | **108** | **71.3%** | **+2.07%** | **+5.00%** | **+223.51%** |
| a7 (thin-liquidity/stale-quote) | 34 | 64.7% | -1.15% | +4.09% | -39.23% |
| a8 (leveraged/inverse ETF) | 47 | 76.6% | +7.18% | +5.00% | +337.59% |

*\*a2 and a3's means/sums are badly skewed by pre-existing data-scale anomalies in the ledger, not real returns.* One example found today: CTNT's 2026-09-23 entry logged `entry_price=0.0398`, but CTNT's actual daily-bar trading range that day (and every day since) is in the $4-$6 range — a ~100x inconsistency that predates this firing. Today's carry-forward exit against that entry_price mechanically produced a +10,779% "ROI" that is pure artifact, not signal. Median ROI is the trustworthy summary stat for every guardrail in this table; treat any guardrail's mean/sum as suspect until the ledger's older entry_price values get a data-quality pass. This is a data-integrity issue for a future update, not something fixed in this run (D10 only permits appending new rows and updating still-open rows).

**Basis: a6 specifically (the guardrail under active review since 2026-08-25).** n=108 closed, 71.3% win rate, median +5.00%, summed +223.51%. This is now a large-enough sample to say a6's blocked population is *not* an obviously-profitable pool the guardrail is wrongly excluding — its win rate trails a1/a3/a5/a8's, and its per-trade edge (median +5%, same as everything that closes on a target hit) doesn't distinguish it. Nothing here argues for loosening the 15x threshold; keep it as is per the existing hold.

**Caveats (required every time, per the routine's own D10 spec):**
1. Guardrails short-circuit in a1→a8 order — skip_reason is only each candidate's *first* failure. The `fundamentals_guardrails_failed` column (recording every one of a5/a6/a8 a candidate would have tripped) is available for cross-checking, but a7 is never recomputed here (needs an extra historicals call), so "would have won if aX were removed" is never a clean claim for anything that could also have failed a7.
2. Paper entries fill at the observed price with no spread paid and no slippage; real buys are market orders. Every paper result here is optimistic relative to a real fill, most of all for the thin, wide-spread, low-priced names these guardrails specifically target.
3. Target exits assume a limit fills whenever a bar's high touches it — matching both the real resting GTC order and D3's own methodology, but still an assumption.

**Baseline — actual realized trades today** (the number a guardrail's blocked population has to beat to be worth loosening): 7 of today's 8 buys have already closed (OPTT is still open, excluded). 6 wins / 1 loss = **85.7% realized win rate**, mean **≈+3.58%** per trade — ACET +5.14%, QTEX +5.06%, GLXG +4.78% (GTC profit-target fill), MTEK +5.02%, PTN +4.10%, OCUL +5.08% (all wins), and GTEC -4.12% (the one loss, closed via the a2 60-minute-never-touched-breakeven rule). No guardrail's blocked-population median (all clustering at +4-5%, same as a target hit) clearly beats this today — the real book's realized picture is comparable to, not worse than, what any of the guardrails are excluding.
