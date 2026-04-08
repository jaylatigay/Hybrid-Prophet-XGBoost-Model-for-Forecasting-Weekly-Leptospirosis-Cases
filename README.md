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
- Includes multiple diseases such as:
  - Leptospirosis
  - Dengue
  - Measles
  - Rabies, and more

- Used subset:
  - Weekly leptospirosis cases (2019–2023)
  - 260 observations

This dataset is compiled from nationwide surveillance reports by the Department of Health (DOH) and is widely used for epidemiological modeling and forecasting. :contentReference[oaicite:0]{index=0}

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

### 4. Explainability (XAI)

Used **SHAP (SHapley Additive Explanations)** to interpret:

- When predictions are increased (underestimation)
- When predictions are decreased (overestimation)

---

## 📈 Results

### 🔹 Overall Performance

| Model | MAE | RMSE | SMAPE |
|------|-----|------|-------|
| **Hybrid (Prophet + XGBoost)** | **16.72** | **21.93** | **38.80** |
| Prophet | 23.68 | 28.16 | 50.66 |
| SARIMA | 31.58 | 45.42 | 51.71 |
| SNaive | 64.18 | 97.56 | 93.65 |

✅ Improvements over Prophet:
- **MAE ↓ ~30%**
- **RMSE ↓ ~23%**
- **SMAPE ↓ ~24%**

---

### 🔥 Performance During Outbreaks

| Model | MAE (Outbreaks) |
|------|----------------|
| **Hybrid** | **7.74** |
| SARIMA | 36.68 |
| Prophet | 51.66 |
| SNaive | 180.50 |

👉 The Hybrid model is the **only model that improved during peak outbreaks**.

---

### 📊 Statistical Significance

- Diebold-Mariano test confirms:
  - Hybrid model significantly outperforms all baselines  
  - (p-values < 0.05)

---

## 🧠 Key Insights

- Prophet captures **trend and seasonality**
- XGBoost corrects **nonlinear residual errors**
- Hybrid approach:
  - Reduces extreme forecast errors
  - Improves temporal stability
  - Excels during outbreak periods

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
