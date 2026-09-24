<div align="center">

# MUFASA: Multi-Expert Unified Forecasting Architecture with Simplex Aggregation

**Official reproducibility package for the published MUFASA study on multi-step hourly solar irradiation forecasting**

[![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebooks-F37626?logo=jupyter&logoColor=white)](https://jupyter.org/)
[![DOI](https://img.shields.io/badge/DOI-10.3390%2Fmath14193470-2F80ED)](https://doi.org/10.3390/math14193470)
[![Journal](https://img.shields.io/badge/Mathematics-2026%2C%2014%2C%203470-7A5C1E)](https://doi.org/10.3390/math14193470)

[Overview](#overview) · [Reproducibility](#reproducibility-statement) · [Data](#data) · [Run](#quick-start) · [Citation](#citation)

</div>

---

## Overview

This repository accompanies the published article:

> **Jihoon Moon. “MUFASA: Multi-Expert Unified Forecasting Architecture with Simplex Aggregation for Multi-Step Hourly Solar Irradiation Forecasting.” _Mathematics_ 2026, 14, 3470.**  
> https://doi.org/10.3390/math14193470

MUFASA (**Multi-Expert Unified Forecasting Architecture with Simplex Aggregation**) is a development-anchored forecast-combination framework for **11-step hourly global horizontal irradiation forecasting from 08:00 to 18:00**.

The framework integrates:

- a nonlinear temporal expert;
- a regularized Ridge expert;
- physically structured meteorological and solar-geometry features;
- Gaussian-process Bayesian hyperparameter optimization;
- development-calibrated shrinkage;
- Euclidean projection onto the probability simplex;
- dependence-aware multi-horizon statistical inference; and
- end-to-end grouped permutation analysis for interpretation of the deployed predictor.

The published study evaluates six major metropolitan areas in South Korea using a fixed chronology in which **2016–2018** are used for primary training, **2019** for development and model selection, and **2020** as a held-out evaluation year.

## Citation

If you use this repository, its code, processed data, experimental protocol, or results in academic work, **please cite the published MUFASA article**:

> Moon, J. **MUFASA: Multi-Expert Unified Forecasting Architecture with Simplex Aggregation for Multi-Step Hourly Solar Irradiation Forecasting.** _Mathematics_ **2026**, _14_, 3470. https://doi.org/10.3390/math14193470

BibTeX:

```bibtex
@article{moon2026mufasa,
  author  = {Moon, Jihoon},
  title   = {MUFASA: Multi-Expert Unified Forecasting Architecture with Simplex Aggregation for Multi-Step Hourly Solar Irradiation Forecasting},
  journal = {Mathematics},
  year    = {2026},
  volume  = {14},
  pages   = {3470},
  doi     = {10.3390/math14193470},
  url     = {https://doi.org/10.3390/math14193470}
}
```

## Reproducibility statement

The notebook, data structure, chronology, model-selection rules, fixed seeds, benchmark definitions, and aggregation procedure are aligned with the published study. With the study datasets present and a compatible Python/PyTorch/CUDA environment, running the notebook from top to bottom should reproduce results **close to the values reported in the article**.

Small numerical differences may occur because GPU kernels, CUDA/cuDNN versions, PyTorch versions, operating systems, and low-level floating-point execution are not guaranteed to be bitwise identical across machines.

The repository is designed for **scientific reproduction of the published experimental behavior**, not for manual retuning to force a particular result. The hyperparameter search space, chronological splits, random seeds, evaluation protocol, and aggregation logic are encoded in the workflow.

Under the published controlled conditional/oracle-weather protocol, the held-out 2020 evaluation reports:

- **Macro RMSE:** 0.3430 MJ m⁻²
- **Macro MAE:** 0.2444 MJ m⁻²
- **Macro R²:** 0.8740
- **Site-level RMSE:** lowest for MUFASA at all six study sites against 18 trainable benchmark architectures
- **Site–horizon ranking:** first in 62 of 66 comparisons

## Experimental scope

The model-fitting chronology is fixed before evaluation:

- **2016–2018:** primary training;
- **2019:** development, architecture screening, Bayesian hyperparameter optimization, seed policy, and aggregation calibration;
- **2020:** held-out evaluation year.

No 2020 observation contributes numerically to hyperparameter optimization, scaler fitting, seed weighting, or aggregation-weight estimation.

The primary experiment uses a **controlled conditional/oracle-weather protocol**. Observed target-horizon meteorological variables are supplied identically to all forecasting architectures and are treated as perfectly known forward weather. Target-horizon irradiation observations are never used as predictors.

Accordingly, the reported results should be interpreted as **conditional forecasting performance under a high-information weather boundary**, rather than as direct operational NWP-driven day-ahead accuracy.

## Repository layout

```text
repository-root/
├── README.md
├── RELEASE_CHECKLIST.md
├── requirements.txt
├── .gitignore
├── data/
│   ├── README.md
│   ├── checksums.sha256
│   └── *_2016_2020_complete.csv
├── notebooks/
│   └── MUFASA_Public_Release.ipynb
├── scripts/
│   └── validate_data.py
└── outputs/
```

The notebook is organized as an ordered experimental workflow. Each major block documents its role and dependencies so that the analysis can be rerun from a clean kernel without reconstructing the study from the paper alone.

## Data

A complete six-site run uses:

```text
Busan_2016_2020_complete.csv
Daegu_2016_2020_complete.csv
Daejeon_2016_2020_complete.csv
Gwangju_2016_2020_complete.csv
Incheon_2016_2020_complete.csv
Seoul_2016_2020_complete.csv
```

Each complete city file contains **20,097 daytime observations**, corresponding to **1,827 complete days × 11 hourly forecast steps**.

Required columns are:

| Column | Description |
|---|---|
| `Year` | Calendar year |
| `Month` | Calendar month |
| `Day` | Calendar day |
| `Hour` | Hour of day; expected 08–18 |
| `Temp` | Air temperature |
| `Humi` | Relative humidity |
| `WS` | Wind speed |
| `WD` | Wind direction |
| `Solar` | Hourly accumulated global horizontal irradiation |

The modeled response is **hourly accumulated global horizontal irradiation** in **MJ m⁻²**. In the article terminology, *irradiance* denotes radiant power per unit area (W m⁻²), whereas *irradiation* denotes energy per unit area accumulated over a time interval.

The processed-data lineage follows the Korea Meteorological Administration (KMA) Meteorological Data Open Portal / Automated Synoptic Observing System (ASOS) workflow documented in the article. The derivative files do not retain original station identifiers or independent sensor metadata. City coordinates used in the notebook are fixed reference coordinates for deterministic solar-geometry calculations and should not be interpreted as reconstructed sensor positions.

SHA-256 hashes are provided in `data/checksums.sha256` so that the exact data snapshot used for a run can be checked explicitly.

## Quick start

### 1. Clone the repository

```bash
git clone https://github.com/johnnyone89/MUFASA-SolarIrradiation.git
cd MUFASA-SolarIrradiation
```

### 2. Create the environment

```bash
python -m venv .venv
source .venv/bin/activate        # macOS/Linux
# .venv\Scripts\activate       # Windows

python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 3. Validate the data

```bash
python scripts/validate_data.py
```

### 4. Run the study

```bash
jupyter lab notebooks/MUFASA_Public_Release.ipynb
```

Run the notebook from top to bottom in a fresh kernel. Generated predictions, evaluation tables, figures, diagnostics, and manuscript-oriented artifacts are written under `outputs/`.

A full study-scale run is computationally expensive and is most practical on a CUDA-capable GPU. CPU-only execution is useful for validation and partial inspection but should not be expected to match the full runtime profile.

## Analysis stages

The notebook follows the published workflow:

1. runtime and experiment contract;
2. data, chronology, and information boundaries;
3. physically structured feature construction;
4. nonlinear temporal and regularized Ridge experts;
5. development-only model selection and simplex aggregation;
6. 2016–2019 refit and held-out 2020 evaluation;
7. matched-budget benchmark comparison;
8. ablation and loss-sensitivity analysis;
9. dependence-aware statistical inference;
10. grouped permutation / explainability analysis;
11. publication-oriented tables and figures.

## Reproducibility safeguards

The public workflow checks for:

- required columns and exact 08:00–18:00 coverage;
- duplicate or incomplete date–hour rows;
- non-finite or negative irradiation values;
- chronological split violations;
- target-day irradiation leakage;
- incomplete six-site input in publication mode;
- invalid or non-finite model predictions;
- benchmark completeness; and
- seed or aggregation decisions that improperly depend on the held-out 2020 evaluation year.

These checks are intended to make deviations from the published protocol visible rather than silently changing the experiment.

## Statistical interpretation

The repository separates **numerical ranking** from **inferential evidence**. The published workflow uses dependence-aware procedures including HAC-based inference, moving-block bootstrap analysis, multiplicity adjustment, simultaneous multi-horizon comparison, superior-predictive-ability diagnostics, and model-confidence-set analysis.

A lower RMSE is not automatically interpreted as statistically unique superiority. Likewise, grouped permutation importance is interpreted as **predictive dependence**, not atmospheric causality.

## Outputs

Generated artifacts under `outputs/` include:

- data and protocol audits;
- development-screening and HPO records;
- held-out 2020 prediction files;
- benchmark provenance and performance tables;
- site- and horizon-level rankings;
- dependence-aware statistical summaries;
- ablation and loss-sensitivity results;
- grouped permutation / XAI outputs; and
- publication-oriented figures and tables.

Generated output folders should be treated as reproducible artifacts rather than manually edited result directories.

## Data and license note

The code and processed-data release should be interpreted subject to the applicable source-data attribution and redistribution conditions. The original meteorological-data lineage is documented in the published article and in `data/README.md`.

## Contact

For reproducibility questions or implementation issues, please open a GitHub issue. For academic correspondence:

**Jihoon Moon, Ph.D.**  
Assistant Professor, Department of Data Science  
Duksung Women's University, Seoul 01369, Republic of Korea  
[jmoon25@duksung.ac.kr](mailto:jmoon25@duksung.ac.kr)
