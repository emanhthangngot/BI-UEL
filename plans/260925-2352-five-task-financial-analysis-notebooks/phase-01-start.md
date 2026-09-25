---
phase: 1
title: "Task 1 — exploratory data analysis and environment"
status: pending
priority: P1
effort: "1d"
dependencies: []
---

# Phase 1: Task 1 — exploratory data analysis and environment

## Overview
Build the reproducible Python/Jupyter environment and the first executed English notebook. Answer all five exact EDA questions on pp. 6–7 of the official PDF, with numbered charts and evidence-based Markdown interpretations immediately after the corresponding chart cells.

## Current Behavior
- `.gitignore:1` ignores `/data/` only; a project `.venv/` and notebook checkpoints would otherwise be untracked noise.
- `data/consumer_financial_health_case_study.md:34-52,84-94` gives two grains and synthetic caveats; it describes broader teaching tasks, not the official Task 1 checklist.
- `data/consumer_financial_health_engagement_2025.csv:1-4` has `consumer_id`, `analysis_month`, health score, age, and monthly ratios; `data/consumer_transactions_2025.csv:1-7` has transaction time, category, VND amount, channel, and flags. `data/data_dictionary.xlsx` defines units and channel/online semantics.
- `docs/BI10_ROUND01.pdf` pp. 6–7 defines EDA Q1–Q5 and exact numerical deliverables. `code/` and `image/task1/` are empty; the linked sample notebook shows Markdown heading → explanatory prose → short code → figure → interpretation prose, but covers unrelated hotel data.
Unknowns: measured local notebook runtime and actual installed package versions; discover during implementation, not invented in this plan.

## Target Change
- Modify: `.gitignore:1` to ignore `.venv/` and `.ipynb_checkpoints/`, preserving `/data/`; do **not** ignore `image/taskN/*.png` or the five notebooks.
- Create: root `requirements.txt` with reproducible pinned Python dependencies (`numpy`, `pandas`, `matplotlib`, `scikit-learn`, `nbformat`, `nbconvert`, `ipykernel`, `openpyxl` only if dictionary loaded programmatically). Create `.venv/` during implementation, not in the plan; no system-wide pip.
- Create: `code/01_exploratory_data_analysis.ipynb`; export only small PNG charts to `image/task1/`. Do not duplicate the 596 MB CSV or create an extra notebook.

## Requirements Covered
R-01, R-02, R-03, R-04, R-09.

## Implementation Steps
1. **R-02, R-09** — `python -m venv .venv`; pin/install dependencies with `.venv/bin/python -m pip install --no-cache-dir -r requirements.txt`; register `.venv/bin/python -m ipykernel install --prefix .venv --name bi-uel --display-name BI-UEL` and verify the custom kernel is discoverable from the venv. Record `python --version` and library versions in notebook; add ignore entries only for environment/checkpoints.
2. **R-01, R-03** — Notebook section sequence in English: title and business question; data provenance and grain; imports/environment and `BI_DATA_DIR`/`BI_OUTPUT_DIR`; quality checks; metric definitions; Q1–Q5 each as question/metric/code/chart/interpretation; synthesis and limitations. Validate notebook cwd=`code/` or accept an explicit root override, then construct absolute `DATA_DIR`, task `FIGURE_DIR` and `code/outputs` paths from the contract in `plan.md:38-43`; create artifact directories before saving and fail on missing input. Embed captions and sources with each chart.
3. **R-03, R-04** — Load monthly CSV with parsed dates and validated `(consumer_id, analysis_month)` uniqueness. Process the transaction CSV in bounded chunks (`usecols`, explicit dtypes, selected fields); normalize each transaction's `transaction_month` integer to `2025-MM-01` and verify timestamp month agrees before reconciling `transaction_count` and `total_spend_vnd` per `(consumer_id, analysis_month)`. Assert transaction ID uniqueness without a giant all-column dataframe. Do not drop duplicate IDs blindly, fill unobserved months with zero, or imply full-year history for all customers.
4. **R-04** — Q1: compute total VND and annual %, count and average ticket for peak and trough months, decompose spend difference using count × ticket. Include a monthly trend chart, explicit number/percentage, and no unsupported real-world seasonal cause. Q2: **use the monthly table directly** (`essential_spend_vnd`, `discretionary_spend_vnd`, `financial_health_score`) to compare `<40` vs `>=80`; report both shares, difference and group sizes, distinguishing monetary-weighted from average-customer share; no large transaction join for this question. Q3: define high volume before selecting at least two provinces below overall **transaction-count-weighted** digital share; digital = non-POS incl. QR, contrasted with online flag and optionally spend-weighted share; POS/digital counts, shares, percentage-point gap; chart channels for selected provinces. Q4: category top by count and top by total spend, average VND/ticket, chart and interpretation. Q5: predefine age bins and active transaction-frequency metric, compare health, essential ratio and spend/income, report cohort sizes; rerun with equal weight per customer and disclose if lowest group changes.
5. **R-01, R-02, R-09** — Save charts with deterministic names (`q1_monthly_spend.png` etc.) under `image/task1/` via `savefig` and close figures. **After each chart output add a separate English Markdown cell** with calculated finding, comparator, caveat and source; update prose after execution if values differ. Keep cells small and outputs compact; no copied hotel-specific conclusions.

## Verification
- `.venv/bin/python -m pip check` → exit 0; kernel found under the venv prefix.
- `.venv/bin/python -m nbconvert --to notebook --execute --inplace --ExecutePreprocessor.kernel_name=bi-uel --ExecutePreprocessor.timeout=900 code/01_exploratory_data_analysis.ipynb` → exit 0 with numeric answers Q1–Q5 and no error cells. First run `.venv/bin/jupyter kernelspec list` and confirm `bi-uel` is found under the venv prefix; notebook runtime cwd must resolve to `code/`.
- Notebook assertions → 1,852,394 transaction rows, 10,992 unique customer-months, 999 customers and exact spend/count reconciliation; if dataset changes, stop and explain instead of forcing these expected counts.
- Inspect saved notebook: five Q sections, English Markdown **immediately after each figure**, numeric breakdowns and sources; `image/task1/` contains legible nonempty PNGs for Q1–Q5. A reviewer can trace one Q2 share back to sums in displayed tables.
- Record peak memory/time/free disk during the representative local run. If local execution fails for resource reasons after chunking and avoiding raw copies, document exact error/resource measurement and recommend Kaggle with input/output mounts, but do not label an unexecuted notebook completed.

## Load-Bearing Assumptions & Risks
- Local disk currently has ~4 GB free and RAM ~6.2 GB available. A venv plus full-CSV copies and rich notebook outputs could exhaust disk/memory; signal: observed OOM, ENOSPC or runaway run after chunked read → remove redundant artifacts, cap output and rerun; persistent failure → Kaggle with recorded evidence, not a fabricated local result.
- `online_transaction_flag` excludes QR transactions in the observed data; signal: channel×flag cross-tab changes → recompute digital/online definitions from dictionary and rerun Q3, do not conflate them.
- The sample notebook's prose contains precomputed hotel conclusions; signal: Markdown claims unsupported by local output → replace with data-driven English interpretation after execution.

## Checklist
- [x] Install pinned dependencies and isolate the notebook kernel in `.venv`.
- [x] Execute Q1–Q5 with reconciled metrics, figures and adjacent English interpretations.
- [x] Verify the notebook output and `image/task1/` PNGs before proceeding.
