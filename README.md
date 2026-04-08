# 📊 Hybrid Prophet-XGBoost Model for Weekly Leptospirosis Forecasting

This repository contains the implementation and research for a **Hybrid Prophet-XGBoost Residual Learning Framework** designed to forecast weekly leptospirosis cases in the Philippines.

📢 **This research paper has been officially accepted at CSPA 2026 (Conference on Signal Processing and Applications).**

---

## 🎥 Video Presentation
You can watch the full presentation of the paper here:

👉 https://youtu.be/cXsWAeYOQas

---

## 📌 Overview

Forecasting infectious diseases like leptospirosis is challenging due to:

- Strong **seasonality** (e.g., monsoon season)
- Sudden **outbreak spikes**
- **Non-stationary behavior** of epidemiological signals

Traditional models (e.g., SARIMA, Prophet) capture trends well but often **fail to detect outbreak intensity**.

### 💡 Proposed Solution
We developed a **Hybrid Model** that combines:

- **Prophet** → captures trend and seasonality  
- **XGBoost** → learns and corrects residual errors (especially outbreak spikes)

This allows the model to remain:
- ✅ Accurate  
- ✅ Robust during outbreaks  
- ✅ Interpretable  

---

## 📂 Data Source

The dataset used in this study is publicly available from Mendeley Data:

🔗 https://data.mendeley.com/datasets/v98325dpxv/1

**Citation:**
> Parreño, S. J. (2023). *Philippine Epidemiological Data*. Mendeley Data. https://doi.org/10.17632/v98325dpxv.1

### 📊 Dataset Description
- Weekly disease case counts in the Philippines (2017–2023)
- Derived from:
  - Epidemiology and Disease Control Surveillance (EDCS)
  - Philippine Integrated Disease Surveillance and Response (PIDSR)
- Used subset:
  - Weekly leptospirosis cases (2019–2023)
  - 260 observations

---

## ⚙️ Methodology

### 1. Data Preprocessing

A **context-aware imputation strategy** was applied:

- Seasonal averaging (excluding pandemic anomalies)
- Domain-informed adjustments:
  - Dampened values during COVID lockdowns
  - Full seasonal weights during monsoon season
- Linear interpolation for isolated gaps

---

### 2. Model Architecture

#### 🔹 Step 1: Prophet (Baseline Model)
- Models:
  - Trend (piecewise linear)
  - Weekly + yearly seasonality
- Uses multiplicative seasonality

---

#### 🔹 Step 2: Residual Learning (XGBoost)

Instead of predicting cases directly:

r[t] = y[t] - ŷ_base[t]

XGBoost learns residuals using:
- Prophet predictions (`yhat`)
- Week number (`WeekNum`)

---

#### 🔹 Step 3: Forecast Reconstruction

ŷ_final = max(0, ŷ_base + r̂)

- Combines baseline + correction
- Ensures non-negative outputs using ReLU

---

### 3. Evaluation Framework

- **Rolling-Origin Cross Validation**
  - Initial training: 115 weeks
  - Forecast horizon: 2 weeks
  - Sliding window: +16 weeks

- Benchmarks:
  - Seasonal Naive (SNaive)
  - SARIMA
  - Prophet

- Statistical validation:
  - Diebold-Mariano Test

---

## 📈 Results

### 🔹 Overall Performance

| Model | MAE | RMSE | SMAPE | Bias |
|------|-----|------|-------|------|
| **Hybrid (Prophet + XGBoost)** | **16.72** | **21.93** | **38.80** | **-0.72** |
| Prophet | 23.68 | 28.16 | 50.66 | 1.05 |
| SARIMA | 31.58 | 45.42 | 51.71 | -4.81 |
| SNaive | 64.18 | 97.56 | 93.65 | -34.47 |

✅ Improvements over Prophet:
- **MAE ↓ ~30%**
- **RMSE ↓ ~23%**
- **SMAPE ↓ ~24%**

---

### 🔥 Performance During Outbreaks (90th Percentile)

| Model | MAE | RMSE | SMAPE | Bias |
|------|-----|------|-------|------|
| **Hybrid** | **7.74** | **7.90** | **2.82** | -1.56 |
| SARIMA | 36.68 | 48.59 | 13.26 | -36.68 |
| Prophet | 51.66 | 52.50 | 19.11 | -9.33 |
| SNaive | 180.50 | 218.14 | 103.81 | -122.50 |

👉 The Hybrid model is the **only model that improved during peak outbreaks**, showing strong robustness under high-intensity conditions.

---

### 📊 Statistical Significance (Diebold-Mariano Test)

| Comparison | DM Statistic | p-value |
|-----------|-------------|--------|
| Hybrid vs Prophet | -1.8242 | 0.0429 |
| Hybrid vs SARIMA | -2.2669 | 0.0184 |
| Hybrid vs SNaive | -2.6524 | 0.0084 |

✅ All results are **statistically significant (p < 0.05)**  
→ Confirms that improvements are not due to chance.

---

### 📉 Residual Diagnostics & Stability

- Hybrid model shows:
  - Reduced **heavy-tail errors**
  - Better alignment with normal residual distribution
  - Lower variance across validation folds

👉 Indicates:
- Improved robustness
- Better handling of extreme outbreak errors

---

### 📊 Forecast Behavior

- Hybrid model:
  - Closely tracks actual case counts
  - Improves predictions over time via rolling validation
  - Captures outbreak peaks more accurately than all baselines

---

## 🧠 Explainable AI (SHAP Results)

To interpret the XGBoost residual corrections, **SHAP values** were used.

### 🔹 Feature 1: Baseline Prediction (ŷ)

- **ŷ < ~110**
  - Minimal corrections  
  → Prophet performs well in low-case regions

- **ŷ ≈ 110–200**
  - Negative SHAP values  
  → Model reduces predictions (Prophet overestimates)

- **ŷ > 200**
  - Positive SHAP values  
  → Model increases predictions (Prophet underestimates outbreaks)

👉 Insight:
- XGBoost **amplifies outbreak intensity** when needed

---

### 🔹 Feature 2: Week Number (Seasonality)

- **Weeks 1–10**
  - Negative SHAP values  
  → Overestimation corrected downward

- **Weeks 11–35**
  - Near-zero SHAP values  
  → Minimal correction needed

- **Weeks 35–52**
  - Increasing positive SHAP values  
  → Strong upward corrections

👉 Insight:
- Model detects **late-season outbreak risk** (monsoon + flooding effects)

---

### 🔍 Key Interpretation

- Positive SHAP → increase predicted cases  
- Negative SHAP → decrease predicted cases  

👉 The model:
- Corrects **systematic bias in Prophet**
- Learns **nonlinear environmental triggers**
- Maintains **interpretability despite hybrid design**

---

## 🧠 Key Insights

- Prophet captures **trend and seasonality**
- XGBoost captures **nonlinear outbreak behavior**
- Hybrid model:
  - Reduces extreme forecast errors
  - Improves temporal stability
  - Excels during outbreaks
  - Remains interpretable via SHAP

---

## 🚀 Future Work

- Explore:
  - SARIMA + ML hybrids
  - LSTM / SVM residual models
- Incorporate:
  - Weather data (rainfall, flooding)
  - Regional datasets
- Extend to other diseases

---

## 📚 Citation

If you use this work, please cite:
