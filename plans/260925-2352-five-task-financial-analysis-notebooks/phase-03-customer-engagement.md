---
phase: 3
title: "Task 3 — customer engagement analysis"
status: pending
priority: P1
effort: "0.5d"
dependencies: [1]
---

# Phase 3: Task 3 — customer engagement analysis

## Overview
Deliver a standalone executed English notebook addressing all six Task 3 objectives and six deliverables on PDF pp. 11–12, with suitable chart types, all five channels, exact online-spend-share definition and a defensible healthy-but-less-engaged crossover.

## Current Behavior
- `docs/BI10_ROUND01.pdf` pp. 11–12 requires engagement distribution and band shares, POS/QR/E-commerce/Mobile App/Recurring adoption, average online spend share, category diversity, frequency, recency, and a crossover with chosen/compared cutoffs.
- `data/consumer_transactions_2025.csv:1-7` exposes the five channel labels and `online_transaction_flag`; `data/consumer_financial_health_engagement_2025.csv:1-7` exposes engagement, recency, transaction count, category diversity, online spend and ratios. `data/data_dictionary.xlsx` distinguishes the transaction online flag from channel.
- Prior direct scan counted 367,682 QR transactions with `online_transaction_flag=0`; 99.0% of consumer-month rows were in high/very-high engagement bands, 94.93% had recency 0, and there were 0 December rows with `health>=80`. Recompute the counts and do not treat `digital` and `online` as synonyms.
Unknowns: none affecting the ability to compute Task 3; the business meaning of weak engagement is inferential rather than observed attrition.

## Target Change
- Create: `code/03_customer_engagement_analysis.ipynb`; export compact charts under `image/task3/` (e.g., score distribution, channel breakdown, online spend share, diversity relationship, frequency/recency relationship, crossover sensitivity).

## Requirements Covered
R-01, R-02, R-03, R-06, R-09.

## Implementation Steps
1. **R-01, R-03, R-06** — Independently read the monthly table and stream only relevant columns from the large transaction CSV. Document denominator, grain and time window for each measure; count a channel adopter by distinct consumer, channel usage by transactions and spend share by VND separately.
2. **R-06** — Show engagement score histogram and original segment distribution with exact count/% of customer-months and customers; show any ceiling effect without claiming high engagement equals wellbeing or retention.
3. **R-06** — Show POS, QR Payment, E-commerce, Mobile App, Recurring Payment individually by transaction count/share and VND share; compute average **customer-month `online_spend_ratio`** and aggregate `online_spend_vnd/total_spend_vnd` as distinct quantities. Explain QR offline-flag convention and compare to non-POS digital adoption; chart channel breakdown rather than one aggregate digital bar.
4. **R-06** — Plot category-diversity range versus score, transaction-frequency range versus score, and recency distribution versus score. Handle recency zero mass and diversity ceiling with fitting visual encodings (binned scatter/boxplot/bar, clear n), not spurious linear correlations asserted as independent drivers.
5. **R-06** — Cross-tab healthy `health>=80` with low engagement at `<40`, `<60` and `<70` as sensitivity; choose and justify a primary cutoff against original engagement bands and business meaning. Report exact customer-month count, distinct customers with at least one matching month in 2025, and December-observed matches as a separate snapshot, plus profiles with `n_observed_months`; zero December healthy cases must appear as zero, not trigger an unannounced health cutoff change. Do not call historical cases currently disengaged or low engagement confirmed churn.
6. **R-01, R-02, R-09** — Put English Markdown interpretation after each figure (at least as carefully as Tasks 1–2), naming measured denominator, window, cutoff and caution. Save PNGs in `image/task3/` before closing figures; end with a concise executive takeaways section.

## Verification
- `.venv/bin/python -m nbconvert --to notebook --execute --inplace --ExecutePreprocessor.kernel_name=bi-uel --ExecutePreprocessor.timeout=900 code/03_customer_engagement_analysis.ipynb` → exit 0; all six objectives and six p. 12 deliverables have a chart/table and explanatory text.
- Compare chart source table against direct transaction aggregation: exactly five named channels, QR present, total channel count equals 1,852,394; report online ratio denominator and non-POS digital share separately.
- `image/task3/` holds nonempty task-specific PNGs; inspect health>=80 and low-engagement sensitivity table for historical distinct customers, zero December and sparse-history share.

## Completion Evidence

- Executed with the documented `bi-uel` nbconvert command; no notebook exception cells, and the previous `pd.cut` warning was removed by numeric frequency-bin codes without suppressing warnings.
- All seven `image/task3/` PNGs were regenerated with source/year/grain/denominator captions inside each image. Five-channel and QR-versus-online definitions remain distinct.

## Load-Bearing Assumptions & Risks
- Channel categories in dictionary remain exhaustive: signal: a new/unknown channel value in CSV → expose it and update grouping instead of silently excluding it.
- An engagement cutoff from the source segments might produce a tiny crossover: signal: `health>=80 & engagement<60` has very few customers → report the honest small group and sensitivity; do not relabel 60–69 as officially low merely to inflate reach.
- Full transaction scan is expensive locally: signal: observed OOM/time issue despite `usecols` and bounded chunks → reduce unnecessary passes/reuse small aggregates or route actual execution to Kaggle, with evidence recorded.

## Checklist
- [x] Calculate score/channel/diversity/frequency/recency views and crossover sensitivity.
- [x] Export and interpret Task 3 figures in English.
- [x] Execute notebook and verify five-channel totals plus explicit online/digital distinction.
