# ADR 009 — Clarified POC runtime and evaluation stack

- **Status:** accepted
- **Affects:** Thai web chat, Conversation and report service, Analysis coordinator, Visual extraction agent, Insight analysis agent, Evidence validator, Thai report agent, Local inference service, Production policy controls, Reviewed-example governance, Evaluation harness

## Decision

Supersede ADR 001 with the user's revised answer to interview Decision 2: **2. Accept with changes**.

Keep Python, FastAPI, pytest, Jupyter, and the minimal HTML/CSS/JavaScript chat for the POC. FastAPI provides the browser-facing API. Components running in the same process communicate through Python interfaces; selecting FastAPI does not require HTTP between those components or require FastAPI for inference serving. Decide service-to-service APIs in the deployment-boundary discussion; the previously drafted private HTTP inference transport is not a settled choice.

Retain both mandatory evaluation requirements: executable Jupyter notebooks for every developed POC component and a reproducible end-to-end evaluation of the integrated workflow. Record actual outputs, metrics, limitations, and explicit human acceptance at both levels; component acceptance alone does not establish integrated acceptance.

Record the user's clarification to interview Question 1: the host OS is Windows and the development environment is Ubuntu in WSL. The inference runtime, execution location, and hardware acceleration remain open. If inference runs in WSL, measure memory available inside that environment before deciding model sizes and concurrency; the host's 15.5 GB usable RAM is not a measured WSL budget. The earlier single-request evaluation starting point remains provisional pending that measurement.

## Why

The user explicitly replaced the original Accept answer with Accept with changes. The stack remains suitable for the POC, while the clarification separates the browser API, same-process component interfaces, and deployment-specific service transport. It also prevents the host hardware figures from being treated as an established inference memory allowance inside WSL. Accepted deployment and workflow decisions are retained except where this clarification explicitly leaves service APIs and measured runtime capacity open. Model/backend versions and runtime compatibility still require evaluation.

## Rejected

- HTTP/FastAPI for every logical component — same-process components use Python interfaces; logical agents do not automatically need network APIs.
- Selecting inference service transport as part of the application stack — service-to-service APIs belong to the deployment-boundary discussion and remain pending.
- Using host RAM figures to fix WSL model sizes or concurrency — memory available in the actual inference environment must be measured first.
- Component notebooks alone as POC acceptance evidence — a separate reproducible integrated evaluation and human acceptance are also mandatory.
- Separate React/TypeScript frontend for the POC — the minimal HTML/CSS/JavaScript interface remains the accepted scope.
