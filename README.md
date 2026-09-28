[README.md](https://github.com/user-attachments/files/32775804/README.md)
# Pipeline Health Check — EDA, Corruption & Distribution Shift

## Objective

Develop a reproducible data-quality and drift-monitoring workflow for a country-year panel, combining domain checks, exploratory analysis, and distribution comparisons before the data is used in a model.

## Methodology

- Inspected summary statistics, value distributions, and country-year counts to diagnose five planted problems: negative GDP, life expectancy recorded in months, duplicate country-year observations, inconsistent trade-percentage units and invalid entries, and GDP values in the wrong scale.
- Applied documented corrections to recover comparable units, kept one observation per country-year, and removed trade entries whose original values could not be recovered. Compared the 260-row source with the 230-row cleaned panel.
- Split the cleaned data into 138 training and 92 inference rows, increased inference GDP by 30% to simulate drift, and computed the Population Stability Index (PSI) for each numeric variable.
- Compared manual EDA with the available automated profiling summary. Packaged constraint checks, PSI calculation, and EDA summaries in `eda_utils.py`, then used the module in an interactive pipeline-health dashboard with editable constraints and drift alerts.

## Key Findings

- The original data had five distinct corruption mechanisms. An exact-row duplicate check found zero duplicates because duplicated country-year observations had slightly different GDP values; checking the country-year key exposed them.
- GDP showed a substantial distribution shift between training and inference (**PSI = 2.0000**). Population (**0.0917**), life expectancy (**0.1635**), and trade share (**0.1553**) also produced nonzero PSI values despite no deliberate shift, illustrating why fixed PSI cutoffs can overstate drift in small samples.
- Automated profiling can surface outliers and extreme ranges, but it cannot infer the intended GDP and life-expectancy units or the panel's unique key from values alone. The dashboard pairs those domain checks with distribution monitoring while keeping alerts subject to source investigation.

*PSI thresholds of 0.10 and 0.25 are conventions, not significance tests. Production alert levels should be calibrated against PSI values from unchanged data at the relevant sample size.*
