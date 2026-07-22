# ADR-002: Hourly Forecasting

## Status

Accepted

## Date

2026-07-22

## Context

The project specification requires forecasting air quality for the next three days.

Two forecast granularities were evaluated.

## Decision

The system shall predict pollutant concentrations hourly for the next 72 hours.

The dashboard may aggregate hourly predictions into daily summaries for visualization.

## Alternatives Considered

### Alternative 1 — Daily Prediction

Pros

- Simpler implementation
- Smaller datasets

Cons

- Approximately 24× fewer training samples
- Reduced temporal resolution
- Lower predictive capability

Rejected due to insufficient data density.

### Alternative 2 — Hourly Prediction

Pros

- Larger training dataset
- Better captures temporal dynamics
- More informative forecasts

Cons

- More predictions generated
- Slightly more computational cost

Selected.

## Consequences

Positive

- Better model performance
- Higher data resolution
- Supports both hourly and daily visualizations

Negative

- Larger datasets
- Longer training times

## Related Documents

- Data Design
- ML Design