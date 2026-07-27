# Daily Analytics — 2026-07-27

**526 unique candidates** across today's 4 firings (9:30/10:30/11:30/3:30 ET), simulating the live decay/trailing sell algorithm from each symbol's first-seen price/time through market close. 61 symbols had insufficient historicals data (mostly zero-volume ETFs/leveraged products and the narrow 24-minute window for 3:30pm-firing candidates) and are excluded from win-rate denominators below.

## Headline win rate

**60.0% overall** (279/465 closed by market close, n=465 with usable data)

## By time bucket (KEY number for the 10:30/11:30 expansion decision)

| Bucket | Win rate | n |
|---|---|---|
| Morning (9:30/10:30/11:30 firings) | **64.6%** | 381 |
| Afternoon (3:30 firing) | **39.3%** | 84 |

Morning candidates continue to close out at a meaningfully higher rate than afternoon ones, consistent with the pattern seen on 07-21/07-22/07-24. The afternoon number is depressed in part by the 3:30 firing's very short runway to close (as little as ~24 minutes for late-appearing symbols) — 22 of the 106 afternoon candidates had insufficient data for exactly that reason, vs 39 of 381 in the morning.

## Within the live buy bracket (<$10, 10–25% decline) — the exact population Phase B trades

| Bucket | Win rate | n |
|---|---|---|
| Bracket, morning | **70.8%** | 48 |
| Bracket, afternoon | 50.0% | 4 |

Bracket-morning (70.8%, n=48) continues to outperform both the morning average (64.6%) and the overall average (60.0%), supporting the current bracket calibration. The bracket-afternoon sample (n=4) is too small to draw any conclusion from.

## By price bucket

| Bucket | Win rate | n |
|---|---|---|
| <$1 | 65.1% | 109 |
| $1–3 | 54.2% | 96 |
| $3–10 | 55.3% | 103 |
| $10–120 | 57.3% | 117 |
| $120–500 | 80.0% | 40 |

## By source list

| List | Win rate | n |
|---|---|---|
| under120 | 58.1% | 425 |
| 120to500 (data collection only) | 80.0% | 40 |

The $120–500 list continues to show a notably higher simulated win rate than under-$120, but it's a data-collection-only universe (n=40) — not something Phase B trades from.

## Real-trade cross-reference

All 6 of today's actual Phase B buys (9:30am firing: RGTZ, SKYQ, BCAR, REPL, TPET, ASTN) are in this dataset. The backtest simulation's closed/open call **matches the real outcome for all 6**: RGTZ, REPL, and ASTN both actually filled their real resting sell today *and* the simulation independently marked them closed; SKYQ, BCAR, and TPET remain open positions today and the simulation likewise called them still-open. This is a useful sanity check that the backtest methodology is faithfully replicating the live decay/trailing algorithm.

## Skipped-candidate guardrail correlation (small samples — caveat below)

| Skip reason | Simulated win rate | n |
|---|---|---|
| a1_spread (wide bid/ask) | 100% | 5 |
| a3_prior_spike | 100% | 1 |
| a5_compliance (NASDAQ deficiency flag) | 57% | 7 |
| a6_split_proxy (reverse-split fingerprint) | 82% | 11 |
| broker_halt_alert | 100% | 1 |

All groups here are n≤11 — **not statistically meaningful on their own**. Notably, a5_compliance-skipped candidates show a lower simulated win rate (57%, n=7) than the other guardrail categories, which is directionally consistent with the guardrail's intent (compliance-flagged stocks being a riskier/less-predictable population) but the sample is far too small to confirm.

## Suggested end-of-day liquidations (advisory, not executed)

None today. All 3 of today's still-open buys (SKYQ, BCAR, TPET) were checked: BCAR is down 13.8% since buy but has bounced well off its intraday low (current price $7.835 vs low-since-buy $7.545, i.e. not near_low) so it does not meet all three SUGGEST_LIQUIDATE conditions. SKYQ (-1.8%) and TPET (-0.7%) are only marginally below cost. No manual liquidation action suggested — these remain fine to sit as normal GTC resting orders per the account owner's guidance.

## Cohort tracking

328 new under-$120/5–30%-decline candidates logged today (673 total distinct symbols tracked across both days). This cycle's recovery-tracking quote batch covered the 150 most-recently-added symbols (all from today, since 673 total exceeds the ~150 cap); 2 malformed scan-data artifacts (blank symbol, "NA") were skipped and noted. Not yet enough days of data to draw recovery-vs-continued-decline conclusions — this remains pure accumulation for now.
