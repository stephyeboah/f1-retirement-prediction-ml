# 🏎️ F1 Race Retirement Prediction — Machine Learning Project

> **Machine Learning Classification Project** | Master's in Data Science  
> Predicting whether an F1 driver will finish a race using pre-race data

![KNIME](https://img.shields.io/badge/KNIME-FFD800?style=for-the-badge&logo=knime&logoColor=black)
![ML](https://img.shields.io/badge/Machine%20Learning-Classification-blue?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge)

---

## 📌 Project Overview

In Formula 1, every decision matters — including whether a driver will make it to the finish line. This project applies **machine learning classification techniques** to predict, *before the race starts*, whether a driver will retire (due to crash or mechanical failure) or finish the race.

The model is designed to be applied after qualifying and before the race, using only data available at that point — driver history, team performance, circuit characteristics, and weather forecasts.

**Target variable:** `retirement_target` → 0 = finished, 1 = retired

---

## 👥 Team & Roles

| Name | Role |
|------|------|
| **Stephen Adu Poku Yeboah** | Data preprocessing — missing value imputation (Random Forest Regression & median imputation) |
| Emmanuel Ampah | EDA, data analysis |
| M. Aondio | Feature selection, model tuning |
| P. Russo | Model evaluation, lift analysis |
| G. Licciardello | Model implementation, results interpretation |

---

## 📊 Dataset

- **Source:** [Formula 1 World Championships (Kaggle)](https://www.kaggle.com/datasets/rohanrao/formula-1-world-championship-1950-2020) + weather/circuit data via [FastF1 API](https://docs.fastf1.dev)
- **Size:** 2,979 observations × 43 variables
- **Period:** 2018–2024 (7 seasons)
- **Train/Test split:** Trained on 6 seasons, tested on the 7th (realistic forecasting scenario)

### Key Features

| Category | Features |
|----------|---------|
| Driver info | age, experience, number of races |
| Race performance | grid position, qualifying gap, championship standing |
| Incident history | crash rate (Laplace-adjusted), recent mechanical issues |
| Circuit | number of corners, altitude |
| Weather | air/track temperature, pressure, % rainfall, wet/dry |

---

## 🔧 My Contribution — Data Preprocessing & Missing Value Imputation

The dataset contained several variables with significant missing data, particularly circuit-specific incident statistics. I handled the full **imputation pipeline** in KNIME:

### Missing Value Strategy
| Variable Type | Missing % | Method |
|--------------|-----------|--------|
| Circuit crash/race stats | ~19% | **Random Forest Regression** — predicted from patterns in other variables |
| `historical_%crashes_circuit` | 6% | Random Forest Regression |
| `recent%crashes`, `recent_laplace_crashes` | ~5% | Random Forest Regression |
| `q1_gap` | 1% | **Median imputation** |
| `prevraces_positiondiff` | 0.63% | **Median imputation** |

- Variance checks were performed **after imputation** to confirm the distributions remained stable
- The same trained imputation model was applied consistently to both training and test sets to avoid data leakage
- Rows from the **first race of each year** were removed due to disproportionately high missing values

### Additional Preprocessing Steps (team)
- Multicollinearity assessment — removed highly correlated features (r > 0.7 or r < -0.7)
- Feature selection via Random Forest importance scores (threshold ≥ 30)
- Class imbalance handled with a **hybrid SMOTE + undersampling** approach on training set only

---

## 🤖 Models Tested

All models were implemented and tuned in **KNIME Analytics Platform** with 5-fold stratified cross-validation:

| Model | Approach |
|-------|---------|
| Naive Bayes (NB) | Baseline probabilistic classifier |
| Logistic Regression (LR) | Linear with L2 regularization |
| K-Nearest Neighbors (KNN) | k tuned from 3–15 |
| Support Vector Machine (SVM) | RBF kernel, Platt scaling |
| Decision Tree (DT) | Gini index, max depth tuned |
| Random Forest (RF) | Ensemble, cost-sensitive learning |
| Gradient Boosting (GB) | Sequential boosting, low learning rate |
| Multi-Layer Perceptron (MLP) | ReLU + dropout regularization |

---

## 📈 Results

### Cross-Validation Performance (Top Models)

| Model | AUC | Class 1 Precision | Class 1 Recall |
|-------|-----|-------------------|----------------|
| SVM | 0.823 | 0.488 | **0.997** |
| MLP | 0.818 | 0.833 | 0.754 |
| KNN | 0.813 | 0.896 | 0.618 |

### Test Set Performance (7th Season)

| Model | AUC | Class 1 Precision | Class 1 Recall |
|-------|-----|-------------------|----------------|
| **NB** | **0.622** | 0.636 | 0.150 |
| LR | 0.608 | 0.764 | 0.172 |
| KNN | 0.602 | 0.673 | 0.146 |

> ⚠️ Complex models (SVM, RF, GB, MLP) overfitted the training data heavily and underperformed on the test set. Simpler models (NB, LR, KNN) generalized better.

### Final Model: Naive Bayes
Selected based on highest Lift Factor (LF = 2.0 at 50th percentile) — best at early identification of likely retirements, which aligns with the project goal of **preventing** retirements before the race.

---

## ⚠️ Key Challenges

- **Class imbalance:** ~85% of drivers finish races — minority class (retirements) was hard to predict
- **Overfitting:** Complex models excelled in cross-validation but failed to generalize to new seasons
- **External factors:** Car reliability, driver confidence, and single-component failures are largely unquantifiable
- **FIA regulation changes:** Annual rule changes alter car behavior and retirement causes across seasons
- **Unpredictability:** Some retirements (e.g., animal on track, random collisions) are inherently impossible to predict

---

## 🛠️ Tools & Technologies

| Tool | Purpose |
|------|---------|
| KNIME Analytics Platform | Full ML pipeline — EDA, preprocessing, modelling, evaluation |
| FastF1 API | Weather and circuit data collection |
| Kaggle Dataset | Base F1 race data (1950–2024) |

---

## 📁 Repository Structure

```
f1-retirement-prediction/
├── README.md
├── F1_ML_workflow.knwf        # Exported KNIME workflow (open with KNIME)
└── ML_F1_Report.pdf           # Full project report
```

---

## 🚀 How to Open the Workflow

1. Download [KNIME Analytics Platform](https://www.knime.com/downloads) (free)
2. Open KNIME → **File** → **Import KNIME Workflow**
3. Select `F1_ML_workflow.knwf`

> The workflow includes all nodes for: data loading, imputation, feature selection, SMOTE balancing, model training, cross-validation, ROC curves, and lift analysis.

---

## 📄 License

Academic project — Università degli Studi di Milano-Bicocca, Data Science MSc.
