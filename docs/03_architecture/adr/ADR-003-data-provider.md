# ADR-003: External Data Provider Selection

**Status:** Accepted

**Date:** 2026-07-22

## Context

The AQI prediction system requires a reliable external data provider to support the complete machine learning lifecycle, including:

- Historical data collection for model training
- Real-time data ingestion
- Hourly feature pipeline execution
- Daily model retraining
- Real-time prediction
- Weather feature engineering

The project requires access to:

- Historical meteorological observations
- Historical air pollutant concentrations
- Current weather and air pollution observations
- Air pollution forecasts for comparison
- Hourly data granularity

Two candidate providers were evaluated:

- **OpenWeather**
- **AQICN (World Air Quality Index Project)**

## Decision

The system shall use **OpenWeather** as the sole external data provider.

## Rationale

### Complete Data Coverage

OpenWeather provides a unified set of APIs that satisfy all project requirements.

The Air Pollution API includes:

- Historical hourly air pollution data (available from **27 November 2020**)
- Current air pollution observations
- Four-day hourly air pollution forecasts
- Pollutant concentrations for:
  - PM₂.₅
  - PM₁₀
  - NO
  - NO₂
  - SO₂
  - CO
  - O₃
  - NH₃

Additionally, OpenWeather provides meteorological data required for feature engineering, allowing both weather variables and pollutant concentrations to originate from the same provider.

### Consistent Machine Learning Pipeline

Using a single provider ensures consistency between:

- Historical training data
- Real-time inference data
- Scheduled feature pipeline execution

This eliminates the need to reconcile datasets from multiple providers that may differ in timestamps, units, or measurement methodologies.

### Evaluation of AQICN

AQICN was evaluated as an alternative data provider.

Although AQICN offers current air quality observations and downloadable historical data, it was not selected because:

- The historical archive for Karachi contains **daily PM₂.₅ AQI values only**.
- The historical archive currently extends only until **March 2025**, leaving a significant gap between the latest available data and the present.
- Historical observations for the remaining required pollutant gases (PM₁₀, NO₂, SO₂, CO, O₃, NH₃) are not available through the evaluated historical dataset.
- The available historical data does not satisfy the project's requirement for hourly observations.

Consequently, AQICN cannot support the selected one-model-per-pollutant forecasting strategy.

## Consequences

### Positive

- Single external dependency
- Unified data format across the entire pipeline
- Simplified feature engineering
- Simplified maintenance
- Supports hourly feature pipeline execution
- Supports automated model retraining
- Provides historical and real-time pollutant concentrations required for model training
- Eliminates data synchronization issues between multiple providers

### Negative

- The system depends on the availability and reliability of OpenWeather services.
- Changes to OpenWeather pricing or API policies may require future migration.
- Historical endpoint availability depends on the selected OpenWeather subscription plan.

## Alternatives Considered

### AQICN

Rejected because the evaluated historical dataset:

- Provides only daily PM₂.₅ AQI values for Karachi.
- Does not provide complete historical pollutant concentration data required by the selected architecture.
- Does not provide sufficiently current historical observations for continuous retraining.

## Related Decisions

- ADR-001 — Prediction Target
- ADR-002 — Forecast Granularity
- ADR-004 — Model Strategy