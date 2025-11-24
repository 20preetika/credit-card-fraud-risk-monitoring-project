# 🧾 Credit Card Fraud & Risk Monitoring Dashboard

## 🎯 Problem Statement

Banks process millions of credit card transactions every day — while only a tiny percentage are fraudulent, they result in significant financial loss and broken customer trust.

Fraud analysts need:
- A **fast way to flag high-risk transactions**
- A **risk score instead of binary labeling**
- Clear **KPIs and dashboard insights** to detect patterns

This project builds an **end-to-end fraud monitoring pipeline** using Python, SQL, Machine Learning, and Power BI.

---

## 📌 Project Overview

**Goal:**  
Assign each transaction a fraud **risk score (0–1)**, bucket it into **High / Medium / Low**, and monitor patterns in an interactive dashboard.

**What was built:**
- ✔️ Full end-to-end dataset cleaning (~1.3M rows)
- ✔️ SQL feature store using **SQLite + SQLAlchemy**
- ✔️ ML model to calculate fraud probability
- ✔️ Categorization into risk buckets
- ✔️ Power BI dashboard for monitoring fraud behavior

---

## 📂 Dataset Details

- **Source:** Synthetic credit card transaction dataset
- **Total Rows Used:** ~1,296,675  
- **Target Variable:** `is_fraud` (0 = genuine, 1 = fraud)

**Key Features:**
- Transaction: `amt`, `unix_time`
- Customer: `gender`, `age`, `state`, `city_pop`
- Merchant: `merchant`, `category`, `merch_lat`, `merch_long`
- Engineered:
  - `amt_log` → log transformed amount  
  - `distance_km` → Haversine distance  
  - `time_of_day` → Morning / Afternoon / Evening / Night  
  - `risk_score`, `risk_bucket`

> For GitHub size constraints, I included **only a sample dataset**:  
> `fraud_sample.csv`

---

## 🛠 Tech Stack

| Category | Tools |
|---------|-------|
| Data Processing | Python, Pandas, NumPy |
| Feature Store | SQLite, SQLAlchemy |
| ML Model | Logistic Regression (scikit-learn) |
| Evaluation | Recall, Precision, F1, ROC-AUC, PR-AUC |
| Dashboard | Power BI |
| Version Control | Git & GitHub |

---

## 🚧 Modeling Workflow

### **1️⃣ Data Preparation**
- Loaded raw data in **chunks** due to file size
- Cleaned and transformed into parquet format
- Created a reusable SQL feature table

---

### **2️⃣ Feature Engineering**

Implemented in: `CreditCardFraud_EDA_and_Model.ipynb`

- Log transformation on skewed values (`amt_log`)
- Haversine distance calculation (`distance_km`)
- Time segmentation (`time_of_day`)
- Encoding strategy:
  - Frequency encoding → `merchant`, `category`
  - Label encoding → `gender`, `state`, `time_of_day`
- Stratified train-test split on `is_fraud`

---

### **3️⃣ Machine Learning Model**

Used a simple but explainable model:

```python
model = LogisticRegression(
    class_weight="balanced",
    max_iter=300,
    n_jobs=-1
)
### 🔍 Model Choice: Logistic Regression

Logistic Regression was selected because:

- ✔️ Works well for **imbalanced tabular datasets**
- ✔️ Produces **interpretable probability scores**
- ✔️ Fast to train even on **large datasets (~1.3M rows)**
- ✔️ Aligns with **real-world fraud risk scoring systems** used by banks

---

### 📈 Model Performance (Test Set)

| Metric | Score |
|--------|-------|
| Precision | ~0.084 |
| Recall | ~0.75 |
| F1 Score | ~0.15 |
| ROC-AUC | ~0.89 |
| PR-AUC | ~0.20 |

> The model is intentionally optimized for **high recall** to detect as many fraudulent transactions as possible — accepting more false positives to avoid missing true fraud cases.

---

## 📊 Power BI Dashboard

📁 File: `fraud_risk_dashboard.pbix`  
🖼 Preview: `fraud_risk_dashboard.png`

This dashboard uses the machine learning output (`risk_score`, `risk_bucket`) to monitor fraud behavior at scale.

---

### ⭐ Key Metrics Displayed

- **Total Transactions**
- **Total Fraud Transactions**
- **Fraud Rate (%)**
- **High-Risk Transaction Count**
- **High-Risk Fraud Rate (%)**

---

### 📌 Dashboard Visuals

| Visual | Purpose |
|--------|---------|
| 📈 **Fraud Trend Over Time** | Identify spikes and seasonal patterns |
| 🥧 **Risk Bucket Distribution** | Compare High vs Medium vs Low risk volumes |
| 🏬 **Top High-Risk Merchants** | Detect repeat suspicious merchant activity |
| 🛒 **Top High-Risk Categories** | See which purchase types drive fraud |
| 🗺 **Fraud by State (Map)** | Identify geographical fraud hotspots |
| 👩‍🦰 **Fraud Rate by Gender** | Analyze demographic behavior |
| 📏 **Fraud vs Distance** | Explore correlation between risk and travel distance |

---

### 🧠 Insights Summary

Key findings from the dashboard:

- 🔺 The **High-risk bucket contains most confirmed fraud cases**.
- 📍 Fraud is more likely when:
  - The **distance between customer and merchant increases**
  - Transactions occur during specific **high-risk time periods**
- 🏷 A **small group of merchants/categories are repeatedly linked** to fraud — indicating patterned behaviour.

---


