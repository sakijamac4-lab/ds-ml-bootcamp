# 📊 Final Project Proposal

---

# 1. Certificate Name

**Maryan Mohamed Adam**

---

# 2. Project Title and Description

## **Title**
**Machine Learning-Based Employment Status Prediction Using Somali Labour Force Data**

Employment is one of the most pressing socioeconomic challenges in Somalia, with many young people and women facing unemployment or remaining outside the labour force entirely.

This project builds a machine learning model that predicts an individual's employment status (**Employed, Unemployed, or Not in Labour Force**) from demographic and household characteristics.

The tool is intended for use by government agencies, NGOs, and development organizations that need fast, data-driven insight into labour market patterns to design and target employment interventions, rather than waiting on slow manual survey analysis.

---

# 3. Problem Type

> **Classification**

The target column, **`employment_status`**, has three categories:

- Employed
- Unemployed
- Not in Labour Force

Therefore, this is a **multiclass classification** problem.

---

# 4. Dataset

### Source

`lfs_somalia_synthetic_2000.csv`

Synthetic Somali labour force dataset modeled on the structure of the **Somali Labour Force Survey (SLFS 2019)**.

Reference:

https://microdata.nbs.gov.so/index.php/catalog/57

### Dataset Information

| Item | Description |
|------|-------------|
| **Rows** | 2,000 |
| **Columns** | 10 |
| **Target** | `employment_status` |
| **Problem Type** | Multiclass Classification |

### Target Distribution

| Class | Rows |
|------|------:|
| Employed | 1,104 |
| Unemployed | 541 |
| Not in Labour Force | 355 |

Classes are imbalanced, which will be addressed using:

- Class weighting
- Stratified sampling

### Main Features

- **`age`** — respondent's age in years
- **`gender`** — Male / Female
- **`region`** — one of 7 Somali regions (e.g., Banadir, Puntland, Somaliland)
- **`education`** — Primary, Secondary, Tertiary, Vocational, or missing (~25% missing, will be imputed/flagged)
- **`marital_status`** — Single, Married, Widowed, Divorced
- **`sector`** — industry sector of work (e.g., Trade, Agriculture, Fishing); mostly missing for non-workers, so it will be used only as a secondary/derived feature, not a core predictor.

### Excluded Features

The following variables will **not** be used as model inputs:

- `hours_worked`
- `monthly_wage_usd`

Checking the data shows that **`hours_worked = 0`** for every non-employed record and only for non-employed records. Including either feature would leak the answer directly into the model instead of allowing it to learn genuine patterns.

---

# 5. Algorithms You Plan to Train

### 1. Logistic Regression *(Bootcamp)*

- Simple and interpretable multiclass baseline.
- Good reference point before trying more complex models.

### 2. Decision Tree *(Bootcamp)*

- Produces an interpretable tree.
- Shows the exact split rules (age, education, region, etc.).
- Provides a comparison against Random Forest.

### 3. Random Forest *(Bootcamp)*

- Handles mixed categorical and numerical features.
- Captures nonlinear interactions.
- Requires minimal preprocessing.

### 4. XGBoost *(Self-Researched)*

- Gradient boosting model.
- Typically achieves the highest accuracy on structured/tabular datasets.
- Includes built-in support for handling class imbalance.

### Optional Exploratory Step (Unsupervised)

Before classifier training, **K-Means Clustering** will be applied using:

- age
- education
- region
- gender
- marital_status

Purpose:

- Explore natural demographic segments.
- Support feature engineering.
- Improve exploratory data analysis (EDA).

Since K-Means does **not** use the target variable (`employment_status`), it will **not** be compared using Accuracy, Precision, Recall, or Macro F1.

Instead, it will be evaluated using:

- **Silhouette Score**

---

# 6. Evaluation Plan

### Metrics

- Accuracy
- Precision
- Recall
- Macro F1-Score
- Confusion Matrix

### Model Selection

The best model will be selected using **Macro F1-Score**, because:

- the classes are imbalanced,
- Macro F1 evaluates every class equally,
- it prevents the majority class ("Employed") from dominating the evaluation.

---

# 7. Deployment Sketch

### Framework

**FastAPI**

### Endpoint

```http
POST /predict
```

### Input (JSON)

```json
{
  "age": 28,
  "gender": "Female",
  "region": "Banadir",
  "education": "Secondary",
  "marital_status": "Single"
}
```

### Output (JSON)

```json
{
  "predicted_status": "Unemployed",
  "probabilities": {
    "Employed": 0.22,
    "Unemployed": 0.61,
    "Not in Labour Force": 0.17
  },
  "model_used": "XGBoost"
}
```

---

# 8. Repository Plan

```text
maryan-dev-final-project/
├── dataset/
│   └── lfs_somalia_synthetic_2000.csv
├── src/
│   ├── preprocess.py
│   ├── train.py
│   └── evaluate.py
├── api/
│   └── app.py
├── models/
│   └── best_model.pkl
├── README.md
└── project_paper.md
```