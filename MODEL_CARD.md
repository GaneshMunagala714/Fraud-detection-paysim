# Model Card — Fraud Detection (PaySim, LightGBM)

## Overview / Intended Use
This model flags potentially fraudulent transactions to support human review. It is an educational/demo project and **not** a production decisioning system.

## Data
- **Source:** PaySim simulated financial transactions (Kaggle).
- **Target:** `isFraud` (1 = fraud, 0 = non-fraud)
- **Features used:** `step`, `type` (label-encoded), `amount`, `oldbalanceOrg`, `newbalanceOrg`, `oldbalanceDest`, `newbalanceDest`
- **Dropped:** `nameOrig`, `nameDest` (high-cardinality IDs), `isFlaggedFraud` (uninformative)

## Preprocessing & Split
- Label-encode `type`
- **Time-aware split:** 70% Train → 10% Val → 20% Test (ordered by `step`)
- No SMOTE on full data; imbalance handled via **class_weight='balanced'**

## Model & Training
- **Algorithm:** LightGBM (gradient boosting trees)
- **Class imbalance:** `class_weight='balanced'`
- **Early stopping:** yes (callbacks)
- **Primary metric:** PR-AUC (imbalanced data)
- **Secondary metric:** ROC-AUC
- **Thresholding:** choose a probability threshold on **validation** to achieve **Precision ≥ 90%**, then evaluate on **test**

## Evaluation (fill with your numbers)
- **Validation:** PR-AUC = `...`, ROC-AUC = `...`
- **Test:** PR-AUC = `...`, ROC-AUC = `...`
- **Threshold (picked on Val):** `...`
- **Precision (Test @ threshold):** `...`
- **Recall (Test @ threshold):** `...`
- **Precision@Top 0.5% (Test):** `...`
- **Confusion Matrix (Test @ threshold):** `[[TN, FP], [FN, TP]] = [...]`

> Plots: see `artifacts/pr_curve_test.png`, `artifacts/roc_curve_test.png`, `artifacts/confusion_matrix_test.png`.

## Limitations & Risks
- PaySim is **simulated**; real-world drift and behavior may differ.
- High precision reduces false positives but can lower recall — tune threshold to business costs.
- Requires ongoing monitoring (data drift, calibration).

## Intended Users & Impact
- For analysts/reviewers to **prioritize** suspicious transactions.
- Not a substitute for compliance/operations processes.

## How to Reproduce
1. Install deps: `pip install -r requirements.txt`
2. Open `fraud_detection.ipynb`, set path to the PaySim CSV.
3. Run cells top→bottom; metrics & plots are saved in `artifacts/`.

## Data & License Notes
- Respect Kaggle’s dataset terms. Do **not** commit raw data to the repo.

_Last updated: YYYY-MM-DD_
