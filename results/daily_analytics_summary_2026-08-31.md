# Daily analytics — 2026-08-31 (PARTIAL — D10 still running, follow-up commit pending)

## Headline

- **413** unique candidates seen today across all 4 firings (9:30/10:30/11:30/3:30 ET); **285** in scope for simulation (in_bracket/shallow_outside/deep_outside), 9 had zero real bars (unsimulated).
- Overall simulated win rate (decay/trailing target hit by close): **53.3%** (152/285).
- **In-experiment bracket (live buy bracket, price<$10, 10-25% decline): 74.1% win rate (20/27 simulated)** — the core bracket-calibration number.

## Win rate by time bucket

| Bucket | n | wins | win rate |
|---|---|---|---|
| morning (9:30/10:30/11:30) | 218 | 122 | 56.0% |
| afternoon (3:30) | 67 | 30 | 44.8% |

## Win rate by price bucket (simulated only — 10-120/120-500 have 0 simulated rows, as expected since the bracket requires price<$10)

| Bucket | n | wins | win rate |
|---|---|---|---|
| <1 | 92 | 52 | 56.5% |
| 1-3 | 94 | 52 | 55.3% |
| 3-10 | 99 | 48 | 48.5% |

## Win rate by decline_bucket

| Bucket | n | wins | win rate |
|---|---|---|---|
| in_bracket (10-25%, live buy bracket) | 27 | 20 | 74.1% |
| shallow_outside (5-10%) | 258 | 132 | 51.2% |
| deep_outside (25-30%) | 0 | 0 | — (none seen today) |

## In-bracket win rate by firing (the direct evidence for the 10:30 window and whether 11:30 should follow)

| Firing | n | wins | win rate |
|---|---|---|---|
| 9:30 | 19 | 15 | 78.9% |
| 10:30 | 4 | 3 | 75.0% |
| 11:30 | 0 | 0 | — (no in-bracket candidates first-seen at 11:30 today) |
| 3:30 | 4 | 2 | 50.0% |

Sample sizes are small (esp. 10:30 n=4, 3:30 n=4) — directional only. Both live buy windows (9:30, 10:30) continue to outperform the 3:30 slot in-bracket, consistent with the morning-outperforms-afternoon pattern that motivated the original narrowing. 11:30 had zero in-bracket candidates first-seen there today, so today's data is silent on whether 11:30 should be added as a third buy window.

## Scope note

Per the 2026-07-29 scope fix, only decline_bucket in (in_bracket, shallow_outside, deep_outside) — i.e. price<$10 with a 5-30% decline — is simulated (285 of 413 candidates). The other_price_or_range population (119 candidates, mostly the $120-$500 data-collection scan and out-of-range under-$120 names) is intentionally not simulated to keep compute bounded.

## Today's trades cross-reference

6 real buys today (1 at 9:30: NCNA; 5 at 10:30: VACI, MPU, GP, DSC, RYET). Of these, NCNA/MPU/DSC were classified in_bracket in today's D2 pass (their *first-seen* price/decline differs from their actual fill, since first-seen is earliest-across-firings, not fill time); VACI/RYET/GP were shallow_outside at first-seen (they deepened into the live bracket by the time they were actually bought at a later firing). All 6 were liquidated or hit target intraday except RYET (still open, see D7).

## Exit-rule ledger (D6a)

No step 16a3 (time-stop/drawdown-stop) liquidations fired today. All of today's forced closes (NCNA, MPU, GP, DSC) were step 16a2 60-minute never-touched-breakeven auto-liquidations, executed by the local 10-minute Phase C loop shortly after each position's 60-minute mark; VACI closed via a normal resting-limit target fill, not a liquidation.

## Guardrail saturation check (D6b)

Both buy firings placed trades today (9:30: 1 trade; 10:30: 5 trades) — the mandatory zero-buy diagnostic does not apply. For completeness: a3_prior_spike was the largest single skip reason at both firings (9/18 at 9:30, 8/18 at 10:30) but did not approach 100% saturation at either, so no guardrail malfunction is flagged.

## EOD liquidation suggestions (D7, advisory only — NOT executed)

Only RYET is open (bought 10:30 window, 5 sh @ $0.85). pct_change_since_buy is **+1.04%** (not a decline), so SUGGEST_LIQUIDATE is **not** flagged — none of the three required conditions (≤-5% decline, near 60-min low, downtrend) are met. See results/eod_liquidation_suggestions_2026-08-31.csv.

## Cohort tracking (D8)

294 new candidates appended to results/decliner_cohort_log.csv for today (0 already logged). 11 symbols were genuinely new to the log ever and got fresh sector/industry fundamentals lookups; the other 283 copied sector/industry forward from their earliest prior appearance (most of which predate sector/industry capture and are blank). 150 of 2,047 total distinct cohort symbols (most-recently-added, capped) were re-quoted for recovery tracking — all 150 succeeded with no delistings/errors.

## Guardrail paper-trade ledger (D10)

**Still running as a background agent at commit time — this section will be completed and this summary republished in a follow-up commit once it finishes.**
