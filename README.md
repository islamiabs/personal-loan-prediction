# 🏦 Universal Bank — Personal Loan Campaign Analysis

![Python](https://img.shields.io/badge/Python-3.10+-blue?logo=python&logoColor=white)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange?logo=jupyter&logoColor=white)
![scikit-learn](https://img.shields.io/badge/scikit--learn-1.3+-green?logo=scikit-learn&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-lightgrey)

## 📌 Project Overview

Universal Bank wants to convert its liability customers (depositors) into personal loan customers.
A previous campaign achieved a **9.6% conversion rate**.

The goal is to **score each customer by their probability of accepting a personal loan offer**, so the next campaign can contact fewer customers while reaching most of the acceptors.

Pipeline:
- Exploratory Data Analysis
- Customer segmentation with K-Means
- Predictive modeling: logistic regression baseline, decision tree, random forest, gradient boosting
- Threshold selection and campaign targeting (cumulative gains)

---

## 📂 Repository Structure

```
personal-loan-prediction/
│
├── notebook.ipynb          # Full analysis, executed (all figures and numbers are reproducible)
├── UniversalBank.csv       # Dataset (5,000 customers)
├── requirements.txt        # Python dependencies
└── README.md
```

---

## 📊 Dataset

5,000 customers, 14 columns, no missing values. Target: `PersonalLoan` — **480 acceptors (9.6%)**, i.e. imbalanced classes.

| Feature | Description |
|---------|-------------|
| `Age` | Customer age (years) |
| `Experience` | Years of professional experience — *dropped (see below)* |
| `Income` | Annual income ($K) |
| `ZIP Code` | Home address ZIP code — *dropped* |
| `Family` | Family size (1–4) |
| `CCAvg` | Average credit card spending per month ($K) |
| `Education` | 1 = Undergrad, 2 = Graduate, 3 = Advanced/Professional |
| `Mortgage` | Mortgage value ($K) |
| `SecuritiesAccount` | Has a securities account (0/1) |
| `CDAccount` | Has a certificate of deposit account (0/1) |
| `Online` | Uses internet banking (0/1) |
| `CreditCard` | Has a credit card issued by the bank (0/1) |
| `PersonalLoan` ⭐ | **Target** — accepted the loan offer (1) or not (0) |

---

## 🔬 Methodology

### 1. Cleaning
- `ID` dropped (identifier).
- `ZIP Code` dropped (467 distinct values for 5,000 rows — too sparse to be used directly).
- `Experience` dropped: 52 negative values **and** a 0.994 correlation with `Age`. Dropping the redundant column keeps all 5,000 customers instead of deleting rows.

### 2. Evaluation protocol
- Stratified 80/20 train/test split; the **test set is used only once**, at the end.
- **5-fold stratified cross-validation** on the training set for model comparison and tuning.
- Main metric: **average precision (PR-AUC)**, suited to imbalanced classes. Accuracy is not used for selection: predicting "no" for everyone already gives 90.4%.
- Decision threshold chosen by maximising F1 on **out-of-fold** training predictions.

### 3. Models
| Model | Preprocessing | Role |
|-------|---------------|------|
| Dummy (majority class) | — | Floor reference |
| Logistic regression | Standardisation + one-hot `Education` | Linear baseline |
| Decision tree (depth 5) | — | Interpretable rules |
| Random forest | — | Main model, tuned by grid search |
| Gradient boosting (HistGradientBoosting) | — | Challenger |

### 4. Segmentation
K-Means on standardised `Age, Income, CCAvg, Mortgage, Family, Education`; k chosen with the elbow curve and the silhouette score.

---

## 📈 Key Results

**Cross-validation (training set, 5 folds)**

| Model | Average precision | F1 (threshold 0.5) |
|-------|------|------|
| Dummy | 0.096 | 0.000 |
| Logistic regression | 0.840 ± 0.049 | 0.736 ± 0.055 |
| Decision tree | 0.945 ± 0.013 | 0.903 ± 0.013 |
| **Random forest** | **0.983 ± 0.007** | 0.917 ± 0.019 |
| Gradient boosting | 0.983 ± 0.004 | 0.925 ± 0.020 |

Tree-based ensembles clearly outperform the linear baseline: acceptance depends on **interactions** (income × education × family size). Random forest and gradient boosting are equivalent within CV variability; the random forest was kept and tuned (`max_depth=10`, 300 trees, no class weighting).

**Final random forest — test set (1,000 customers, 96 acceptors, threshold 0.38)**

| Average precision | Precision | Recall | F1 |
|---|---|---|---|
| 0.992 | 93.9% | 96.9% | 0.954 |

93 acceptors found, 3 missed, 6 false positives. With only 96 positives in the test set, the CV estimate (≈ 0.98 average precision) is the more reliable figure.

**Targeting:** ranking customers by score, the **top 10% contains 93 of the 96 acceptors (97%)**, with a 93% conversion rate in that group vs 9.6% overall.

**Main drivers** (permutation importance): **Income**, **Education**, **Family size**, then CCAvg.
- Income > $100K: 36.1% acceptance vs 1.1% below.
- Graduate / professional: ~13% vs 4.4% for undergraduates.
- `CDAccount` holders accept at 46.4%, but this signal is largely captured by the other variables in the model.
- `Online` and `CreditCard` show no difference in acceptance.

**Segmentation:** the two high-income clusters (mean income > $110K) show 26–32% acceptance vs 2–3% in the other two. Silhouette scores are low (< 0.3), so the segments are descriptive rather than natural clusters.

---

## 💡 Business Recommendations

- **Score all customers** with the random forest and contact them by decreasing score; a campaign on ~10–15% of the base reaches almost all likely acceptors.
- Prioritise **high-income, graduate/professional customers**, and larger families.
- Use **CD Account holders** as a cross-sell audience.

---

## ⚠️ Limitations & Next Steps

- Only 480 positives: metrics vary across splits.
- Threshold set by F1; with real costs (contact cost vs loan margin) it should maximise **expected profit**.
- Trained on one past campaign: a new offer or channel may change behaviour → monitor after deployment.
- Scores are used for ranking and are not calibrated probabilities.
- Next: profit-based threshold, probability calibration, SHAP explanations, logistic regression with interaction terms.

---

## 🚀 Getting Started

```bash
pip install -r requirements.txt
jupyter notebook notebook.ipynb
```

## 📦 Dependencies

```
pandas>=1.5
numpy>=1.23
matplotlib>=3.6
seaborn>=0.12
scikit-learn>=1.3
jupyter
```

---

## 👤 Author

Islamia Biloghe — [github.com/islamiabs](https://github.com/islamiabs)

## 📄 License

This project is open source and available under the [MIT License](LICENSE).
