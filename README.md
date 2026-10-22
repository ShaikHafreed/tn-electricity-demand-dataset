# Tamil Nadu Electricity Demand Dataset (2023)

A daily, feature-engineered dataset of Tamil Nadu's electricity demand for 2023, merging grid demand with weather, calendar, and festival/agriculture-season signals. Built as the training dataset for a demand-forecasting pipeline (Random Forest, R² = 0.88) on the MANGO analytics platform (IIT Madras WCTE).

## What's in it

- **365 daily rows**, 2023-01-01 to 2023-12-31
- **67 columns**: raw demand, raw weather, and ~30 engineered features (lags, rolling means, cyclical date encodings, ranges)

| File | Description |
|---|---|
| `data/tn_electricity_demand_2023.csv` | The full 365-row, 67-column dataset |

## Column dictionary

**Identifiers / calendar**
`Date`, `Year`, `Month`, `Month_Name`, `Day`, `Day_of_Week`, `Is_Weekend`, `Is_NonWorking_Day`, `Day_of_Year`, `Day_of_Year_Sin`, `Day_of_Year_Cos`, `Day_of_Week_Sin`, `Day_of_Week_Cos`

**Target**
`Electricity_Energy_Met_MU` — daily electricity demand met, in Million Units (MU). `Electricity_Data_Status` flags row-level data quality for this column.

**Weather** (min/mean/max where applicable)
`Temperature_Min/Mean/Max`, `Dewpoint_Min/Mean/Max`, `Solar_Radiation_Min/Mean/Max`, `Cloud_Cover_Min/Mean/Max`, `UTCI_Min/Mean/Max` (Universal Thermal Climate Index), `Wind_U_Mean`, `Wind_V_Mean`, `Rainfall_mm`, `Rainfall_Valid_Grid_Cell_Count`, `Rainfall_Valid_Area_Percent`, `Rainfall_Data_Status`, `Temperature_Range`, `Dewpoint_Range`, `Solar_Radiation_Range`, `Cloud_Cover_Range`, `Dewpoint_Depression`, `Rainy_Day_Flag`

**Calendar context (engineered)**
`Festival_Name`, `Festival_Flag`, `Agriculture_Season`, `Extreme_Weather_Flag`, `Extreme_Weather_Type`

**Lag / rolling features (engineered, derived from the target and weather columns)**
`Energy_Lag_1/7/14/28`, `Energy_RollingMean_7D/14D/28D`, `Rainfall_Lag_1D/7D`, `Rainfall_RollingSum_3D/7D`, `Temperature_Mean_Lag_1D/7D`, `Temperature_Mean_RollingMean_3D/7D`

**Provenance / quality**
`Data_Quality_Status`, `Source_Electricity`, `Source_Weather`, `Source_Rainfall`, `Notes`

## Known limitations

- **One missing value**: `2023-03-30` has an empty `Electricity_Energy_Met_MU` (flagged `Missing - Source verification pending` in `Data_Quality_Status`). It was left as a gap, not interpolated or zero-filled — handle it explicitly before training on this file.
- `Festival_Flag`, `Agriculture_Season`, `Extreme_Weather_Flag`, and all lag/rolling/cyclical columns are **engineered additions**, not part of the original raw demand or weather feeds — see Provenance below.
- This is a single calendar year (2023); it is not large enough on its own for long-horizon seasonal modeling without external data.

## Provenance and license

- **Raw electricity demand and weather** (temperature, dewpoint, wind, solar radiation, cloud cover, UTCI): sourced from **"Daily electricity demand data for Indian states (2014–2024)"** by **Kieran Hunt and Hannah Bloomfield**, published on Zenodo: [zenodo.org/records/14983362](https://zenodo.org/records/14983362), licensed **CC BY 4.0**.
- **Rainfall**: derived from public rainfall/climate sources for Tamil Nadu.
- **Festival, agriculture-season, lag/rolling features, and all other engineered columns**: built by the author (Shaik Hafreed) for this project.

Per the source license's attribution requirement: if you use this dataset, credit **Kieran Hunt and Hannah Bloomfield** for the original electricity demand and weather records, in addition to citing this repository for the merge and engineered features below. This repository's own contribution (merging, feature engineering, and this packaging) is released under **CC BY 4.0** — see `LICENSE`.

## Citation

If you use this dataset, please cite:

```
Shaik Hafreed (2026). Tamil Nadu Electricity Demand Dataset (2023).
GitHub: https://github.com/ShaikHafreed/tn-electricity-demand-dataset

Original electricity demand and weather records:
Hunt, K. and Bloomfield, H. Daily electricity demand data for Indian states (2014-2024).
Zenodo. https://doi.org/10.5281/zenodo.14983362
```

## Built with

Part of a demand-forecasting pipeline built on the [MANGO](https://mango.wcte.iitm.ac.in) analytics platform (IIT Madras WCTE). Model: Random Forest, R² = 0.88, MAE = 14.19 MU, MAPE = 4.03% on a held-out test split.
