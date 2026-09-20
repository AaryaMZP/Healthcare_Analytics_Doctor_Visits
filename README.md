# 🏥 Healthcare Analytics: Doctor Visits
Exploratory data analysis of a 5,190-patient healthcare survey dataset, examining how demographics, chronic illness, and insurance coverage drive doctor visit frequency.
### Understanding the Drivers of Healthcare Utilisation

An end-to-end exploratory data analysis (and predictive modelling) project on a cross-sectional health survey of **5,190 Australian individuals**, examining their healthcare utilisation, demographics, socioeconomic status, and insurance coverage.

**Central question:** *What individual and socioeconomic factors drive the number of doctor visits a person makes in a given period?*

---

## 📁 Repository Contents

| File | Description |
|---|---|
| `Healthcare_Analytics_Doctor_Visits.ipynb` | Clean, unexecuted analysis notebook (source of truth for the code). |
| `Healthcare_Analytics_Doctor_Visits_executed.ipynb` | Fully executed notebook with all outputs, charts, and printed results. |
| `Healthcare_Analytics_Report.html` | Standalone HTML export of the executed notebook — open directly in a browser, no setup required. |
| `1776250375-P2-Healthcare_Analytics_for_Doctor_Visits.csv` | Raw source dataset. |

---

## 📊 Dataset Dictionary

| Column | Type | Description |
|---|---|---|
| `visits` | int | Number of doctor visits in a 2-week reference period (target variable) |
| `gender` | categorical | `male` / `female` |
| `age` | float | Age ÷ 100 (e.g. 0.19 → 19 years) |
| `income` | float | Annual household income (unit-normalised) |
| `illness` | int | Number of illnesses in the past 2 weeks (0–5) |
| `reduced` | int | Number of days activity was reduced due to illness/injury |
| `health` | int | Self-assessed health status (0 = excellent, higher = worse) |
| `private` | binary | Holds private health insurance? (`yes`/`no`) |
| `freepoor` | binary | Free government insurance — low income? (`yes`/`no`) |
| `freerepat` | binary | Free government insurance — repatriate/veteran? (`yes`/`no`) |
| `nchronic` | binary | Has a non-limiting chronic condition? (`yes`/`no`) |
| `lchronic` | binary | Has a limiting chronic condition? (`yes`/`no`) |

---

## 🔍 Analysis Workflow

The notebook follows a structured, reproducible pipeline:

1. **Environment Setup** — Imports, plotting aesthetics/palette.
2. **Data Loading & First Look** — Shape, dtypes, summary statistics.
3. **Data Understanding** — Column-level numerical/categorical profiling.
4. **Data Preprocessing**
   - Missing value check (none found)
   - **Duplicate detection & removal** — 1,320 exact duplicate rows (25.5% of raw data) identified and dropped, leaving **3,870 clean records**
   - Feature engineering: decoded `age_years`, `age_group` bands, `income_quartile`, binary-encoded categorical flags
   - IQR-based outlier detection (outliers retained as genuine high-utilisation cases)
5. **Exploratory Data Analysis & Storytelling** — 11 narrative-driven sections with charts covering:
   - Zero-inflation of the `visits` distribution
   - Gender & age patterns in utilisation
   - Illness burden as a predictor
   - Income & insurance effects
   - Chronic illness impact
   - Reduced activity days as a severity proxy
   - Full correlation matrix
   - Self-rated health vs. care-seeking behaviour
   - Multivariate profile of high-utilisation patients (3+ visits)
   - Statistical significance tests (Mann-Whitney U, Kruskal-Wallis, Spearman correlation)
   - Visited vs. not-visited feature distributions (violin plots)
6. **Summary Dashboard** — Consolidated 6-panel overview of key metrics.
7. **Consolidated Findings & Recommendations** — Ranked table of key findings with statistical evidence.
8. **Predictive Modelling**
   - Logistic Regression (visit / no-visit classification) + Poisson Regression (visit count)
   - Random Forest Classifier (non-linear feature importance)
   - Model evaluation via ROC curves and confusion matrices on a held-out test set
9. **Final Conclusions** — Narrative summary tying findings to policy/operational recommendations.

---

## 🔑 Key Findings

| # | Finding | Evidence |
|---|---------|----------|
| 1 | **74% of individuals made zero visits** — severe zero-inflation | 2,880 / 3,870 = 74.4% after deduplication |
| 2 | **Reduced activity days is the strongest utilisation driver** | Spearman r = 0.40, p < 0.001 |
| 3 | **Illness count is the second-strongest driver** | r = 0.185, Kruskal-Wallis H = 189, p = 5.7e-39 |
| 4 | **Females visit significantly more than males** | Mann-Whitney U, p < 0.0001; gap widest in the 30–44 age band |
| 5 | **Low-income patients with free insurance visit most** | FreeRepat r = 0.12; income r = −0.08 (both p < 0.001) |
| 6 | **Limiting chronic conditions drive ~2× higher visit rates** | Mann-Whitney p < 0.0001 |
| 7 | **Poor self-rated health maps to more consultations** | r = 0.149, monotonic trend |
| 8 | **1,320 duplicate records detected and removed** | 25.5% of raw data was duplicated |

### Predictive Modelling Results
- **Random Forest** (AUC ≈ 0.66) outperformed **Logistic Regression** (AUC ≈ 0.61) on held-out test data for predicting whether a person visits a doctor at all.
- Both linear and tree-based models agree: **reduced activity days**, **illness count**, and **self-rated health** are the top drivers; **income/insurance status** contribute comparatively little to individual-level prediction, despite being significant at the population level.
- Given the heavy zero-inflation, a **Zero-Inflated Negative Binomial (ZINB) or two-part hurdle model** is recommended over standard OLS/Poisson for any deployment-grade forecasting.

---

## 💡 Strategic Recommendations

1. Use **zero-inflated count models** (ZINB / hurdle) for predictive work — OLS violates count-data assumptions here.
2. Prioritise **"reduced activity days"** as the top feature in any utilisation prediction model.
3. Target **care management programs** at older females with limiting chronic conditions and multiple illnesses — a predictable, identifiable high-need subgroup.
4. Investigate the **uninsured mid-income gap**, where cost barriers appear to suppress care-seeking despite potential need.
5. Use **self-rated health** as a low-cost, high-signal screening tool for utilisation risk.
6. **Audit data collection processes** — a 25.5% duplicate rate points to survey/entry quality issues worth addressing upstream.

---

## 🛠️ Tech Stack

- **Python** (pandas, numpy)
- **Visualization:** matplotlib, seaborn
- **Statistics:** scipy.stats (Mann-Whitney U, Kruskal-Wallis, Spearman correlation)
- **Modelling:** scikit-learn (LogisticRegression, PoissonRegressor, RandomForestClassifier, StratifiedKFold, ROC/AUC evaluation)

## ▶️ How to Run

```bash
pip install pandas numpy matplotlib seaborn scipy scikit-learn jupyter
jupyter notebook Healthcare_Analytics_Doctor_Visits.ipynb
```

Or simply open `Healthcare_Analytics_Report.html` in a browser to view the fully rendered analysis without running any code.

---

## 📖 Summary

Three-quarters of surveyed individuals had zero doctor visits in the two-week reference window — the system is carried by a small, identifiable minority. When people do visit, **illness severity** (captured by reduced activity days) is the trigger far more than demographics or income. **Chronic conditions** — especially limiting ones — create a high-need subgroup that drives disproportionate demand, while **insurance status** primarily removes cost barriers rather than reflecting underlying health need. These patterns point toward proactive, preventive care targeting for high-risk patients and zero-inflated modelling approaches for any future utilisation forecasting.
