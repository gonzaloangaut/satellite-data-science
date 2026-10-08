# Satellite Data Science

A data science portfolio project exploring satellite and orbital data through exploratory analysis, data curation, supervised learning, and unsupervised learning.

The project studies the structure of the satellite population and investigates whether physical, orbital, mission, and launch-related characteristics can predict a satellite's reported expected-lifetime category.

## Why This Matters

Understanding the expected operational lifetime of satellites is relevant to modeling the long-term evolution of the orbital environment.

This project does **not** directly predict future space-debris generation. Instead, expected-lifetime prediction can be viewed as one component of a broader population-forecasting framework. Combined with launch-rate scenarios, orbital-decay and disposal models, and collision or fragmentation risk, lifetime estimates could help characterize how satellite populations and orbital congestion may evolve over time.

The clustering analysis provides a complementary perspective by identifying physically distinct satellite populations and orbital regimes, which may be useful when studying congestion, tracking requirements, or debris-mitigation strategies across different regions of orbital space.

## Project Overview

The analysis is organized into three stages:

1. **Exploratory Data Analysis**
   - satellite and space-object populations;
   - orbital regimes and orbital parameters;
   - launch history and debris-associated launch cohorts;
   - responsible country/entity codes;
   - radar cross-section size.

2. **Data Cleaning and Curation**
   - validation of NORAD and COSPAR identifiers across data sources;
   - duplicate resolution;
   - feature selection;
   - missing-value analysis;
   - leakage-safe train/validation/test splitting;
   - training-only imputation, encoding, and scaling.

3. **Supervised and Unsupervised Learning**
   - satellite expected-lifetime classification with Random Forest, XGBoost, and a calibrated SVM;
   - Bayesian hyperparameter optimization;
   - model comparison on an independent validation set;
   - final evaluation on a held-out test set;
   - robustness analysis on previously unseen feature profiles;
   - feature-importance analysis;
   - K-Means and DBSCAN clustering of physical and orbital characteristics.

## Key Results

The supervised-learning task classifies satellites into short-, medium-, and long-expected-lifetime categories.

Random Forest achieved the strongest validation performance and was selected as the final model. On the held-out test set it achieved:

| Metric             | Score |
| ------------------ | ----: |
| Macro F1           | 0.966 |
| Balanced Accuracy  | 0.976 |
| Multiclass ROC-AUC | 0.999 |
| Accuracy           | 0.984 |

Performance remained strong for the minority short- and long-lifetime classes.

Because many satellites share identical selected feature profiles, an additional robustness check was performed on test observations whose exact feature vector was absent from the combined training and validation data. On this subset, the Random Forest retained a **macro F1-score of 0.956**, suggesting that the strong test performance is not explained solely by repeated feature configurations.

Feature-importance analysis indicated that launch mass and orbital characteristics were the most informative predictors.

The unsupervised analysis also revealed clear physical structure. K-Means recovered two distinct LEO-dominated populations together with GEO- and MEO-dominated groups. DBSCAN independently recovered the main dense orbital regimes while identifying a small population of atypical observations, particularly among elliptical orbits.

## Notebooks

- [`01_exploratory_data_analysis.ipynb`](notebooks/01_exploratory_data_analysis.ipynb)  
  Explores the orbital catalog, population composition, temporal trends, orbit regimes, responsible entities, and radar cross-section size.

- [`02_data_cleaning_and_curation.ipynb`](notebooks/02_data_cleaning_and_curation.ipynb)  
  Validates and merges satellite metadata, defines the modeling population, prevents target leakage, and produces reproducible train/validation/test datasets.

- [`03_supervised_and_unsupervised_learning.ipynb`](notebooks/03_supervised_and_unsupervised_learning.ipynb)  
  Compares supervised classifiers, evaluates the selected model on held-out data, performs a robustness analysis, analyzes feature importance, and explores satellite structure with K-Means and DBSCAN.

## Data

Raw datasets are not stored in this repository.

The notebooks retrieve data programmatically from a fixed source revision:

`EnzoRg/space_debris`  
commit `1c49c73b92e84d3eba2c53f43313ed861d08f896`

The main sources include an orbital object catalog and the UCS satellite database.

Intermediate train/validation/test files are generated locally by the data-curation notebook and are excluded from version control.

## Reproducibility

The project was tested with **Python 3.12.3**.

Create and activate a Python environment, then install the project dependencies:

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
jupyter lab
```


Run the notebooks in numerical order:
1. `01_exploratory_data_analysis.ipynb`
2. `02_data_cleaning_and_curation.ipynb`
3. `03_supervised_and_unsupervised_learning.ipynb`

The notebooks use a fixed random seed where stochastic methods are involved.

## Methodological Notes

The supervised workflow separates training, validation, and test data before fitting data-dependent preprocessing transformations. The held-out test set is used only after model selection.

Reported expected lifetime is treated as a reported/design characteristic rather than an observed realized lifetime.

Because repeated satellite feature profiles occur in the dataset, the final model is additionally evaluated on test observations whose exact selected feature configuration was not present in the development data. This remains less stringent than a satellite-family-, constellation-, launch-, or time-disjoint benchmark.

The clustering analysis is exploratory. Known orbital classes are used to interpret the resulting clusters, not to construct them.

Model feature importance indicates predictive association and should not be interpreted as evidence of causality.

## Background

This repository is a revised portfolio adaptation of a collaborative project originally developed during the Data Science and Machine Learning diploma program at FaMAF, Universidad Nacional de Córdoba.

Original project authors: Ana Paula Cobresí, Gonzalo Angaut, Javier Albiero, and Octavio Santi.

Portfolio adaptation: Gonzalo Angaut.