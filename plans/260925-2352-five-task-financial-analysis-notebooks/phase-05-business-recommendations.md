---
phase: 5
title: "Task 5 — evidence-linked business recommendations"
status: pending
priority: P1
effort: "0.25d"
dependencies: [1, 2, 3, 4]
---

# Phase 5: Task 5 — evidence-linked business recommendations

## Overview
Create the fifth executed English notebook with exactly six non-punitive, data-backed proposals, one per tool type on PDF p. 17. Compute actual reach by unique consumer, transparently rank by reach and evidence strength, and record how success would be measured without fabricating impact.

## Current Behavior
- `docs/BI10_ROUND01.pdf` pp. 16–17 requires budgeting tools, spend alerts, financial-planning reminders, financial education, digital nudges and suitable product suggestions; each needs a real finding, target size/% and priority by reach and strength of the associated driver. It forbids adverse credit decisions from `financial_health_score`.
- `data/consumer_financial_health_engagement_2025.csv:1-7` supports monthly ratios and customer IDs; `data/consumer_transactions_2025.csv:1-7` contains channel-level transactions needed to distinguish QR from online. `data/consumer_financial_health_case_study.md:122-125,145-151` restates supportive action and ethics constraints. Task 4 exports both `code/outputs/segmentation_features.csv` and `customer_segments.csv` with unique customer keys; 918 customers were observed in December 2025 in the prior profile, 81 were not.
Unknowns: actual interventions' causal effectiveness and consent/channel availability are not measured in supplied data; recommend experiments/KPIs rather than assert outcomes.

## Target Change
- Create: `code/05_business_recommendations.ipynb` with six clearly separated tool proposals, eligibility calculations and English rationale.
- Create: `image/task5/` PNGs only when charts communicate reach, driver contrast or overlap better than a table (e.g. reach/priority chart); no slide deck or predictive model.

## Requirements Covered
R-01, R-02, R-03, R-08, R-09.

## Implementation Steps
1. **R-03, R-08** — Load both Task 4 exports and raw monthly source, assert 1:1 join on all 999 `consumer_id` values; stream relevant channel/time columns from transactions in bounded chunks for customer-level non-POS adoption (full 2025 and December separately). Define two reach populations up front: all 999 historically observed/model-assigned customers, and the 918 observed in December as a recently observed subset. Neither means contactable/consented; 81 without December data remain in historical reporting, not falsely labeled churned. Keep December snapshot features and year-wide model/feature summaries separate.
2. **R-08** — Anchor each of six supportive interventions to an observed Task 4 cluster profile **and** an explicit within-cluster or cross-cluster measured need, without assigning one tool per cluster or treating cluster ID alone as a need. Candidate mechanisms: budget tools (persistently higher spend/income), spend alerts (discretionary share or spend increase relative to an available prior-month baseline), planning reminders (observed stressed months/recent strain), financial education (relevant essential/discretionary pattern), digital nudges (lower observed non-POS adoption; QR is digital but not `online` flag), suitable product suggestions (observable need, subject to separate suitability/consent assessment, not adverse credit decisions). For one-month histories mark trend as unknown; do not turn the December-wide spending shock into a chronic distress signal. Set final thresholds only after examining distributions and cluster profiles; unsupported or empty actions are reported honestly and redesigned without pretending eligibility.
3. **R-08** — Keep 999-person historical and 918-person December reach separate. For independent evidence ranking, define each target and fit its thresholds on Jan–Aug only (requiring at least 3 training months), then measure the direction-aligned outcome in disjoint Sep–Dec for customers with at least one validation month. Standardize target-vs-other holdout means by the validation-population SD; fail clearly on empty groups, non-finite/zero SD, or zero transaction denominators. Rank this descriptive out-of-time replication first, historical reach second and action name third; display counts, means and score. Treat anchors as descriptive links, not evidence; do not claim causal efficacy.
4. **R-08, R-09** — State in prominent English Markdown: `financial_health_score` is a wellbeing indicator only and must never determine approval/denial, limit cuts or account blocking. Note synthetic, noncausal, small-group and unobserved-consent limitations. Chart six counts/priority or overlap when useful, save any figures to `image/task5/`, and write an English interpretation after them.
5. **R-01, R-02** — Execute from a clean kernel and review all six proposals against Task 1–4 observed results. Finish with a table ready for later slide development, but do not generate slides in this phase.

## Verification
- `.venv/bin/python -m nbconvert --to notebook --execute --inplace --ExecutePreprocessor.kernel_name=bi-uel --ExecutePreprocessor.timeout=900 code/05_business_recommendations.ipynb` → exit 0; six distinct tool sections and one summary table, each with historical and December-observed counts/percentages, model-profile link, finding and rank.
- Notebook assertions → feature/assignment IDs match all 999 input customers exactly; December-observed set is a subset of the 999; every action target ID belongs to its declared population, union reach <= that population's size, no duplicate IDs within an action; rows with no prior month never claim a month-over-month trend. If counts differ from prior review, correct narrative, do not suppress mismatch.
- Inspect optional `image/task5/` PNGs when figure cells exist and English prose after them; manually check no line suggests an adverse credit decision, realized uplift or confirmed churn.

## Completion Evidence

- Final Task 5 execution: 1,852,394 streamed transaction rows and all 10,992 customer-month keys/counts reconciled before shares; transaction months are finite integers 1–12, monthly counts positive integers, and monthly dates are 2025 month-start keys. Task 4 and Task 5 use the same exact 2025-12-01 December population.
- Out-of-time evidence cohort: 908 customers with at least 3 Jan–Aug months and at least 1 Sep–Dec month. All six comparisons have nonempty target/other groups and finite holdout-population-SD contrasts: Financial education 1.292, planning reminders 1.277, digital nudges 0.997, budgeting tools 0.813, suitable product suggestions 0.611, spend alerts 0.413. These are descriptive repeatability scores, not efficacy.
- Historical/December reach counts by action: budgeting 102/230, spend alerts 227/228, reminders 70/196, education 250/230, digital nudges 250/230, suitable products 247/230. The final reach chart and all six proposal details were regenerated.

## Load-Bearing Assumptions & Risks
- Product fit and consent are not observed: signal: recommendation requires product eligibility, income realism or opt-in fields absent from dictionary → describe supportive product categories as a hypothesis and measurement/consent gate; never present an executable credit offer decision.
- Reach depends on period, short history and de-duplication: signal: a count mixes yearly historical membership with December observation, or sums overlapping groups → recalculate two separate sets with distinct IDs and explicit denominators, with provisional eligibility for one-month customers.
- A segmentation model could yield weakly separated clusters: signal: Task 4 reports low stability or no distinctive cluster profile for a proposed action → state the limitation and ground targeting in measured behavioral criteria while showing the descriptive cluster mix; do not claim that clusters cause response or force a one-to-one cluster-to-tool mapping.

## Checklist
- [x] Link six proposals to real findings and compute distinct eligible counts/percentages.
- [x] Rank by reach and evidence strength; report overlap, limitations and safety rule.
- [x] Execute fifth English notebook and verify all outputs/artifacts.
