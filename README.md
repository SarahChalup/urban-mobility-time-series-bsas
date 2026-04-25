# 🚇 Urban Mobility Time Series Analysis — Buenos Aires (2013–2025)

Academic project for the Time Series Analysis course of the Master's in Data Science 
at Universidad Austral (Buenos Aires, Argentina).

## 📌 Overview

This project analyzes the temporal dynamics of urban mobility in the City of Buenos Aires 
through three monthly time series:

- **Subway passengers** (GCBA/Sbase, 2013–2025)
- **Vehicular flow at AUSA toll stations** (GCBA/AUSA, 2013–2025)
- **Monthly precipitation** (Open-Meteo, 2013–2025)

The main objective is to model the temporal structure of mobility series using the 
Box-Jenkins methodology, quantify the structural impact of COVID-19, and generate 
short-term forecasts for 2026.

---

## 🛠️ Tools & Libraries

| Category | Tools |
|---|---|
| Data wrangling | `pandas`, `numpy` |
| Visualization | `matplotlib`, `seaborn` |
| Time series modeling | `statsmodels` (SARIMAX) |
| Statistical tests | `adfuller`, `kpss`, `acorr_ljungbox`, `jarque_bera` |
| Metrics | `sklearn.metrics` |
| Data sources | `requests`, `zipfile`, `io` |



---

## 🔬 Methodology

### 1. Data Collection & Processing
Raw data is downloaded programmatically from open government portals (GCBA) and 
public APIs (Open-Meteo). Significant preprocessing was required to handle format 
inconsistencies across years, including changing delimiters, encoding issues (BOM UTF-8), 
varying column schemas, and a RAR file disguised as ZIP (2025 toll data).

### 2. Stationarity Analysis
- Visual inspection of rolling mean and variance (12-month window)
- Formal unit root testing: **ADF** and **KPSS** tests
- Log transformation to stabilize variance
- Regular differencing (d=1) and seasonal differencing (D=1, s=12)

### 3. Model Identification & Selection
- ACF and PACF analysis across three differentiation stages
- Grid search over SARIMA(p,d,q)(P,D,Q)[12] combinations:
  - p, q ∈ {0, 1, 2} · P, Q ∈ {0, 1} → 36 combinations per series
- Model selection by minimum AIC, verified with BIC
- Exogenous variables: monthly precipitation and COVID-19 binary dummy

### 4. Selected Models
| Series | Model | AIC |
|---|---|---|
| Subway | SARIMA(2,1,1)(0,1,1)[12]-X | −23.93 |
| Tolls | SARIMA(0,1,0)(0,1,1)[12]-X | −12.76 |

### 5. Out-of-Sample Evaluation
- Train: June 2013 – December 2023
- Test: January 2024 – December 2025
- Two forecasting strategies: **static forecast** and **Rolling Forecast Origin**
- Benchmarks: SARIMA without exogenous variables and Seasonal Naïve

### 6. SARIMAX Extension
An extended SARIMAX model for subway demand incorporates **monthly temperature** 
and **national holidays** as alternative exogenous regressors, exploring whether 
calendar and climate variables improve model fit at monthly frequency.

### 7. Forecasting
Models retrained on the full sample generate 12-month ahead forecasts for 2026, 
with 95% confidence intervals reported in original scale (exp of log forecast).

---

## 📊 Key Findings

- Subway and toll series are non-stationary and require d=1, D=1, s=12
- COVID-19 reduced subway demand by **~56%** (dummy coefficient significant at p<0.001)
- Precipitation is **not significant** at monthly frequency in either series
- Rolling Forecast Origin outperforms static forecast (MAPE: 12.0% vs 16.0% for subway)
- Seasonal Naïve is competitive with parametric models in the post-COVID period

---

## ▶️ How to Run

1. Clone the repository
2. Install dependencies:
```bash
pip install pandas numpy matplotlib seaborn statsmodels scikit-learn requests
```
3. Open `tp1-AST-movilidad-urbana.ipynb`
4. Run the **Quick Load** cell if data has been previously downloaded, 
   or run Section 1 to download all raw data from source

> ⚠️ Full data download may take several minutes due to large file sizes 
> (AUSA 2021 CSV is ~197MB). Annual files are cached locally after first download.

---

## 👥 Authors

- Alvaro, Giuliana
- Chalup, Sarah
- Fontán, Mariana  
- Franco, Agustina

*Master's in Data Science — Universidad Austral, Buenos Aires, 2026*
