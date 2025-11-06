# 📘 README: Cancer Incidence and Mortality Trends in Ontario (PHU-Level)

## 📌 Project Overview

This project analyzes **cancer incidence and mortality trends** across Ontario's Public Health Units (PHUs), focusing on **age-standardized rates for all cancers**. The primary goal is to explore temporal patterns, regional disparities, and potential correlations between incidence and mortality from 2003 to 2015. You can also enrich the analysis with social and contextual information: our cleaned Social Determinants of Health (SDOH) data and the ON-Marg index provide additional lenses on the social environment of each PHU.

---

## 📂 Included Files

### 1. `Cancer_Incidence_AllCancers_AgeStandardized.csv`
- **Source**: Ontario Cancer Incidence Snapshot (2010–2014)
- **Description**: Age-standardized incidence rates for all cancers across 55 PHUs.
- **Columns**:
  - `Geography`: PHU name
  - `2010` to `2014`: Incidence rates per year
  - `Geography_clean`: Lowercased PHU name for merging

### 2. `Cancer_Mortality_AllCancers_AgeStandardized.csv`
- **Source**: Ontario Cancer Mortality Snapshot (2003–2015)
- **Description**: Age-standardized mortality rates for all cancers across 55 PHUs.
- **Columns**:
  - `Geography`: PHU name
  - `2003` to `2015`: Mortality rates per year
  - `Geography_clean`: Lowercased PHU name for merging

### 3. `SDOH_2021_Selected.csv`
- **Source**: Ontario Social Determinants of Health Data (snapshot for 2021)
- **Description**: Key social indicators for PHUs to provide context to the cancer trends. Rather than all 21 measures, this cleaned file contains a selection of representative variables; you can expand the list or pivot the original data in a similar fashion.
- **Columns**:
  - `Geography_clean`: Lowercased PHU name to match the cancer files
  - `% immigrant population`, `% low income (LIM-AT) - All ages`, `% employed`, `% without a high school diploma`, `% visible minority population` (the selected measures; indicator names appear verbatim in the column headers)

### 4. `Ontario_Marginalization_PHU_Cleaned.csv`
- **Source**: Cleaned ON-Marg file derived from the Ontario Marginalization Index
- **Description**: Deprivation and marginalization dimensions across PHUs (e.g. material deprivation, residential instability, dependency, ethnic concentration) along with population values.
- **Columns**: includes the four ON-Marg quintile scores, population and other summary statistics, and a `Geography_clean` field for merging

---

## 🧼 Data Cleaning Notes

- The cancer files were read using the correct header row (`header=2`) to skip metadata, filtered to the "All cancers" indicator and the "Age-standardized rate (both sexes)" measure, and pivoted into wide format (years as columns). A `Geography_clean` field was added to enable joins.
- The SDOH file was similarly read with `header=2`, restricted to 2021 values, filtered to a handful of key indicators, pivoted to wide format, and cleaned (`Geography_clean` added).
- The ON-Marg file is a cleaned version of the downloaded index and already contains a cleaned join key.
- All tables share a consistent join key (`Geography_clean`), facilitating merges across files.

---

## 📊 Suggested Analyses

- **Trend Visualization**: Plot line graphs of incidence and mortality over time for individual PHUs or aggregated groups.
- **Regional Comparison**: Identify PHUs with increasing/decreasing cancer trends, high or low peak values, or other anomalous behaviour.
- **Incidence vs Mortality**: Compare the trajectories side-by-side for overlapping years (2010–2014), e.g. compute ratios or differences.
- **Aggregate Statistics**: Derive provincial-level summaries (means, medians, confidence intervals) or compute variation across PHUs.
- **Contextual Analysis**: Use the SDOH and ON-Marg data to stratify PHUs by social factors—ask questions like "Do PHUs with high deprivation also show higher incidence or mortality?" or "Is the trend slope associated with levels of employment or education?".
- **Regression/Modeling**: Fit simple models (linear regression, mixed effects) to examine how social variables predict variation in cancer rates or their change over time.
- **Clustering or Dimension Reduction**: Group PHUs based on combined patterns of cancer trends and social characteristics to discover types of regions.

---

## 📎 How to Use

1. Load the CSV files using pandas:
   ```python
   import pandas as pd
   inc  = pd.read_csv('Cancer_Incidence_AllCancers_AgeStandardized.csv')
   mort = pd.read_csv('Cancer_Mortality_AllCancers_AgeStandardized.csv')
   sdoh = pd.read_csv('SDOH_2021_Selected.csv')
   marg = pd.read_csv('Ontario_Marginalization_PHU_Cleaned.csv')
   ```

2. Align years for comparison if desired:
   ```python
   years = ['2010','2011','2012','2013','2014']
   inc_common  = inc[years + ['Geography_clean']]
   mort_common = mort[years + ['Geography_clean']]
   ```

3. Merge across files on the `Geography_clean` key:
   ```python
   merged = inc_common.merge(mort_common, on='Geography_clean', suffixes=('_inc','_mort'))
   merged = merged.merge(sdoh, on='Geography_clean', how='left')
   merged = merged.merge(marg, on='Geography_clean', how='left')
   ```
   Adjust the `how=` argument depending on whether you want to keep all PHUs or only those present in all datasets.

4. Perform analyses on the `merged` DataFrame or its components.

---
