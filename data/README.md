# Data

All four files are public and are copied here unchanged so the notebooks can run
offline. The notebooks also download them from the original repositories if the
local copies are missing.

## Venezuela dengue and malaria (univariate experiments)

Source: [Epicasting](https://github.com/mad-stat/Epicasting) (`Datasets/Dengue_datasets/`,
`Datasets/Malaria_datasets/`).

| File | Rows | Period | Columns |
| --- | --- | --- | --- |
| `Venezuela_Dengue.xlsx` | 660 | 2002 week 1 – 2014 week 36 | `Year`, `Week`, `Cases` |
| `Venezuela_Malaria.xlsx` | 669 | 2002 week 1 – 2014 week 45 | `Year`, `Week`, `Cases` |

- Weekly reported case counts for Venezuela, indexed by epidemiological year and week.
- No missing weeks, duplicates or negative counts.
- The notebooks place the weeks on a Monday-based weekly index (`W-MON`) starting 2001-12-31.

| | Min | Max | Mean | Median | Skewness | CV |
| --- | --- | --- | --- | --- | --- | --- |
| Dengue | 165 | 3,606 | 1,038 | 811.5 | 1.57 | 0.67 |
| Malaria | 189 | 2,295 | 854 | 771 | 1.04 | 0.42 |

## Mumbai PM10 (multivariate experiments)

Source: [GraphSVR](https://github.com/ctanujit/GraphSVR/tree/main/Data).

| File | Shape | Contents |
| --- | --- | --- |
| `Mumbai_PM10_Data.csv` | 1,461 × 17 | Daily mean PM10 (µg/m³), one column per station (`V4` … `V30`) |
| `Mumbai_Stations_PM10.csv` | 17 × 3 | `Station`, `LAT`, `LONG` |

- The PM10 file has no date column. Its rows are the days 2021-01-01 to 2024-12-31, and its
  columns match the rows of the station file **by order**.
- Some missing days in the published data were filled by linear interpolation. These
  values are kept as published. `multivariate/results/final-testing.ipynb` flags them so
  the forecast figure shows stations with real measurements in the test period.
- Station distances are computed from the coordinates with the Haversine formula.
