# Antisocial-behaviour-crime-prediction-in-Greater-London

This project explores and models **antisocial behaviour (ASB) crime** in Greater London using open crime data and machine learning, with the goal of identifying patterns and predicting future ASB incidents at a local area level. 

## 🎯 Objectives

- Analyse spatial and temporal patterns of antisocial behaviour crime in Greater London. 
- Engineer features from historical crime records and contextual data (e.g. area, time, category).
- Train and evaluate predictive models to estimate future ASB counts or risk by area. 

## 📂 Project Structure

Example structure (update to match your repo):

- `data/` – Raw and processed crime datasets (not tracked in Git, or with sample data only).
- `notebooks/` – Exploratory data analysis (EDA) and modelling notebooks.
- `scripts/` – Python scripts for data cleaning, feature engineering, and training.
- `models/` – Saved model artefacts (if applicable).
- `reports/` – Generated plots and summary reports.
- `README.md` – Project documentation (this file).

## 🧮 Data

- Source: UK police / open crime data for Greater London, filtered to the **anti-social behaviour** category. 
- Typical fields: date, longitude/latitude or LSOA, crime type, context.
- Aggregation: records grouped by spatial unit (e.g. borough, ward, LSOA, or grid cell) and time period (e.g. month). 

> Note: Due to licensing and size, raw data files may be excluded from the repository. Instructions for downloading are provided in the notebooks or scripts.

## 🛠 Tech Stack

- Python (>= 3.9)
- Data: pandas, NumPy
- Visualisation: matplotlib / seaborn / plotly
- Machine Learning: scikit-learn (and/or XGBoost, LightGBM, etc.) 
- Geospatial (optional): GeoPandas, shapelyb:

