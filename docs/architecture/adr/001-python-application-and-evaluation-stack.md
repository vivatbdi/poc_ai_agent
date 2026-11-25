# ADR 001 — Python application and evaluation stack

- **Status:** superseded by 009
- **Affects:** Thai web chat, Conversation and report service, Analysis coordinator, Visual extraction agent, Insight analysis agent, Evidence validator, Thai report agent, Local inference service, Production policy controls, Reviewed-example governance, Evaluation harness

## Decision

Use Python, FastAPI, and pytest for the application and its isolated tests, with executable Jupyter component evaluation notebooks. Serve a minimal HTML/CSS/JavaScript chat interface from the application. Keep model-serving backends independently selectable.

## Why

The user accepted decision 2. Python keeps analytical code and executable evaluation notebooks in one language; FastAPI supplies explicit application APIs. A minimal web interface meets the POC chat requirement without an additional frontend build pipeline. Exact runtime/dependency versions and environment compatibility require implementation-time validation.

## Rejected

- Separate React/TypeScript frontend and Python backend — adds a second build and test toolchain before the minimal POC interaction requires it.

