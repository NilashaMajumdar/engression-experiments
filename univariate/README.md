# Univariate experiments

Seven engression models from [`engressionts`](https://pypi.org/project/engressionts/)
(EnBlockRNN, EnTCN, EnTransformer, EnTSMixer, EnBEATS, EnHiTS, EnTiDE) compared with their
probabilistic Darts counterparts (Gaussian likelihood) on weekly Venezuela dengue
(2002 W1 – 2014 W36) and malaria (2002 W1 – 2014 W45) data, at horizons of 4 and 13 weeks.
The data are from [Epicasting](https://github.com/mad-stat/Epicasting) and are also in [`../data/`](../data/).

## Contents

- `tuning/`: Optuna tuning notebooks (one per model × horizon)
- `studies/`: finished Optuna studies (`.db`)
- `results/engressionts-results.ipynb`: final training and evaluation of all models
- `results/`: config, metrics, tables (CSV + LaTeX), figures and forecasts

## Reproducing

Requires `darts==0.47.0` (0.48 breaks `engressionts 0.1.1`), `engressionts==0.1.1`,
`pytorch-lightning`, `optuna` and `openpyxl`. The experiments were run on Kaggle with a T4 GPU.

1. (Optional) Rerun the tuning notebooks. The finished studies are already in `studies/`.
2. Run `results/engressionts-results.ipynb` with `studies/` and the data files attached,
   and `RUN_MODELS = "all"`. Finished runs are cached, so rerunning it after an interrupted
   session only trains what is missing.
