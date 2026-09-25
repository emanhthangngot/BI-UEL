---
phase: 4
title: "Task 4 — model-based customer segmentation"
status: pending
priority: P1
effort: "0.75d"
dependencies: [1, 2, 3]
---

# Phase 4: Task 4 — model-based customer segmentation

## Overview
Fit a genuine unsupervised customer segmentation model, validate its cluster quality and stability, assign **all** consumers exactly once, profile actionable groups and export the cleaned feature/assignment tables. Do not substitute a rule-based health×engagement matrix for the model.

## Current Behavior
- `docs/BI10_ROUND01.pdf` pp. 14–15 permits clustering or rules, requires preprocessed/engineered dataset, parameter justification, 100% coverage, cluster validation, profiles, limitations and future work. User explicitly chooses a model.
- `data/consumer_financial_health_engagement_2025.csv:1-7` supplies ratio-based monthly behaviors, derived health/engagement scores and customer demographics. `data/data_dictionary.xlsx` marks scores derived and target `next_month_low_health_flag` future-derived. `data/consumer_financial_health_case_study.md:155-173` warns about synthetic volume and optional prediction leakage.
- Prior full profile: 999 customers, 908 observed for 12 months, 86 for one, five for two; no customer has **annual mean** health<40 although 70 customers have at least one stressed month. An annual-mean two-score-only clustering would obscure this. No model or `code/outputs/` exists yet.
Unknowns: exact score ingredients and latent synthetic generator; cluster quality cannot be guaranteed before fitting and measuring.

## Target Change
- Create: `code/04_customer_segmentation.ipynb` with in-notebook preprocessing, model selection, stability diagnostics, segment profiles, English limitations and exported charts in `image/task4/`.
- Create during execution: `code/outputs/segmentation_features.csv` (one clean, engineered row per consumer, including observed-month coverage and historical/recent diagnostics) and `code/outputs/customer_segments.csv` (one `consumer_id`, `cluster`, `cluster_label` assignment per consumer). Task 5 joins these **both** on unique `consumer_id` and the raw monthly table for checks; no extra notebook or large duplicate raw-data export.

## Requirements Covered
R-01, R-02, R-03, R-07, R-09.

## Implementation Steps
1. **R-03, R-07** — Build exactly one record per consumer from monthly ratios: e.g. median/mean spend-to-income, credit utilization, essential share, online share, and transformed average monthly transaction frequency. Calculate `n_observed_months`, latest observed month, stressed-month count/share (`health<40`), and latest health/engagement for diagnostics. Preserve 999 IDs and all 91 sparse-history consumers; no imputed missing months or treating a one-month history as a full year. Use feature aggregations defined for `n=1`; do not compute an undefined month-over-month delta as a model input. Assert selected feature matrix contains only finite numeric values before scaling/K-Means, with an explicit error listing offending fields/IDs rather than silent imputation.
2. **R-07** — Train CPU `KMeans` on a small, predeclared nonredundant set of numeric behavioral features after transparent clipping of extreme values and `StandardScaler`/`RobustScaler`; exclude identity, age/gender/occupation/province, raw VND, `financial_health_score`, `engagement_score`, derived band labels, `next_month_low_health_flag` and unknown-window `spending_volatility` from the fit. These recommended dimensions are represented by underlying ratios/frequency; including a score **and** its potentially overlapping components would double count information. Scores/demographics remain post-hoc interpretation and fairness overlays; explicitly test whether the model's behavioral archetypes miss health×engagement crossovers. Pin random state and `n_init`.
3. **R-07** — Evaluate k=2..6 using silhouette, minimum cluster size and stability (adjusted Rand index across several seeds), not silhouette alone. Diagnose whether clusters merely isolate outliers, 91 short-history people, or a December shock. Compare fit on all 999 with a fit on the 908 complete-history consumers (ARI on the shared 908 IDs) and assignments of sparse-history cases; if initial result is unstable/tiny, adjust scaling/features and refit **K-Means**, documenting the change instead of falling back to hand rules.
4. **R-07** — Profile every cluster side by side: customer counts/%, ratios/frequency, health and engagement post-hoc, historical stress rate, recent scores, month coverage, age/occupation/province with sample sizes. Name business-facing groups only after profiles support names; a rare healthy–disengaged or stressed–engaged intersection may cross clusters and remains a separate descriptive target, not necessarily a model cluster. Compare 2025-wide clusters with December-observed membership to reveal timing effects.
5. **R-07, R-09** — Export a preprocessed feature CSV with documented typed columns (`consumer_id`, model features, `n_observed_months`, `latest_observed_month`, `months_health_under_40`, recent health/engagement) and a 999-row assignment CSV (`consumer_id`, `cluster`, `cluster_label`). Validate unique keys and complete 1:1 join; Task 5 consumes both files rather than recreating hidden notebook variables. Save k-diagnostic, cluster-profile and history/fairness charts to `image/task4/`; English Markdown explains feature choices, selected k, validation, business use, bias, synthetic limitations and future improvement.
6. **R-01, R-02** — Execute the notebook cleanly in the local venv, keeping only compact displayed tables. All figures have captions and are saved outside notebooks; neither fit nor scoring needs a GPU.

## Verification
- `.venv/bin/python -m nbconvert --to notebook --execute --inplace --ExecutePreprocessor.kernel_name=bi-uel --ExecutePreprocessor.timeout=900 code/04_customer_segmentation.ipynb` → exit 0 and displays k selection, silhouette/stability/size comparison, selected model/profile and limitations.
- `.venv/bin/python -c "import pandas as p; a=p.read_csv('code/outputs/customer_segments.csv'); b=p.read_csv('code/outputs/segmentation_features.csv'); assert len(a)==len(b)==999 and a.consumer_id.is_unique and b.consumer_id.is_unique and a.cluster.notna().all() and set(a.consumer_id)==set(b.consumer_id); print(a.cluster.value_counts().to_dict())"` → exit 0 with nonempty clusters; compare ID set to raw consumer-month IDs in a notebook assertion, not only length.
- Inspect `image/task4/` → nonempty model-validation/profile PNGs; notebook reports counts/ratios for every cluster, sparse-history composition, stress retention, and explicitly states the clustering features exclude scores and the next-month target. An assertion before fitting proves all model features finite for 999 consumers.

## Completion Evidence

- Final Task 4 execution selected k=2 from the complete-history fit cohort (n=908), with silhouette 0.201 (weak separation), mean seed ARI 1.000, and fit minimum cluster share 40.2%. All-population candidates failed guardrails; selected all-vs-full ARI was 0.061, while the projected sparse-history maximum cluster share was 19.6%.
- K-Means predicts labels for all 999 customers (cluster counts 545/454; all 91 sparse histories assigned), and both CSV contracts contain 999 unique IDs. The population-standardized profile check reports 0 of 8 columns forced to ±1.00; all three Task 4 PNGs were regenerated and visually checked.

## Load-Bearing Assumptions & Risks
- No hidden ground-truth segment exists; signal: all k produce low silhouette/unstable or tiny clusters → say so, reevaluate preprocessing/features and report weak separation honestly. Do not invent a model-success claim or switch to rule-based against the user's explicit choice.
- Strong correlation and high-volume synthetic customers could dominate Euclidean distance: signal: feature correlations, outlier isolation or one feature dominating standardized centroids → prune/reweight a documented feature, refit and compare stability.
- Annual behavioral averages may hide transient stress: signal: stress-month customers appear only in healthy-looking cluster profiles → keep stress share/latest state alongside cluster label, never replace task 2's month-level findings.

## Checklist
- [x] Fit and select a validated customer-level K-Means model, preserving all IDs.
- [x] Diagnose sparse-history, outlier, temporal and post-hoc demographic risks.
- [x] Export 999-row features/assignments and interpreted Task 4 figures.
