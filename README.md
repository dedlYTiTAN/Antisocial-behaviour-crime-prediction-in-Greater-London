# Antisocial Behaviour Crime Prediction in Greater London
This project implements a spatio-temporal modelling pipeline to analyse and predict anti-social behaviour (ASB) crime patterns across Greater London using machine learning and deep learning on large-scale crime and geospatial data.
​

# Project Overview
Title in notebook: “Spatio-Temporal Prediction of Anti-Social Behaviour in London using Machine Learning and DEEP LEARNING”.
Uses a combined street-level crime dataset for London (Metropolitan Police, City of London, British Transport Police) from 2022–2024, with over 3.1 million records.
Focuses on aggregating ASB crimes on a regular spatial grid over time and learning how crime levels evolve monthly in each grid cell.
​

# Data and Preprocessing
Loads a combined CSV file (e.g. combined_data.csv) containing crime records for multiple forces and years.
Uses Google Colab to mount cloud storage and read the large dataset efficiently.
Unzips and loads UK boundary shapefiles and Greater London shapefiles (including OSM buildings, land use, natural features, places, POIs) for spatial processing.
Constructs a grid over London and aggregates crime counts, past (lag) crime counts, and points-of-interest (POI) counts per grid cell and month.
​

# Features and Target
**Spatial index:** grid_id identifies each grid cell in Greater London.​
**Temporal features:** monthly crime counts from May 2022 to December 2024 are stored as separate columns (e.g. 2022-05, 2022-06, …, 2024-12).​
**Additional features:** poi_counts, historical crime_counts_lag, and the cell’s geometry (polygon) are included in the final dataset.
**Target variable:** crime_counts represents ASB crime counts to be predicted for each grid cell and time step.
​

# Modelling and Evaluation
Builds spatio-temporal models that combine historical counts and contextual features to predict future ASB crime levels per grid cell.
Includes a spatial lag model component (e.g. slm_pred) capturing the influence of neighbouring cells, and corresponding residuals (resid_slm).
Produces a final modelling table with 1,736 rows and 39 columns, including all temporal features, crime counts, spatial geometry, lag features, predictions and residuals.
​

**Notebook**
**Main notebook**: Crime-Prediction-In-Greater-London-1.ipynb.​

Organised into sections such as dataset loading, spatial data preparation, feature engineering, model training, and diagnostics/visualisation.
