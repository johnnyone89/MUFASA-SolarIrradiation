# Data used by the MUFASA reproducibility workflow

This directory contains the processed daytime meteorological and solar-energy datasets used by the MUFASA manuscript workflow.

## Manuscript-aligned dataset

A complete six-site run uses:

- `Busan_2016_2020_complete.csv`
- `Daegu_2016_2020_complete.csv`
- `Daejeon_2016_2020_complete.csv`
- `Gwangju_2016_2020_complete.csv`
- `Incheon_2016_2020_complete.csv`
- `Seoul_2016_2020_complete.csv`

Each complete file contains **20,097 rows = 1,827 days × 11 hourly observations**, covering 08:00 through 18:00.

## Schema

| Field | Meaning |
|---|---|
| `Year` | Calendar year |
| `Month` | Calendar month |
| `Day` | Calendar day |
| `Hour` | Hour of day |
| `Temp` | Air temperature |
| `Humi` | Relative humidity |
| `WS` | Wind speed |
| `WD` | Wind direction |
| `Solar` | Hourly accumulated global horizontal irradiation |

The response variable `Solar` is treated as **hourly global horizontal irradiation in MJ m⁻²**. Consistent with the manuscript terminology, *irradiance* refers to radiant power per unit area (W m⁻²), while *irradiation* refers to radiant energy per unit area accumulated over a specified interval.

## Integrity checks

The public notebook and `scripts/validate_data.py` verify that:

1. all required columns are present;
2. each day contains exactly the 11 target hours;
3. the chronology covers 2016-01-01 through 2020-12-31;
4. no duplicate date-hour rows exist;
5. no required values are missing;
6. irradiation values are non-negative;
7. manuscript mode contains the six expected sites.

## Provenance

The manuscript documents the Korea Meteorological Administration (KMA) Meteorological Data Open Portal / Automated Synoptic Observing System (ASOS) workflow as the source lineage for these processed observations.

The derivative files do **not** retain original station identifiers or separate sensor-metadata records. City coordinates used by the forecasting code are fixed reference coordinates for deterministic solar-geometry calculations and are not reconstructed sensor locations.

Users should verify the applicable KMA attribution and redistribution conditions before redistributing processed source-derived data.

## Reproducibility expectation

These files are intended to be used together with `notebooks/MUFASA_Public_Release.ipynb`. With the manuscript-aligned inputs and a compatible software/hardware environment, a top-to-bottom run should reproduce experimental behavior and summary metrics close to those reported in the manuscript. Small numerical differences may arise from GPU, CUDA/cuDNN, PyTorch, and floating-point implementation differences.

## Checksums

`checksums.sha256` records the SHA-256 hash of each supplied CSV. It should be regenerated whenever a dataset is intentionally replaced so that the exact data snapshot associated with a run remains auditable.
