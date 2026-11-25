# ADR 010 — Reconfirmed POC deployment boundaries

- **Status:** accepted
- **Affects:** Thai web chat, Conversation and report service, Analysis coordinator, Visual extraction agent, Insight analysis agent, Evidence validator, Thai report agent, Local inference service, Evaluation harness

## Decision

Supersede ADR 002 with the user's explicit re-confirmation of interview Decision 3: **1. Accept the separate inference process proposal for the POC**.

Use one modular FastAPI application process and one separate local inference process. Internal components within the application communicate through Python interfaces, as settled in ADR 009. Private HTTP versus local IPC is the next pending decision; accepting a separate process does not select either transport or require inference serving to use FastAPI.

If the inference process fails during model loading or analysis, keep the application available when its own resources permit, preserve saved screenshots and prior reports, show a Thai error, and allow retry. Failed or incomplete inference must not appear completed or approved. Test that process failure boundary and the selected communication path in component and integrated evaluations.

Start with one inference job at a time as a benchmark baseline. The application and inference process share the laptop's memory budget; process isolation does not isolate host-wide memory exhaustion. Model sizes and operational concurrency follow measured results. Windows is the host OS and Ubuntu in WSL is the development environment. Inference runtime, Windows/WSL execution placement, and hardware acceleration remain open. If inference runs in WSL, measure memory available there before deciding model sizes and concurrency.

Container packaging, Docker Compose compatibility, application worker topology, polling, persistent job storage, and detailed retry/restart mechanisms remain open; this deployment acceptance does not re-accept Decisions 4–9. Production deployment boundaries require POC evidence and a later decision.

## Why

The user explicitly re-confirmed Decision 3 after withdrawing the prior answers. A separate inference process provides a useful failure boundary during model loading and analysis while allowing the application to preserve conversation state and offer retry, supporting the PRD recovery requirement. Two processes and their communication path add management and testing work. Shared memory pressure still affects the whole machine, so benchmark results must determine viable capacity.

## Rejected

- Same-process inference for this POC — simplifies process management but couples model-loading and inference failures more closely to application availability and state handling.
- Treating this acceptance as approval of HTTP, IPC, Docker Compose, a particular backend, or a durable worker implementation — those choices need their own discussion and explicit answers.
- Treating the single-job baseline as proof of safe model size or permanent capacity — actual inference-environment memory and benchmark results remain necessary.
