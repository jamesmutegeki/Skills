---
name: data-engineering
description: Use for data analysis, data cleaning, statistical modeling, visualization, ETL pipelines, and data-driven decision making. Covers pandas, numpy, SQL, dbt, Jupyter, plotting libraries, and BI tooling.
---

# Data Analysis

## Principles

- **Reproducibility first**: every analysis must be scripted end-to-end. No manual steps in Excel or GUI tools.
- **Assert data quality early**: check for nulls, outliers, type mismatches, and distribution shifts before modeling.
- **Visualize to validate**: always plot distributions and relationships before statistical testing.

## Recommended Stack

| Layer | Tools |
|-------|-------|
| Querying | SQL (DuckDB, BigQuery, Postgres), dbt |
| Wrangling | pandas, polars, numpy |
| Stats | scipy, statsmodels, pingouin |
| ML | scikit-learn, xgboost, lightgbm |
| Visualization | matplotlib, seaborn, plotly, altair |
| Notebooks | Jupyter, marimo, quarto |
| Pipelines | dbt, dagster, prefect |

## Workflow

### 1. Exploratory Data Analysis (EDA)
```
df.info() / df.describe() / df.isnull().sum()
df[col].value_counts() / .hist() / .boxplot()
Correlation matrix + pairplot for numeric cols
```

### 2. Data Cleaning Checklist
- Handle missing values (impute, drop, or flag)
- Detect and treat outliers (IQR, Z-score, domain caps)
- Normalize/standardize features if needed
- Validate data types and parse dates
- Deduplicate records

### 3. Analysis Patterns

**Statistical Testing**
```python
from scipy import stats
# Normal? -> t-test / ANOVA, else Mann-Whitney / Kruskal
# Categorical? -> chi-square / fisher
```

**Time Series**
```python
# Decompose: trend + seasonality + residual
# Stationarity: ADF test
# Forecasting: ARIMA, Prophet, or LightGBM with lags
```

**Cohort / Segmentation**
```python
# RFM segmentation, k-means clustering, percentile bucketing
# Always validate with silhouette score or domain review
```

## Visualization Standards

- Use `seaborn` or `altair` for static, `plotly` for interactive
- Every chart needs: title, labeled axes, legend if multi-series
- Colorblind-friendly palettes (e.g., `viridis`, `colorblind10`)
- Avoid 3D charts and pie charts

## Reporting

- Prefer automated notebooks (Quarto / marimo) over manual slides
- Key outputs: executive summary (1 page), technical appendix, interactive dashboard if stakeholders need self-serve
- Track decisions and assumptions in a companion markdown doc
