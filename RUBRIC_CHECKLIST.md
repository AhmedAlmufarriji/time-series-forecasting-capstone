# Rubric Checklist — Workforce Demand Capstone

## 1. Time series structure & diagnostics — 15 pts

- [x] STL decomposition actually run (`STL(..., period=7, robust=True)`).
- [x] ACF/PACF actually plotted for raw and differenced series.
- [x] ADF actually run and interpreted.
- [x] First/seasonal differencing connected to stationarity and weekly persistence.

## 2. Classical forecasting models — 15 pts

- [x] SARIMAX actually fitted.
- [x] SES, Holt, and Holt-Winters actually fitted.
- [x] SARIMAX p/q/P/Q selected by comparing AIC/BIC candidates with fixed differencing orders.
- [x] Ljung-Box residual diagnostic run at lags 7, 14, and 21.

## 3. ML/GBM forecasting & feature engineering — 15 pts

- [x] Shift-based lag features: 1, 7, 14, 28.
- [x] Rolling means and rolling standard deviation built from shifted history.
- [x] Calendar features: day-of-week, month, weekend, day-of-year cyclic encoding.
- [x] LightGBM actually fitted.
- [x] Leakage risk explained and controlled in training and recursive forecasting.

## 4. Backtesting & time-based validation — 20 pts

- [x] Real walk-forward splits built with shared course functions.
- [x] Shared `run_backtest` drives fold loops and refits fresh per fold.
- [x] Both expanding and rolling windows actually used and compared.
- [x] Seasonal-naive baseline actually computed.
- [x] Fold-boundary leakage addressed explicitly.
- [x] Extra structural-break stress test places a fold on 2025-04-01 to examine regime-change behavior.

## 5. Evaluation metrics — 10 pts

- [x] Shared `mae` and `rmse` used.
- [x] Shared WAPE and MASE both computed.
- [x] Metric choice justified against this series: no zeros, weekly seasonal baseline, operational percentage readability.

## 6. Probabilistic forecasting — 15 pts

- [x] Prophet actually fitted and native uncertainty interval used.
- [x] sktime's `predict_interval` actually called.
- [x] Quantile LightGBM fitted at q10/q50/q90 and pinball loss computed.
- [x] Coverage and interval width reported together.
- [x] Split conformal prediction actually implemented and its time-series caveat discussed.

## 7. Model comparison & documentation — 10 pts

- [x] statsmodels, Prophet, sktime, and LightGBM all discussed in a real decision framework.
- [x] Programme + September 2026 cohort stated.
- [x] SDAIA Academy GitHub linked.
- [x] README and technical documentation included.
- [x] Notebook produces concrete measured results when run.

## Pre-submission run check

- [ ] Open the final GitHub notebook in a fresh Google Colab runtime.
- [ ] Runtime → Run all.
- [ ] Confirm Prophet and sktime install/fits complete.
- [ ] Confirm every cell has captured output.
- [ ] Save the output-bearing notebook back to GitHub.
- [ ] Verify at least three folds, no overlapping train/test data, and both window types are visible.
- [ ] Verify no API key or credential appears in the notebook or git history.
