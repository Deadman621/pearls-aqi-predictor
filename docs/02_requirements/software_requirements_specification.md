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

---

# 2. Overall Description

## 2.1 Product Perspective

The Pearls AQI Predictor is a standalone end-to-end machine learning system that automates the complete workflow of air quality forecasting for Karachi, Pakistan.

The system periodically collects meteorological and air pollution data from external services, transforms the collected data into machine learning features, trains forecasting models, generates pollutant predictions, derives the corresponding Air Quality Index (AQI), and presents the results through an interactive web dashboard.

The system follows a serverless architecture in which managed cloud services are used for feature storage, model management, workflow automation, and prediction serving, minimizing infrastructure management while supporting an automated MLOps workflow.

---

## 2.2 Product Functions

At a high level, the system provides the following functions:

- Collect historical and real-time meteorological and air pollution data.
- Perform feature engineering on collected data.
- Store engineered features in a centralized Feature Store.
- Train and evaluate machine learning models for pollutant forecasting.
- Register and manage trained model versions.
- Generate hourly pollutant forecasts for the next three days.
- Derive Air Quality Index (AQI) values from predicted pollutant concentrations.
- Present forecasts and supporting visualizations through a web dashboard.
- Explain model predictions using explainable AI techniques.
- Execute data collection and model retraining automatically according to predefined schedules.

---

## 2.3 User Classes and Characteristics

The system is designed for a single primary user.

### Primary User

The primary user is the project developer, who is responsible for configuring, monitoring, evaluating, and using the forecasting system.

The user is expected to possess:

- Basic understanding of machine learning concepts.
- Familiarity with software development workflows.
- Basic knowledge of air quality indicators and environmental data.

Future versions of the system may support additional user classes; however, multi-user capabilities are outside the scope of this project.

---

## 2.4 Operating Environment

The system operates across both cloud and local development environments.

The cloud environment hosts the automated machine learning workflow, including data ingestion, feature storage, model management, and scheduled pipeline execution.

The local environment is used for development, experimentation, exploratory data analysis, and maintenance activities.

Users access the forecasting dashboard through a modern web browser with an active internet connection.

---

## 2.5 Design and Implementation Constraints

The system shall be developed subject to the following constraints:

- The system shall forecast air quality exclusively for Karachi, Pakistan.
- The system shall rely on OpenWeather as the external environmental data provider.
- The system shall use Hopsworks as the Feature Store and Model Registry.
- The system shall execute scheduled workflows using GitHub Actions.
- The system shall follow a serverless architecture.
- The implementation shall use Python as the primary programming language.
- The project shall be developed as an individual academic project.
- The implementation shall comply with the usage limits imposed by external APIs and cloud services.

---

## 2.6 Assumptions and Dependencies

The system depends on the availability of several external services.

The following assumptions are made throughout the project:

- OpenWeather services remain available and provide accurate environmental data.
- Hopsworks services remain available for feature and model management.
- GitHub Actions successfully executes scheduled workflows.
- Internet connectivity is available whenever scheduled pipelines execute.
- External API schemas remain stable throughout the project duration.
- Required API credentials are valid throughout development and deployment.