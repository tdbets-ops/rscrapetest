# Daily Analytics — 2026-09-21

## Headline
- 401 unique candidates seen today across the under-$120 and $120-$500 scans (4 firings: 9:30/10:30/11:30/3:30 ET).
- 267 in scope for simulation (decline_bucket in_bracket/shallow_outside/deep_outside per the 2026-07-29 scope fix); 259 actually simulated (8 had no usable real-trade bars in their post-entry window — thin/no-real-trade names).
- Overall simulated win rate: **47.9%** (124/259).

## By time bucket
| bucket | wins | n | win rate |
|---|---|---|---|
| morning (9:30/10:30/11:30) | 103 | 193 | 53.4% |
| afternoon (3:30) | 21 | 66 | 31.8% |

## By decline bucket (bracket calibration)
| bucket | wins | n | win rate |
|---|---|---|---|
| in_bracket (10-25% decline, <$10 — the live buy bracket) | 26 | 36 | 72.2% |
| shallow_outside (5-10% decline, <$10) | 96 | 220 | 43.6% |
| deep_outside (25-30% decline, <$10) | 2 | 3 | 66.7% |

## By price bucket (sim population only)
| bucket | wins | n | win rate |
|---|---|---|---|
| <1 | 47 | 80 | 58.8% |
| 1-3 | 37 | 90 | 41.1% |
| 3-10 | 40 | 89 | 44.9% |
| 10-120 / 120-500 | 0 | 0 | n/a — out of D3 scope by design (price>=10 or the $120-500 list is always decline_bucket=other_price_or_range and never simulated) |

## In-bracket win rate by firing
The direct evidence for whether the 10:30 buy window is pulling its weight and whether 11:30 should also start buying:

| firing | wins | n | win rate |
|---|---|---|---|
| 9:30 (live buy window) | 13 | 16 | 81.2% |
| 10:30 (live buy window) | 8 | 12 | 66.7% |
| 11:30 (no buy) | 2 | 2 | 100.0% (n=2, not meaningful) |
| 3:30 (no buy) | 3 | 6 | 50.0% |

Both live buy windows clear the in-bracket population's overall 72.2% comfortably on the 9:30 side and are in line with it on the 10:30 side; 11:30's sample is too small (n=2) to read anything into extending buying there. 3:30 (never a buy window) is the weakest slice at 50%, consistent with the original morning-only rationale.

## Scope note
Per the 2026-07-29 scope fix, only decline_bucket in (in_bracket, shallow_outside, deep_outside) is simulated. 134 of 401 candidates today were classified other_price_or_range (either priced >=$10 in the under-$120 scan and outside the 5-30% decline band, or from the $120-500 data-collection-only scan) and were skipped entirely — no historicals fetched, simulated=false.

simulated_count=259, total_candidates_seen=401.

## Today's real trades cross-reference
- **9:30 — LVWR** bought at $1.4699 (bracket -10.42%). The simulated decay/trailing algo would have closed this at $1.5201 (+3.4%) by 18:55 UTC. In reality it never touched breakeven within 60 minutes and was auto-liquidated (step 16a2) at $1.3801 — a real loss, illustrating that the simulation (which assumes the position is held through any early dip) doesn't capture the a2 60-minute guardrail's effect on individual real trades even though D3a evaluates that same rule as its own scenario. LVWR's D3a liquidation_triggered/roi is in daily_analytics_20260921.csv.
- **10:30 — FLX** bought at $0.6459 (bracket -20.41%). The simulated algo's target would have closed it at $0.848 (+31.7%). In reality it hit its live resting limit target and filled at $0.6182 for a real win.

## Exit-rule firings (step 16a3 time-stop / drawdown-stop)
None fired today. All four Phase C position-management cycles today (9:30/10:30/11:30/3:30) recorded only `no_change_needed` for the four carried positions (BAK, MRLN, TNON, NUWE) — none crossed the -25% drawdown stop or reached 4 sessions held. The only liquidation event today was LVWR's step 16a2 auto-liquidate (never-touched-breakeven-60min) at the 10:30 firing, which is a distinct rule from 16a3 and not part of this ledger.

## Guardrail saturation check (D6b)
Both buy firings today placed at least one real trade (9:30: LVWR; 10:30: FLX), so the zero-buy diagnostic does not apply. For reference, per-guardrail skip counts among today's bracket-eligible candidates:
- 9:30 (16 candidates reached step 12): a1=2, a2=1, a3=2, a5=2, a6=4, a7=2, a8=3 — no single guardrail exceeded 25% of skips.
- 10:30 (35 candidates reached step 12): a1=7, a2=3, a3=10, a4=1, a5=3, a6=5, a7=2, a8=4 — no single guardrail exceeded 29% of skips.
No saturation pattern observed.

## Manual EOD liquidation suggestions (D7, advisory only — never executed automatically)
Both of today's real buys (LVWR, FLX) had already closed by the time of this 4:30pm ET firing (LVWR via auto-liquidate, FLX via its real target), so there were no still-open same-day positions to evaluate. `results/eod_liquidation_suggestions_20260921.csv` is header-only today.

## Decliner-bracket cohort tracking (D8)
- 267 candidates in the 5-30%-decline/<$10 tracking band were newly logged today (12 of them logged for the very first time ever — fresh sector/industry fundamentals pulled: TULP, EPM, SPCQ, DRK, HIND, MITQ, MAMO, TXMD, MOBI, USGO, LONA, MDXH).
- The full cohort log now spans 2,215 distinct symbols. Recovery tracking quoted the 150 most-recently-added symbols this cycle (uncapped total 2,215) and appended to `decliner_recovery_tracking.csv`; 3 symbols (UNI, WLFI, DOT) could not be quoted (likely delisted).

## Guardrail paper-trade ledger (D10)
*Pending — the paper-trade ledger update (opening today's new paper positions from both skipped_candidates files and carrying forward all pre-existing open positions) is still running as a background subagent at the time of this commit. Per the 2026-07-28 lesson about not promising a follow-up that never lands, this partial report is being committed now; the ledger update and its per-guardrail win-rate table will follow in a separate commit later in this same firing once the subagent completes.*
