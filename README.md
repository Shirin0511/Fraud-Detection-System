# Fraud Detection System (Finance / FinTech)



## Problem Statement

This project builds an industry-style fraud detection system using transaction-level data, temporal feature engineering, cost-sensitive learning, and threshold-based decisioning.



## Key Challenges Addressed

- Extreme class imbalance (<5% fraud)
- Temporal dependency in transaction behavior
- Precision–recall trade-offs instead of accuracy
- Risk of time-based data leakage
- Evaluation under real-world business constraints



## Dataset Used

This project uses the **IEEE-CIS Fraud Detection Dataset** from Kaggle.

**Link:**  
https://www.kaggle.com/competitions/ieee-fraud-detection/data



## Exploratory Data Analysis (EDA)

Key observations:

- Fraud rate varies over time, so time-aware validation is required
- Significant missingness in identity-related features
- Fraud is driven by behavioural and temporal patterns rather than individual transactions



## Temporal Feature Engineering

Fraud detection is fundamentally a **temporal behaviour problem**.

To capture velocity and behavioural deviations, the following features were engineered:

- Number of transactions in the last **1 hour** and **1 day**
- Time since last transaction per card
- Transaction amount deviation from recent history
- Card–address and card–email usage patterns
- Acceleration and ratio-based transaction features

These features provide the model with **memory of past behavior**, which is critical for realistic fraud detection.  
Even when metric gains are modest, temporal features improve **robustness, interpretability, and deployability**.



## Model Training

**Model:** LightGBM Classifier  
**Objective:** Binary classification



## Training Strategy

- Cost-sensitive learning using `scale_pos_weight`
- Time-based train/validation split to prevent temporal leakage
- Hyperparameter tuning via `RandomizedSearchCV` with `TimeSeriesSplit`
- Optimization target: **PR-AUC**

The final model and hyperparameters are **frozen after tuning**.



## Evaluation Strategy

Accuracy is misleading for fraud detection. The model is evaluated using:

- **PR-AUC** (ranking quality)
- **Recall at fixed precision ≥ 90%** (business constraint)
- **Confusion matrix** at the selected operating threshold

Threshold selection is performed using the **precision–recall curve**, mirroring real-world fraud decision policies.


## Final Results (Validation Set)

| Metric | Value |
|------|------|
| PR-AUC | ~0.52 |
| Precision (operating point) | ~0.90 |
| Recall @ 90% precision | ~0.20 |

### Confusion Matrix

[[102002 80]
[ 2890 721]]

These results are realistic for highly imbalanced fraud datasets and reflect a **precision-first strategy** designed to minimize customer friction while capturing meaningful fraud signals.
