# Software Requirements Specification (SRS)

**Project:** Pearls AQI Predictor

**Version:** 1.0

**Date:** 2026-07-22

---

# 1. Introduction

## 1.1 Purpose

The purpose of this Software Requirements Specification (SRS) is to define the functional and non-functional requirements for the Pearls AQI Predictor. The document establishes a common understanding of the system's objectives, expected behavior, constraints, and quality attributes for all stages of development.

This specification serves as the primary reference for system architecture, detailed design, implementation, testing, and deployment throughout the project lifecycle.

---

## 1.2 Scope

The Pearls AQI Predictor is a serverless machine learning system that forecasts hourly air quality conditions for Karachi, Pakistan, for the next three days.

The system automatically collects historical and real-time meteorological and air pollution data from OpenWeather, performs feature engineering, trains machine learning models to predict individual pollutant concentrations, derives the corresponding Air Quality Index (AQI), and presents the predictions through an interactive web dashboard.

In addition to forecasting, the system provides automated data collection, scheduled model retraining, model version management, explainability through SHAP, and visualization of forecast results.

The system is intended for educational purposes as an end-to-end demonstration of modern machine learning operations (MLOps) practices using a fully serverless architecture.

---

## 1.3 Definitions, Acronyms, and Abbreviations

| Term | Definition |
|------|------------|
| ADR | Architecture Decision Record |
| AQI | Air Quality Index |
| API | Application Programming Interface |
| CI/CD | Continuous Integration / Continuous Deployment |
| EDA | Exploratory Data Analysis |
| Feature Store | Centralized repository for engineered machine learning features |
| GitHub Actions | Workflow automation platform used for scheduled pipeline execution |
| Hopsworks | Feature Store and Model Registry platform |
| MLOps | Machine Learning Operations |
| RMSE | Root Mean Squared Error |
| MAE | Mean Absolute Error |
| R² | Coefficient of Determination |
| SHAP | SHapley Additive exPlanations |
| SRS | Software Requirements Specification |

---

## 1.4 References

The following documents were used during the planning and requirements analysis of this project.

1. Project Description – Pearls AQI Predictor
2. OpenWeather API Documentation
3. Hopsworks Documentation
4. GitHub Actions Documentation
5. Scikit-learn Documentation
6. PyTorch Documentation
7. IEEE 29148 – Systems and Software Requirements Engineering
8. Architecture Decision Records (ADRs)