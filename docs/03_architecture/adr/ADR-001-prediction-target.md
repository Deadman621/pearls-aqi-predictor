# ADR-001: Predict Pollutants Instead of AQI

## Status

Accepted

## Date

2026-07-22

## Context

The project requires forecasting air quality for Karachi. Two approaches were considered:

1. Predict AQI directly.
2. Predict pollutant concentrations and derive AQI.

AQI is a derived environmental indicator calculated from pollutant concentrations rather than a directly measured variable.

## Decision

The system shall predict future concentrations of:

- PM2.5
- PM10
- NO₂
- SO₂
- CO
- O₃

AQI values shall be calculated from the predicted pollutant concentrations using the standard AQI calculation methodology.

## Alternatives Considered

### Alternative 1 — Direct AQI Prediction

Pros

- Simpler implementation
- Single prediction target

Cons

- Cannot explain which pollutant influences AQI
- Less scientifically meaningful
- Less extensible

Rejected because AQI is a derived metric and predicting pollutants provides greater interpretability and flexibility.

## Consequences

Positive

- Better explainability
- Modular architecture
- Pollutant-level insights
- Easier future extensions

Negative

- Six prediction targets instead of one
- More models to train and maintain

## Related Documents

- SRS
- ML Design
- System Architecture