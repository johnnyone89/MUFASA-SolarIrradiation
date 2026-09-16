# MUFASA: Multi-Expert Unified Forecasting Architecture with Simplex Aggregation for Multi-Step Hourly Solar Irradiation Forecasting

**Manuscript-aligned code and data for reproducible multi-step hourly solar irradiation forecasting**

![Manuscript](https://img.shields.io/badge/manuscript-under%20review-orange)
![Python](https://img.shields.io/badge/python-3.10%2B-blue)
![Protocol](https://img.shields.io/badge/evaluation-held--out%202020-brightgreen)

> **Manuscript status:** Under review.  
> This repository accompanies the current review-stage version of the MUFASA study and is intended as an executable research companion rather than a separate implementation specification.

MUFASA is a simplex-constrained multi-expert forecasting framework for **11-step hourly global horizontal irradiation forecasting from 08:00 to 18:00**. The workflow combines a nonlinear temporal expert with a regularized ridge expert, physically structured meteorological and solar-geometry features, development-only model selection, simplex-constrained forecast aggregation, dependence-aware statistical inference, and end-to-end grouped permutation analysis.

## Reproducibility statement

The notebook, data structure, chronology, model-selection rules, fixed seeds, benchmark definitions, and aggregation procedure are aligned with the manuscript. The intended use is straightforward: with the manuscript-aligned city datasets present and a compatible Python/PyTorch/CUDA environment, running the notebook from top to bottom should reproduce results **close to the values reported in the paper**. Small numerical differences can occur because GPU kernels, CUDA/cuDNN versions, PyTorch versions, and low-level floating-point execution are not guaranteed to be bitwise identical across machines.

The repository is therefore designed for **scientific reproduction of the reported experimental behavior**, not for manual retuning to force a particular number. The hyperparameter search space, chronological splits, random seeds, evaluation protocol, and aggregation logic are already encoded in the notebook.

For the current manuscript-aligned configuration, the held-out 2020 evaluation reports approximately:

- **Macro RMSE:** 0.3430 MJ m⁻²
- **Macro MAE:** 0.2444 MJ m⁻²
- **Macro R²:** 0.8740
- **Site-level RMSE:** lowest for MUFASA at all six study sites against 18 trainable benchmark architectures
- **Site–horizon ranking:** first in 62 of 66 comparisons

These values describe the current review-stage manuscript and may be updated if the manuscript changes during peer review.

## Experimental scope

The chronology is fixed before the 2020 evaluation:

- **2016–2018:** primary training
- **2019:** development, architecture screening, Bayesian hyperparameter optimization, seed policy, and aggregation calibration
- **2020:** held-out evaluation year

No 2020 observation is used numerically for hyperparameter optimization, scaler fitting, seed weighting, or aggregation-weight estimation.

The primary experiment uses a **controlled conditional/oracle-weather protocol**. Observed target-horizon meteorological variables are supplied identically to all forecasting architectures and are interpreted as perfectly known forward weather. Target-horizon irradiation observations are never used as predictors. The reported accuracy should therefore be interpreted as conditional forecasting performance under a high-information weather boundary, not as direct operational NWP-driven day-ahead accuracy.

## Repository layout

```text
MUFASA-SolarRad/
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

The notebook is organized as an ordered experimental workflow. Each major block documents its role and dependencies so that the analysis can be rerun from a clean kernel without reconstructing the study from prose alone.

## Data

A complete six-site manuscript run uses:

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

The modeled response is an **hourly accumulated global horizontal irradiation** quantity in **MJ m⁻²**. In the manuscript terminology, *irradiance* is reserved for radiant power per unit area (W m⁻²), whereas *irradiation* denotes energy per unit area accumulated over a time interval.

The processed-data lineage follows the Korea Meteorological Administration (KMA) Meteorological Data Open Portal / Automated Synoptic Observing System (ASOS) workflow documented in the manuscript. The derivative files do not retain original station identifiers or independent sensor metadata. City coordinates used in the notebook are fixed reference coordinates for deterministic solar-geometry calculations and should not be interpreted as reconstructed sensor positions.

SHA-256 hashes are provided in `data/checksums.sha256` so that the exact data snapshot used for a run can be checked explicitly.

## Running the study

Create a clean Python environment, install the listed dependencies, validate the data, and open the notebook:

```bash
python -m venv .venv
source .venv/bin/activate        # macOS/Linux
# .venv\Scripts\activate       # Windows

python -m pip install --upgrade pip
pip install -r requirements.txt
python scripts/validate_data.py
jupyter lab notebooks/MUFASA_Public_Release.ipynb
```

The notebook begins with a lightweight environment/data validation profile so that missing dependencies or malformed inputs can be detected quickly. The manuscript-aligned full profile is selected in the opening configuration block; the search space, fixed seeds, model-selection rules, and statistical settings are already encoded and do not need to be manually reconstructed from the paper.

A full manuscript-scale run is computationally expensive and is most practical on a CUDA-capable GPU. CPU-only execution is useful for validation and partial inspection but should not be expected to match the full runtime profile.

## Analysis stages

The notebook follows the manuscript workflow:

1. runtime and experiment contract;
2. data, chronology, and information boundaries;
3. physically structured feature construction;
4. nonlinear temporal and regularized ridge experts;
5. development-only model selection and simplex aggregation;
6. 2016–2019 refit and held-out 2020 evaluation;
7. matched-budget benchmark comparison;
8. ablation and loss-sensitivity analysis;
9. dependence-aware statistical inference;
10. grouped permutation / explainability analysis;
11. manuscript-oriented tables and figures.

For a clean reproduction, run the notebook from top to bottom in a fresh kernel. Downstream cells depend on objects created in earlier stages.

## Reproducibility safeguards

The public workflow checks for:

- required columns and exact 08:00–18:00 coverage;
- duplicate or incomplete date–hour rows;
- non-finite or negative irradiation values;
- chronological split violations;
- target-day irradiation leakage;
- incomplete six-site input in manuscript mode;
- invalid or non-finite model predictions;
- benchmark completeness;
- seed or aggregation decisions that improperly depend on the held-out 2020 evaluation year.

These checks are intended to make deviations from the manuscript protocol visible rather than silently changing the experiment.

## Statistical interpretation

The repository separates **numerical ranking** from **inferential evidence**. The manuscript workflow uses dependence-aware procedures including HAC-based inference, moving-block bootstrap analysis, multiplicity adjustment, simultaneous multi-horizon comparison, superior-predictive-ability diagnostics, and model-confidence-set analysis.

A lower RMSE is not automatically described as statistically unique superiority. Likewise, grouped permutation importance is interpreted as **predictive dependence**, not atmospheric causality.

## Outputs

Generated artifacts are written under `outputs/`, including:

- data and protocol audits;
- development-screening and HPO records;
- held-out 2020 prediction files;
- benchmark provenance and performance tables;
- site- and horizon-level rankings;
- dependence-aware statistical summaries;
- ablation and loss-sensitivity results;
- grouped permutation / XAI outputs;
- manuscript-oriented figures and tables.

Generated output folders should be treated as reproducible artifacts rather than manually edited result directories.

## Citation

If this repository is used before publication, please cite the repository together with the associated manuscript title:

> **MUFASA: Multi-Expert Unified Forecasting Architecture with Simplex Aggregation for Multi-Step Hourly Solar Irradiation Forecasting**

A formal journal citation will be added after publication.

## Data and license note

The code and processed-data release should be interpreted subject to the applicable source-data attribution and redistribution conditions. The original meteorological-data lineage is documented in the manuscript and in `data/README.md`.

## Contact

For reproducibility questions, open a GitHub issue and include the operating system, Python/PyTorch/CUDA versions, GPU model if used, the failing notebook stage, traceback, and data checksums. Please do not post confidential peer-review correspondence or unpublished editorial material in public issues.
