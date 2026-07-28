# Software Requirements Specification (SRS)

**Project:** Pearls AQI Predictor

**Version:** 1.0

**Date:** 2026-07-22

---

# Table of Contents

- [Software Requirements Specification (SRS)](#software-requirements-specification-srs)
- [Table of Contents](#table-of-contents)
- [1. Introduction](#1-introduction)
  - [1.1 Purpose](#11-purpose)
  - [1.2 Scope](#12-scope)
  - [1.3 Definitions, Acronyms, and Abbreviations](#13-definitions-acronyms-and-abbreviations)
  - [1.4 References](#14-references)
- [2. Overall Description](#2-overall-description)
  - [2.1 Product Perspective](#21-product-perspective)
  - [2.2 Product Functions](#22-product-functions)
  - [2.3 User Classes and Characteristics](#23-user-classes-and-characteristics)
    - [Primary User](#primary-user)
  - [2.4 Operating Environment](#24-operating-environment)
  - [2.5 Design and Implementation Constraints](#25-design-and-implementation-constraints)
  - [2.6 Assumptions and Dependencies](#26-assumptions-and-dependencies)
- [3. Functional Requirements](#3-functional-requirements)
  - [3.1 Data Collection](#31-data-collection)
    - [FR-001](#fr-001)
    - [FR-002](#fr-002)
    - [FR-003](#fr-003)
  - [3.2 Feature Engineering](#32-feature-engineering)
    - [FR-004](#fr-004)
    - [FR-005](#fr-005)
  - [3.3 Model Training](#33-model-training)
    - [FR-006](#fr-006)
    - [FR-007](#fr-007)
    - [FR-008](#fr-008)
  - [3.4 Prediction](#34-prediction)
    - [FR-009](#fr-009)
    - [FR-010](#fr-010)
  - [3.5 Explainability](#35-explainability)
    - [FR-011](#fr-011)
  - [3.6 Dashboard](#36-dashboard)
    - [FR-012](#fr-012)
    - [FR-013](#fr-013)
    - [FR-014](#fr-014)
  - [3.7 Workflow Automation](#37-workflow-automation)
    - [FR-015](#fr-015)
    - [FR-016](#fr-016)
    - [FR-017](#fr-017)
- [4. External Interface Requirements](#4-external-interface-requirements)
  - [4.1 User Interface](#41-user-interface)
    - [UI-001 Dashboard](#ui-001-dashboard)
  - [4.2 Software Interfaces](#42-software-interfaces)
    - [SI-001 OpenWeather API](#si-001-openweather-api)
    - [SI-002 Hopsworks](#si-002-hopsworks)
    - [SI-003 GitHub Actions](#si-003-github-actions)
  - [4.3 Communication Interfaces](#43-communication-interfaces)
  - [4.4 Hardware Interfaces](#44-hardware-interfaces)
- [5. Non-Functional Requirements](#5-non-functional-requirements)
  - [5.1 Performance](#51-performance)
    - [NFR-001](#nfr-001)
    - [NFR-002](#nfr-002)
    - [NFR-003](#nfr-003)
  - [5.2 Reliability](#52-reliability)
    - [NFR-004](#nfr-004)
    - [NFR-005](#nfr-005)
  - [5.3 Availability](#53-availability)
    - [NFR-006](#nfr-006)
  - [5.4 Maintainability](#54-maintainability)
    - [NFR-007](#nfr-007)
    - [NFR-008](#nfr-008)
    - [NFR-009](#nfr-009)
  - [5.5 Usability](#55-usability)
    - [NFR-010](#nfr-010)
    - [NFR-011](#nfr-011)
  - [5.6 Security](#56-security)
    - [NFR-012](#nfr-012)
    - [NFR-013](#nfr-013)
  - [5.7 Reproducibility](#57-reproducibility)
    - [NFR-014](#nfr-014)
    - [NFR-015](#nfr-015)
- [6. Acceptance Criteria](#6-acceptance-criteria)
  - [6.1 Data Pipeline](#61-data-pipeline)
    - [AC-001](#ac-001)
    - [AC-002](#ac-002)
    - [AC-003](#ac-003)
  - [6.2 Machine Learning Pipeline](#62-machine-learning-pipeline)
    - [AC-004](#ac-004)
    - [AC-005](#ac-005)
    - [AC-006](#ac-006)
  - [6.3 Forecasting System](#63-forecasting-system)
    - [AC-007](#ac-007)
    - [AC-008](#ac-008)
    - [AC-009](#ac-009)
  - [6.4 Explainability and Visualization](#64-explainability-and-visualization)
    - [AC-010](#ac-010)
    - [AC-011](#ac-011)
    - [AC-012](#ac-012)
  - [6.5 Automation and Deployment](#65-automation-and-deployment)
    - [AC-013](#ac-013)
    - [AC-014](#ac-014)
    - [AC-015](#ac-015)
- [Appendix A — Use Cases](#appendix-a--use-cases)
  - [A.1 Actors](#a1-actors)
- [UC-001: View AQI Forecast](#uc-001-view-aqi-forecast)
  - [Description](#description)
  - [Primary Actor](#primary-actor)
  - [Preconditions](#preconditions)
  - [Main Flow](#main-flow)
  - [Postconditions](#postconditions)
- [UC-002: Generate Predictions](#uc-002-generate-predictions)
  - [Description](#description-1)
  - [Primary Actor](#primary-actor-1)
  - [Preconditions](#preconditions-1)
  - [Main Flow](#main-flow-1)
  - [Postconditions](#postconditions-1)
- [UC-003: Execute Data Pipeline](#uc-003-execute-data-pipeline)
  - [Description](#description-2)
  - [Primary Actor](#primary-actor-2)
  - [Preconditions](#preconditions-2)
  - [Main Flow](#main-flow-2)
  - [Postconditions](#postconditions-2)
- [UC-004: Retrain Prediction Models](#uc-004-retrain-prediction-models)
  - [Description](#description-3)
  - [Primary Actor](#primary-actor-3)
  - [Preconditions](#preconditions-3)
  - [Main Flow](#main-flow-3)
  - [Postconditions](#postconditions-3)
- [UC-005: Analyze Prediction Explanations](#uc-005-analyze-prediction-explanations)
  - [Description](#description-4)
  - [Primary Actor](#primary-actor-4)
  - [Preconditions](#preconditions-4)
  - [Main Flow](#main-flow-4)
  - [Postconditions](#postconditions-4)
- [Appendix B — Glossary](#appendix-b--glossary)

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
- The system shall use a recognized AQI calculation standard for converting pollutant concentrations into AQI values.
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

---

# 3. Functional Requirements

## 3.1 Data Collection

### FR-001

**Title:** Historical Air Pollution Data Collection

**Description:**

The system shall retrieve historical hourly air pollution observations for Karachi from OpenWeather to construct the training dataset.

---

### FR-002

**Title:** Historical Weather Data Collection

**Description:**

The system shall retrieve historical meteorological observations for Karachi from OpenWeather to support feature engineering.

---

### FR-003

**Title:** Real-Time Data Collection

**Description:**

The system shall retrieve current meteorological and air pollution observations at scheduled intervals for prediction and model maintenance.

---

## 3.2 Feature Engineering

### FR-004

**Title:** Feature Generation

**Description:**

The system shall generate machine learning features from collected environmental observations, including temporal and derived features required for pollutant forecasting.

---

### FR-005

**Title:** Feature Storage

**Description:**

The system shall store engineered features in the configured Feature Store for training and inference.

---

## 3.3 Model Training

### FR-006

**Title:** Pollutant Model Training

**Description:**

The system shall train independent forecasting models for each target pollutant using historical feature data.

---

### FR-007

**Title:** Model Evaluation

**Description:**

The system shall evaluate trained models using predefined regression metrics before deployment.

---

### FR-008

**Title:** Model Registration

**Description:**

The system shall register successfully trained models in the configured Model Registry.

---

## 3.4 Prediction

### FR-009

**Title:** Pollutant Forecasting

**Description:**

The system shall generate hourly forecasts of pollutant concentrations for the next three days.

---

### FR-010

**Title:** AQI Calculation

**Description:**

The system shall derive Air Quality Index (AQI) values from the predicted pollutant concentrations using the selected AQI calculation methodology.

---

## 3.5 Explainability

### FR-011

**Title:** Prediction Explainability

**Description:**

The system shall provide explanations for model predictions using SHAP.

---

## 3.6 Dashboard

### FR-012

**Title:** Forecast Visualization

**Description:**

The system shall present pollutant forecasts and derived AQI values through an interactive web dashboard.

---

### FR-013

**Title:** Forecast Comparison

**Description:**

The system shall display OpenWeather forecast values alongside system-generated forecasts for comparison purposes.

---

### FR-014

**Title:** Hazard Alerts

**Description:**

The system shall identify and highlight forecast periods where predicted AQI exceeds predefined hazardous thresholds.

---

## 3.7 Workflow Automation

### FR-015

**Title:** Feature Pipeline Scheduling

**Description:**

The system shall execute the feature engineering pipeline automatically according to a predefined schedule.

---

### FR-016

**Title:** Automated Model Retraining

**Description:**

The system shall retrain forecasting models automatically according to a predefined schedule.

---

### FR-017

**Title:** Prediction Pipeline Execution

**Description:**

The system shall generate updated forecasts automatically after new observations become available.

---

# 4. External Interface Requirements

## 4.1 User Interface

### UI-001 Dashboard

The system shall provide a web-based dashboard accessible through a modern web browser.

The dashboard shall allow the user to:

- View predicted hourly AQI for the next three days.
- View predicted pollutant concentrations.
- View historical air quality trends.
- View model explainability visualizations.
- Compare system forecasts with OpenWeather forecasts.
- View hazardous air quality alerts.

---

## 4.2 Software Interfaces

### SI-001 OpenWeather API

The system shall communicate with the OpenWeather APIs to retrieve:

- Historical weather observations.
- Historical air pollution observations.
- Current weather observations.
- Current air pollution observations.
- Air pollution forecasts.

---

### SI-002 Hopsworks

The system shall communicate with Hopsworks to:

- Store engineered features.
- Retrieve features for training and inference.
- Register trained models.
- Retrieve production models for prediction.

---

### SI-003 GitHub Actions

The system shall use GitHub Actions to automate scheduled workflows, including:

- Feature pipeline execution.
- Model retraining.
- Prediction pipeline execution.

---

## 4.3 Communication Interfaces

The system shall communicate with external services using secure HTTPS connections.

External APIs shall exchange data using JSON over REST.

Authentication with external services shall be performed using API keys or service credentials as required.

---

## 4.4 Hardware Interfaces

The system has no direct hardware interface requirements.

The application shall execute on commodity computing hardware capable of running Python applications and accessing cloud services via the Internet.

---

# 5. Non-Functional Requirements

## 5.1 Performance

### NFR-001

The system shall generate a three-day hourly air quality forecast within **10 seconds** under normal operating conditions.

---

### NFR-002

The feature engineering pipeline shall complete within **15 minutes** for each scheduled execution.

---

### NFR-003

The model training pipeline shall complete within **2 hours** under normal operating conditions.

---

## 5.2 Reliability

### NFR-004

The system shall gracefully handle temporary failures of external APIs and report appropriate error messages.

---

### NFR-005

The system shall record pipeline execution failures for troubleshooting purposes.

---

## 5.3 Availability

### NFR-006

The system shall be capable of executing scheduled workflows without manual intervention.

---

## 5.4 Maintainability

### NFR-007

The system shall maintain a modular architecture separating data collection, feature engineering, model training, prediction, and presentation components.

---

### NFR-008

The system shall maintain version control for source code and project documentation using Git.

---

### NFR-009

The system shall maintain versioned machine learning models through the configured Model Registry.

---

## 5.5 Usability

### NFR-010

The dashboard shall present forecasts using clear visualizations that are understandable without requiring knowledge of machine learning algorithms.

---

### NFR-011

The dashboard shall clearly distinguish between system-generated forecasts and reference forecasts obtained from external providers.

---

## 5.6 Security

### NFR-012

The system shall store API credentials securely and shall not expose sensitive credentials within the source code repository.

---

### NFR-013

The system shall communicate with external services using encrypted HTTPS connections.

---

## 5.7 Reproducibility

### NFR-014

The system shall support reproducible model training through version-controlled source code, feature definitions, and registered model artifacts.

---

### NFR-015

The system shall document all external dependencies required to reproduce the development environment.

---

# 6. Acceptance Criteria

The Pearls AQI Predictor shall be considered complete when the following criteria are satisfied.

---

## 6.1 Data Pipeline

### AC-001

The system successfully retrieves historical weather and air pollution data for Karachi from OpenWeather.

### AC-002

The system successfully executes the automated feature engineering pipeline and stores generated features in the Feature Store.

### AC-003

The system successfully retrieves new environmental observations according to the configured schedule.

---

## 6.2 Machine Learning Pipeline

### AC-004

The system successfully trains pollutant forecasting models using historical environmental data.

### AC-005

The trained models are evaluated using regression metrics including:

- Mean Absolute Error (MAE)
- Root Mean Squared Error (RMSE)
- R² Score

### AC-006

The trained models are successfully registered in the Model Registry.

---

## 6.3 Forecasting System

### AC-007

The system generates hourly pollutant concentration forecasts for the next three days.

### AC-008

The system successfully calculates AQI values from predicted pollutant concentrations.

### AC-009

The dashboard displays forecasted AQI values and pollutant concentrations.

---

## 6.4 Explainability and Visualization

### AC-010

The system provides model prediction explanations using SHAP visualizations.

### AC-011

The dashboard displays comparison between system-generated forecasts and OpenWeather forecasts.

### AC-012

The dashboard identifies forecast periods with hazardous AQI levels.

---

## 6.5 Automation and Deployment

### AC-013

The automated workflows execute successfully through GitHub Actions.

### AC-014

The deployed application is accessible through a web browser.

### AC-015

The system can execute the complete workflow without manual intervention after deployment.

---

# Appendix A — Use Cases

## A.1 Actors

| Actor | Description |
|------|-------------|
| End User | A user who accesses the dashboard to view AQI forecasts and analysis. |
| System Administrator | The project developer responsible for maintaining pipelines, models, and configurations. |
| External Data Provider | OpenWeather service that provides weather and air pollution data. |

---

# UC-001: View AQI Forecast

## Description

The user views the predicted Air Quality Index and pollutant forecasts for Karachi.

## Primary Actor

End User

## Preconditions

- The prediction service is available.
- Trained models are available.

## Main Flow

1. User opens the web dashboard.
2. System retrieves the latest available predictions.
3. System displays:
   - Predicted AQI values.
   - Predicted pollutant concentrations.
   - Forecast timeline.
4. User reviews the forecast information.

## Postconditions

The user successfully views the latest AQI forecast.

---

# UC-002: Generate Predictions

## Description

The system generates future pollutant concentration predictions and calculates AQI values.

## Primary Actor

System

## Preconditions

- Trained models are available.
- Required input features are available.

## Main Flow

1. System retrieves required features.
2. System loads the registered prediction models.
3. System generates pollutant forecasts.
4. System calculates AQI from predicted pollutant concentrations.
5. System stores or serves the generated forecast.

## Postconditions

A new AQI forecast is available for visualization.

---

# UC-003: Execute Data Pipeline

## Description

The system collects new environmental observations and updates the Feature Store.

## Primary Actor

System Administrator / Automated Workflow

## Preconditions

- External APIs are available.
- API credentials are configured.

## Main Flow

1. Scheduled workflow starts.
2. System requests weather and pollution data from OpenWeather.
3. System processes incoming data.
4. System generates engineered features.
5. System stores updated features.

## Postconditions

The Feature Store contains updated environmental features.

---

# UC-004: Retrain Prediction Models

## Description

The system periodically retrains pollutant forecasting models using updated historical data.

## Primary Actor

System Administrator / Automated Workflow

## Preconditions

- Updated training data is available.
- Training environment is available.

## Main Flow

1. Scheduled training workflow starts.
2. System retrieves training features.
3. System trains pollutant prediction models.
4. System evaluates model performance.
5. System registers successful models.

## Postconditions

Updated models are available for future predictions.

---

# UC-005: Analyze Prediction Explanations

## Description

The user views explanations of model predictions.

## Primary Actor

End User

## Preconditions

- Predictions have been generated.
- SHAP explanations are available.

## Main Flow

1. User opens the explanation section.
2. System loads prediction explanations.
3. System displays feature contributions affecting predictions.

## Postconditions

The user understands factors influencing the forecast.

---

# Appendix B — Glossary

| Term | Definition |
|------|------------|
| Air Quality Index (AQI) | A numerical indicator representing air pollution levels based on measured pollutant concentrations. |
| API | A software interface that allows communication between different systems or services. |
| AQI Forecast | A prediction of future air quality conditions based on predicted pollutant concentrations. |
| CI/CD | Continuous Integration and Continuous Deployment practices used to automate software workflows. |
| Feature | An input variable used by a machine learning model to make predictions. |
| Feature Engineering | The process of transforming raw data into meaningful machine learning inputs. |
| Feature Store | A centralized system for storing and managing machine learning features. |
| Forecast Horizon | The future time period for which predictions are generated. |
| Ground Truth | The actual observed value used to evaluate model predictions. |
| Inference | The process of using a trained machine learning model to generate predictions. |
| Machine Learning Model | A computational algorithm trained on historical data to make predictions on new data. |
| Model Registry | A system used to store, version, and manage trained machine learning models. |
| MLOps | Practices that combine machine learning development and operational workflows to automate model deployment and maintenance. |
| Pollutant Concentration | The measured quantity of a pollutant present in the atmosphere, such as PM₂.₅, PM₁₀, or NO₂. |
| Prediction Pipeline | The workflow responsible for generating future pollutant forecasts and AQI values. |
| Regression Metric | A measurement used to evaluate continuous prediction performance, such as MAE, RMSE, or R² score. |
| Serverless Architecture | A cloud architecture where infrastructure management is handled by managed services rather than manually maintained servers. |
| SHAP | A method for explaining machine learning model predictions by measuring feature contributions. |
| Time Series Data | Data collected sequentially over time where previous observations may influence future values. |