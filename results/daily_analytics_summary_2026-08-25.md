# Daily Analytics — 2026-08-25

**Guardrail saturation check comes first: no single-guardrail lockout today.** Both buy firings (9:30am, 10:30am) placed zero buys, but unlike the a5 bug that silently zeroed three sessions (08-21, 08-24, 08-25 pre-fix), skips today were spread across multiple guardrails at both firings — see D6b below. This is the expected "guardrails working" pattern, not a malfunction.

## Headline

- **314** unique candidates seen across today's 4 scans (9:30/10:30/11:30/3:30 ET); **225** simulated (in_bracket/shallow_outside/deep_outside only, per the 2026-07-29 scope cut — see Scope note below).
- Overall simulated win rate: **59.1%** (n=225).
- 0 buys placed today at either buy window (9:30 or 10:30) — every bracket candidate at both firings failed at least one guardrail (22/22 at 9:30, 33/33 at 10:30).

## Win rate by decline bucket (bracket calibration)

| Bucket | Win rate | n |
|---|---|---|
| in_bracket (10-25% decline, <$10 — live Phase B bracket) | **73.0%** | 37 |
| shallow_outside (5-10% decline, <$10) | 56.4% | 188 |
| deep_outside (25-30% decline, <$10) | n/a — 0 candidates today | 0 |

in_bracket continues to outperform shallow_outside (73.0% vs 56.4%), consistent with prior days (77.1% vs 48.2% on 08-24).

## In-bracket win rate, morning vs afternoon

| Bucket | Win rate | n |
|---|---|---|
| Morning (9:30/10:30/11:30) | 71.4% | 35 |
| Afternoon (3:30) | 100.0% | 2 |

## In-bracket win rate, by firing (new 2026-08-25 breakdown)

Now that 9:30 and 10:30 are both live buy windows and 11:30 is not, this is the direct evidence for whether the second window is paying off and whether 11:30 should follow:

| Firing | Win rate | n |
|---|---|---|
| 9:30 | 77.3% | 22 |
| 10:30 | 70.0% | 10 |
| 11:30 | 33.3% | 3 |
| 3:30 | 100.0% | 2 |

**Caveat: n is small everywhere except 9:30.** 10:30's 70.0% (n=10) is close to 9:30's 77.3% (n=22) and supports keeping the second window, but one day is not enough to confirm it. 11:30's 33.3% (n=3) is too small to say anything about extending buying to that firing — it would need many more sessions of data first.

## Win rate by price bucket

| Bucket | Win rate | n |
|---|---|---|
| <$1 | 61.2% | 67 |
| $1-3 | 60.3% | 78 |
| $3-10 | 56.3% | 80 |
| $10-120 | n/a — not simulated (out of scope) | 0 |
| $120-500 | n/a — not simulated (out of scope) | 0 |

## Win rate by source list

| Bucket | Win rate | n |
|---|---|---|
| under120 | 59.1% | 225 |
| 120to500 | n/a — data-collection only, never simulated | 0 |

## ROI figures

- Average decay-close ROI (closed positions only): **+4.45%** (n=133)
- Average EOD-sell ROI (all simulated candidates): **+4.56%** (n=225)
- 60-minute liquidation-rule scenario: triggered for 6/225 simulated candidates, average ROI on those **-2.44%**.

## Scope note

Per-symbol simulation (D3/D3a/D3b) runs only on decline_bucket in (in_bracket, shallow_outside, deep_outside) — 225 of 314 candidates. The other 89 (84 other_price_or_range + 3 in-scope symbols with no non-interpolated bars — CRV, ZORA not found on the exchange feed, plus 1 additional unsimulable) are excluded, per the 2026-07-29 change made after the unscoped simulation failed to finish in one firing.

## Guardrail-metric correlations

Sample too small today to draw guardrail-specific correlations beyond what D10 (below) already isolates by skip_reason.

## Exit-rule firings

None — no step 16a3 (time-stop/drawdown-stop) or step 16a2 (auto-liquidate) firings today. The account held zero open positions all day (flat since before 9:30am, per every firing's commit message), so Phase C position management was a no-op at every firing and there was nothing to exit.

## Guardrail saturation check (D6b)

Both buy firings placed zero buys. Per-guardrail skip counts:

**9:30am firing — 22 bracket candidates, 22 skipped, 0 bought**

| Guardrail | Count | % of skipped |
|---|---|---|
| a5_compliance | 11 | 50.0% |
| a3_prior_spike | 7 | 31.8% |
| a1_spread | 4 | 18.2% |

No single guardrail hit 100% — max was a5_compliance at 50.0%.

**10:30am firing — 33 bracket candidates, 33 skipped, 0 bought**

| Guardrail | Count | % of skipped |
|---|---|---|
| a6_reverse_split_proxy | 7 | 21.2% |
| a5_compliance | 6 | 18.2% |
| a3_prior_spike | 6 | 18.2% |
| a1_spread | 5 | 15.2% |
| a2_atr | 3 | 9.1% |
| a8_leveraged_etf | 3 | 9.1% |
| a4_earnings_recency | 2 | 6.1% |
| a7_thin_liquidity | 1 | 3.0% |

No single guardrail hit 100% — max was a6_reverse_split_proxy at 21.2%, and skips are spread across all 7 active guardrails (a1-a2, a3-a5, a7-a8). **This is the a5-bug-fixed pattern working as intended** — every candidate that reached the bracket failed at least one real guardrail, but no single check is silently eating the whole pool the way the old a5 test did on 08-21/08-24/08-25 (pre-fix).

## Suggested end-of-day liquidations (advisory, not executed)

None — no buys were filled today, so there are no open same-day positions to evaluate. `eod_liquidation_suggestions_2026-08-25.csv` is header-only.

## Cohort tracking

- 230 candidates appended to `decliner_cohort_log.csv` today (5-30% decline, <$10 band). Distinct cohort pool now **1,971** symbols.
- Sector/industry captured for **17** genuinely-new symbols this run (2 of the 17 — ZORA, CRV — returned not_found on the fundamentals feed and were logged with blank sector/industry).
- 150 most-recently-added of 1,971 distinct cohort symbols targeted for `decliner_recovery_tracking.csv`; 148 quoted successfully (2 not_found: ZORA, CRV, same instruments as above — logged as skipped rather than blocking the rest of the step).

## Guardrail paper-trade ledger (D10)

First day this ledger runs. Opened **44** new paper positions today (22 from the 9:30 skip list + 33 from the 10:30 skip list, minus 11 symbols that appeared in both — deduped to the 9:30 entry per the "never open a second paper position for a symbol that already has an open row" rule). 29 closed same-day, 15 still open (carried forward on future runs).

| skip_reason | n closed | win rate | mean ROI | sum ROI | still open |
|---|---|---|---|---|---|
| a1_spread | 7 | 100.0% | +5.41% | +37.89% | 0 |
| a3_prior_spike | 9 | 88.9% | +3.42% | +30.77% | 1 |
| a5_compliance | 8 | 87.5% | +3.31% | +26.50% | 6 |
| a8_leveraged_etf | 1 | 100.0% | +5.07% | +5.07% | 2 |
| a6_reverse_split_proxy | 3 | 33.3% | **-8.57%** | -25.70% | 2 |
| a4_earnings_recency | 1 | 0.0% | -7.87% | -7.87% | 1 |
| a2_atr | 0 | — | — | — | 2 |
| a7_thin_liquidity | 0 | — | — | — | 1 |
| **All closed** | **29** | **82.8%** | **+2.30%** | — | — |

**a6 (reverse-split proxy), the guardrail under review, shows its worst same-day result of any skip_reason: 1 win / 2 losses (33.3%), -8.57% mean ROI.** This is a single day with n=3 closed (2 more still open) — nowhere near enough to conclude the 15x threshold is right or wrong, but it's the first real data point since a6 was flagged as unvalidated and became the largest single blocker at the 10:30 firing.

**Caveats (per the D10 spec, must not be dropped for brevity):**
1. Guardrails short-circuit in a1→a8 order, so skip_reason is only a candidate's *first* failure. These results measure each guardrail's marginal cost given a1..a(X-1) already passed. The `fundamentals_guardrails_failed` column that should cross-check this (step 12z) is **not populated today** — the 9:30 and 10:30 skipped_candidates files were written in the pre-2026-08-25 column format (symbol, last_price, pct_change, guardrail, detail) and don't yet carry entry_price/evaluated_at/firing/fundamentals_guardrails_failed as separate standardized columns. This run inferred entry_price from last_price and firing/evaluated_at from each file's timestamp, but fundamentals_guardrails_failed is left blank for all 44 rows. **This should self-correct once a Phase B firing runs the current prompt version and emits the standardized columns.** a7 is never recomputed regardless (by design, no extra call).
2. Paper entries fill at the observed price with no spread paid and no slippage; real buys are market orders. Every paper result is optimistic relative to a real fill, most of all for the wide-spread/thin/low-priced names these guardrails specifically target.
3. Target exits assume a limit fills whenever a bar's high touches it — matches how the real resting GTC limit behaves and how D3 simulates, but is still an assumption.

**Baseline comparison:** the account placed **zero real buys today**, so there is no same-day realized win rate/P&L to compare against. This comparison becomes meaningful once the ledger and the account both accumulate more days — flagging this as a gap to close as soon as real trades resume.
