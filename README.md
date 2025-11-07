# Cancer Trends Project

## Overview

This project investigates trends in cancer incidence and mortality across Ontario's Public Health Units (PHUs) and situates these trends within their socio-economic context. The objective is to merge and analyze cancer snapshot data with contextual covariates reflecting social determinants of health (SDOH) to better understand how population characteristics relate to disease patterns.

Originally a marginalization dataset was considered for context, but integration issues lead to its removal. Instead, the cleaned SDOH dataset is used.

## Data Sources

- **Cancer Incidence Snapshot (2010–2014):** Stored in `Cancer_Incidence_Snapshot_2010_2014.csv`. Contains counts and age-standardized rates for all cancer types across PHUs.
- **Cancer Mortality Snapshot (2003–2015):** Stored in `Cancer_Mortality_Snapshot_2003_2015.csv`. Contains counts and age-standardized rates for all cancer types across PHUs.
- **SDOH Data:** Original Excel (`Ontario Social Determinants of Health Data.xlsx`) pivoted to wide format and stored in `SDOH_Clean_Wide.csv`. Contains socio-economic indicator percentages for PHUs.

## Data Cleaning and Harmonization

- **PHU Names:** The old units “Huron County Health Unit” and “Perth District Health Unit” were merged into “Huron Perth Public Health” to reflect their 2020 amalgamation. Names are harmonized across all files.
- **SDOH Pivot and Cleaning:** The SDOH sheet has been pivoted so each PHU-year occupies one row and each indicator its own column. Numeric values coerced to float; blank values appear as NaN.
    - Indicators: 21 variables (e.g. Per cent (%) immigrant population, Per cent (%) lone parent households, Per cent (%) of households spending more than 30% of their income on shelter costs, Per cent (%) of the labour force population who are unemployed, Per cent (%) of the population in the labour force (participation rate), ...).
    - PHUs covered: 54.
    - Years available: [2016, 2021].

## Usage Instructions

To load the data in Python:
```python
import pandas as pd
inc = pd.read_csv("Cancer_Incidence_Snapshot_2010_2014.csv")
mort = pd.read_csv("Cancer_Mortality_Snapshot_2003_2015.csv")
sdoh = pd.read_csv("SDOH_Clean_Wide.csv")
```
Create a clean key for merging:
```python
for df in (inc, mort, sdoh):
    df["Geography_clean"] = df["Geography"].str.strip().str.lower()
```
Perform a merge:
```python
merged = inc.merge(sdoh, on=["Geography_clean","Year"], how="left")
```
Adjust `how` param as needed (e.g. left, inner, outer). Handle NaNs explicitly.

## Analysis Ideas

- Correlate incidence/mortality with SDOH indicators.
- Regression or time-series modeling with SDOH variables as covariates.
- Cluster PHUs based on context and outcomes.
- Trend plots across PHUs or groups.

## Caveats

- SDOH available only for years [2016, 2021], while cancer data cover other years. Choose appropriate alignment or averaging.
- Marginalization data is excluded. If later included, ensure matching and normalization.
- The merger of Huron and Perth units means pre-2020 they were separate jurisdictions; they are unified here. Modify grouping if analysis by pre-merger is desired.

## File Summary

- `Cancer_Incidence_Snapshot_2010_2014.csv`: Cleaned incidence data.
- `Cancer_Mortality_Snapshot_2003_2015.csv`: Cleaned mortality data.
- `SDOH_Clean_Wide.csv`: Copied SDOH snapshot in pivoted form.
- `README.md`: This file.
