# Turbidity Prediction

## Pipeline

The repository implements a three-stage pipeline. Each stage feeds its outputs into the next.

```
Stage A                    Stage B                         Stage C
PRISMA Image (234 bands)   Data_subset1.csv (191 bands)    top5_bands.csv
        │                          │                       tuned_models/*.joblib
        ▼                          ▼                       model_performance_ranking.csv
  Noisy Band Detection       9-Model Training                    │
  (Spatial Coherence +       + Hyperparameter Tuning             ▼
   Rényi's Entropy)          + Feature Importance          StackingRegressor
        │                          │                       (9 base + meta-learner)
        ▼                          ▼                             │
  191 Clean Bands            top5_bands.csv                      ▼
                             tuned_models/              Final Turbidity Prediction
                             model_performance_ranking.csv
```

| Stage | Notebook                          | Input                                | Output                                                             |
| ----- | --------------------------------- | ------------------------------------ | ------------------------------------------------------------------ |
| **A** | `PRISMA_band_selection.ipynb`     | PRISMA L1 image (234 bands)          | Clean band indices                                                 |
| **B** | `feature_importance_prisma.ipynb` | `Data_subset1.csv`                   | `top5_bands.csv`, `tuned_models/`, `model_performance_ranking.csv` |
| **C** | `SMOF.ipynb`                      | `Data_subset1.csv` + Stage B outputs | `SMOF_comparison_table.csv`, comparison plots                      |

---

## Stage A — Noisy Band Detection in hyperspectral satellite dataset

- Divides each band into **23×23 non-overlapping quadrats**
- Computes **intra-band spatial coherence (IBSC)** via pairwise inter-quadrat covariance
- Finds a **single optimal threshold** using Rényi's entropy (α = 0.5)
- Bands with IBSC ≤ threshold are flagged as noisy

## Stage B — Feature Selection

- Trains **9 ML regressors**: CatBoost, Decision Tree, Extra Trees, Gradient Boosting, LightGBM, RFE, Random Forest, SVR (RBF), XGBoost
- Tunes each with `RandomizedSearchCV` (10-fold CV)
- Evaluates on a **held-out 30% test set**
- Ranks models using **−Norm(MAPE) + Norm(R²)**
- Extracts **top 5 bands** from the best-ranked model
- Exports all tuned models as `.joblib` files for Stage C

## Stage C — SMOF Stacked Ensemble

- Loads tuned models and top bands from Stage B (zero hardcoding)
- Builds `StackingRegressor` with **all 9 tuned base models** and the **best model as meta-learner**
- Uses **10-fold CV** for meta-feature generation
- Exports the SMOOF model as '.joblib' file for further use. 

---

## Data

| File               | Description                                 |
| ------------------ | ------------------------------------------- |
| `Data_subset1.csv` | 191 clean spectral bands + turbidity target |
| `LR_234_bands`     | PRISMA L1 hyperspectral image               |

## Usage

Run notebooks in order:

```
1. Stage A → PRISMA_band_selection.ipynb
2. Stage B → feature_importance_prisma.ipynb
3. Stage C → SMOF.ipynb
```

> Stages B and C can run independently using `Data_subset1.csv`. Stage A requires the PRISMA image file. Stage C depends on Stage B outputs (`top5_bands.csv`, `tuned_models/`, `model_performance_ranking.csv`).

---

## Citation

```
Bhattacharjee, R., Gaur, S., Chander, S., Ohri, A., Srivastava, P. K., & Mishra, A. (2024). Stacked Ensemble with Machine Learning Regressors on Optimal Features (SMOF) of hyperspectral sensor PRISMA for inland water turbidity prediction. Environmental Science and Pollution Research, 31(57), 65464-65480.
```
