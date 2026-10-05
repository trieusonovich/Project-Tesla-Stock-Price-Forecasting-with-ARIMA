# Project-Tesla-Stock-Price-Forecasting-with-ARIMA

![Python](https://img.shields.io/badge/Python-3.10+-blue?logo=python&logoColor=white)
![statsmodels](https://img.shields.io/badge/statsmodels-ARIMA-informational)
![pandas](https://img.shields.io/badge/pandas-data%20analysis-150458?logo=pandas&logoColor=white)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?logo=jupyter&logoColor=white)

A financial econometrics project that models and forecasts Tesla's monthly closing stock price using the Box-Jenkins ARIMA methodology. The workflow covers exploratory analysis, trend regression with residual diagnostics, stationarity testing, model selection, and a 12-month forecast.
---

## Table of Contents

- [Overview](#overview)
- [Dataset](#dataset)
- [Methodology](#methodology)
- [Key Results](#key-results)
- [Forecast](#forecast)
- [Limitations](#limitations)
- [Repository Structure](#repository-structure)
- [Getting Started](#getting-started)
- [Tech Stack](#tech-stack)
- [Author](#author)

---

## Overview

Stock prices are driven by many macro- and microeconomic factors, which makes forecasting them difficult. This project applies time series econometrics to answer a focused question: **can an ARIMA model capture the dynamics of Tesla's price well enough to produce a useful short-term reference forecast?**

**Objectives**

1. Collect and explore 10+ years of Tesla price data
2. Fit a linear time-trend regression and diagnose its residuals
3. Test the series for stationarity and apply the transformation needed
4. Identify, estimate and compare ARIMA models
5. Evaluate forecast accuracy and produce a 12-month forecast

## Dataset

| Property | Value |
|---|---|
| Frequency | Monthly closing price |
| Period | January 2015 – December 2025 |
| Observations | 132 |
| File | `data/Tesla_stock_data_for_10_years.xlsx` |
| Columns | `Date`, `Price` |

**Descriptive statistics**

| Mean | Median | Std. Dev. | Min | Max | Skewness | Kurtosis |
|---|---|---|---|---|---|---|
| 140.25 | 83.69 | 132.96 | 12.34 | 456.56 | 0.57 | -0.95 |

The price distribution is right-skewed: the mean sits well above the median because a strong rally in the last few years pulls the upper tail.

## Methodology

**1. Exploratory analysis**
Time series plot and histogram of closing prices, plus descriptive statistics.

**2. Time-trend regression (OLS)**
`Price = β₀ + β₁·t + ε`, followed by residual diagnostics (Durbin-Watson for autocorrelation, a heteroskedasticity test).

**3. Stationarity testing**
ADF and KPSS tests on the price level and on the first difference.

**4. ARIMA identification and estimation**
ACF/PACF inspection of the differenced series, then model comparison using AIC and BIC.

**5. Evaluation and forecasting**
Ljung-Box test on residuals, error metrics (MSE, MAE, MAPE), and a 12-month-ahead forecast with standard errors.

## Key Results

### Trend regression

| Metric | Value |
|---|---|
| Equation | `Price = -59.61 + 3.005·t` |
| R² | 0.7475 |
| F-statistic | 384.91 (p ≈ 1.1e-40) |
| Durbin-Watson | 0.216 |

The linear trend explains about 75% of price variation, but the very low Durbin-Watson statistic shows strong positive autocorrelation in the residuals, and the heteroskedasticity test (p = 0.003) rejects constant variance. OLS standard errors are therefore unreliable, which motivates a proper time series model.

### Stationarity

| Series | ADF p-value | KPSS result | Conclusion |
|---|---|---|---|
| Price (level) | 0.502 | Rejected at 1% | Non-stationary |
| First difference | 6.5e-06 | Not rejected at 10% | Stationary |

The series is integrated of order 1, so `d = 1`.

### Selected model: ARIMA(4, 1, 0)

| Metric | Value |
|---|---|
| AIC | 1263.45 |
| BIC | 1277.82 |
| Ljung-Box (12 lags) | Q = 8.89, p = 0.352 |
| MAE | 17.38 USD |
| MAPE | 11.88% |
| MSE | 834.33 |

ARIMA(4,1,0) had the lowest AIC and BIC among the candidates tested. The AR(3) and AR(4) terms are significant at the 1% level. The Ljung-Box test finds no remaining autocorrelation, so the residuals behave like white noise.

Fitted model on the differenced series:

```
Δyₜ = 0.108·Δyₜ₋₁ − 0.135·Δyₜ₋₂ − 0.212·Δyₜ₋₃ + 0.262·Δyₜ₋₄ + εₜ
```

## Forecast

12-month-ahead forecast (January – December 2026):

| Month | Forecast (USD) | Std. Error |
|---|---|---|
| Jan 2026 | 481.93 | 28.89 |
| Feb 2026 | 491.48 | 43.11 |
| Mar 2026 | 477.08 | 51.64 |
| Apr 2026 | 472.52 | 55.93 |
| May 2026 | 480.39 | 62.61 |
| Jun 2026 | 487.42 | 70.02 |
| Jul 2026 | 484.30 | 76.72 |
| Aug 2026 | 480.15 | 81.56 |
| Sep 2026 | 480.70 | 86.35 |
| Oct 2026 | 483.82 | 91.33 |
| Nov 2026 | 484.15 | 96.32 |
| Dec 2026 | 482.56 | 100.71 |

The standard error grows with the horizon, which is expected: uncertainty compounds the further ahead the model looks.

## Limitations

- **Univariate model.** It uses only past prices and ignores earnings, macro conditions and news.
- **Short-horizon use only.** Forecasts are roughly flat and their uncertainty widens quickly, so they are a reference point, not a trading signal.
- **Reported errors are in-sample.** MAE and MAPE were not computed on a held-out test set; a rolling-origin or train/test evaluation would give a more honest estimate.
- **Volatility is not modelled.** The regression diagnostics show heteroskedasticity; a GARCH extension would address it.

**Possible next steps:** out-of-sample validation, SARIMA/GARCH comparison, adding exogenous regressors (ARIMAX), and a benchmark against a naive random-walk forecast.

## Repository Structure

```
.
├── data/
│   └── Tesla_stock_data_for_10_years.xlsx
├── notebooks/
│   └── Tesla_stock.ipynb
├── report/
│   └── Report_on_the_analysis_of_Tesla_s_stock_price.docx   # full write-up (in Russian)
├── images/                                                   # exported charts
└── README.md
```

## Getting Started

**1. Clone the repository**

```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>
```

**2. Install dependencies**

```bash
pip install pandas matplotlib statsmodels openpyxl jupyter
```

**3. Run the notebook**

```bash
jupyter notebook notebooks/Tesla_stock.ipynb
```

> The notebook was written in Google Colab and loads data from Google Drive. To run it locally, remove the `drive.mount(...)` lines and change the data path to:
>
> ```python
> df = pd.read_excel('data/Tesla_stock_data_for_10_years.xlsx')
> ```

## Tech Stack

- **Language:** Python
- **Libraries:** pandas, matplotlib, statsmodels
- **Tools:** Jupyter / Google Colab, Gretl (regression diagnostics and KPSS), Microsoft Excel

## Author

**Nguyen Dinh Chieu**
Economics (Analytical Economics and Econometrics), Plekhanov Russian University of Economics

- GitHub: [@your-username](https://github.com/your-username)
- Email: your.email@example.com
