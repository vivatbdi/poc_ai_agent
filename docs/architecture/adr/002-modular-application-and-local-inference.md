# ADR 002 — Modular application and separate local inference

- **Status:** superseded by 010
- **Affects:** Thai web chat, Conversation and report service, Analysis coordinator, Visual extraction agent, Insight analysis agent, Evidence validator, Thai report agent, Local inference service

## Decision

Use one modular FastAPI application process plus a separate local inference process. Keep logical agents independently testable inside the application. Initially evaluate one active inference request at a time. Use Docker Compose where the selected backend is compatible; document equivalent native local process wiring otherwise. Reassess production service boundaries using POC evidence.

## Why

The user accepted decision 3. The supplied system has 16 GB RAM, 15.5 GB usable, shared with integrated graphics. The design controls process and model concurrency while preserving explicit contracts. This trades independent per-agent deployment for simpler POC operation; production microservice topology and Kubernetes are not settled.

## Rejected

- One microservice/container per logical agent immediately — adds communication, deployment, and operational complexity before measured workloads establish a need.

