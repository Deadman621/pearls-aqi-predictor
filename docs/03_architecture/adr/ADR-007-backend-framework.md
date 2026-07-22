# ADR-007: FastAPI as the Backend Framework

## Status

Accepted

## Date

2026-07-22

## Context

The project requires a backend service to expose machine learning predictions and communicate with the web dashboard. The project specification permits the use of either Flask or FastAPI.

The backend should support:

- RESTful API endpoints
- Request and response validation
- Easy integration with machine learning models
- Automatic API documentation
- Future extensibility

## Decision

FastAPI shall be used as the backend framework for the prediction service.

## Alternatives Considered

### Alternative 1 — Flask

Pros

- Mature ecosystem
- Simple to learn
- Large community
- Extensive extensions

Cons

- Manual request validation
- No built-in API documentation
- Requires additional libraries for features available by default in FastAPI

Rejected because the project primarily exposes a prediction API, where FastAPI provides stronger native support.

### Alternative 2 — FastAPI

Pros

- High performance
- Automatic OpenAPI (Swagger) documentation
- Built-in request and response validation
- Excellent support for Python type hints
- Well suited for machine learning inference services

Cons

- Slightly steeper learning curve than Flask
- Smaller ecosystem compared to Flask

Selected.

## Consequences

Positive

- Self-documenting REST API
- Strong request validation
- Cleaner API implementation
- Easier future expansion

Negative

- Team members (or future contributors) must learn FastAPI
- Some Flask-specific extensions are not directly available

## Related Documents

- API Design
- System Architecture
- Deployment Design