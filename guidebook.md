# Guidebook: How and Why Each Step Was Set Up

This guide explains the structure of `Statistic_research.ipynb` and how it follows the course lectures.

## 1) Load data
**What:** Load `crash_data.csv` with explicit encoding and basic preview.  
**Why:** The dataset is not UTF-8; loading with `latin1` avoids decode errors and ensures the analysis starts cleanly.

## 2) Structure checks
**What:** `df.info()` and `df.head()` to inspect columns, types, and missingness.  
**Why:** Topic 5 emphasizes verifying variable types and data quality before modeling.

## 3) Duplicates
**What:** Count duplicate rows.  
**Why:** Duplicates can bias frequency-based results and distort model training. This is the first step in the Topic 5 workflow.

## 4) Missing values profile
**What:** Compute missing rates per column.  
**Why:** Topic 5 requires identifying missingness and reasoning about MCAR/MAR/MNAR before choosing a treatment.

## 5) Define the dependent variable (Y)
**What:** Choose a target column (default placeholder is `Injury Severity`).  
**Why:** The lecture sequence determines which model is appropriate (linear, logistic, or Poisson) based on Y.

## 6) Data preparation workflow (Topic 5)
**What:** Placeholder steps for:
- type fixes and logical ranges
- missing values (MCAR/MAR/MNAR logic)
- outliers
- encoding (one-hot/target)
- scaling
- rare category merging
- feature engineering
- class balancing
- train/validation/test split

**Why:** This matches the exact workflow recommended in the data preparation lecture.

## 7) Exploratory analysis
**What:** Crosstabs and distributions for key predictors vs the target.  
**Why:** This provides early evidence of potential causes and helps decide which variables to model.

## 8) Modeling
**What:** Placeholders for the correct model type:
- Linear / multiple regression for continuous targets
- Logistic regression for binary targets
- Poisson regression for count targets

**Why:** These are the core modeling methods taught in Topics 3, 4, 6b, and 6c.

## 9) Variable selection and regularization (Topic 7)
**What:** Placeholder to reduce irrelevant variables using selection criteria or regularization.  
**Why:** The lecture stresses parsimony, interpretability, and stable estimation.

## 10) Random Forest feature importance (Topic 11)
**What:** Optional step to rank important variables.  
**Why:** Random Forests provide a non-linear cross-check on which features matter most.

## 11) Reporting
**What:** In your final report, describe associations, effect sizes (e.g., Odds Ratios), and limitations.  
**Why:** This matches the course focus on correct statistical interpretation, not causal proof.
