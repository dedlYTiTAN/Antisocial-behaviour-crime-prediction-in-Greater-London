# Spatio-Temporal Prediction of Anti-Social Behaviour in Greater London

Predicting where anti-social behaviour (ASB) will be concentrated next month, on a 1 km grid across Greater London, using police-recorded crime data, OpenStreetMap points of interest, and spatial and temporal lag features. Two models are compared: gradient-boosted trees (XGBoost) and a causal 1D convolutional neural network.

> **Status: research notebook.** Results are from a single held-out month and have not yet been compared against simple baselines such as "same as last month". See [Limitations](#limitations) before drawing conclusions.

## Data

The data is not included in this repository.

| Source | Use |
|---|---|
| Street-level crime data from [data.police.uk](https://data.police.uk) for the Metropolitan Police, City of London Police and British Transport Police, 2022 to 2024, combined into `combined_data.csv` | Crime records: 3,114,670 after removing rows with missing coordinates, of which 625,032 are anti-social behaviour |
| OpenStreetMap shapefile layers for Greater London (points of interest file `gis_osm_pois_free_1.shp`) | Points-of-interest counts per grid cell |
| UK and Greater London boundary shapefiles | Defining the London boundary and the grid |

Contains public sector information licensed under the Open Government Licence v3.0. Map data © OpenStreetMap contributors (ODbL).

## Method

1. **Clean and filter.** Drop records with missing coordinates, then keep `Anti-social behaviour` records.
2. **Grid.** Build a 1 km grid over Greater London and assign each record to a cell with a spatial join.
3. **Monthly counts.** Count ASB incidents per cell per month, May 2022 to December 2024, giving 1,736 cells with a monthly series each.
4. **Points of interest.** Count OpenStreetMap points of interest per cell.
5. **Spatial statistics.** Moran's I for spatial autocorrelation, a spatial lag model (`spreg`), and Gi* hotspot and coldspot maps.
6. **Features.**
   - `crime_counts_tlag1`: the cell's count in the previous month.
   - `crime_counts_lag`: the average count of neighbouring cells (Queen contiguity) in the same month.
   - `poi_counts`: points of interest in the cell.
7. **Models and split.** Trained on 2023, tested on January 2024 (1,736 test cells).
   - **XGBoost:** 600 trees, depth 4, learning rate 0.05, subsample and column sample 0.9, L2 regularisation 1.0.
   - **Causal 1D-CNN:** six-month windows per cell; three causal convolution layers (64, 64, 32 filters), dropout 0.2, global average pooling, dense layers (64, 16, 1); Adam (1e-3), up to 250 epochs, batch size 256, 10% of the training data held out for checkpoint selection. Features are scaled using training data only.

## Results (January 2024, 1,736 grid cells)

| Model | RMSE | MAE | R² |
|---|---|---|---|
| XGBoost | 5.396 | 3.180 | 0.853 |
| 1D-CNN | 5.215 | 3.060 | 0.863 |

Figures are the values printed by the evaluation cells (RMSE and MAE are in incidents per cell per month). The notebook also includes predicted-versus-actual scatter plots and maps of the predictions.

## Limitations

- **No baseline yet.** The models have not been compared with simple forecasts such as last month's count, a moving average, or the same month last year. Since last month's count is already an input, such a baseline may score close to the figures above. Without it the models' added value is unknown.
- **XGBoost uses information from the month it predicts.** Its `crime_counts_lag` feature is the neighbouring cells' count in the same month as the target, which would not be known in a real forecast. The CNN uses only earlier months, so the two models are not compared on equal information.
- **One test month.** January 2024 alone says little about stability or seasonality, and the 1,736 cells are spatially correlated, so they are not independent observations.
- **Count data.** ASB counts are skewed and overdispersed, and squared-error metrics are dominated by a few busy central cells. A count-appropriate loss (Poisson or Tweedie) and scaled errors would be more informative.
- **No demographic or deprivation covariates.** Features are limited to past counts, neighbour counts and points of interest.
- **What the data measures.** Recorded ASB reflects reporting and policing patterns, not only underlying behaviour. This is a modelling exercise, not an operational tool. Predictive-policing systems can reinforce existing bias through feedback loops, and any real use would need careful ethical review.
- **Not reproducible as-is.** The notebook mounts Google Drive in Google Colab and expects the data files at Colab paths.

## Suggested next steps

1. Add baselines: last month, three-month average, same month last year.
2. Replace the same-month spatial lag with the neighbours' lag at month t-1 so XGBoost and the CNN see the same information.
3. Evaluate on several rolling test months and report the mean and spread.
4. Compute the comparison chart from the model predictions instead of typed-in values.
5. Try Poisson or Tweedie objectives, and add deprivation and population covariates.

## Running the notebook

The notebook is `Crime Prediction In Greater London.ipynb`. It was developed in Google Colab.

```bash
pip install geopandas shapely libpysal esda spreg contextily scikit-learn xgboost tensorflow pandas numpy matplotlib
```

1. Download the street-level crime CSVs for the three forces from data.police.uk and combine them into `combined_data.csv`.
2. Download the OpenStreetMap shapefile layers for Greater London and the UK and London boundary shapefiles.
3. Update the file paths in the first cells (they point to Google Drive), then run the notebook top to bottom.

## Author

Keerthirajan Murugesan, MSc Artificial Intelligence and Data Science, University of Hull.
