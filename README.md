# Astronomical Classification: Reliable Photometric Quasar Identification

## Scientific Motivation
Modern astronomy relies heavily on large-scale surveys that map the sky across different wavelengths. A fundamental challenge in astronomical data analysis is distinguishing between different types of celestial objects. This project focuses on the identification of quasars (quasi-stellar objects) under conditions of severe class imbalance and intrinsic information constraints.

## Photometry vs Spectroscopy
There is a fundamental tradeoff in astronomical observations:
- **Spectroscopy** provides a detailed spectrum of light, making it highly informative and accurate for object classification and redshift determination. However, spectroscopic observations are slow, expensive, and limited to a relatively small number of objects.
- **Photometry** measures the flux (brightness) of objects in broad wavelength bands (e.g., $u, g, r, i, z$). It is scalable, inexpensive, and can map billions of objects rapidly, but it provides compressed and limited information compared to spectroscopy.

This project investigates how reliably quasars can be identified using **ONLY photometric observations** as a candidate selection mechanism prior to expensive spectroscopic follow-up.

## Why Quasars Matter
Quasars are extremely luminous active galactic nuclei powered by supermassive black holes. They are critical for tracing the large-scale structure of the early universe, studying galaxy evolution, and probing the intergalactic medium. Finding them efficiently is a major objective of modern cosmology.

## Why Class Imbalance Matters
In any wide-field photometric survey, stars and normal galaxies vastly outnumber quasars. This creates a severe class imbalance. A naive classifier might achieve high overall accuracy simply by predicting the majority classes (Stars/Galaxies), while failing completely at the actual scientific objective: finding quasars. 

## Project Objective
The central research objective is: **"Reliable photometric quasar identification under intrinsic information constraints and severe class imbalance."**
We are not attempting to replace spectroscopy; rather, we are optimizing the photometric candidate selection process. 

## Dataset Description
The dataset consists of photometric observations from the Sloan Digital Sky Survey (SDSS). 
Features include:
- Base photometric magnitudes: `u, g, r, i, z`
- Target labels: `STAR`, `GALAXY`, `QSO`

*Note: Redshift is intentionally excluded from the main experiments because spectroscopic redshift injects richer spectral information that breaks the photometric constraints. A final comparison experiment with redshift will be performed separately.*

**The dataset should be placed manually inside `data/raw/` before executing the pipeline.**

## Binary Reformulation (Quasar vs Non-Quasar)
While the raw dataset contains three classes (Star, Galaxy, QSO), the primary experimental task is formulated as a binary classification problem: **Quasar vs. Non-Quasar**.
- **Positive Class:** QSO (Quasar)
- **Negative Class:** STAR + GALAXY (Non-Quasar)

This transition correctly aligns the machine learning objective with the scientific objective of quasar candidate prioritization.

## Repository Structure
```
project/
├── configs/                     # YAML configuration files for reproducibility
│   ├── experiment_config.yaml
│   ├── feature_config.yaml
│   └── model_config.yaml
├── data/
│   ├── raw/                     # Place raw dataset here
│   ├── processed/               # Preprocessed splits and metadata
│   └── balanced_versions/       # Oversampled datasets (saved after split)
├── notebooks/
│   ├── 01_baseline_eda_preprocessing.ipynb
│   ├── individual_experiments/  # Dedicated notebooks for each resampling strategy
│   │   ├── member1_tomek_logreg.ipynb
│   │   ├── member2_borderline_svm.ipynb
│   │   └── member3_smoteenn_xgboost.ipynb
│   └── 99_final_comparative_analysis.ipynb
├── src/                         # Reusable Python modules
│   ├── preprocessing.py
│   ├── feature_engineering.py
│   ├── evaluation.py
│   └── visualization.py
├── results/
│   ├── figures/                 # Subdirectories for baseline and member plots
│   ├── metrics/                 # Automated CSV metric tracking
│   ├── confusion_matrices/
│   └── shap/                    # SHAP explainability outputs
├── models/                      # Saved trained models
├── report/                      # Final project reports
├── requirements.txt
└── .gitignore
```

## Experimental Pipelines
The project explores different resampling philosophies coupled with appropriate model architectures:

| Pipeline | Sampling Philosophy | Model Philosophy |
| -------- | ------------------- | ---------------- |
| **Tomek Links + LogReg** | Pure Undersampling (Boundary Cleaning) | Linear boundary |
| **Borderline-SMOTE + SVM** | Pure Oversampling (Boundary Enrichment) | Margin optimization |
| **SMOTEENN + XGBoost** | Hybrid (Balance + Aggressive Cleaning) | Nonlinear boosted ensemble |

*Crucially, all oversampling is performed strictly **after** the train-test split to ensure scientific validity and prevent data leakage.*

## Evaluation Metrics
Because accuracy is a misleading metric for highly imbalanced data, this project emphasizes:
- **Quasar Recall:** The fraction of true quasars successfully identified.
- **Precision-Recall AUC (PR-AUC):** The area under the precision-recall curve, providing a robust measure of performance on the minority class.
- **F1-Score & ROC-AUC:** As secondary performance indicators.

## Future Work
- **Threshold Analysis:** Calibrating decision thresholds for specific operational constraints (e.g., allocating a fixed number of spectroscopic telescope fibers).
- **Redshift Benchmarking:** Comparing the photometric-only pipeline against a model that has access to spectroscopic redshift.
- **SHAP Explainability:** Analyzing the physical interpretation of feature importance (e.g., which color indices drive the model decisions).

give discription for this 
