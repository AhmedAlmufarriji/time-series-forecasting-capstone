# Technical Documentation

## 1. Forecasting problem

The project forecasts `required_headcount` from the official synthetic `workforce_demand.csv` dataset. It is a daily single-series problem with a strong weekly cycle, gradual trend, noise, and a sustained structural break beginning on 2025-04-01.

The central design question is not only which model forecasts best, but **how validation and uncertainty should respond when older history becomes less representative after a regime change**.

## 2. Data contract

- Frequency: daily
- Period: 2024-01-01 to 2025-12-31
- Target: `required_headcount`
- Seasonal period: 7 days
- Missing values: expected none
- Zeros: expected none
- Structural break: 2025-04-01 in the synthetic generator

The notebook fetches this file from the official course repository. It does not relabel the synthetic observations as real operational data.

## 3. Diagnostics

### STL
Robust STL with `period=7` separates the weekly component from slower movement and residual variation. A vertical marker at the break date prevents the level shift from being visually mistaken for ordinary seasonality.

### ACF/PACF
Raw and first-differenced ACF/PACF are plotted. The weekly autocorrelation is expected to be pronounced at lag 7 and its multiples. These plots support the use of seasonal differencing and weekly seasonal models.

### ADF
ADF is run on the raw series, first difference, and lag-7 seasonal difference. The raw series is non-stationary; differencing is motivated by the test together with the observed level shift and autocorrelation structure.

## 4. Classical models

### SARIMAX
Candidate orders vary p/q and P/Q while holding `d=1`, `D=1`, and `s=7` fixed. This allows AIC/BIC to select the dynamic structure without comparing likelihood criteria across inconsistent differencing setups. The selected model is evaluated on a final 60-day holdout.

### Exponential smoothing
SES, Holt, additive Holt-Winters, and multiplicative Holt-Winters are all fitted. Cross-family comparison is based on out-of-sample metrics rather than direct AIC comparison with SARIMAX.

### Residual diagnostic
Ljung-Box is reported at lags 7, 14, and 21. The diagnostic checks whether meaningful autocorrelation remains after fitting.

## 5. LightGBM feature lineage

Features include:

- lags: 1, 7, 14, 28 days;
- rolling means: 7 and 28 days;
- rolling standard deviation: 7 days;
- day of week;
- month;
- weekend indicator;
- cyclical day-of-year sine/cosine.

Every rolling statistic starts from `shift(1)`. The current target is never included in its own feature vector.

### Recursive forecasting
At forecast step 1, lags can come from observed training history. At later steps, any lag that falls inside the forecast horizon comes from an earlier **prediction**, never a true future observation. This avoids the common multi-step leakage error.

## 6. Backtesting architecture

The shared course functions `expanding_window_splits`, `rolling_window_splits`, and `run_backtest` are used.

### Primary test
- 5 folds
- 14-day horizon
- expanding minimum train history: 365 days
- rolling train history: 365 days
- models: seasonal-naive, Holt-Winters, recursive LightGBM

Every fit is created inside the `fit_predict_fn` callback, so each fold receives a fresh model.

### Correct rolling-window calendar dates
`run_backtest` passes a numeric `y_train` array. A callback factory therefore tracks the corresponding fold slice and reconstructs the exact date index from the original series. This prevents a rolling LightGBM fold from accidentally receiving the wrong day-of-week/month features.

### Structural-break stress test
A second backtest ends on 2025-05-20 and uses eight 10-day folds, deliberately placing a test fold on 2025-04-01. This is not used to claim foreknowledge of the break. It demonstrates that honest walk-forward validation exposes a sudden regime change as an error spike and then measures recovery as post-break observations become available.

## 7. Metrics

The shared course module computes:

- MAE — average absolute headcount error;
- RMSE — emphasizes large misses;
- WAPE — scale-aware percentage of total volume;
- MASE — scales error against a weekly seasonal-naive in-sample benchmark.

The series has no zeros, so WAPE and MASE are both stable choices. WAPE is emphasized for operational readability; MASE is used to indicate whether a model beats a weekly naive yardstick.

## 8. Probabilistic forecasting

### Prophet
Fits the daily training series and uses its native 80% uncertainty interval.

### Quantile LightGBM
Three regressors are fitted at q=0.1, 0.5, and 0.9. The central 80% interval is q10-q90. Pinball loss is reported for each quantile and quantile crossing is checked.

### sktime
`ThetaForecaster(sp=7)` is fitted and `predict_interval(..., coverage=0.8)` is called directly.

### Split conformal
A separate calibration block estimates the 80% conformal radius from absolute forecast errors. The final point model is then refit on all pre-test history and the radius is applied to the holdout prediction. Because time-series residuals are not perfectly exchangeable—especially around a structural break—the documentation explicitly treats conformal calibration as something that may need refreshing after a regime shift.

### Calibration rule
Coverage is never reported alone. Every method reports empirical coverage together with mean interval width.

## 9. Model-family decision framework

The notebook compares:

- statsmodels (SARIMAX / ETS),
- Prophet,
- sktime,
- LightGBM.

The deployment decision considers measured accuracy, fold stability, sensitivity to old history, interpretability, interval support, compute cost, maintenance burden, and future ability to add exogenous drivers. The conclusion is therefore not a disguised "lowest WAPE wins" rule.

## 10. Reproducibility

The course dataset is deterministic. The notebook sets `RNG_SEED = 20260912`. It can run in a fresh Colab environment from the setup cell with no API keys.

Before submission, run all cells top to bottom and save the output-bearing notebook to GitHub.
