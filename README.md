# Time Series Modeling and Forecasting of U.S. Energy Metrics

## Project Overview

This project explores historical U.S. nuclear energy data (1957-present)
sourced from the EIA's Monthly Energy Review (`MER_T08_01`), and investigates
how nuclear energy trends relate to U.S. energy policy events over time.

**Research Question:** How do nuclear energy capacity trends relate to policy
changes within the United States?

## Repository Structure

```
.
├── data/
│   ├── MER_T08_01__1_.csv              # Raw EIA Monthly Energy Review data (all 5 MSN series)
│   ├── MER_T08_01 (1)_NUCASUS.csv      # Split: Nuclear Capacity Factor
│   ├── MER_T08_01 (1)_NUETPUS.csv      # Split: Nuclear Electricity Net Generation
│   ├── MER_T08_01 (1)_NUETSUS.csv      # Split: Nuclear Share of Electricity Net Generation
│   ├── MER_T08_01 (1)_NUGBPUS.csv      # Split: Nuclear Generating Units, Average Burnup
│   ├── MER_T08_01 (1)_NUOUPUS.csv      # Split: Nuclear Generating Units, Total Operable Units
│   ├── NUCASUS.csv                     # Standalone Capacity Factor dataset
│   ├── NUETSUS.csv                     # Standalone Nuclear Share of Generation dataset
│   └── policy.csv                      # U.S. nuclear energy policy events (1957-present)
└── notebooks/
    ├── LinearRegression_DataVisualization.ipynb
    ├── NUCASUS_data.ipynb
    ├── NUETPUSdata__1_.ipynb
    └── NUETSUSdata__1_.ipynb
```

## Data Dictionary

| MSN | Description | Unit |
|---|---|---|
| NUCASUS | Nuclear Generating Units, Capacity Factor | Percent |
| NUETPUS | Nuclear Electricity Net Generation | Million Kilowatthours |
| NUETSUS | Nuclear Share of Electricity Net Generation | Percent |
| NUGBPUS | Nuclear Generating Units, Average Burnup | Various |
| NUOUPUS | Nuclear Generating Units, Total Operable Units | Number |

`policy.csv` contains a timeline of major U.S. policy and historical events
(e.g. Price-Anderson Act, Three Mile Island, Arab Oil Embargo) tagged as
Pro-Nuclear or Anti-Nuclear / negative-sentiment events, with a
`Policy_Indicator` column (+1 / -1) used to build a cumulative policy
sentiment score over time.

## Notebooks

### `LinearRegression_DataVisualization.ipynb`
- Loads the raw `MER_T08_01__1_.csv` and splits it into one CSV per MSN series.
- Cleans the `NUGBPUS` and `NUOUPUS` series (datetime conversion, numeric
  coercion, dropping "Not Available" rows).
- Engineers a continuous `Time` variable (months since year 0) for `NUOUPUS`.
- Fits a simple linear regression of `Value` over `Time` and reports R²,
  coefficient, and intercept, then plots actual vs. predicted values.

### `NUCASUS_data.ipynb`
- Analyzes the Nuclear Capacity Factor (`NUCASUS`) from 1957-present.
- Builds annual and monthly time series plots.
- Computes year-over-year change and identifies the largest single-year
  drops/spikes (notably the 1979 Three Mile Island accident).
- Merges with `policy.csv`, builds a cumulative policy sentiment score, and
  compares it against capacity factor on a dual-axis chart, including a
  5-year lagged version.
- Runs K-Means clustering (3 clusters) on lagged policy sentiment vs.
  year-over-year capacity factor change.

### `NUETPUSdata__1_.ipynb`
- Same analytical pipeline as above, applied to Nuclear Electricity Net
  Generation (`NUETPUS`, Million kWh).
- Identifies the largest single-year drop (1997) and discusses early plant
  retirements driven by cheap natural gas.

### `NUETSUSdata__1_.ipynb`
- Same pipeline applied to Nuclear Share of Electricity Net Generation
  (`NUETSUS`, %).
- Includes a deep-dive on the 1967-1977 window, when nuclear's share of U.S.
  electricity grew from 0.6% to 11.8%, contextualized against Cold War-era
  nuclear investment and the Vietnam War.

## How to Run

```bash
pip install pandas matplotlib scikit-learn jupyter
jupyter nbconvert --to notebook --execute --inplace notebooks/<notebook_name>.ipynb
```

Notebooks should be run from the `notebooks/` directory; all data paths are
relative (`../data/...`). Run `LinearRegression_DataVisualization.ipynb`
first if the split `MER_T08_01 (1)_*.csv` files in `data/` need to be
regenerated.

## Key Findings

- Nuclear capacity factor rose from roughly 50% in the 1970s to over 90% by
  the 2000s, reflecting major operational efficiency gains.
- The 1979 Three Mile Island accident corresponds to the steepest single-year
  drop in capacity factor (-6.1%), triggering stricter safety regulation.
- Nuclear's share of U.S. electricity generation grew sharply from 1967-1977
  (0.6% to 11.8%), aligning with Cold War-era nuclear expansion.
- Even when policy sentiment turned negative in the 1980s (post-Three Mile
  Island), nuclear output continued rising in the short term due to long
  construction lead times on already-approved plants, with the negative
  sentiment showing up as a lag of several years in output growth.
- K-Means clustering of lagged policy sentiment vs. year-over-year output
  change identifies distinct regimes where plants improved performance even
  during periods of negative policy sentiment, versus periods where both
  policy and output declined together.
