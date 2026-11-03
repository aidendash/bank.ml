# bank.ml
Predicting term-deposit subscription from bank marketing calls in R: comparing a baseline decision tree, logistic regression, lasso, random forest and gradient boosting.
# Term Deposit Subscription Prediction (R)

Predicting whether a bank client will subscribe to a term deposit after a
telemarketing call, using only information available before the call.

## Overview
- **Data:** Portuguese bank direct-marketing campaign, 4,521 clients, 16 predictors
  ([UCI Bank Marketing dataset](https://archive.ics.uci.edu/dataset/222/bank+marketing),
  Moro et al., 2014). About 11.5% of clients subscribed.
- **Split:** 70/30 stratified train/test (`set.seed(893)`); all models are
  evaluated on the same untouched test set.
- **Models:** decision tree, logistic regression, lasso (`glmnet`),
  random forest (`randomForest`), gradient boosting (`gbm`).

## Results (test-set AUC)
| Model | AUC |
|---|---|
| Lasso | 0.737 |
| Random forest | 0.746 |
| Gradient boosting (194 trees) | **0.748** |

AUC of about 0.75 appears to be the limit for this dataset without leakage.

## Methodology
- **Leakage removed:** `duration` (call length) is only known after the call,
  so it is excluded from every model.
- **Redundant variables:** `pdays == -1` is identical to
  `poutcome == "unknown"`, so `pdays` is dropped; `day` is dropped as
  uninformative.
- **Factors:** categorical variables are converted once, before the split, so
  train and test share the same levels.
- **Rows kept:** "unknown" categories are retained instead of deleting rows.
- **Boosting:** `interaction.depth = 2`, `shrinkage = 0.05`,
  5-fold cross-validation to choose the number of trees (194 of a possible 1,000).

## Key findings (gradient boosting)
- **Most important variables:** contact month (31% relative influence) and
  previous-campaign outcome (29%), then balance (11%), job (8%) and age (7.5%).
- **Previous success** is the strongest signal (about 45% predicted
  subscription rate versus 8-9% otherwise).
- **Seasonality:** highest in October, December and March; lowest in
  January, May, July and November.
- **Age and balance** effects are modest and not monotonic.

## Limitations
- Only about 500 subscribers in total, so estimates for small groups
  (e.g. previous successes) are imprecise.
- Results come from a single train/test split; differences between the top
  models are small and likely within sampling noise.
- No date variable, so stability across campaigns could not be tested.
- Importance and partial dependence describe associations, not causal effects.

## How to run
1. Install: `readr`, `ggplot2`, `rpart`, `randomForest`, `caret`, `caTools`,
   `pROC`, `car`, `glmnet`, `gbm`.
2. Put `bank.csv` (semicolon-separated) in the project folder.
3. Open the `.Rmd` in RStudio and knit.

## Author
Aiden Liu
