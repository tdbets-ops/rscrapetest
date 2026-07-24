# Daily Analytics — 2026-07-24

Mock-purchase simulation (first-seen scan price → decay/trailing sell algorithm) across all 756 unique candidates seen on today's under-$120 and $120-$500 scans (4 firings: 9:30/10:30/11:30/3:30 ET).

**Headline: 217/756 closed by market close — 28.7% win rate.**

## Time bucket (KEY number for the 10:30/11:30 expansion decision)

| Bucket | Win rate | n |
|---|---|---|
| Morning (9:30/10:30/11:30) | **32.6%** | 181/556 |
| Afternoon (3:30 only) | **18.0%** | 36/200 |

Morning candidates close out at roughly 1.8x the rate of the afternoon firing today, consistent with the account owner's prior belief and the 2026-07-21/22 history (46.7%/37.1% morning vs 39.1%/16.3% afternoon). Small-sample caveat applies to any single day, but the direction is consistent across four days now.

## In-experiment bracket (<$10, 10–25% decline) — the exact population Phase B trades

| Bucket | Win rate | n |
|---|---|---|
| Morning | 52.6% | 20/38 |
| Afternoon | 42.9% | 3/7 |
| Combined | 51.1% | 23/45 |

n=7 for bracket-afternoon is too small to draw a conclusion from alone — direction matches the broader morning/afternoon gap but treat as noise until more days accumulate.

## Price bucket

| Bucket | Win rate | n |
|---|---|---|
| <$1 | 49.6% | 67/135 |
| $1-3 | 32.9% | 50/152 |
| $3-10 | 26.9% | 50/186 |
| $10-120 | 19.6% | 48/245 |
| $120-500 | 5.3% | 2/38 |

Win rate falls off sharply as price rises — cheaper, more volatile names are far more likely to hit a quick decay/trailing target. The $120-500 data-collection-only universe essentially never closes (2/38) — its inclusion is informational only, Phase B never buys from it.

## Source list

| List | Win rate | n |
|---|---|---|
| under120 | 29.9% | 215/718 |
| 120to500 | 5.3% | 2/38 |

## Today's real trades vs simulation (small sample, guardrail cross-reference)

Today's 9:30am firing bought under the **old, wider 5–25%-decline bracket** (narrowed to 10–25% only *this evening*, after the firing), so these 41 trades aren't a clean read on the new bracket — but the guardrail cross-reference is still informative:

- **41 trades placed → 6 closed by day end (14.6%)**, well below both the overall 28.7% and bracket 51.1% simulated rates.
- **52 bracket-eligible-but-guardrail-skipped candidates → 29 would have closed (55.8%) had they been bought.** The gap is striking (n=41 vs n=52, both small) — worth watching over more days before concluding the guardrails are filtering out winners, since skipped ≠ random sample (they specifically failed a1-a6 checks for other reasons).
- Closed trades averaged a lower ATR-14 (15.5%, n=6) than trades that stayed open (23.0%, n=35) — consistent with the ATR guardrail's premise that calmer names are more likely to hit target, though n=6 is too small to lean on.

## Suggested end-of-day liquidations (advisory, not executed)

13 of today's 36 still-open buys are down ≥5% since fill, sitting within 2% of today's low, and still trending down through the second half of the session — the account owner may want to review these manually:

| Symbol | Since buy | Rationale |
|---|---|---|
| AEHG | -17.0% | near low, trending down all day |
| CRWU | -13.7% | near low, trending down all day |
| IREG | -10.2% | near low, trending down all day |
| COHH | -10.2% | near low, trending down all day |
| GLXU | -10.1% | near low, trending down all day |
| POEL | -10.0% | near low, trending down all day |
| MPG | -8.7% | near low, trending down all day |
| TOPP | -8.4% | near low, trending down all day |
| PLU | -8.0% | near low, trending down all day |
| SKHL | -7.8% | near low, trending down all day |
| KSCP | -7.0% | near low, trending down all day |
| AMPG | -6.2% | near low, trending down all day |
| KEEL | -5.9% | near low, trending down all day |

23 other open positions are excluded — either down less than 5%, have bounced off today's low, or aren't trending down through the day (per account owner guidance, a flat small-decline position like ARI is fine to leave on its normal GTC order). **These are advisory only — no orders were placed or cancelled.** Full detail in `eod_liquidation_suggestions_2026-07-24.csv`.

## Cohort tracking (pure logging, no action)

468 candidates in today's 5–30%-decline/<$10 tracking band logged to `decliner_cohort_log.csv` (first day this log exists). Quoted 150 of these (capped from 468 distinct — the 150 biggest first-seen decliners were prioritized this cycle; the remaining 318 will be picked up as the cap rotates on subsequent days) into `decliner_recovery_tracking.csv`, day 0 of what will become a multi-day recovery/decline panel.
