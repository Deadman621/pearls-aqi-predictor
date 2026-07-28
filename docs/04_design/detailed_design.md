# Detailed Design

**Project:** Pearls AQI Predictor

**Version:** 1.0

**Date:** 2026-07-28

---

# Table of Contents

- [Detailed Design](#detailed-design)
- [1. Introduction](#1-introduction)
- [2. Backend Design](#2-backend-design)
  - [2.1 Responsibilities](#21-responsibilities)
  - [2.2 Internal Structure](#22-internal-structure)
  - [2.3 API Endpoints](#23-api-endpoints)
  - [2.4 Request and Response Models](#24-request-and-response-models)
- [3. Prediction Pipeline Design](#3-prediction-pipeline-design)
  - [3.1 Pipeline Workflow](#31-pipeline-workflow)
  - [3.2 Feature Retrieval](#32-feature-retrieval)
  - [3.3 Model Loading](#33-model-loading)
  - [3.4 Inference Execution](#34-inference-execution)
  - [3.5 SHAP Explanation Generation](#35-shap-explanation-generation)
  - [3.6 Response Formatting](#36-response-formatting)
- [4. Feature Engineering Pipeline Design](#4-feature-engineering-pipeline-design)
  - [4.1 Data Ingestion](#41-data-ingestion)
  - [4.2 Feature Engineering](#42-feature-engineering)
  - [4.3 Feature Validation](#43-feature-validation)
  - [4.4 Feature Store Publication](#44-feature-store-publication)
- [5. Model Training Pipeline Design](#5-model-training-pipeline-design)
  - [5.1 Dataset Construction](#51-dataset-construction)
  - [5.2 Model Training](#52-model-training)
  - [5.3 Model Evaluation](#53-model-evaluation)
  - [5.4 Model Registration](#54-model-registration)
- [6. Dashboard Design](#6-dashboard-design)
  - [6.1 User Interface Layout](#61-user-interface-layout)
  - [6.2 Prediction Workflow](#62-prediction-workflow)
  - [6.3 Visualisation Components](#63-visualisation-components)
- [7. Repository Structure](#7-repository-structure)
- [8. Design Decisions](#8-design-decisions)

---

# 1. Introduction

This document describes the detailed design of the Pearls AQI Predictor system. It expands upon the System Architecture document by specifying the internal design of the application's major components and defining how they collaborate to implement the proposed architecture.

The detailed design provides implementation-oriented specifications for the FastAPI backend, prediction pipeline, feature engineering pipeline, model training pipeline, and Streamlit dashboard. It also documents the repository organisation, internal workflows, application interfaces, and design decisions that guide the development of a modular and maintainable machine learning system.

This document serves as the technical blueprint for the implementation phase. It bridges the gap between the high-level architectural views presented in the System Architecture document and the source code that realises those designs.

---