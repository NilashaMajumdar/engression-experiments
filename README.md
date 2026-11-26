# Engression for temporal and spatiotemporal forecasting: experiments

Code, tuning studies, forecasts and results for the empirical evaluation of
engression-based forecasting models. The evaluation uses two weekly epidemic series and
one daily spatiotemporal air-quality dataset. Every model is tuned with Optuna on
validation CRPS. Final models are trained over 5 seeds and evaluated with point metrics
(MAE, RMSE, MASE, …) and probabilistic metrics (CRPS, 95% coverage and width, Winkler,
pinball).

## Experiments

| | [Univariate](univariate/) | [Multivariate (spatiotemporal)](multivariate/) |
| --- | --- | --- |
| Data | Venezuela dengue and malaria, weekly cases, 2002–2014 | Mumbai PM10, daily, 17 stations, 2021–2024 |
| Horizons | 4 and 13 weeks | 30 and 90 days |
| Engression models | EnBlockRNN, EnTCN, EnTransformer, EnTSMixer, EnBEATS, EnHiTS, EnTiDE ([`engressionts`](https://pypi.org/project/engressionts/) 0.1.1) | MVEN, GCEN, STEN ([`stengression`](https://pypi.org/project/stengression/) 0.0.3) |
| Benchmarks | The matching Darts architecture trained with Gaussian likelihood, using the engression model's tuned hyperparameters | DeepAR, Prob-iTransformer, GpGp, DiffSTG, STESN (default or published settings) |
| Main notebook | [`univariate/results/engressionts-results.ipynb`](univariate/results/engressionts-results.ipynb) | [`multivariate/results/final-testing.ipynb`](multivariate/results/final-testing.ipynb) |

Each folder has its own README with the setup, folder layout and steps to reproduce.

## Repository layout

```
data/           input data (Venezuela dengue and malaria, Mumbai PM10 and station coordinates)
univariate/     tuning notebooks, Optuna studies, final results
multivariate/   MVEN / GCEN / STEN tuning, final results for all 8 models
archive/        earlier per-model notebooks and studies, kept for reference (not needed to reproduce)
```

## Environment

All runs were on Kaggle with a Tesla T4 GPU and Python 3.13, `torch` 2.11 and `optuna` 5.1.
The two parts need **different Darts versions**, so use separate environments:

- univariate: `darts==0.47.0`, `engressionts==0.1.1` (Darts 0.48 breaks `engressionts` 0.1.1)
- multivariate: `stengression==0.0.3`, `darts==0.48.1`, `gluonts==0.17`, and R with `GpGp`

The exact package versions of each run are recorded in `*/results/config/run_info.json`.

## Data sources

- Dengue and malaria: [Epicasting](https://github.com/mad-stat/Epicasting)
- Mumbai PM10: [GraphSVR](https://github.com/ctanujit/GraphSVR/tree/main/Data)
