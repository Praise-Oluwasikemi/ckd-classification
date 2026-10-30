# Chronic Kidney Disease Classification

Predicting chronic kidney disease (CKD) from 24 clinical measurements, with a close look at how missing values should be handled in clinical data.

**Kaggle notebook:** [YOUR KAGGLE NOTEBOOK LINK]

> Learning project. Not validated for clinical use.

## Problem
CKD often goes undetected until it is advanced. This project builds a classifier that predicts whether a patient has CKD from routine clinical measurements such as blood pressure, hemoglobin and serum creatinine.

## Dataset
- 400 patient records collected at Apollo Hospitals, Karaikudi (Tamil Nadu, India) in 2015, available through the UCI Machine Learning Repository.
- 24 features (11 numeric, 13 categorical) plus the target `class` (`ckd` or `notckd`).
- 250 CKD and 150 non-CKD patients.
- Many missing values (for example, about 38% of `rbc`).
- The raw `.arff` file has formatting quirks (stray spaces, a few rows with an extra empty field), so the notebook parses it manually.

## Approach
1. Parse and clean the raw file
2. Exploratory data analysis (class balance, hemoglobin, serum creatinine, correlations)
3. Train/test split (80/20, stratified), then scikit-learn Pipelines that fill missing values **inside** cross-validation to avoid data leakage
4. Compare Logistic Regression, Random Forest and KNN with 5-fold cross-validation
5. Check for overfitting (train vs validation scores)
6. Final evaluation on 80 unseen patients
7. Feature importance from the Logistic Regression coefficients
8. Sensitivity analysis of missing-value handling

## Results

**5-fold cross-validation:**

| Model | Accuracy | Precision | Recall | F1 |
|---|---|---|---|---|
| Logistic Regression | 0.994 | 0.995 | 0.995 | 0.995 |
| Random Forest | 0.991 | 0.990 | 0.995 | 0.993 |
| KNN | 0.959 | 1.000 | 0.935 | 0.966 |

**Held-out test set (Logistic Regression, 80 patients):** 79 of 80 classified correctly. CKD recall was 0.98, and the one error was a CKD patient predicted as non-CKD.

**Key drivers:** low hemoglobin, packed cell volume, red blood cell count and specific gravity point toward non-CKD. High albumin and serum creatinine, plus hypertension and diabetes, point toward CKD. This matches clinical knowledge of how kidney disease affects blood and urine.

## Missing values: a clinical caution
- Missingness differs sharply by class: `rbc` is missing for 57% of CKD patients vs 6% of non-CKD patients, which probably reflects how the data was collected.
- Only 158 of 400 rows are complete, and dropping the rest would remove 83% of CKD patients but only 23% of non-CKD patients, so deleting rows is not an option.
- I compared median fill, median fill with "was missing" flags, and a model that handles missing values natively (HistGradientBoosting). All scored within 0.6% accuracy of each other with identical CKD recall (0.995), so the results do not appear to depend on the handling method.
- Imputation is still an assumption: in real clinical data, missing results are often informative, and filling them can hide abnormal cases.

## Limitations
- Only 400 patients from a single hospital, so results may not generalise.
- The classes separate very cleanly, so this is easier than real-world screening, and one error in 80 test patients is too few to draw firm conclusions.
- Scores near 100% leave little room to detect differences between strategies.

## How to run
1. Clone the repo and open the notebook in Jupyter or Kaggle.
2. Install dependencies: `pip install pandas numpy scikit-learn seaborn matplotlib`
3. The notebook reads the data from Kaggle's input path. To run it locally, download the dataset and change the `path` variable in the loading cell.

## Author
PRAISE ADETAYO certified data scientist(TS Academy)
[Kaggle profile](https://www.kaggle.com/praiseoluwasikemi) | [LinkedIn]([YOUR LINKEDIN LINK](https://www.linkedin.com/in/praiseadetayo))
