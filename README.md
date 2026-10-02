# PCF-SPR-ML-xAI

**Machine Learning and Explainable AI for PCF-Based Surface Plasmon Resonance Sensors**

[![DOI](https://img.shields.io/badge/DOI-10.1002%2Fadmi.70593-blue)](https://doi.org/10.1002/admi.70593)
![Python](https://img.shields.io/badge/Python-3.10-3776AB?logo=python&logoColor=white)
![Journal](https://img.shields.io/badge/Advanced%20Materials%20Interfaces-2026-green)

This repository contains the machine learning and explainable artificial intelligence (xAI) workflow developed for the study:

> **A Hybrid Computational Framework for PCF-based SPR Sensor With Machine Learning and xAI**

**Authors:** Mahabur Rahman Fahim, Sudeep Paul, Musrat Jahan, Md Abu Shahid Chowdhury
**Affiliation:** Department of Biomedical Engineering, Khulna University of Engineering & Technology (KUET), Khulna, Bangladesh
**Journal:** *Advanced Materials Interfaces* (2026)
**DOI:** [10.1002/admi.70593](https://doi.org/10.1002/admi.70593)

---

## Table of Contents

- [Overview](#overview)
- [Research Workflow](#research-workflow)
- [Dataset](#dataset)
- [Feature Engineering](#feature-engineering)
- [Machine Learning Models](#machine-learning-models)
- [Model Evaluation](#model-evaluation)
- [Explainable AI](#explainable-ai)
- [Repository Structure](#repository-structure)
- [Installation](#installation)
- [Running the Workflow](#running-the-workflow)
- [Reproducibility](#reproducibility)
- [Scope and Limitations](#scope-and-limitations)
- [Citation](#citation)
- [License](#license)
- [Contact](#contact)

---

## Overview

This repository provides the Python-based machine learning and explainable AI workflow used to predict the performance of a photonic crystal fiber (PCF)-based surface plasmon resonance (SPR) sensor.

The framework uses simulation-derived data from the proposed PCF-SPR sensor and combines:

- Data preprocessing
- Polynomial feature expansion
- Random Forest Regression (RFR)
- Gradient Boosting Regression (GBR)
- CatBoost Regression (CatBR)
- 5-fold cross-validation
- Regression performance evaluation
- SHAP-based explainable AI (xAI)
- External/generalization validation

The models predict two key sensor-performance characteristics:

1. **Confinement Loss (CL)**
2. **Amplitude Sensitivity (AS)**

The goal is a computationally efficient alternative for rapidly estimating sensor performance without repeatedly running expensive numerical simulations.

---

## Research Workflow

```mermaid
flowchart TD
    A[COMSOL FEM Simulation] --> B[Simulation-Derived Dataset]
    B --> C[Data Preprocessing]
    C --> D[Polynomial Feature Expansion]
    D --> E[80/20 Train-Test Split]
    E --> F[5-Fold Cross-Validation]
    F --> G[RFR]
    F --> H[GBR]
    F --> I[CatBoost]
    G --> J[Model Evaluation<br/>MAE / RMSE / R²]
    H --> J
    I --> J
    J --> K[SHAP Analysis]
    K --> L[Feature Contribution Interpretation]
    L --> M[External Validation]
```

---

## Dataset

The dataset was generated from numerical simulations of the proposed PCF-SPR sensor.

**Input features**

| Feature        | Description                                           |
| -------------- | ----------------------------------------------------- |
| `RI`           | Analyte refractive index                              |
| `Au_thickness` | Thickness of the gold plasmonic layer                 |
| `wavelength`   | Operating wavelength                                  |
| `Re_eff`       | Real component of the effective refractive index      |
| `Im_eff`       | Imaginary component of the effective refractive index |

The study used **2,745 data points for each feature**, covering nine analyte refractive-index values from **1.31 to 1.39**.

**Target variables**

- `CL` — Confinement Loss
- `AS` — Amplitude Sensitivity

> The dataset is derived from COMSOL-based numerical simulations, not experimental measurements.

---

## Feature Engineering

To capture nonlinear relationships between the inputs and sensor performance, polynomial feature expansion is applied before model training. Higher-order terms are generated for:

- Wavelength
- Refractive index
- Imaginary component of the effective refractive index

Second-, third-, and fourth-order terms are included in the feature representation.

---

## Machine Learning Models

Three lightweight ensemble regression algorithms are implemented.

### 1. Random Forest Regressor (RFR)

An ensemble of decision trees whose predictions are combined to estimate the target.

**Confinement Loss**

```text
n_estimators = 175
max_depth = 15
max_features = "sqrt"
min_samples_leaf = 1
min_samples_split = 2
```

**Amplitude Sensitivity**

```text
n_estimators = 25
max_depth = 20
max_features = "sqrt"
min_samples_leaf = 1
min_samples_split = 3
```

### 2. Gradient Boosting Regressor (GBR)

Builds an ensemble sequentially, with each new estimator reducing the errors of the previous ones.

**Amplitude Sensitivity**

```text
n_estimators = 100
learning_rate = 0.1
max_depth = 5
min_samples_split = 2
min_samples_leaf = 3
max_features = "sqrt"
subsample = 1.0
```

### 3. CatBoost Regressor (CatBR)

A lightweight gradient-boosting approach.

```text
iterations = 500
bagging_temperature = 0.2
depth = 4
l2_leaf_reg = 1
learning_rate = 0.1
```

---

## Model Evaluation

Models are evaluated with:

- Mean Absolute Error (MAE)
- Root Mean Squared Error (RMSE)
- Coefficient of Determination (R²)
- Execution time

Five-fold cross-validation is used during evaluation.

**Confinement Loss — test-set R²**

| Model    | Test R² |
| -------- | ------: |
| RFR      |    0.98 |
| GBR      |    0.98 |
| CatBoost |    0.97 |

**Amplitude Sensitivity — test-set R²**

| Model    | Test R² |
| -------- | ------: |
| RFR      |    0.88 |
| GBR      |    0.86 |
| CatBoost |    0.87 |

Predictive performance is stronger for confinement loss than for amplitude sensitivity, reflecting the more complex nonlinear behavior of amplitude sensitivity.

---

## Explainable AI

[SHAP](https://github.com/shap/shap) (SHapley Additive exPlanations) is used to interpret the trained models, examining how individual features and their engineered polynomial terms contribute to predictions.

- **Confinement Loss:** the imaginary component of the effective refractive index and its higher-order terms contribute strongly.
- **Amplitude Sensitivity:** wavelength-related features (including polynomial wavelength terms) and the real component of the effective refractive index are most important.

This provides an interpretable link between the model predictions and the optical characteristics of the sensor.

---

## Repository Structure

```text
PCF-SPR-ML-xAI/
│
├── data/
│   ├── raw/
│   └── processed/
│
├── notebooks/
│   ├── 01_data_exploration.ipynb
│   ├── 02_feature_engineering.ipynb
│   ├── 03_cl_prediction.ipynb
│   ├── 04_as_prediction.ipynb
│   ├── 05_model_comparison.ipynb
│   ├── 06_shap_analysis.ipynb
│   └── 07_external_validation.ipynb
│
├── src/
│   ├── preprocessing.py
│   ├── feature_engineering.py
│   ├── models.py
│   ├── train.py
│   ├── evaluate.py
│   └── explain.py
│
├── configs/
│   └── model_parameters.yaml
│
├── results/
│   ├── metrics/
│   ├── predictions/
│   ├── shap/
│   └── figures/
│
├── scripts/
│   ├── train_models.py
│   ├── evaluate_models.py
│   └── generate_shap.py
│
├── requirements.txt
├── CITATION.cff
├── LICENSE
└── README.md
```

---

## Installation

Clone the repository:

```bash
git clone https://github.com/<your-username>/PCF-SPR-ML-xAI.git
cd PCF-SPR-ML-xAI
```

Create a virtual environment:

```bash
python -m venv .venv
```

Activate it:

```bash
# Windows
.venv\Scripts\activate

# Linux / macOS
source .venv/bin/activate
```

Install the dependencies:

```bash
pip install -r requirements.txt
```

### Requirements

The workflow was developed with Python 3.10 and uses:

- NumPy
- Pandas
- Scikit-learn
- Matplotlib
- Seaborn
- CatBoost
- SHAP
- Jupyter

Exact package versions are pinned in `requirements.txt`.

---

## Running the Workflow

The notebooks follow the main stages of the analysis:

| Step | Notebook | Purpose |
| ---- | -------- | ------- |
| 1 | `notebooks/01_data_exploration.ipynb` | Dataset inspection and exploratory analysis |
| 2 | `notebooks/02_feature_engineering.ipynb` | Generate polynomial features for training |
| 3 | `notebooks/03_cl_prediction.ipynb` | Train and evaluate RFR, GBR, and CatBoost for confinement loss |
| 4 | `notebooks/04_as_prediction.ipynb` | Train and evaluate the models for amplitude sensitivity |
| 5 | `notebooks/05_model_comparison.ipynb` | Compare MAE, RMSE, R², and execution time |
| 6 | `notebooks/06_shap_analysis.ipynb` | SHAP feature-importance and local explanation plots |
| 7 | `notebooks/07_external_validation.ipynb` | Generalization test on an additional, previously reported sensor dataset |

---

## Reproducibility

The workflow is based on simulation-generated data from the PCF-SPR sensor described in the associated publication. To reproduce the results:

1. Use the provided dataset where permitted.
2. Use the specified feature-engineering procedure.
3. Apply the reported train/test strategy (80/20 split).
4. Use 5-fold cross-validation.
5. Use the documented model configurations.
6. Compare predictions using MAE, RMSE, and R².
7. Apply SHAP to the trained models for interpretation.

The COMSOL finite-element simulations used to generate the original optical data are not part of this Python repository unless explicitly provided separately.

---

## Scope and Limitations

This repository contains the computational machine-learning and xAI analysis for a simulation-driven PCF-SPR sensor study. Because the models learn from numerically simulated data:

- Predictions should not be interpreted as direct experimental measurements.
- Model performance depends on the distribution and quality of the simulation-derived dataset.
- Amplitude sensitivity is more nonlinear and harder to predict than confinement loss.
- Experimental validation is required before translating the results into a physical sensing device.

---

## Citation

If you use this repository or the associated methodology in academic work, please cite:

```bibtex
@article{fahim2026pcfspr,
  title   = {A Hybrid Computational Framework for PCF-based SPR Sensor With Machine Learning and xAI},
  author  = {Fahim, Mahabur Rahman and Paul, Sudeep and Jahan, Musrat and Chowdhury, Md Abu Shahid},
  journal = {Advanced Materials Interfaces},
  year    = {2026},
  doi     = {10.1002/admi.70593}
}
```

---

## License

This repository is intended for academic and research use. Please see the [`LICENSE`](LICENSE) file for the applicable terms.

---

## Contact

**Mahabur Rahman Fahim**
Department of Biomedical Engineering
Khulna University of Engineering & Technology (KUET)
Khulna, Bangladesh

For questions about the machine learning and xAI implementation, please open an issue in this repository.
