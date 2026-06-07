# README

## Notebook Guide

**File:** `Manish_Tiwari_RA.ipynb`
**Author:** Manish Tiwari, May 2026
**Datasets required:** `atlas.csv` (Google Drive) and `summit.csv` (Google Drive)

---

## What This Notebook Does

This notebook investigates whether private equity (PE) portfolio companies pay different loan interest rates when the lender has a prior or affiliated relationship with the PE sponsor, versus borrowing from an arm's-length lender.

It uses two synthetic datasets:

* **Atlas** — 315,238 deal-level records (buyouts, loans, IPOs, exits) across 112,276 companies
* **Summit** — 48,317 fund-investment records with entry financials and realised returns

---

## Step-by-Step Overview

| Step   | Section    | Description                                                                     |
| ------ | ---------- | ------------------------------------------------------------------------------- |
| 1      | 1-A to 1-J | Load and verify both datasets; parse dates; save Parquet caches                 |
| 2      | 2-A to 2-L | Reconstruct PE ownership timelines (spell start and end dates)                  |
| 3      | 3-A to 3-D | Match each loan to its active PE owner using date-range joins                   |
| 4      | 4-A to 4-D | Group legal entity names into parent-organisation umbrellas                     |
| 5      | 5-A to 5-E | Classify lender-sponsor relationship (affiliate, reciprocal, unrelated)         |
| 6      | 6-A to 6-E | OLS regression: does relationship type affect loan spread (bps)?                |
| 7      | 7-A to 7-G | Fuzzy name matching to link Atlas companies to Summit outcomes                  |
| 8      | 8-A to 8-D | Compare entry characteristics and exit outcomes across relationship types       |
| 9      | 9-A to 9-D | Three additional observations: cycle stability, leverage uniformity, stickiness |
| Export | Final cell | Package all figures and tables into `figures.zip` and `tables.zip`              |

---

## Figures Generated

| File                                 | Description                                                   | Memo Section |
| ------------------------------------ | ------------------------------------------------------------- | ------------ |
| `figure1_deal_type_dist.png`         | Bar chart of top 10 Atlas deal types                          | 2.1          |
| `figure2_spell_starts_by_year.png`   | New PE ownership spells per year with crisis shading          | 2.2          |
| `figure3_spread_boxplot.png`         | Loan spread boxplot by relationship type (most compelling)    | 3.1          |
| `figure4_spread_over_time.png`       | Mean spread by relationship type over time (cycle stability)  | 6.1          |
| `figure5_sponsor_lender_heatmap.png` | PE × lender deal-count heatmap (top 15 each)                  | 5            |
| `figure6_forest_plot.png`            | Forest plot: IRR and TVM regression coefficients with 95% CIs | 4            |
| `figure7_entry_leverage.png`         | Entry leverage by relationship type (near-identical)          | 6.2          |
| `figure8_reciprocal_share.png`       | Top 20 PE sponsors by reciprocal deal share                   | 6.3          |
| `figure9_pair_stickiness.png`        | Deal count per pair plus repeat-versus-one-time pair split    | 6.3          |
| `figure10_time_gaps.png`             | Distribution and CDF of inter-deal gaps within pairs          | 6.3          |

---

## Tables Generated (as Images)

| File                                    | Description                                          | Memo Section |
| --------------------------------------- | ---------------------------------------------------- | ------------ |
| `figure_table1_funnel.png`              | Funnel from raw Atlas events to cross-deals          | 2.1          |
| `figure_table2_spread_descriptives.png` | Loan spreads by relationship type (mean, median, SD) | 3.1          |
| `figure_table3_ols_results.png`         | OLS coefficients, standard errors, and p-values      | 3.2          |
| `figure_table4_outcomes.png`            | Entry characteristics and exit outcomes by type      | 4            |

---

## CSV Outputs

| File                                  | Contents                                                   |
| ------------------------------------- | ---------------------------------------------------------- |
| `atlas_parsed.parquet`                | Atlas with verified column types and `EffectiveDate`       |
| `summit_parsed.parquet`               | Summit with verified column types                          |
| `match_review_file_resolved.csv`      | All Atlas–Summit name-match decisions (`ACCEPT`/`REJECT`)  |
| `outcome_summary_by_relationship.csv` | Mean entry and outcome metrics by relationship type        |
| `pair_stickiness_summary.csv`         | One row per PE–lender pair with deal count and repeat flag |
| `pair_time_gaps.csv`                  | One row per consecutive deal gap within a pair             |

---

## How to Run

1. Open in Google Colab (`Runtime → Run all`) or run locally with JupyterLab.
2. Run cells in order. Each step depends on variables produced by previous steps.
3. After all figure cells have run, execute the final **Export** cell to produce `figures.zip` and `tables.zip`.

### Python Dependencies

* `pandas`
* `numpy`
* `matplotlib`
* `statsmodels`
* `rapidfuzz`
* `gdown`

These are installed automatically by cell **1-A**.

---

## Key Design Choices

### Umbrella Definition

The notebook uses the first meaningful token of the cleaned entity name to define organisational umbrellas. Two-token matching captures most subsidiary structures, although conglomerate edge cases remain a limitation.

### Prior-Reciprocal versus Any-Time Reciprocal

The preferred causal measure relies only on pre-deal history to avoid look-ahead bias.

### Growth Equity Exclusion

`INCLUDE_GROWTH_EQUITY = False` in cell **2-D**.

Set this value to `True` to include minority-stake PE investments.

### Fuzzy-Match Threshold

The default threshold is **85** using `token_sort_ratio`.

Lowering the threshold increases the number of candidate matches but also raises the risk of false positives.
