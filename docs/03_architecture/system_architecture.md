# 1. Introduction

This document describes the software architecture of the Pearls AQI Predictor system. It provides a high-level view of the system structure, identifies its major components, and explains how they interact to satisfy the requirements defined in the Software Requirements Specification (SRS).

The architecture is derived from the approved Architectural Decision Records (ADRs) and serves as the blueprint for the subsequent design and implementation phases. It defines the responsibilities of each subsystem, the flow of data through the machine learning pipeline, and the deployment strategy for the application.

This document does not describe implementation details such as algorithms, source code, or class structures. These will be specified during the detailed design phase.

---

# 2. Architectural Goals

The architecture of the Pearls AQI Predictor is designed to achieve the following goals:

## AG-001: Serverless Architecture

The system shall leverage managed cloud services where possible to minimize infrastructure management while supporting automated machine learning workflows.

## AG-002: Modular Design

The system shall separate data collection, feature engineering, model training, prediction, visualization, and workflow orchestration into independent components with clearly defined responsibilities.

## AG-003: Reproducibility

Machine learning experiments, datasets, features, and trained models shall be versioned to ensure reproducible training and prediction results.

## AG-004: Automation

Data collection, feature generation, model retraining, and deployment workflows shall execute automatically through scheduled workflows with minimal manual intervention.

## AG-005: Maintainability

System components shall be loosely coupled to simplify maintenance, testing, and future enhancements.

## AG-006: Explainability

The architecture shall support model interpretation by integrating SHAP-based explanations into the prediction workflow.

## AG-007: Scalability

Although the initial deployment targets a single city (Karachi), the architecture shall remain extensible to support additional locations and prediction models with minimal structural changes.

## AG-008: Traceability

Major architectural decisions shall be documented through ADRs, and architectural components shall be traceable to the requirements defined in the SRS.

---

# 3. Architectural Drivers

The architecture of the Pearls AQI Predictor is driven by the functional and non-functional requirements defined in the Software Requirements Specification (SRS), together with the architectural decisions documented in the project's ADRs.

## 3.1 Functional Drivers

The following functional requirements have the greatest influence on the system architecture:

| Requirement | Architectural Impact |
|-------------|----------------------|
| Automated data collection | Requires a scheduled data ingestion pipeline. |
| Feature engineering | Requires a dedicated feature processing component and Feature Store integration. |
| Model training | Requires an isolated training pipeline capable of producing versioned models. |
| Pollutant prediction | Requires independent prediction models for each target pollutant. |
| AQI calculation | Requires a post-processing component that derives AQI from predicted pollutant concentrations. |
| Explainability | Requires integration of SHAP into the prediction workflow. |
| Interactive dashboard | Requires a web-based presentation layer for visualization and user interaction. |

---

## 3.2 Quality Attribute Drivers

The architecture is designed to satisfy the following quality attributes:

| Quality Attribute | Architectural Impact |
|-------------------|----------------------|
| Reproducibility | Versioned datasets, features, experiments, and models. |
| Maintainability | Modular components with clearly defined responsibilities. |
| Reliability | Automated workflows with deterministic execution. |
| Performance | Efficient retrieval of features and models for prediction. |
| Usability | Simple web dashboard accessible through a browser. |

---

## 3.3 Architectural Constraints

The following constraints have been established during project planning:

| Constraint | Impact |
|------------|--------|
| Serverless architecture | Managed cloud services shall be preferred over self-managed infrastructure. |
| OpenWeather API | Environmental data shall be obtained from OpenWeather. |
| Hopsworks | Feature storage and model registry shall be provided by Hopsworks. |
| GitHub Actions | Workflow automation shall be implemented using GitHub Actions. |
| FastAPI | Backend services shall expose prediction functionality through FastAPI. |
| Streamlit | The user interface shall be implemented using Streamlit. |
| PyTorch | Deep learning models shall be implemented using PyTorch. |

---

## 3.4 Architectural Decision Records

The architecture is guided by the following approved Architectural Decision Records:

| ADR | Summary |
|-----|---------|
| ADR-001 | Predict AQI by deriving it from predicted pollutant concentrations. |
| ADR-002 | Perform forecasting at hourly granularity. |
| ADR-003 | Use OpenWeather as the environmental data provider. |
| ADR-004 | Train one prediction model per pollutant. |
| ADR-005 | Use Hopsworks as the Feature Store and Model Registry. |
| ADR-006 | Use GitHub Actions for workflow automation. |
| ADR-007 | Use FastAPI as the backend framework. |

---

# 4. System Context

The Pearls AQI Predictor operates within a broader ecosystem of external users and cloud services. At the highest level, the system collects environmental observations from OpenWeather, stores machine learning assets in Hopsworks, executes scheduled workflows using GitHub Actions, and provides AQI forecasts to end users through a web dashboard.

The system boundary consists of all software developed as part of the Pearls AQI Predictor. External services communicate with the system through well-defined interfaces and are not considered part of the application architecture.

![System Context Diagram](figures/context_diagram.svg)

**Figure 4.1.** System Context Diagram showing the external actors and services interacting with the Pearls AQI Predictor.