---
title: "Five English BI10 analysis notebooks"
description: "Execute the five official PDF tasks as five reproducible English notebooks with task-scoped charts and model-based customer segmentation."
status: completed
priority: P1
effort: "3d"
tags: [feature, experimental]
blockedBy: []
blocks: []
created: 2026-09-25
---

# Five English BI10 analysis notebooks

## Overview
Implement exactly five executed notebooks in `code/`, one per Task 1–5 in `docs/BI10_ROUND01.pdf` (pp. 5–17). Each uses titled Markdown sections, concise code cells, displayed numeric results and charts, English interpretation after chart cells, and PNG exports in `image/task1/` through `image/task5/`. Do not build slides. The official PDF takes precedence over the six-task teaching case and the saved submission form.

## Evidence and constraints
- Official task scope: `docs/BI10_ROUND01.pdf`, pp. 3–19. Case caveats: `data/consumer_financial_health_case_study.md:7-9,34-52,84-94,145-173`; dictionary: `data/data_dictionary.xlsx` sheets `Transaction_Level` and `Customer_Month`.
- Local CSVs: 1,852,394 transactions (~596 MB), 10,992 customer-month rows, 999 customers. Earlier direct scan reconciled transaction counts and spend by customer-month. 908 customers have 12 observed months; 91 have one or two; December has 918 observed customers and no health score >=80. Redo assertions in notebooks, never hard-code conclusions without computed support.
- Reference notebook: `https://github.com/emanhthangngot/LTKHDL_Final/blob/main/notebooks/02_data_exploration.ipynb` — follow heading/Markdown-before-code/figure/Markdown-after-figure rhythm, not its hotel dataset, Vietnamese prose, blanket drop-duplicates, or unverified claims.
- Python 3.11, 16 logical CPUs, ~6.2 GB available RAM at inspection, ~4.0 GB disk free. Local CPU execution first; stream/select columns in the 596 MB CSV, no full raw-data copy or large cached parquet. GPU is unnecessary. Install pinned project requirements inside `.venv` with pip caching disabled during implementation. If an actual representative local run exceeds memory/disk/time budget, document the observed failure and supply a Kaggle execution path rather than pretending the notebook ran.
- Neither a dashboard nor prediction is required. Model-based clustering for Task 4 is explicitly requested; a rule-based matrix must not replace it. Credit decisions from `financial_health_score` are prohibited.

## Requirements
| ID | Requirement | Phase |
|---|---|---|
| R-01 | Exactly five cleanly sectioned `.ipynb` in `code/`, English Markdown/code labels/plots/results, corresponding one-to-one to official Tasks 1–5; chart interpretation Markdown **after** each chart in Tasks 1–2 | 1–5 |
| R-02 | Local `.venv` installation, reproducible pinned dependencies and kernel; task charts exported as PNG under `image/taskN/`; notebooks execute to saved results or give an evidence-backed Kaggle recommendation on genuine resource failure | 1–5 |
| R-03 | Shared metric/grain contract, normalized customer-month key for reconciliation when needed, quality checks, synthetic-data caveats, no silent zero-filling or causal/credit claims | 1–5 |
| R-04 | Task 1 answers all five PDF EDA questions with exact values, percentages, decomposition, suitable charts and grounded interpretations | 1 |
| R-05 | Task 2 reports health distribution, associated factors, comparisons by occupation/age/province, and reproducible stressed–high-engagement crossover with profile | 2 |
| R-06 | Task 3 reports engagement distribution and bands, five channels plus average online spend share, diversity/frequency/recency relationships, and defensible healthy–low-engagement cutoff/size/profile | 3 |
| R-07 | Task 4 trains/validates a customer-level clustering model (not rule-based), exports cleaned engineered features and 999/999 assignments, profiles segments, and checks temporal/sparse-history/feature/cutoff risks and limits | 4 |
| R-08 | Task 5 gives one non-punitive data-linked proposal for each of six specified tool types, exact unique-customer reach/% and transparent priority by reach and evidence strength | 5 |
| R-09 | No slide deck; preserve notebook execution evidence and source captions. Supporting CSV/model outputs are allowed, not extra notebooks or placeholder text | 1–5 |

## Data and execution contract
- Files: `code/01_exploratory_data_analysis.ipynb`, `code/02_financial_health_analysis.ipynb`, `code/03_customer_engagement_analysis.ipynb`, `code/04_customer_segmentation.ipynb`, `code/05_business_recommendations.ipynb`. `image/task1`–`task5` already exist. `code/outputs/segmentation_features.csv` and `code/outputs/customer_segments.csv` are the small, reusable Task 4 → Task 5 artifacts; no raw CSV copy.
- At runtime the notebook working directory is `code/`. Resolve `PROJECT_ROOT = Path.cwd().resolve().parent` only after validating the expected `code/` cwd (or accept an explicit project-root override); `BI_DATA_DIR` overrides input data and `BI_OUTPUT_DIR` overrides the **artifact root**. Local defaults: `PROJECT_ROOT/data`, `PROJECT_ROOT/image/taskN`, `PROJECT_ROOT/code/outputs`. Kaggle output root must be writable and contain the same `image/taskN` and `code/outputs` structure. Create only task output directories; fail clearly if inputs are absent or output unwritable. No `__file__` in notebooks and no fabricated fallback data. One fixed metric sheet in Task 1: numerator, denominator, date window, row grain, customer count, digital (non-POS incl. QR) versus online (`online_transaction_flag`), monetary-weighted versus customer-weighted means. Reuse definitions/provenance in Tasks 2–5.
- Each notebook is independently runnable for its own analyses except Task 5, which requires Task 4's feature and assignment CSVs. Task 4 rereads monthly data rather than hidden state from prior kernels. Task 2 uses monthly aggregates; optional transaction detail only for a clearly labeled deeper analysis. Task 3 and Task 5 stream selected transaction columns for channels. Figures use `savefig` before `show`/`close`; no huge dataframe output embedded.
- Task 4 model choice: CPU K-Means on one engineered record per customer; fit on robustly scaled/trimmed behavioral ratios and average frequency, **not** scores, demographics, target label or full raw VND. Profile scores, stressed-month history and the rare health×engagement crossovers as post-hoc overlays; clusters are behavioral archetypes, not guaranteed copies of official score bands. Scan k=2..6 with silhouette, minimum segment size, reproducibility across seeds and business interpretability; report winner and failures. Check short-history consumers do not form a spurious cluster. If initial features produce only unstable/tiny clusters, investigate scaling and feature set and rerun a genuine clustering model; no rule-based fallback.
- Task 5 reports two distinct reach views for every action: (a) the modeled 999-customer **historical/analytical** population, with each person's observation window and support for eligibility; (b) the 918 customers observed in December 2025 as a **recently observed** subset, not confirmed contactable customers. Do not infer current retention, contact permissions or churn from either view. Compare December signals to available prior months to avoid calling a population-wide spike chronic distress; single-month histories have no trend claim. Tie each action to at least one model cluster profile, then qualify with observed behavioral evidence. Interventions may overlap; union reach is a distinct-customer union within each declared population.

## Phases
| Phase | Deliverable | Dependency |
|---|---|---|
| 1 | [Task 1 — EDA and environment](./phase-01-start.md) | None |
| 2 | [Task 2 — Financial health](./phase-02-financial-health.md) | 1 |
| 3 | [Task 3 — Customer engagement](./phase-03-customer-engagement.md) | 1 |
| 4 | [Task 4 — Model segmentation](./phase-04-model-segmentation.md) | 1, 2, 3 |
| 5 | [Task 5 — Business recommendations](./phase-05-business-recommendations.md) | 1–4 |

## Completion gate
All five notebooks execute top-to-bottom in the same `.venv` from clean kernels with no exception cells; every required deliverable has an English table/chart and adjacent interpretation; PNG files are saved to their task directories; K-Means assigns exactly 999 unique consumers; Task 5 contains six evidence-linked actions with separate reach views and a non-punitive rule. Its priority score must use a disjoint temporal holdout and remain explicitly observational, never causal efficacy. Record observed runtime/memory/disk and recommend Kaggle only if local execution genuinely fails or remaining resource headroom is inadequate. No slide work in this plan.

## Red Team Review
### Session — 2026-09-26
Three independent lenses reviewed requirements, assumptions and execution failure modes; duplicate findings were collapsed. User approved applying evidence-backed corrections without cutting requested scope.

| Finding | Disposition | Applied to |
|---|---|---|
| Redundant 1.85M-row Q2 join; month key type mismatch | Accept — compute Q2 from monthly table; normalize month key only for reconciliation/optional detail join | Phase 1, plan contract |
| Unclear notebook path/kernel and low disk during pip install | Accept — explicit artifact root, venv kernel discovery, uniform nbconvert command, `--no-cache-dir` | Phases 1–5, plan contract |
| Short-history NaN/model artifact and hidden Task 4 → 5 dependency | Accept — finite feature assertion, post-hoc history diagnostics, unique feature+assignment exports | Phases 4–5 |
| December-only recommendations and snapshot shock | Accept — historical 999 and December-observed 918 reported separately; prior-month baseline and no trend claim for one-month history | Phase 5, plan contract |
| Detached cluster profiles and historical vs snapshot crossover counts | Accept — link proposals to measured cluster profiles; report distinct yearly versus December counts | Phases 2–5 |
| Force health/engagement scores into K-Means | Reject — PDF p. 15 says metrics are recommended, not compulsory; score plus constituent ratios can double count. Scores/crossovers remain post-hoc overlays and model failure must be shown honestly | Phase 4 |
| Mandatory Task 2 transaction streaming and Q3 spend-share qualification | Reject as mandatory — PDF p. 9 calls raw transaction detail optional for Task 2; Q3 specifies **digital transaction share**, not spend share. Optional deeper checks are allowed and denominators explicit | Phases 1–2 |
| Same-sample recommendation priority and unsupported online/digital equivalence | Accept — rank evidence by direction-aligned Jan–Aug training-target vs Sep–Dec holdout contrast; rename Task 4 segment to measured online-spend share only; keep non-POS/QR distinct | Phases 4–5 |
| Two-segment heatmap exaggeration and crowded fairness labels | Accept — standardize customer-level profiles against the 999-person population before segment means; use short ticks and full visible key | Phase 4 |
| Silent invalid month/count denominators and different December filters | Accept — validate integer transaction months before casting, require positive monthly counts and month-start keys, and use exact 2025-12-01 in both tasks | Phase 5 |

### Whole-Plan Consistency Sweep
- Files reread: `plan.md`, phases 01–05. Decision deltas checked: month join/Q2 source, venv/path contract, 999-vs-918 reach, Task 4 feature exports, cluster-profile linkage.
- Reconciled superseded notebook commands, old December-only reach, ambiguous monthly channel detail, and Task 4 → 5 file contract; all five phases retain the requested English/figure/interpretation requirements.
- Unresolved contradictions: 0. Unmeasured cluster quality and local execution resource use remain explicit implementation risks, not claims of proven success.

### Rule Notes
- Use descriptive variable names: Avoid meaningless names like df1, df2, x, or y (except for common mathematical variables). Choose clear names such as df_customers or sales_2026.
- Group imports: Place all library import statements in the notebook's first cell; do not scatter imports across subsequent cells
- Break down the code: Each cell should perform a single task (e.g., one cell to load data, one to handle missing values, and one to display a chart).
<!-- slug: five-task-financial-analysis-notebooks -->
