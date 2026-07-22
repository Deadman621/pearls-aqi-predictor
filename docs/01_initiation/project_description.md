# Pearls AQI Predictor

## Project Overview

Predict the Air Quality Index (AQI) in your city for the next **3 days** using a **100% serverless** machine learning stack.

This project builds an end-to-end ML pipeline for AQI forecasting, including:

- Automated data collection
- Feature engineering
- Model training
- Model registry
- Real-time predictions
- Interactive web dashboard

**Project Description:**
https://drive.google.com/file/d/1HPf17hvqI6icNTjRPkPuydkV1ub_lxO5/view?usp=sharing

---

## Technology Stack

### Core Technologies

- Python
- Scikit-learn
- TensorFlow
- Hopsworks or Vertex AI
- Apache Airflow or GitHub Actions
- Streamlit
- Flask
- AQICN or OpenWeather APIs
- SHAP
- Git

---

## Key Features

### Feature Pipeline Development

- Fetch raw weather and pollutant data from AQICN or OpenWeather APIs
- Compute engineered features, including:
  - Time-based features (hour, day, month)
  - AQI change rate
- Store processed features in a Feature Store (Hopsworks or Vertex AI)

### Historical Data Backfill

- Run the feature pipeline on historical dates
- Generate a complete dataset for training and evaluation

### Training Pipeline

- Retrieve historical features and targets from the Feature Store
- Train and compare multiple models:
  - Random Forest
  - Ridge Regression
  - TensorFlow/PyTorch models
- Evaluate using:
  - RMSE
  - MAE
  - R² Score
- Store trained models in a Model Registry

### Automated CI/CD

- Run the feature pipeline every hour
- Retrain models daily
- Automate workflows using Apache Airflow, GitHub Actions, or similar tools

### Web Application Dashboard

- Load models and features from the Feature Store
- Generate real-time AQI predictions for the next 3 days
- Display results using Streamlit/Gradio with Flask/FastAPI

### Advanced Analytics

- Perform Exploratory Data Analysis (EDA)
- Explain predictions using SHAP or LIME
- Generate alerts for hazardous AQI levels
- Support multiple forecasting approaches, from statistical models to deep learning

---

## Project Resources

- Project Description:
  https://drive.google.com/file/d/1HPf17hvqI6icNTjRPkPuydkV1ub_lxO5/view?usp=sharing
