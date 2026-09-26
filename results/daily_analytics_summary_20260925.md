# Daily analytics — 2026-09-25

> **Backfill note:** this Phase D run was completed on 2026-09-26. The original
> 4:30pm ET firing for 2026-09-25 was lost mid-run to two consecutive container
> restarts before anything could be committed. All simulation inputs (scan
> snapshots, trades, skipped-candidates) are the real files from that trading
> day; only the analytics computation itself ran a day late.

## Headline

- **378** unique candidates seen today across the under-$120 and $120–$500
  scans (10:30, 11:30, 3:30 ET firings — **there was no 9:30am firing today**).
- **262 / 271** in-scope candidates actually simulated (9 had no non-interpolated
  bars in their window and are excluded; scope note below).
- **Overall win rate (simulated): 45.4%** (119/262).

## By time bucket

| bucket | wins | n | win rate |
|---|---|---|---|
| morning (9:30/10:30/11:30) | 95 | 186 | 51.1% |
| afternoon (3:30) | 24 | 76 | 31.6% |

## By price bucket

(only `<1` / `1-3` / `3-10` are populated — see scope note)

| bucket | wins | n | win rate |
|---|---|---|---|
| <1 | 44 | 82 | 53.7% |
| 1-3 | 40 | 92 | 43.5% |
| 3-10 | 35 | 88 | 39.8% |

## By source list

| list | wins | n | win rate |
|---|---|---|---|
| under120 | 119 | 262 | 45.4% |
| 120to500 | 0 | 0 | n/a (none of the 5 candidates that day were in-scope for simulation) |

## By decline bucket (bracket calibration)

| bucket | wins | n | win rate |
|---|---|---|---|
| in_bracket (10-25% decline, <$10) | 24 | 45 | 53.3% |
| shallow_outside (5-<10% decline, <$10) | 94 | 215 | 43.7% |
| deep_outside (>25-30% decline, <$10) | 1 | 2 | 50.0% (n=2, ignore) |

The live buy bracket (`in_bracket`) outperforms the shallow-decline population
by about 10 points today, consistent with the bracket being a real (if noisy)
signal rather than arbitrary.

## In-bracket win rate by firing

This is the direct evidence on whether the 10:30 window was worth adding and
whether 11:30 should start buying too.

| firing | wins | n | win rate |
|---|---|---|---|
| 10:30 (live buy window) | 20 | 36 | 55.6% |
| 11:30 (no buy window) | 0 | 0 | n/a — no in-bracket candidates were first-seen at 11:30 today |
| 3:30 (no buy window) | 4 | 9 | 44.4% |

Note: there was no 9:30am firing today, so the "second window" comparison this
routine is normally tracking (9:30 vs 10:30) isn't available for 2026-09-25.
10:30's 55.6% in-bracket win rate is a healthy single-day read; 11:30 produced
zero in-bracket candidates today so it offers no evidence either way. Sample
sizes are small (n=36, n=9) — treat as one data point, not a verdict.

## Scope note

Per the standing D3 scope rule, only `decline_bucket` in
(in_bracket, shallow_outside, deep_outside) is simulated — `other_price_or_range`
candidates (price ≥ $10, or price < $10 outside the 5-30% decline tracking
range) get `simulated=false` with null scenario columns. This cut the
population from 378 to 271 in-scope, then 9 of those 271 had zero
non-interpolated bars in their first-seen→4pm window (thin/no-trade names) and
are excluded from the win-rate stats above, leaving 262 simulated rows. All
120to500 candidates today (5 total) were priced ≥$10 and therefore
`other_price_or_range` — none were in scope, hence the 0/0 "n/a" row above.

## Exit-rule firings (D6a)

No positions were liquidated by the step 16a3 time-stop/drawdown-stop rule on
2026-09-25 (checked `latest_position_mgmt.csv` and all three of that day's
`position_mgmt_*.csv` files — no `time_stop` or `drawdown_stop` reason strings
present).

## Guardrail saturation check (D6b)

The 10:30 firing was the only buy window that day. 37 candidates entered the
bracket; **2 were bought** (QCLS, NTRB) and 35 were skipped by a guardrail.
Per-guardrail skip counts among the 35:

| guardrail | skips |
|---|---|
| a6_reverse_split_proxy | 12 |
| a5_compliance_guardrail | 11 |
| a1_spread_guardrail | 4 |
| a8_leveraged_inverse_etf | 2 |
| a7_thin_liquidity_stale_quote | 2 |
| a4_earnings_recency | 2 |
| a3_prior_spike_guardrail | 1 |
| a2_atr_guardrail | 1 |

**No malfunction flag:** no single guardrail rejected anywhere close to 100%
of the candidates that reached it, and the firing was not a zero-buy session
(2 buys placed) — the mandatory zero-buy diagnostic in the routine's Phase B
instructions does not apply today.

Both of today's actual buys (QCLS, NTRB) remained open per the decay/trailing
simulation as of the 4:00pm ET cutoff (never reached target); this is a
same-day snapshot only, not a verdict on the eventual trade outcome. In the
REAL account, NTRB was closed intraday (15:53 UTC) by the step 16a2 60-minute
auto-liquidate rule at $7.7501 (bought at $8.1199, -4.5%) — its peak never
came within 0.1% of breakeven inside the 60-minute window. That is a step
16a2 auto-liquidation, not a step 16a3 time-stop/drawdown-stop, so it is not
counted in the "Exit-rule firings" section above.

## Manual EOD liquidation suggestions (D7, advisory only — retrospective)

Of today's two buys, only **QCLS** was still open at 2026-09-25 4:00pm ET
(NTRB had already been auto-liquidated, above). Evaluated against the
standard three-condition rule using that day's bars through 20:00 UTC:

| symbol | pct_change_since_buy | near_low | trend_down | flagged |
|---|---|---|---|---|
| QCLS | -2.25% | true (0.8678 vs low×1.02=0.8697) | true | **no** — pct_change_since_buy did not clear the -5% threshold |

`results/eod_liquidation_suggestions_20260925.csv` is header-only (nothing
flagged). **This D7 read is purely retrospective** — it reconstructs what the
advisory would have said at 2026-09-25 4pm ET using that day's data; it is
not actionable today (2026-09-26, a non-trading Saturday) and no order was
or would be placed regardless, per this section's advisory-only design.
