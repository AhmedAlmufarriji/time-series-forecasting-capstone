# Workforce Demand Forecasting Under a Structural Break

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/AhmedAlmufarriji/time-series-forecasting-capstone/blob/main/time_series_forecasting_capstone.ipynb)

A capstone project for **SDAIA Academy — Time Series Forecasting for AI Systems**, focused on forecasting a daily workforce-demand series that experiences a sustained structural break.

**Author:** Ahmed Almufarriji  
**Programme:** SDAIA Academy — Time Series Forecasting for AI Systems  
**Cohort:** September 2026  
**Course reference:** https://mohammadyusif.github.io/time-series-forecasting-ai-systems/  
**SDAIA Academy GitHub:** https://github.com/SDAIAAcademy

## Project idea

The notebook asks a practical forecasting question: **what happens to model accuracy and uncertainty when the data-generating regime changes?** The official course `workforce_demand.csv` series has a sustained level shift on 2025-04-01, making it especially suitable for the rubric's requirement to compare expanding and rolling walk-forward validation.

This is the official synthetic course dataset, so no instructor approval for an external dataset is needed. It contains no real employer or workforce information.

## Why this dataset was selected

Before selecting the final series, the four official course choices were screened for rubric fit and modeling risk:

| Candidate | Main advantage | Main capstone risk |
|---|---|---|
| Retail demand | Rich trend + weekly/yearly seasonality and many observations | It is the course's main running example, so the capstone is less distinctive |
| **Workforce demand** | Strong weekly pattern + clear structural break; ideal for expanding-vs-rolling validation | Regime shifts require careful interpretation and recalibration |
| Economic indicator | Clean low-frequency forecasting problem | Only 108 monthly observations; less evidence for ML/backtesting |
| Intermittent demand | Highly distinctive sparse-demand problem | ~95% zeros; several required course models are a poor structural match |

The workforce series gave the best balance of **originality, rubric coverage, model diversity, and honest operational interpretation**.

## Pre-selection tests performed

The official deterministic workforce series was tested before the project was rewritten:

- 731 daily observations, no missing values, no zeros.
- Raw ADF p-value ≈ **0.879**: the level is non-stationary.
- First-difference ADF p-value ≈ **6.8×10⁻13**: differencing strongly stabilizes the series.
- Raw lag-7 autocorrelation ≈ **0.953**: the weekly structure is extremely strong.
- Mean level rises from about **60.6** before 2025-04-01 to about **86.6** after it; that observed mean ratio includes trend and seasonality in addition to the generator's step change.
- A tested SARIMAX grid selected **(1,1,2) × (0,1,1,7)** by AIC (≈ **4064.6**) on the 60-day holdout training period.
- Ljung-Box p-values on that fit were above 0.13 at lags 7, 14, and 21, indicating no strong residual autocorrelation at those checkpoints.
- Latest five-fold backtest (14-day horizon): expanding Holt-Winters mean WAPE ≈ **4.65%**; rolling Holt-Winters ≈ **4.60%**; expanding LightGBM ≈ **4.75%**; seasonal-naive ≈ **6.15%**.
- A structural-break stress fold starting on 2025-04-01 produced a WAPE spike around **24–26%** across seasonal-naive, Holt-Winters, and LightGBM, then recovered as post-break data entered training. That is exactly the kind of behavior a real walk-forward test should expose.

These tests are dataset-selection evidence, not a substitute for the notebook's final captured Colab outputs.

## What the notebook contains

1. **Structure & diagnostics:** robust STL, ACF/PACF, ADF, motivated first/seasonal differencing.
2. **Classical forecasting:** SARIMAX candidate search with AIC/BIC; SES, Holt, additive/multiplicative Holt-Winters; Ljung-Box residual checks.
3. **ML/GBM:** LightGBM with lag, rolling, and calendar features plus leakage-safe recursive forecasting.
4. **Backtesting:** `run_backtest` with both expanding and rolling windows, a seasonal-naive baseline, and a separate structural-break stress test.
5. **Metrics:** MAE, RMSE, WAPE, and MASE from the shared course metrics module.
6. **Probabilistic forecasting:** Prophet native intervals, LightGBM quantiles with pinball loss, sktime `predict_interval`, and split conformal intervals; coverage is always reported with width.
7. **Model comparison:** statsmodels, Prophet, sktime, and LightGBM compared on accuracy, history behavior, interpretability, interval support, compute, and maintenance.

## Repository structure

```text
.
├── time_series_forecasting_capstone.ipynb
├── README.md
├── TECHNICAL_DOCUMENTATION.md
├── RUBRIC_CHECKLIST.md
└── .gitignore
```

## Run in Google Colab

1. Click the **Open in Colab** badge above.
2. Select **Runtime → Run all**.
3. Allow the first setup cell to install the required open-source packages.
4. Confirm every cell completes and save the notebook with its outputs.

No API key, credential, paid service, or private dataset is required.

## Reproducibility and leakage controls

- Official course synthetic dataset and shared utilities are fetched from the course repository.
- Random seed: `20260912`.
- Time order is never shuffled.
- Models are fitted fresh inside each walk-forward fold.
- Rolling and lag features are built from past values only.
- Recursive LightGBM feeds predictions forward instead of true future holdout values.
- Calendar dates for rolling-window LightGBM are recovered from the actual fold slices so calendar features remain correct.
- No credential is stored in the notebook or repository.

## Documentation

See `TECHNICAL_DOCUMENTATION.md` for the forecasting architecture and `RUBRIC_CHECKLIST.md` for a criterion-by-criterion grading check.

## Programme acknowledgment

Completed under the **SDAIA Academy — Time Series Forecasting for AI Systems** programme, September 2026 cohort.
