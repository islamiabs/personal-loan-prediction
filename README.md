# 🏦 Universal Bank — Personal Loan Campaign Analysis

![Python](https://img.shields.io/badge/Python-3.10+-blue?logo=python&logoColor=white)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange?logo=jupyter&logoColor=white)
![scikit-learn](https://img.shields.io/badge/scikit--learn-1.3-green?logo=scikit-learn&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-lightgrey)

## 📌 Project Overview

Universal Bank wants to convert its liability customers (depositors) into personal loan customers.  
A previous campaign achieved a **9.6% conversion rate**.  
The goal of this project is to **identify which customer profiles are most likely to accept a personal loan offer**, so the bank can run smarter, more targeted marketing campaigns.

This project covers the full data mining pipeline:
- Exploratory Data Analysis
- Customer Segmentation with K-Means Clustering
- Predictive Modeling with Decision Tree & Random Forest

---

## 📂 Repository Structure

```
universal-bank-data-mining/
│
├── notebook.ipynb          # Main analysis notebook (fully documented)
├── UniversalBank.csv       # Dataset (5,000 customers)
├── requirements.txt        # Python dependencies
└── README.md               # This file
```

---

## 📊 Dataset

The dataset contains **5,000 customer records** with the following features:

| Feature | Description |
|---------|-------------|
| `Age` | Customer age (years) |
| `Experience` | Years of professional experience |
| `Income` | Annual income ($K) |
| `Family` | Family size (1–4) |
| `CCAvg` | Average credit card spending per month ($K) |
| `Education` | 1 = Undergrad, 2 = Graduate, 3 = Professional |
| `Mortgage` | Mortgage value ($K) |
| `Securities Account` | Has a securities account (0/1) |
| `CD Account` | Has a certificate of deposit account (0/1) |
| `Online` | Uses internet banking (0/1) |
| `CreditCard` | Has a bank-issued credit card (0/1) |
| `Personal Loan` ⭐ | **Target** — accepted loan offer (1) or not (0) |

---

## 🔬 Methodology

### 1. Data Cleaning
- Removed the `ID` column (non-informative)
- Removed `ZIP Code` (not predictive at this scale)
- Dropped 52 rows with negative `Experience` values (data entry errors)

### 2. Exploratory Data Analysis
- Target class distribution (class imbalance: 9.7% positive)
- Distribution of continuous features split by loan acceptance
- Acceptance rate per categorical feature (Education, Family, CD Account...)

### 3. Correlation Analysis
- Heatmap of all features
- Top predictors: Income, CCAvg, CD Account, Education

### 4. K-Means Clustering
- Elbow method to select optimal k
- k = 4 clusters based on financial profile
- Cluster interpretation and loan acceptance rate per segment

### 5. Predictive Modeling
- Train/test split (80/20, stratified)
- **Decision Tree** (max_depth=5, class_weight='balanced')
- **Random Forest** (200 trees, max_depth=10, class_weight='balanced')
- Evaluation: accuracy, precision, recall, F1-score, confusion matrix
- Feature importance analysis

---

## 📈 Key Results

| Model | Accuracy |
|-------|----------|
| Decision Tree | ~84% |
| **Random Forest** | **~88%** ✅ |

**Top predictors of loan acceptance:**
1. 💰 **Income** — strongest signal
2. 💳 **CCAvg** — high spenders are more likely to take a loan
3. 🏦 **CD Account** — existing depositors show much higher acceptance
4. 🎓 **Education** — professionals accept more often

---

## 💡 Business Recommendations

- **Target high-income customers** (>$100K/year) who do not yet have a personal loan
- **Cross-sell to CD Account holders** — highest acceptance rate among all segments
- **Focus digitally** — online banking users are already engaged and easy to reach
- **Use the Random Forest model** to score and rank customers before each campaign

---

## 🚀 Getting Started

### Requirements

```bash
pip install -r requirements.txt
```

### Run the notebook

```bash
jupyter notebook notebook.ipynb
```

---

## 📦 Dependencies

```
pandas>=1.5
numpy>=1.23
matplotlib>=3.6
seaborn>=0.12
scikit-learn>=1.2
jupyter
nbformat
```

---

## 👤 Author

Made as part of a Data Mining portfolio project.  
Feel free to fork, star ⭐, or open an issue if you have suggestions!

---

## 📄 License

This project is open source and available under the [MIT License](LICENSE).
