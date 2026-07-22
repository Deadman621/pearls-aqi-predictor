# ADR-006: GitHub Actions as the Workflow Orchestrator

## Status

Accepted

## Date

2026-07-22

## Context

The project requires scheduled execution of:

- Data collection
- Feature engineering
- Model training
- Testing
- Deployment

The specification allows either Apache Airflow or GitHub Actions.

## Decision

GitHub Actions shall orchestrate the project's automated workflows.

## Alternatives Considered

### Alternative 1 — Apache Airflow

Pros

- Powerful workflow orchestration
- DAG-based scheduling
- Advanced monitoring
- Highly scalable

Cons

- Requires additional infrastructure
- More complex deployment
- Overkill for a single-project pipeline

Rejected due to unnecessary operational complexity.

### Alternative 2 — GitHub Actions

Pros

- Integrated with GitHub
- Easy scheduling using cron
- Native CI/CD support
- No infrastructure management
- Simpler configuration

Cons

- Less flexible than Airflow for complex workflows
- Limited execution time compared to dedicated workflow platforms

Selected.

## Consequences

Positive

- Simple automation
- Integrated testing and deployment
- Easy maintenance
- Minimal operational overhead

Negative

- Less suitable for large-scale workflow orchestration
- Dependent on GitHub Actions execution limits

## Related Documents

- CI/CD Design
- Deployment Architecture
- System Architecture