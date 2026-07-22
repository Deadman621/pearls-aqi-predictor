# ADR-005: Hopsworks as the Feature Store and Model Registry

## Status

Accepted

## Date

2026-07-22

## Context

The project requires a centralized platform for storing engineered features and managing trained machine learning models. The specification allows the use of either Hopsworks or Vertex AI.

The chosen solution should support:

- Offline feature storage for model training
- Online feature retrieval for inference
- Model versioning
- Model registry integration
- Compatibility with a serverless machine learning workflow

## Decision

Hopsworks shall be used as the project's Feature Store and Model Registry.

## Alternatives Considered

### Alternative 1 — Vertex AI

Pros

- Fully managed Google Cloud service
- Excellent integration with the Google Cloud ecosystem
- Enterprise-grade infrastructure

Cons

- Greater dependency on Google Cloud Platform
- More complex setup
- Some functionality may require cloud resources or billing

Rejected due to increased infrastructure complexity relative to project needs.

### Alternative 2 — Hopsworks

Pros

- Purpose-built for machine learning workflows
- Integrated Feature Store and Model Registry
- Supports offline and online features
- Simpler setup for experimentation
- Well suited for academic ML projects

Cons

- Additional platform to learn
- Smaller ecosystem compared to Google Cloud

Selected.

## Consequences

Positive

- Centralized feature management
- Consistent features between training and inference
- Integrated model versioning
- Cleaner ML pipeline architecture

Negative

- Project depends on Hopsworks availability
- Team members (or future contributors) must learn Hopsworks concepts

## Related Documents

- System Architecture
- Data Design
- ML Design