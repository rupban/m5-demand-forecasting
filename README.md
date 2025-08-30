# Demand Forecasting for Retail (M5-based)

A pragmatic, reproducible pipeline that turns the M5 retail dataset into business-aligned demand forecasts. The focus is simple: encode the right signals, ship a stable baseline fast, and climb the accuracy curve with targeted upgrades where they actually move inventory, working capital, and trust.

---

This repository contains my end-to-end work on the [Kaggle M5 Forecasting - Accuracy](https://www.kaggle.com/competitions/m5-forecasting-accuracy) competition.  
The goal is to forecast daily Walmart sales across thousands of products and stores, using multiple approaches and the WRMSSE metric.

## 📄 Project Report
A full research-style report is available in [`m5_forecasting_report.pdf`](report/m5-demand-forecasting.pdf).

## 🔑 Key Features
## Project Overview

This repository implements an end-to-end weekly forecasting workflow inspired by the M5 Forecasting task, with a strong emphasis on feature engineering, interpretable baselines, and business-weighted evaluation.

- **Core idea:** Engineer lag/seasonality, calendar/events, and price features; benchmark SES/MA/ARIMA against Linear, Ridge, and Lasso; select using business-aligned metrics.
- **Why weekly aggregation:** Clearer seasonality, simpler joins with weekly prices, lower volatility for robust, production-friendly training.
- **Business alignment:** Model selection and prioritization guided by WRMSSE so that improvements where dollars live get the most attention.
- **Roadmap:** Deploy Ridge now; add a promotion-aware adjustment; pilot LightGBM and MinT next; explore probabilistic and neural models where justified.

---

## Data and Features

- **Datasets:** M5 Accuracy data (sales, calendar, sell_prices) at item-store-day level for 2011–2016.
- **Aggregation:** Weekly consolidation to reduce noise and align with price records.
- **Feature sets:**
  - **Lags:** 1, 4, 52 weeks
  - **Rolling stats:** Means/standard deviations over 2, 4, 8, 52 weeks
  - **Calendar/events:** Week-of-year, month, holidays/events, SNAP flags
  - **Price signals:** Price, lagged price, percentage change, price-drop flags
  - **Identifiers:** Store/department/category encodings (for pooled regressions)

---

## Methods and Models

- **Baselines:**  
  - SES — Level/trend smoothing for sanity checks.  
  - MA — Short-window average for fast adaptation to level shifts.
- **Time series:**  
  - ARIMA — Applied where stationarity holds; specified using ADF tests plus ACF/PACF diagnostics.
- **Regression (tabular TS):**  
  - Linear / Ridge / Lasso — Trained on engineered features; interpretable, stable, and production-friendly.

---

## Evaluation and Metrics

Metrics used: **RMSE**, **sMAPE**, and **WRMSSE**.

- **RMSE:**  which is the square root of the MSE
  
- **sMAPE:** fixes the shortcoming of the original MAPE — it has both the lower (0%) and the upper (200%) bounds.
  
- **WRMSSE:** Weighted average of per-series RMSSE values using recent-dollar-sales weights.

- **Validation:** Walk-forward — train on a rolling window, predict the next fold, roll again.

---

## Results and Insights

**Example SKU (FOODS_3_090 @ CA_3):**

| Model         | RMSE    | sMAPE  | WRMSSE   |
|---------------|---------|--------|----------|
| SES           | 478.62  | 11.36  | 0.1326   |
| MA            | 229.58  | 4.52   | 0.0636   |
| ARIMA         | 304.42  | 5.62   | 0.0843   |
| Ridge/Lasso   | 95.45   | 2.09   | 0.0264   |

**Key takeaways:**
- Feature engineering > model complexity
- Ridge regression is a deployable, interpretable baseline
- Promotion underprediction is the main gap — needs explicit handling

---
