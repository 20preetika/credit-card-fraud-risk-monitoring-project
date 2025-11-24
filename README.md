# 🧾 Credit Card Fraud & Risk Monitoring Dashboard

## 🎯 Problem Statement

Banks process millions of credit card transactions every day.  
Only a tiny percentage are fraudulent, but they cause huge financial loss and break customer trust.

Fraud analysts need:

- A **fast way to flag high-risk transactions**
- A **risk score** instead of a simple yes/no
- Clear **KPIs and dashboards** for monitoring fraud patterns

This project builds an **end-to-end fraud risk pipeline** using Python, SQL, Machine Learning and Power BI.

---

## 📌 Project Overview

**Goal:**  
Score every transaction with a fraud **risk score (0–1)**, bucket it into **High / Medium / Low**, and monitor fraud using an interactive Power BI dashboard.

**What I built:**

- **Data pipeline** to clean ~1.3M credit card transactions
- **Feature store in SQLite** (`model_features` view)
- **Logistic Regression model** to predict fraud probability
- **Risk buckets**:
  - High: risk_score > 0.40  
  - Medium: 0.15 < risk_score ≤ 0.40  
  - Low: risk_score ≤ 0.15
- **Power BI dashboard** to monitor:
  - Overall fraud rate
  - High-risk volumes
  - Top risky merchants & categories
  - Fraud pattern by state, gender, time, and distance

---

## 📂 Dataset Details

- **Source:** Synthetic credit card transactions (Kaggle-style dataset)
- **Rows used:** ~1,296,675 transactions
- **Target column:** `is_fraud` (0 = genuine, 1 = fraud)

**Key Columns Used**

- Transaction: `amt`, `unix_time`
- Customer: `age`, `gender`, `state`, `city_pop`
- Merchant: `merchant`, `category`, `merch_lat`, `merch_long`
- Location: `lat`, `long`
- Engineered:
  - `amt_log` (log of amount)
  - `distance_km` (customer → merchant distance using Haversine)
  - `time_of_day` (Morning / Afternoon / Evening / Night)
  - `risk_score`, `risk_bucket`

> Note: For GitHub, I only uploaded a **sample file**:  
> `fraud_full_predictions_sample.csv` (subset of the full scored data).

---

## 🛠 Tech Stack

| Area          | Tools & Libraries                         |
|---------------|-------------------------------------------|
| Data Cleaning | Python (Pandas, NumPy)                    |
| Feature Store | SQLite + SQLAlchemy                       |
| ML Modeling   | scikit-learn (LogisticRegression)         |
| Evaluation    | precision, recall, F1, ROC-AUC, PR-AUC    |
| Dashboard     | Power BI                                  |
| Versioning    | Git & GitHub                              |

---

## 🔍 Modeling Approach

### 1️⃣ Data Preparation

- Loaded raw CSV (`fraudTrain.csv`) in **chunks** to handle size
- Cleaned and transformed into `train_clean_full.parquet`
- Created a SQL feature table **`model_features`** with:
  - `amt`, `category`, `merchant`, `gender`, `state`, `city_pop`
  - `age`, `unix_time`, `hour`, `month`, `is_weekend`
  - `lat`, `long`, `merch_lat`, `merch_long`
  - `is_fraud` (label)

### 2️⃣ Feature Engineering

In the notebook (`CREDITCARD_JUPYTER.ipynb`):

- `amt_log` = log-transform of amount
- `distance_km` using Haversine formula
- `time_of_day` from `hour`:
  - Night / Morning / Afternoon / Evening
- Encodings:
  - **Frequency encoding** for `merchant`, `category`
  - **Label encoding** for `gender`, `state`, `time_of_day`
- Train / test split with **stratification on `is_fraud`**

### 3️⃣ Model

Used a simple **Logistic Regression**:

```python
model = LogisticRegression(
    class_weight="balanced",
    max_iter=300,
    n_jobs=-1
) ```


Reason for choice:

Works well for imbalanced, tabular data

Fast to train on 1.3M+ rows

Outputs probabilities → easy to convert to risk scores

Easy to explain in interviews & to business users'''

4️⃣ Metrics (on test set)

Precision: ~0.084

Recall: ~0.75

F1-score: ~0.15

ROC-AUC: ~0.89

PR-AUC: ~0.20

Given the dataset is highly imbalanced, the model is tuned to catch as many frauds as possible (high recall), accepting more false positives.

### 🎯 Power BI Dashboard

This dashboard uses the model predictions to monitor fraud risk in (near) real-time.  
It helps identify suspicious activity, high-risk customer segments, and trends over time.

#### 🔑 Key KPIs

| Metric                       | Description                                                     |
|-----------------------------|-----------------------------------------------------------------|
| Total Transactions          | Total number of processed credit card transactions              |
| Total Frauds                | Count of confirmed fraudulent transactions                      |
| Overall Fraud Rate (%)      | Fraud proportion across all transactions                        |
| High-Risk Transaction Count | Transactions classified in the **"High"** risk bucket           |
| High-Risk Fraud Rate (%)    | Fraud rate within the High-risk bucket                          |


