# Detailed Design

**Project:** Pearls AQI Predictor

**Version:** 1.0

**Date:** 2026-07-28

---

# Table of Contents

- [Detailed Design](#detailed-design)
- [Table of Contents](#table-of-contents)
- [1. Introduction](#1-introduction)
- [2. Backend Design](#2-backend-design)
  - [2.1 Backend Class Diagram](#21-backend-class-diagram)
  - [2.2 REST API](#22-rest-api)
  - [2.3 Prediction Endpoint](#23-prediction-endpoint)
    - [Endpoint](#endpoint)
    - [Description](#description)
    - [Request](#request)
    - [Successful Response (HTTP 200)](#successful-response-http-200)
    - [Response Status Codes](#response-status-codes)
  - [2.4 Backend Responsibilities](#24-backend-responsibilities)
- [3. Prediction Pipeline Design](#3-prediction-pipeline-design)
  - [3.1 Prediction Pipeline Class Diagram](#31-prediction-pipeline-class-diagram)
  - [3.2 Pipeline Execution Workflow](#32-pipeline-execution-workflow)
  - [3.3 Component Breakdown](#33-component-breakdown)
    - [3.3.1 FeatureRetriever](#331-featureretriever)
    - [3.3.2 InferenceEngine](#332-inferenceengine)
    - [3.3.3 SHAPExplainer](#333-shapexplainer)
    - [3.3.4 AQICalculator](#334-aqicalculator)
    - [3.3.5 PredictionFormatter](#335-predictionformatter)
  - [3.4 Pipeline Responsibilities](#34-pipeline-responsibilities)
- [4. Model Finetuning Pipeline Design](#4-model-finetuning-pipeline-design)
  - [4.1 Finetuning Pipeline Class Diagram](#41-finetuning-pipeline-class-diagram)
  - [4.2 Pipeline Execution Workflow](#42-pipeline-execution-workflow)
  - [4.3 Component Breakdown](#43-component-breakdown)
    - [4.3.1 PipelineOrchestrator \& Configuration Classes](#431-pipelineorchestrator--configuration-classes)
    - [4.3.2 ModelFetcher](#432-modelfetcher)
    - [4.3.3 FeatureRetriever \& DatasetBuilder](#433-featureretriever--datasetbuilder)
    - [4.3.4 FineTuner \& BasePollutantModel Interface](#434-finetuner--basepollutantmodel-interface)
    - [4.3.5 ModelEvaluator](#435-modelevaluator)
    - [4.3.6 FineTunedModelRegistrar](#436-finetunedmodelregistrar)
  - [4.4 Pipeline Responsibilities](#44-pipeline-responsibilities)
- [5. First-Time Model Training Pipeline Design](#5-first-time-model-training-pipeline-design)
  - [5.1 Training Pipeline Class Diagram](#51-training-pipeline-class-diagram)
  - [5.2 Pipeline Execution Workflow](#52-pipeline-execution-workflow)
  - [5.3 Component Breakdown](#53-component-breakdown)
    - [5.3.1 PipelineOrchestrator \& Configuration Classes](#531-pipelineorchestrator--configuration-classes)
    - [5.3.2 FeatureRetriever \& DatasetBuilder](#532-featureretriever--datasetbuilder)
    - [5.3.3 ModelTrainer \& BasePollutantModel Interface](#533-modeltrainer--basepollutantmodel-interface)
    - [5.3.4 ModelEvaluator](#534-modelevaluator)
    - [5.3.5 ModelRegistrar](#535-modelregistrar)
  - [5.4 Pipeline Responsibilities](#54-pipeline-responsibilities)
- [6. Feature Pipeline Design](#6-feature-pipeline-design)
  - [6.1 Feature Pipeline Class Diagram](#61-feature-pipeline-class-diagram)
  - [6.2 Pipeline Execution Workflow](#62-pipeline-execution-workflow)
  - [6.3 Component Breakdown](#63-component-breakdown)
    - [6.3.1 PipelineOrchestrator](#631-pipelineorchestrator)
    - [6.3.2 RawDataExtractor](#632-rawdataextractor)
    - [6.3.3 DataValidator](#633-datavalidator)
    - [6.3.4 FeatureTransformer](#634-featuretransformer)
    - [6.3.5 FeatureWriter](#635-featurewriter)
  - [6.4 Pipeline Responsibilities](#64-pipeline-responsibilities)

---

# 1. Introduction

This document describes the detailed design of the Pearls AQI Predictor system. It expands upon the System Architecture document by specifying the internal design of the application's major components and defining how they collaborate to implement the proposed architecture.

The detailed design provides implementation-oriented specifications for the FastAPI backend, prediction pipeline, feature engineering pipeline, model training pipeline, and Streamlit dashboard. It also documents the repository organisation, internal workflows, application interfaces, and design decisions that guide the development of a modular and maintainable machine learning system.

This document serves as the technical blueprint for the implementation phase. It bridges the gap between the high-level architectural views presented in the System Architecture document and the source code that realises those designs.

---

# 2. Backend Design

This section describes the detailed design of the backend subsystem responsible for serving prediction requests. It expands upon the architectural decisions defined in the System Architecture document by specifying the backend's external interface, responsibilities, and interaction with internal machine learning components.

The backend is implemented using **FastAPI** and serves as the application's API Gateway. Its primary responsibility is to receive client requests, validate them, delegate prediction execution to the Prediction Pipeline, and return the resulting prediction payload. The backend does not perform feature retrieval, model loading, inference, AQI computation, or pollutant AQI index calculation directly. These responsibilities are encapsulated within the Prediction Pipeline in accordance with the system architecture.

---

## 2.1 Backend Class Diagram

The **RequestController** exposes the REST API endpoint and delegates prediction execution through the **PredictionPipelineAPI** interface. After receiving prediction results, the controller returns a **ResponsePayload**, which aggregates the forecasted AQI values and pollutant-specific AQI indices. The **ResponseFormatter** serializes the response into JSON before it is returned to the client.

The backend itself remains stateless and does not implement prediction logic, preserving a clear separation between request handling and machine learning execution.

![Backend Class Diagram](figures/backend_class_diagram.svg)

**Figure 2.1.** the figure illustrates static structure of the backend subsystem. The backend consists of a lightweight controller responsible for handling HTTP requests, response data transfer objects (DTOs) used to structure prediction results, and interfaces that define communication with external components.

---

## 2.2 REST API

The backend exposes a RESTful API over HTTPS.

| Property | Value |
|----------|-------|
| Framework | FastAPI |
| Protocol | HTTPS |
| Architecture Style | REST |
| Data Format | JSON |
| Character Encoding | UTF-8 |
| Authentication | None (current version) |

---

## 2.3 Prediction Endpoint

### Endpoint

```http
POST /predict
```

### Description

Generates a three-day AQI forecast for Karachi.

Upon receiving a prediction request, the backend delegates execution to the Prediction Pipeline. The pipeline retrieves engineered features from the Hopsworks Feature Store, loads production models from the Model Registry, performs inference, computes pollutant-specific AQI indices and the overall AQI according to the US EPA methodology, generates SHAP explanations, and returns the completed prediction payload.

---

### Request

No request body is required.

Example:

```http
POST /predict HTTP/1.1
Host: pearls-aqi.example.com
```

---

### Successful Response (HTTP 200)

```json
{
  "generated_at": "2026-07-29T08:00:00Z",
  "forecast": [
    {
      "date": "2026-07-30",
      "aqi": 118,
      "category": "Unhealthy for Sensitive Groups"
    },
    {
      "date": "2026-07-31",
      "aqi": 96,
      "category": "Moderate"
    },
    {
      "date": "2026-08-01",
      "aqi": 82,
      "category": "Moderate"
    }
  ],
  "pollutant_indices": [
    {
      "name": "PM2.5",
      "value": 118
    },
    {
      "name": "O3",
      "value": 81
    },
    {
      "name": "NO2",
      "value": 42
    }
  ]
}
```

---

### Response Status Codes

| Status Code | Description |
|-------------|-------------|
| 200 | Prediction generated successfully. |
| 500 | Internal prediction pipeline failure. |
| 503 | Required feature data or production models are unavailable. |

---

## 2.4 Backend Responsibilities

The FastAPI Backend is responsible for:

- Exposing the REST API to client applications.
- Receiving and validating prediction requests.
- Delegating prediction execution to the Prediction Pipeline.
- Returning prediction results to the client.
- Translating internal exceptions into appropriate HTTP responses.
- Serializing response objects into JSON.

The backend intentionally contains minimal business logic. Feature retrieval, model loading, inference execution, AQI computation, pollutant AQI index calculation, and SHAP explanation generation are delegated to the Prediction Pipeline to preserve modularity and maintain a clear separation of concerns.

---

# 3. Prediction Pipeline Design

This section describes the detailed design of the Prediction Pipeline subsystem, which is responsible for executing online inference. The Prediction Pipeline encapsulates all machine learning operational logic, separating inference, feature retrieval, explainability, and index calculations from the web server layer.

Upon invocation by the Backend API Gateway or CLI, the pipeline connects to external cloud services to fetch the required feature data and model artifacts, generates concentration predictions for target atmospheric pollutants, calculates overall and pollutant-specific Air Quality Index (AQI) values, generates SHAP feature attribution explanations, and constructs the unified response payload.

---

## 3.1 Prediction Pipeline Class Diagram

The Prediction Pipeline uses an orchestrator pattern centered around the `PipelineOrchestrator`, which implements the `PredictionPipelineAPI` interface. The orchestrator manages execution across specialized modular components: `FeatureRetriever`, `InferenceEngine`, `SHAPExplainer`, `AQICalculator`, and `PredictionFormatter`.

External infrastructure interactions are encapsulated via interfaces (`FeatureStoreInterface` and `ModelRegistryInterface`), enabling robust testing and decoupling from direct vendor dependencies.

![Prediction Pipeline Class Diagram](figures/prediction_pipeline_class_diagram.svg)

**Figure 3.1.** Static structure of the Prediction Pipeline subsystem. The pipeline uses an orchestrator to coordinate feature retrieval, multi-pollutant model inference, SHAP feature importance calculation, EPA AQI breakpoint conversion, and response formatting.

---

## 3.2 Pipeline Execution Workflow

The end-to-end execution of the prediction process follows a linear operational workflow across the pipeline components:

1. **Invocation**: The `PipelineOrchestrator` receives a forecast request via `predict()`.
2. **Feature Retrieval**: The `FeatureRetriever` connects to the Hopsworks Feature Store to fetch the latest engineered feature vector containing current and lagged meteorological and atmospheric variables.
3. **Model Loading & Inference**: The `InferenceEngine` retrieves the active production model artifacts from the Hopsworks Model Registry and executes batch model inference to output raw pollutant concentrations ($PM_{2.5}, PM_{10}, O_3, NO_2, SO_2, CO$).
4. **SHAP Explanation**: The `SHAPExplainer` calculates additive feature attribution values (SHAP scores) to identify key environmental drivers influencing the model predictions.
5. **AQI Calculation**: The `AQICalculator` transforms concentration predictions into pollutant-specific sub-indices using standard US EPA linear breakpoint equations, identifying the dominant pollutant and overall AQI score.
6. **Response Formatting**: The `PredictionFormatter` converts internal domain objects (`AQIResult`, `SHAPValues`, `Pollutant`) into the final structured `ResponsePayload` DTO ready for serialization.

---

## 3.3 Component Breakdown

### 3.3.1 FeatureRetriever

The `FeatureRetriever` acts as the data access client for online prediction inputs.

* **Primary Responsibility**: Connects to the Hopsworks Feature Store API using configured credentials and retrieves the latest online feature view snapshot for Karachi.
* **Data Returned**: Formatted Pandas DataFrames / NumPy arrays containing lag features (e.g., 1h, 3h, 24h pollutant readings) and current weather observations (temperature, relative humidity, wind speed, surface pressure).
* **Error Handling**: Raises a `FeatureStoreUnavailableException` if connection timeouts occur or if feature vectors are missing required schema variables.

---

### 3.3.2 InferenceEngine

The `InferenceEngine` manages model artifact loading and prediction execution.

* **Primary Responsibility**: Downloads or loads cached production models from the Hopsworks Model Registry corresponding to each target pollutant.
* **Execution Flow**: Evaluates candidate feature arrays against fitted model pipelines (e.g., XGBoost, LightGBM, PyTorch neural networks) to generate multi-step forecasted concentrations ($\mu g/m^3$ or $ppm$) for the target forecast horizon.
* **Output**: A collection of structured `Pollutant` domain models storing pollutant names, forecast timestamps, and predicted numeric values.

---

### 3.3.3 SHAPExplainer

The `SHAPExplainer` provides local model interpretability for generated predictions.

* **Primary Responsibility**: Computes SHAP (SHapley Additive exPlanations) values to quantify the contribution of each input feature to the predicted concentration level.
* **Implementation**: Utilizes model-appropriate explainers (e.g., `TreeExplainer` for tree ensembles, `KernelExplainer` for general functions) initialized with baseline background distributions from training data.
* **Output**: `SHAPValues` DTO containing feature importance vectors and directional impact indicators (positive/negative impact on air pollution levels).

---

### 3.3.4 AQICalculator

The `AQICalculator` implements the mathematical translation from raw concentration metrics into standardized public health indices according to United States Environmental Protection Agency (US EPA) specifications.

* **Sub-Index Formula**: Uses piecewise linear interpolation based on pollutant breakpoint bounds:
$$I_p = \frac{I_{Hi} - I_{Lo}}{BP_{Hi} - BP_{Lo}} (C_p - BP_{Lo}) + I_{Lo}$$


Where:
* $I_p$ = Index value for pollutant $p$
* $C_p$ = Truncated concentration of pollutant $p$
* $BP_{Hi}, BP_{Lo}$ = Breakpoint concentration bounds containing $C_p$
* $I_{Hi}, I_{Lo}$ = AQI index bounds corresponding to $BP_{Hi}, BP_{Lo}$


* **Overall AQI Derivation**: The overall AQI score is assigned as the maximum sub-index across all evaluated pollutants:
$$AQI = \max (I_{PM2.5}, I_{PM10}, I_{O3}, I_{NO2}, I_{SO2}, I_{CO})$$


* **Category Mapping**: Maps numerical scores into standardized public health advisory bands:
* `0 - 50`: Good
* `51 - 100`: Moderate
* `101 - 150`: Unhealthy for Sensitive Groups
* `151 - 200`: Unhealthy
* `201 - 300`: Very Unhealthy
* `301 - 500`: Hazardous



---

### 3.3.5 PredictionFormatter

The `PredictionFormatter` acts as the mapping layer between internal pipeline models and external communication schemas.

* **Primary Responsibility**: Transforms `AQIResult`, `Pollutant`, and `SHAPValues` objects into a standardized `ResponsePayload`.
* **Output**: Ensures target dates, forecast intervals, pollutant sub-indices, categorical labels, and feature importance mappings conform strictly to the expected JSON schema specified by the API Gateway layer.

---

## 3.4 Pipeline Responsibilities

The Prediction Pipeline is strictly responsible for:

* Managing connections to the Hopsworks Feature Store and Model Registry.
* Validating the integrity of incoming feature vectors prior to model inference.
* Loading, caching, and executing production machine learning models.
* Computing pollutant sub-indices and overall AQI values according to US EPA standards.
* Calculating SHAP feature attributions for model interpretability.
* Packaging forecast figures, metadata, and SHAP explainability attributes into standardized domain payloads.

---

# 4. Model Finetuning Pipeline Design

This section describes the detailed design of the Model Finetuning Pipeline subsystem, which is responsible for performing incremental retraining and fine-tuning on active production baseline models using newly ingested atmospheric feature observations.

The pipeline ensures continuous model adaptation without requiring retraining from scratch. It retrieves production artifacts from the Model Registry, fine-tunes parameters based on updated feature store batches, evaluates metrics against baseline models, logs experiments to MLflow, and registers improved models back into the Hopsworks Model Registry.

---

## 4.1 Finetuning Pipeline Class Diagram

The Model Finetuning Pipeline uses an orchestrator pattern centered around the **PipelineOrchestrator**, which implements the **FineTuningPipelineInterface**. The orchestrator manages interactions between configuration classes (**FineTuningConfig**, **FineTuningPollutantConfig**, **FineTunedModelConfig**), data ingestion modules (**FeatureRetriever**, **DatasetBuilder**), retraining engines (**ModelFetcher**, **FineTuner**, **Callbacks**), and validation/registration handlers (**ModelEvaluator**, **FineTunedModelRegistrar**).

External connections to the **AQI Feature Store**, **MLflow Tracking Server**, and **AQI Model Registry** on the **Hopsworks Platform** are managed via dedicated adapter interfaces.

![Finetuning Pipeline Class Diagram](figures/fine_tuning_pipeline_class_diagram.svg)

**Figure 4.1.** Static structure of the Model Finetuning Pipeline subsystem. The pipeline orchestrator manages baseline model retrieval, feature batch dataset construction, adapter/layer fine-tuning, MLflow callback tracking, performance validation against baseline models, and conditional model registration in Hopsworks.

---

## 4.2 Pipeline Execution Workflow

The operational flow of the fine-tuning execution follows a structured, multi-step process:

1. **Invocation**: The `PipelineOrchestrator` receives an execution trigger via `run_finetuning_pipeline()`, taking a structured `FineTuningConfig` payload.
2. **Model Fetching**: The `ModelFetcher` queries the **AQI Model Registry** on Hopsworks via `fetch_best_model()` to download the active production baseline model conforming to the `BasePollutantModel` interface.
3. **Feature Ingestion & Dataset Construction**:
   * The `FeatureRetriever` executes `retrieve()` against the **AQI Feature Store** to pull newly updated feature records.
   * The `DatasetBuilder` processes feature vectors via `construct()` to construct a structured `Dataset` tailored for fine-tuning.


4. **Incremental Retraining & Experiment Tracking**:
   * The `FineTuner` receives hyperparameters from `FineTunedModelConfig` (such as `unfreeze_layers`, `fine_tune_epochs`, `learning_rate`, `weight_decay`, and `warmup_steps`).
   * Retraining is initiated via `fine_tune()`, invoking `fine_tune(dataset)` on the `BasePollutantModel` and logging parameters, metrics, and adapter checkpoints to the **MLflow Tracking Server** via the `Callbacks` interface.


5. **Model Evaluation & Comparison**:
   * The `ModelEvaluator` executes `evaluate()` and `compare_with_baseline()` to compute comparative evaluation metrics.
   * The `validate_improvement(BasePollutantModel)` method determines if the fine-tuned candidate outperforms the current production baseline.


6. **Model Registration**: If performance improvement is validated, the `FineTunedModelRegistrar` executes `register_fine_tuned()` to publish updated model weights and adapters (`save_adapter()`) to the **AQI Model Registry**.

---

## 4.3 Component Breakdown

### 4.3.1 PipelineOrchestrator & Configuration Classes

The orchestrator and configuration DTOs define fine-tuning parameters across all evaluated atmospheric pollutants ($PM_{2.5}, PM_{10}, O_3, NO_2, SO_2, CO$).

* **FineTuningConfig**: Container class aggregating `pollutant_config` settings for each pollutant model.
* **FineTuningPollutantConfig**: Holds pollutant-specific settings and wraps the selected model configuration (`selected_model`).
* **FineTunedModelConfig**: Defines model-level fine-tuning hyperparameters:
* `model_name`: Identifies the target model artifact.
* `do_training`: Boolean flag toggling fine-tuning execution.
* `unfreeze_layers`: Integer specifying top layer count to unfreeze for gradient updates.
* `fine_tune_epochs`: Number of incremental training epochs.
* `warmup_steps`: Number of initial learning rate warmup iterations.
* `learning_rate`: Floating-point step size for gradient updates.
* `weight_decay`: Regularization parameter preventing catastrophic forgetting.



---

### 4.3.2 ModelFetcher

The `ModelFetcher` isolates model retrieval operations from the Hopsworks Model Registry.

* **Primary Responsibility**: Queries the **AQI Model Registry** to retrieve the latest production baseline model.
* **Methods**:
* `fetch_best_model(): BasePollutantModel`: Downloads model binaries, configurations, and adapter states from Hopsworks and returns an instantiated object implementing the `BasePollutantModel` interface.



---

### 4.3.3 FeatureRetriever & DatasetBuilder

These components prepare newly ingested feature batches for model adaptation.

* **FeatureRetriever**:
* `retrieve(): Features`: Queries the **AQI Feature Store** for recent feature group entries containing new meteorological and pollutant observations.


* **DatasetBuilder**:
* `construct(): Dataset`: Merges retrieved features, performs temporal sequence alignment, handles missing values, and builds `Dataset` objects optimized for fine-tuning.



---

### 4.3.4 FineTuner & BasePollutantModel Interface

The core training execution engine handles parameter updates and adapter state saving.

* **FineTuner**:
* `fine_tune(): Status`: Manages execution across fine-tuning epochs, delegating parameter optimization to the underlying model and streaming progress metrics through `Callbacks`.


* **BasePollutantModel Interface**:
* `config: FineTuningConfig`: Property storing active fine-tuning configuration parameters.
* `fine_tune(dataset): Metrics`: Executes gradient updates or incremental boosting steps on target model layers, returning training evaluation metrics.
* `predict(X): ndarray`: Computes predictions on input feature arrays.
* `save_adapter(path): Path`: Exports fine-tuned parameter weights or adapter artifacts to local storage prior to registration.



---

### 4.3.5 ModelEvaluator

The `ModelEvaluator` provides validation checks before candidate models can be promoted.

* **Methods**:
* `evaluate(): Metrics`: Calculates evaluation metrics (RMSE, MAE, $R^2$) on fine-tuning validation splits.
* `compare_with_baseline(): Metrics`: Computes relative performance gain or degradation against the active production model.
* `validate_improvement(BasePollutantModel): bool`: Returns `True` if performance gains satisfy predefined acceptance thresholds, preventing degraded models from entering production.



---

### 4.3.6 FineTunedModelRegistrar

The `FineTunedModelRegistrar` handles version control and deployment candidate updates.

* **Primary Responsibility**: Commits validated fine-tuned models and adapter weights to the **AQI Model Registry**.
* **Methods**:
* `register_fine_tuned(): Status`: Uploads model artifacts, associated metrics, and dependency schemas to Hopsworks, tagging the model version as the new active baseline.



---

## 4.4 Pipeline Responsibilities 

The Model Finetuning Pipeline is strictly responsible for:

* Fetching production models from the Hopsworks Model Registry for incremental adaptation.
* Retrieving new feature observations from the Hopsworks Feature Store to construct fine-tuning datasets.
* Managing hyperparameter settings (unfrozen layers, learning rates, weight decay, warmup steps) per pollutant model.
* Streaming epoch-level metrics and loss logs to the MLflow Tracking Server.
* Validating candidate model performance against production baselines using strict acceptance criteria.
* Registering updated model adapters and artifacts back into the Hopsworks Model Registry.

---

# 5. First-Time Model Training Pipeline Design

This section describes the detailed design of the First-Time Model Training Pipeline subsystem, which is responsible for building, evaluating, and registering baseline pollutant prediction models from scratch using historical atmospheric datasets.

Unlike incremental retraining or fine-tuning, this pipeline initializes candidate model architectures, executes full feature engineering dataset splits, performs hyperparameter training across multiple model variants, evaluates candidate models to select the top performer for each pollutant, and registers the baseline artifacts into the Hopsworks Model Registry.

---

## 5.1 Training Pipeline Class Diagram

The First-Time Model Training Pipeline uses an orchestrator pattern centered around the **PipelineOrchestrator**, which implements the **TrainingPipelineInterface**. The orchestrator coordinates interaction across configuration classes (**TrainingConfig**, **PollutantConfig**, **ModelConfig**), data ingestion components (**FeatureRetriever**, **DatasetBuilder**), model fitting engines (**ModelTrainer**, **PretrainedRegistry**, **Callbacks**), and evaluation/registration handlers (**ModelEvaluator**, **ModelRegistrar**).

External connections to the **AQI Feature Store**, **MLflow Tracking Server**, and **AQI Model Registry** on the **Hopsworks Platform** are managed through dedicated interface boundaries.

![Training Pipeline Class Diagram](figures/training_pipeline_class_diagram.svg)

**Figure 5.1.** Static structure of the First-Time Model Training Pipeline subsystem. The pipeline orchestrator manages historical feature retrieval, training dataset construction, model initialization and fitting from scratch, experiment tracking via MLflow callbacks, best-model evaluation and selection, and baseline registration in Hopsworks.

---

## 5.2 Pipeline Execution Workflow

The end-to-end execution of the first-time model training process follows a structured sequence:

1. **Invocation**: The `PipelineOrchestrator` receives an execution trigger via `run_training_pipeline()`, taking a structured `TrainingConfig` object.
2. **Feature Retrieval & Dataset Construction**:
   * The `FeatureRetriever` executes `retrieve()` against the **AQI Feature Store** to pull historical meteorological and pollutant feature groups.
   * The `DatasetBuilder` processes these raw feature sets via `construct()` to output a formatted `TrainingDataset` containing train, validation, and test splits along with feature scalers.


3. **Model Initialization & Fitting**:
   * The `ModelTrainer` reads hyperparameters from `ModelConfig` (`epochs`, `patience`, `learning_rate`, `do_training`) for each candidate model across pollutants defined in `PollutantConfig`.
   * The trainer calls `train()` to fit models implementing the `BasePollutantModel` interface (`fit(dataset)`), referencing pretrained base dependencies via `PretrainedRegistry` if applicable and streaming training metrics to the **MLflow Tracking Server** via `Callbacks`.


4. **Model Evaluation & Selection**:
   * The `ModelEvaluator` computes performance metrics via `evaluate()` and compares candidate architectures using `compare()`.
   * The `find_best(BasePollutantModel[])` method selects the optimal model variant for each pollutant based on minimal prediction error.


5. **Model Registration**:
   * The selected baseline model saves its artifacts locally via `save(path)`.
   * The `ModelRegistrar` executes `register()` to upload saved binaries, performance metrics, and feature schemas to the **AQI Model Registry** on Hopsworks.



---

## 5.3 Component Breakdown

### 5.3.1 PipelineOrchestrator & Configuration Classes

These classes manage hierarchical pipeline settings for training candidate models from scratch across all evaluated atmospheric pollutants ($PM_{2.5}, PM_{10}, O_3, NO_2, SO_2, CO$).

* **TrainingConfig**: Root configuration container aggregating `pollutant_config` arrays.
* **PollutantConfig**: Holds pollutant-specific configurations and wraps target candidate models (`models`).
* **ModelConfig**: Defines hyperparameters for individual candidate models:
* `model_name`: Identifies the candidate model architecture (e.g., XGBoost, LightGBM, PyTorch MLP).
* `do_training`: Boolean flag indicating whether to execute training for this candidate.
* `epochs`: Total training passes over the dataset.
* `patience`: Early stopping threshold to halt training when validation loss plateaus.
* `learning_rate`: Optimization step size.



---

### 5.3.2 FeatureRetriever & DatasetBuilder

These components handle historical feature access and dataset preparation.

* **FeatureRetriever**:
* `retrieve(): Features`: Queries the **AQI Feature Store** on Hopsworks to retrieve complete historical feature snapshots.


* **DatasetBuilder**:
* `construct(): TrainingDataset`: Performs temporal train/val/test splitting, sequence generation, scaling, and missing-data imputation to build a `TrainingDataset`.



---

### 5.3.3 ModelTrainer & BasePollutantModel Interface

The core fitting engine initializes and trains candidate model architectures.

* **ModelTrainer**:
* `train(): TrainingStatus`: Manages full training loops across candidate models, logging epoch-level metrics and loss curves to the **MLflow Tracking Server** via `Callbacks`.


* **PretrainedRegistry**: Provides access to optional base architecture weights or backbone configurations when initializing models.
* **BasePollutantModel Interface**:
* `config: TrainingConfig`: Property storing active model configuration details.
* `fit(dataset): Metrics`: Trains the model architecture from scratch on the provided dataset split and returns evaluation metrics.
* `predict(X): ndarray`: Generates numeric concentration predictions on input feature matrices.
* `save(path): Path`: Exports complete model weights and pipeline artifacts to a specified local directory.



---

### 5.3.4 ModelEvaluator

The `ModelEvaluator` performs performance analysis across candidate model variants.

* **Methods**:
* `evaluate(): Metrics`: Calculates test set metrics (RMSE, MAE, $R^2$, MAPE) for evaluated models.
* `compare(): Metrics`: Generates comparative performance tables across candidate models for a given pollutant.
* `find_best(BasePollutantModel[]): BasePollutantModel`: Identifies and returns the top-performing model candidate based on target evaluation metrics.



---

### 5.3.5 ModelRegistrar

The `ModelRegistrar` commits approved baseline models to cloud infrastructure.

* **Primary Responsibility**: Registers winning model candidates in the Hopsworks platform.
* **Methods**:
* `register(): RegistrationStatus`: Uploads model binaries, input/output schemas, and evaluation metrics to the **AQI Model Registry**, tagging them as initial production baselines.



---

## 5.4 Pipeline Responsibilities 

The First-Time Model Training Pipeline is strictly responsible for:

* Querying historical atmospheric feature records from the Hopsworks Feature Store.
* Constructing partitioned train, validation, and test datasets with proper temporal boundaries.
* Initializing and fitting baseline model candidates from scratch using configured hyperparameters.
* Logging training parameters, loss trajectories, and artifacts to the MLflow Tracking Server.
* Evaluating candidate models and selecting the top-performing model architecture per pollutant.
* Registering approved baseline model artifacts into the Hopsworks Model Registry.

Here is the complete **Feature Pipeline Design** documentation section, structured as **Section 2** to maintain consistency with your project structure and matching your class diagram.

---

# 6. Feature Pipeline Design

This section describes the detailed design of the Feature Pipeline subsystem, which is responsible for ingesting live weather and air quality observations, validating payload schemas, transforming raw metrics into engineered feature matrices, and writing feature groups to the feature store.

The pipeline runs on a scheduled basis to ensure downstream training, fine-tuning, and prediction pipelines always have access to up-to-date, structured atmospheric feature vectors.

---

## 6.1 Feature Pipeline Class Diagram

The Feature Pipeline follows an orchestrator pattern centered around the **PipelineOrchestrator**, which implements the **FeaturePipelineInterface**. The orchestrator coordinates execution across extraction (**RawDataExtractor**), validation (**DataValidator**), transformation (**FeatureTransformer**), and storage (**FeatureWriter**) components.

External integration points connect the **RawDataExtractor** to the external **OpenWeather API** and the **FeatureWriter** to the **AQI Feature Store** hosted on the **Hopsworks Platform**.

![Feature Pipeline Class Diagram](figures/feature_pipeline_class_diagram.svg)

**Figure 6.1.** Static structure of the Feature Pipeline subsystem. The pipeline orchestrator manages real-time data ingestion from OpenWeather API, data validation checks, feature transformations, and insertion into Hopsworks Feature Store.

---

## 6.2 Pipeline Execution Workflow

The operational flow of the feature ingestion and processing pipeline follows a clear 4-step sequence:

1. **Invocation**: The `PipelineOrchestrator` receives an execution trigger via `run_pipeline()`.
2. **Data Ingestion**: The `RawDataExtractor` queries the external **OpenWeather API** via `extract()` to pull raw meteorological metrics and pollutant concentration levels ($PM_{2.5}, PM_{10}, O_3, NO_2, SO_2, CO$), returning a `RawData` payload.
3. **Validation**:
   * The `DataValidator` evaluates raw incoming data structures via `validate()`.
   * Returns a `Tuple[bool, str]` indicating success/failure status and associated error or warning logs.


4. **Transformation**:
   * Upon successful validation, the `FeatureTransformer` calls `transform()` on `RawData`.
   * Handles missing value imputation, unit conversions, temporal aggregation, and lag/rolling feature calculations, returning `TransformedData`.


5. **Feature Store Ingestion**:
   * The `FeatureWriter` executes `write()` to save the structured feature matrix into designated feature groups inside the **AQI Feature Store** on the **Hopsworks Platform**.



---

## 6.3 Component Breakdown

### 6.3.1 PipelineOrchestrator

The orchestrator implements the primary interface contract for feature ingestion jobs.

* **Interface**: Implements `FeaturePipelineInterface`.
* **Primary Responsibility**: Sequentially executes extraction, validation, transformation, and storage steps. Handles pipeline error bubbling and returns execution status.
* **Methods**:
* `run_pipeline(): Status`: Executes the end-to-end feature pipeline execution flow.



---

### 6.3.2 RawDataExtractor

The extraction component serves as the gateway to external environmental data providers.

* **External Interaction**: Connects via HTTP API calls to the **OpenWeather API**.
* **Primary Responsibility**: Extracts real-time weather parameters (temperature, humidity, wind speed/direction, pressure) and atmospheric pollutant concentrations.
* **Methods**:
* `extract(): RawData`: Fetches raw json responses and returns a structured `RawData` object.



---

### 6.3.3 DataValidator

The validator enforces schema compliance and data quality thresholds before transformation.

* **Primary Responsibility**: Ensures mandatory fields exist, values fall within realistic physical ranges, and null counts stay within allowable limits.
* **Methods**:
* `validate(): Tuple[bool, str]`: Checks raw input data validity. Returns a boolean pass/fail flag along with a descriptive log message.



---

### 6.3.4 FeatureTransformer

The transformation module converts raw observation payloads into model-ready feature sets.

* **Primary Responsibility**: Executes feature engineering logic including timestamp parsing, cyclically encoding temporal features (hour, day of week, month), calculating rolling averages, creating spatial/meteorological interaction features, and producing lag variables.
* **Methods**:
* `transform(): TransformedData`: Converts validated `RawData` into formatted `TransformedData` feature vectors.



---

### 6.3.5 FeatureWriter

The storage handler interfaces with cloud feature store infrastructure.

* **External Interaction**: Connects via Hopsworks SDK to the **AQI Feature Store**.
* **Primary Responsibility**: Writes transformed feature records into target Hopsworks Feature Groups with appropriate primary keys and event timestamps.
* **Methods**:
* `write(): Status`: Inserts or upserts feature rows into Hopsworks and returns an execution status signal.



---

## 6.4 Pipeline Responsibilities 

The Feature Pipeline is strictly responsible for:

* Fetching raw weather and air quality observations from the OpenWeather API.
* Enforcing structural schema validation and value range checks on raw incoming payloads.
* Processing raw observations into engineered lag, temporal, and aggregated feature matrices.
* Persisting structured feature matrices to the Hopsworks Feature Store for downstream training, fine-tuning, and inference.
