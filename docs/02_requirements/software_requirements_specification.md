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