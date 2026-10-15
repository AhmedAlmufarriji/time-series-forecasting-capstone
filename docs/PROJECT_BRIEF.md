# Workforce Demand Forecasting Under a Structural Break

**Project:** SDAIA Academy — Time Series Forecasting for AI Systems (September 2026)  
**Team:** Ahmed Almufarriji (team leader) and Abdulaziz Bakhadlaq

## Problem

Forecast daily workforce demand when a synthetic time series experiences a persistent change in its underlying level. Unlike a stationary toy example, this task examines why older observations can become misleading after a regime shift.

## Data

The course-provided, synthetic `workforce_demand.csv` contains **731 daily observations**, spanning 2024-01-01 through 2025-12-31. The structural break begins on 2025-04-01. There are no real employees or customer records.

## Methods and controls

- Data diagnostics: robust STL, ACF/PACF and ADF tests.
- Classical models: SARIMAX; simple, Holt and Holt-Winters exponential smoothing.
- Machine learning: LightGBM with lag, rolling and calendar predictors and recursive out-of-sample forecasts.
- Evaluation: 60-day holdout and leakage-aware expanding/rolling walk-forward backtesting.
- Metrics: MAE, RMSE, WAPE, MASE; interval coverage and width.
- Predictive uncertainty: Prophet intervals, LightGBM quantile forecasts, sktime intervals and split conformal intervals.

## Selected evidence from the project README

- Five-fold 14-day-horizon backtest: approximate expanding Holt-Winters WAPE of **4.65%** and rolling Holt-Winters WAPE of **4.60%**.
- A break-related stress fold produced approximately **24–26% WAPE** across selected models, illustrating forecast degradation under structural change.
- Exact values depend on the documented fold design and model selection; these are course dataset experiments, **not production demand forecasts**.

## Explore the work

- [Notebook](../time_series_forecasting_capstone.ipynb)
- [Technical documentation](../TECHNICAL_DOCUMENTATION.md)
- [Course rubric](../RUBRIC_CHECKLIST.md)
- [Broader GitHub portfolio](https://github.com/AhmedAlmufarriji/data-science-analytics-portfolio)

**Reproduce:** Open the notebook in Colab and run cells sequentially; its setup instructions fetch public course utilities and synthetic dataset. No private datasets or credentials are required.
