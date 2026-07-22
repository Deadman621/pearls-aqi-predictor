# ADR-005: Independent Model per Pollutant

## Status

Accepted

## Date

2026-07-22

## Context

The project requires predicting six pollutant concentrations.

Two modelling strategies were evaluated.

## Decision

The system shall train one independent machine learning model for each pollutant.

Models include:

- PM2.5
- PM10
- NO₂
- SO₂
- CO
- O₃

Each model will be trained, evaluated, versioned, and deployed independently.

## Alternatives Considered

### Alternative 1 — Single Multi-Output Model

Pros

- One deployment artifact
- Shared representation

Cons

- More difficult to tune
- Harder to debug
- Less flexible

Rejected.

### Alternative 2 — One Model per Pollutant

Pros

- Modular
- Easier experimentation
- Independent retraining
- Better maintainability

Selected.

## Consequences

Positive

- Easier debugging
- Better modularity
- Independent optimization
- Simpler model registry

Negative

- Six models instead of one
- Slightly longer prediction pipeline

## Related Documents

- ML Design
- Training Pipeline
- Model Registry