# Dhaka Air Quality 2000–2025: Analysis and Forecasting

A look at 25 years of hourly air-quality data for Dhaka: data quality check, cleaning, exploratory analysis, statistical tests and forecasting models.

## Files

| File | Description |
|---|---|
| `dhaka_air_quality_analysis.ipynb` | The full analysis notebook |
| `dhaka_air_quality_2000_2025.csv` | Raw hourly data (225,816 rows): AQI, PM2.5, PM10, O3, NO2, SO2, CO, temperature, humidity, wind speed, pressure |
| `dhaka_aq_clean_hourly.csv` | Cleaned hourly data, with quality flags (`AQI_capped`, `PM10_imputed`, `met_clipped`, …) and calendar features |
| `dhaka_aq_daily.csv` | Daily means, plus daily max AQI, capped hours and season |

## Contents

1. **Load and profile**: checks that the hourly index is complete
2. **Data-quality audit**: values stuck at floors and ceilings, physically impossible PM2.5 > PM10 rows, how AQI is calculated
3. **Cleaning and feature engineering**: flags problem rows instead of dropping them
4. **Exploratory visualisation**: long-term trend, month × hour heatmap, diurnal profiles, AQI category shares, correlations
5. **Statistical analysis**: Mann–Kendall / Theil–Sen trends, seasonal and weekend tests, STL decomposition, stationarity
6. **Forecasting and ML**: monthly SARIMA / Holt-Winters, 24 h-ahead hourly gradient boosting, AQI category classification, PM2.5 gap-filling

## Key findings

- **Data issues.** 2.2 % of hours have PM2.5 capped at 250 µg/m³, which limits AQI to about 300. 2.2 % of rows had PM2.5 > PM10, so PM10 was re-imputed for those rows. AQI is simply the US EPA PM2.5 sub-index (r = 0.99998), so same-hour PM values were left out of the models to avoid leakage.
- **Trend.** Annual mean AQI rose from 107 (2000) to 232 (2024). PM2.5 rose by 351 %, NO2, SO2 and CO by about 70 %, and ozone stayed flat.
- **Seasonality.** Winter runs about 1.33 × the annual mean. There is no weekend effect.
- **Predictability.** Once trend, month and hour of day are removed, almost nothing is left to predict (|ACF| < 0.04).

| Task | Best model | Result | Baseline |
|---|---|---|---|
| Monthly AQI (test Jan 2023 – Sep 2025) | SARIMA(1,1,1)(1,1,1)₁₂ | MAE 1.45 | Seasonal naïve 10.76 |
| Hourly AQI, 24 h ahead | HistGradientBoosting | MAE 9.7, R² 0.89 | Persistence 12.7 |
| AQI category, 24 h ahead | Regression output binned | 92.6 % accuracy | Persistence 89.7 % |
| PM2.5 gap-filling | Calendar-only HistGB | R² 0.93 | Weather only 0.01 |


## How to run

```bash
pip install numpy pandas matplotlib scipy statsmodels scikit-learn jupyter
jupyter notebook dhaka_air_quality_analysis.ipynb
```
