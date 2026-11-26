# Multivariate (spatiotemporal) experiments

Three spatiotemporal engression models from
[`stengression`](https://pypi.org/project/stengression/) (v0.0.3): MVEN (multivariate,
no spatial module), GCEN (graph-convolutional) and STEN (spatial-lag). They are compared
with five probabilistic benchmarks on daily PM10 concentrations (µg/m³) at 17 monitoring
stations in Mumbai, India, from 2021-01-01 to 2024-12-31 (1,461 days). The forecast
horizons are 30 and 90 days.
The data are from [GraphSVR](https://github.com/ctanujit/GraphSVR/tree/main/Data)
(`Mumbai_PM10_Data.csv`, `Mumbai_Stations_PM10.csv`) and are also in [`../data/`](../data/).

The protocol follows Pathak & Chakraborty (2026), *Deep Generative Spatiotemporal
Engression for Probabilistic Forecasting of Epidemics* (TMLR).

## Setup

| | H = 30 | H = 90 |
| --- | --- | --- |
| Training | 2021-01-01 → 2024-10-02 | 2021-01-01 → 2024-06-04 |
| Validation | 2024-10-03 → 2024-12-01 (60 days) | 2024-06-05 → 2024-10-02 (120 days) |
| Test | 2024-12-02 → 2024-12-31 | 2024-10-03 → 2024-12-31 |

- Lookback: 60 days. All 17 stations are forecast jointly from one forecast origin.
- Each station is standardised with its training mean and sd. Forecasts are scored on the original scale.
- The gap-filled values in the published data are kept as they are.
- Engression training: energy score (β = 1, M = 2), Adam, 100 epochs, batch size 64.
- Tuning: Optuna (TPE), 100 trials per model and horizon. The objective is validation CRPS from 100 samples.
- Final runs: train on training + validation, seeds 1–5. Per seed, 50 forecast ensembles of 100 trajectories each.
  - Median = point forecast; 2.5% and 97.5% quantiles = 95% interval.
  - Metrics are averaged over the 50 ensembles, then reported as mean (sd) over seeds.
- GCEN graph: thresholded Gaussian kernel on Haversine distances (σ² ≈ 0.995, ε ≈ 0.077), with self-loops.
  - With the distance scaling used ("method A"), this gives a complete graph.
- STEN: row-normalised inverse-distance weights. α and the maximum spatial lag L are tuned in {1, 2, 3}.

### Benchmarks

Each benchmark uses its default or published settings and is **not tuned** on this dataset.

| Model | Type | Implementation |
| --- | --- | --- |
| DeepAR | temporal | Darts (Gaussian likelihood, 100 epochs) |
| Prob-iTransformer | temporal | GluonTS (Student-t output, 100 epochs) |
| GpGp | spatiotemporal GP | R package `GpGp` (`matern_spheretime`) |
| DiffSTG | spatiotemporal diffusion | [authors' code](https://github.com/wenhaomin/DiffSTG); GCEN's graph, early stopping on validation |
| STESN | spatiotemporal echo-state network | [authors' code](https://github.com/hhuang90/KSA-wind-forecast); 100 reservoirs = 100 samples, raw ensemble |

Each benchmark produces one ensemble of 100 trajectories per seed.

## Contents

```
multivariate/
├── mven/
│   ├── MVEN_tuning_Mumbai_PM10 H30,H90.ipynb   tuning notebook
│   ├── config/   best hyperparameters (one JSON per horizon)
│   └── studies/  Optuna studies (.db)
├── gcen/
│   ├── tuning/   gcen-tune-short-1.ipynb, gcen-tune-long-1.ipynb
│   ├── config/   best_params_{short,long}_{1,2}.json
│   └── studies/  Optuna studies (.db)
├── sten/
│   ├── sten-h30-h90.ipynb   tuning notebook (both horizons)
│   ├── config/   best hyperparameters (one JSON per horizon)
│   └── studies/  Optuna studies (.db)
└── results/
    ├── final-testing.ipynb   final training and evaluation of all 8 models
    ├── config/      run_info.json (settings, tuned values, package versions), splits.csv, best_params.json
    ├── metrics/     test metrics per seed, mean, sd; validation vs test CRPS; STEN lag importance
    ├── tables/      S1 training times, S2 seed sd, S3 hyperparameters, S4 all metrics (CSV + LaTeX)
    ├── figures/     forecasts (selected and all stations), CRPS/coverage, PIT, STEN weights
    └── forecasts/   <model>/{short,long}/H<h>_seed<s>.npy, plus forecast_quantiles.csv
```

The GCEN files ending in `_1` are the configuration used in the chapter: method A, a
complete graph. The `_2` files come from a 10-km-scaled graph (method B), which was
compared but not used. Each `forecasts/*.npy` array has shape `(100, H, 17)`
(samples × days × stations).

## Reproducing

Run on Kaggle with a Tesla T4 GPU, Python 3.13, `stengression==0.0.3`, `torch` 2.11,
`optuna` 5.1, `darts` 0.48.1 and `gluonts` 0.17. GpGp and STESN run on the CPU. The
notebook installs R and `GpGp` if they are missing, and clones the DiffSTG and STESN
repositories itself.

1. (Optional) Rerun the tuning notebooks in `mven/`, `gcen/tuning/` and `sten/`. The
   finished studies and best hyperparameters are already included.
2. Run `results/final-testing.ipynb` with the data files and the three `config/` folders
   attached. The notebook:
   - checks each tuning file against the data fingerprint (MD5);
   - uses GCEN method A (`GCEN_METHOD = "A"`);
   - saves each finished model × horizon × seed run, so a rerun after an interrupted
     session only trains what is missing. `RUN_MODELS` can split the work across sessions;
   - offers `QUICK_RUN = True` to test the flow only (its results are not meaningful).
