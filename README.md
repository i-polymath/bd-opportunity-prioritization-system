# BD Opportunity Prioritization System

An AI-driven decision support system for business development opportunity prioritization
in management consulting. Built as a proof-of-concept for a Financial Services Strategy
practice, the system combines machine learning win probability estimation, NLP-based
thematic signal extraction, and a configurable strategic value scoring framework —
delivered through an interactive Power BI dashboard.

---

## Overview

Consulting business development teams face a recurring prioritization problem: too many
open opportunities, limited proposal capacity, and go/no-go decisions that rely heavily
on gut feeling. This system packages that decision into a structure by answering two questions for every
active opportunity:

- **How likely are we to win this?** — a logistic regression model trained on 141
  historical opportunities with resolved win/loss outcomes (AUC-ROC: 0.684).
- **How strategically valuable is this?** — a four-dimensional scoring framework
  covering contract scale & value, client importance, practice growth alignment,
  and portfolio diversification.

A composite decision score integrates both layers using leadership-configurable weights,
and three baseline scenarios (Balanced, Win-Maximizing, Growth-Maximizing) allow
decision-makers to explore how rankings shift under different strategic postures.

---

## System Architecture

<img width="785" height="706" alt="image" src="https://github.com/user-attachments/assets/749cdb21-d10d-43cf-a100-a39e67a59c1f" />


The system uses a two-layer architecture with a clean separation between the
analytical engine and the presentation layer.

thesis_modelling_pipeline.py          Power BI Dashboard (.pbix)
─────────────────────────────         ──────────────────────────────────
1. Data loading                       Portfolio Overview page
2. NLP preprocessing (gensim)    →    Opportunity Prioritisation page
3. LDA topic modeling (k=9)      →    Opportunity Detail & SHAP page
4. Feature matrix construction
5. Model training & evaluation
6. SHAP interpretability
7. Strategic value scoring
8. Composite score calculation
9. Power BI export (.xlsx)

The Python pipeline produces Excel artefacts consumed by Power BI. Neither layer
needs to know the internals of the other beyond the agreed column schema, so the
model can be retrained and the dashboard updated independently.

---

## Key Results

| Metric                          | Value                    |
|---------------------------------|--------------------------|
| Historical opportunities        | 437 records              |
| Modelling subset (resolved)     | 141 records              |
| Overall win rate                | 24.8%                    |
| Unique clients                  | 155                      |
| Selected model                  | Logistic Regression      |
| Cross-validated AUC-ROC         | 0.684 ± 0.080            |
| Cross-validated F1-score        | 0.480 ± 0.089            |
| LDA topics (k)                  | 9 (c_v coherence: 0.454) |
| Scenario rank shift (>50 pos.)  | 39% of opportunities     |
| Spearman ρ (scenario agreement) | 0.877                    |

The Quality of the results was largely determined by the small sample size for model training, and the level of richness of the data itself.

---

## Strategic Value Scoring Framework

| Dimension                   | Business Question                                        |
|-----------------------------|----------------------------------------------------------|
| Contract Scale & Value      | Does this meet our thresholds for high-impact work?      |
| Strategic Client Importance | Is this a key account we need to grow?                   |
| Practice Growth Alignment   | Does this work build a priority capability area?         |
| Portfolio Diversification   | Does this reduce over-reliance on repeat clients?        |

Three weighting scenarios are defined:

- **Balanced** — w_win = 0.50, w_strategic = 0.50
- **Win-Maximizing** — w_win = 0.70, w_strategic = 0.30
- **Growth-Maximizing** — w_win = 0.30, w_strategic = 0.70

---

## Tech Stack

| Component    | Version | Role                                                      |
|--------------|---------|-----------------------------------------------------------|
| Python       | 3.12.3  | Core runtime                                              |
| pandas       | 3.0.1   | Data loading and manipulation                             |
| numpy        | 2.4.3   | Numerical operations                                      |
| scikit-learn | 1.8.0   | Logistic Regression, preprocessing, cross-validation      |
| xgboost      | 3.2.0   | Candidate gradient-boosted classifier                     |
| gensim       | 4.4.0   | LDA topic modeling and coherence scoring                  |
| shap         | 0.51.0  | SHAP LinearExplainer for per-opportunity explainability   |
| scipy        | 1.17.1  | Spearman rank correlation                                 |
| matplotlib   | 3.10.8  | ROC curves, calibration plots, SHAP visualizations        |
| seaborn      | 0.13.2  | Confusion matrix heatmaps                                 |
| openpyxl     | 3.1.5   | Excel I/O for Power BI data model                         |
| Power BI     | —       | Interactive dashboard and scenario visualization          |

---

## Methodology

The pipeline follows the CRISP-DM framework across nine sequential stages. The
classification task targets binary win/loss outcomes with a 1:3 class imbalance
(24.8% wins), handled via `class_weight='balanced'` rather than SMOTE to avoid
synthetic oversampling artefacts on a small dataset. Three candidate models were
evaluated under 5-fold stratified cross-validation repeated three times (15 folds
total): Logistic Regression, Random Forest, and XGBoost. Logistic Regression was
selected based on superior cross-validated AUC-ROC and F1, and for its compatibility
with exact SHAP values via `LinearExplainer`.

---

## Repository Structure

bd-opportunity-prioritization-system/

bd-opportunity-prioritization-system/
│
├── thesis_modelling_pipeline.py   # Full end-to-end pipeline (single script)
├── requirements.txt               # Python dependencies
├── .gitignore
└── README.md

---

## Data Notice

The underlying dataset contains proprietary business development records and is
governed by a Non-Disclosure Agreement between the author and the client
organization. **No data files are included in this repository.** The pipeline code
is shared for portfolio and research purposes only.

---

## Author

**Pascal Chisom Okechukwu**
Master of Business Analytics and Data Science
EU Business School, Barcelona
