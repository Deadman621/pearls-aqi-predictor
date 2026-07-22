# ADR-004: OpenWeather as Primary Data Source

## Status

Accepted

## Date

2026-07-22

## Context

The project requires weather observations and air pollution data from an external provider.

Several providers were considered.

## Decision

OpenWeather shall be used as the primary provider for weather and air pollution data.

## Alternatives Considered

### Alternative 1 — AQICN

Pros

- AQI focused
- Large coverage

Cons

- Less integrated weather information
- AQI-centric rather than pollutant-centric

Rejected.

### Alternative 2 — OpenWeather

Pros

- Weather API
- Air Pollution API
- Consistent authentication
- Comprehensive documentation

Selected.

## Consequences

Positive

- Unified API
- Simplified data collection
- Easier maintenance

Negative

- Historical data availability may depend on subscription tier
- API limits must be managed

## Related Documents

- Data Design
- Feature Engineering