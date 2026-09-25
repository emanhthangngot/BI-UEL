---
phase: 2
title: "Task 2 — financial health analysis"
status: pending
priority: P1
effort: "0.5d"
dependencies: [1]
---

# Phase 2: Task 2 — financial health analysis

## Overview
Create and execute the second English notebook covering every health deliverable on PDF p. 9: score distribution, associated factors, occupation/age/province differences and the stressed-but-highly-engaged crossover, with interpreted chart exports.

## Current Behavior
- `docs/BI10_ROUND01.pdf` p. 9 names consumer-month as the primary grain and requires ratio-based, numbered/chart-backed claims for all four parts.
- `data/consumer_financial_health_engagement_2025.csv:1-7` provides health/engagement scores, occupations, provinces, age, income-related ratios and volatility; `data/data_dictionary.xlsx` marks scores/segments derived and financial amounts synthetic. `data/consumer_financial_health_case_study.md:85-94,145-159` cautions against absolute income interpretations and credit-score use.
- Previous direct profile measured 396 distinct occupations among 999 customers, 346 occupations with fewer than five customers; 95 stressed customer-months (`health<40`) across 70 people; December has 25 stressed–engaged rows for `engagement>=70`. Recompute these in notebook, not as immutable assumptions.
Unknowns: exact score construction and `spending_volatility` calculation window are not published in the dictionary.

## Target Change
- Create: `code/02_financial_health_analysis.ipynb`, English headings/short code/plots and **English Markdown interpretation immediately below each chart**.
- Create: `image/task2/health_distribution.png`, `health_associations.png`, `demographic_profiles.png`, `stressed_engaged.png` (additional task-scoped charts only if needed).

## Requirements Covered
R-01, R-02, R-03, R-05, R-09.

## Implementation Steps
1. **R-01, R-03, R-05** — Load/validate consumer-month table independently in a clean kernel. State whether every statistic weights customer-months or distinct customers, show observed-month coverage and score bands, and state synthetic/derived-score caveats. No raw-income league tables or credit-decision framing.
2. **R-05** — Chart health score histogram/bands and monthly trajectory with segment share and exact denominators. Show month effects (especially December) rather than conflating all 2025 rows with a current customer state.
3. **R-05** — Compare relevant ratios (`spend_to_income_ratio`, `credit_utilization_ratio`, `essential_spend_ratio`, volatility if valid) for stressed `<40` and comparison bands, using effect sizes/medians and month-stratified views; report **associations**, not independent causes, because score ingredients may overlap. Treat unknown volatility window as limitation, not assumed month-to-month behavior.
4. **R-05** — Compare age cohorts, occupation and province with counts of unique customers, uncertainty/small-cell caution, and honest grouped occupation taxonomy if one is defensible; do not rank one-person occupations as robust business signals. Display occupation, age and province findings separately or as legibly faceted charts.
5. **R-05** — Specify `health<40` and defensible high engagement cutoff (start with original high band `>=80`, show sensitivity at `>=70`), count customer-months, distinct customers with at least one matching month in 2025 and December-observed matches separately; profile group ratios, category diversity/online share from monthly fields, observed-month history and temporal persistence. If named merchant categories or physical-vs-digital channel detail is needed to defend a claim, stream only the relevant transaction columns and join on normalized customer-month; otherwise do not scan 596 MB again. Do not make punitive credit recommendations.
6. **R-01, R-02, R-09** — Save each figure before closing it, then add a Markdown cell directly below with observed numeric finding, benchmark, limitations and source; finish with a Task 2 synthesis and unresolved data limitations. No placeholder interpretation or oversized outputs.

## Verification
- `.venv/bin/python -m nbconvert --to notebook --execute --inplace --ExecutePreprocessor.kernel_name=bi-uel --ExecutePreprocessor.timeout=900 code/02_financial_health_analysis.ipynb` → exit 0 with all four p. 9 sections populated; no exception outputs.
- Review `image/task2/` → nonempty legible PNGs for distribution, associated ratios, three demographic cuts and crossover; each has a following English interpretation Markdown cell in notebook.
- Recompute `health<40` count and customer distinct count; identify/reconcile differing snapshot versus year counts. A reviewer can see `n` on occupation/province results and cannot mistake ratio association for a causal or credit claim.

## Load-Bearing Assumptions & Risks
- Hundreds of sparse occupations: signal: extreme group averages with `n` under a disclosed minimum → show counts, aggregate only by transparent semantic taxonomy or avoid ranking; do not silently drop customers.
- Score formula and volatility window unknown: signal: conclusions require causal attribution or cross-month volatility definition → qualify as association and document the unidentified construction, not fabricate formula.
- Health–engagement crossover size is cutoff-sensitive: signal: group vanishes or becomes mostly one-month history under a band boundary → show sensitivity and history stratification; do not relax cutoff only to enlarge the story.

## Checklist
- [x] Compute four official Task 2 outputs and check demographic sample sizes.
- [x] Export/interrogate Task 2 charts with adjacent English Markdown interpretations.
- [x] Execute from clean kernel and distinguish historical from current crossover reach.
