# Regression Modeling Results

## Task

Predict **next-hour PM2.5** at the Kariavattom monitoring location.

### Features

- Current PM2.5
- Previous-hour PM2.5
- PM2.5 from 24 hours earlier
- 24-observation rolling PM2.5 average
- Hour of day
- Day of week
- Month

### Validation design

The main holdout uses a chronological 80/20 split. Random shuffling was not used because this is a time-series prediction problem.

## Chronological holdout results

| Model | MAE | RMSE | R² |
|---|---:|---:|---:|
| Linear Regression | 1.3779 | 1.9043 | 0.8779 |
| Random Forest | 1.2746 | 2.6703 | 0.7600 |
| Gradient Boosting | 1.1989 | 2.0292 | 0.8614 |

These metrics show different error profiles rather than a single universally best model.

## Error analysis

The largest errors were concentrated in a small number of observations.

- Linear Regression: 23 test errors above 5 µg/m³
- Random Forest: 41 test errors above 5 µg/m³
- Gradient Boosting: 30 test errors above 5 µg/m³

A notable Random Forest error occurred on 2026-09-02 00:00 (+05:30): actual PM2.5 was 24.0 while the prediction was about 45.95.

A notable Gradient Boosting error occurred on 2026-09-14 00:00 (+05:30): actual PM2.5 was 22.4 while the prediction was about 38.94.

## Gradient Boosting feature importance

| Feature | Importance |
|---|---:|
| pm25 | 0.877026 |
| pm25_lag1 | 0.046242 |
| pm25_rolling24 | 0.043535 |
| pm25_lag24 | 0.016690 |
| hour | 0.012548 |
| day_of_week_num | 0.003211 |
| month_num | 0.000748 |

The current PM2.5 value dominates the fitted Gradient Boosting model. Feature importance is a model-specific predictive signal and should not be interpreted as causal evidence.

## Time-aware cross-validation

Five chronological folds were used. The average CV results were:

| Model | CV MAE | CV RMSE | CV R² |
|---|---:|---:|---:|
| Linear Regression | 3.7271 | 7.0260 | 0.7919 |
| Random Forest | 8.9960 | 12.6017 | 0.3316 |
| Gradient Boosting | 9.0037 | 12.7858 | 0.3530 |

The cross-validation results differ substantially from the single 80/20 holdout. This is evidence of temporal distribution shift in the dataset.

The clearest shift occurs in Fold 3, whose validation period is in the winter season. Its target mean is about 61.53 µg/m³ compared with much lower means in most other folds.

## Current conclusion

The regression stage demonstrates that next-hour PM2.5 can be modeled from recent pollution history and time features, but performance is not stationary across the full time period. The temporal distribution shift needs to remain part of the project's final interpretation rather than being hidden by a single test score.

Next modeling stage: **pollution-risk classification**.
