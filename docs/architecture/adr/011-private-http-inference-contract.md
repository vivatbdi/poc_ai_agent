# ADR 011 — Private HTTP inference contract for the POC

- **Status:** accepted
- **Affects:** Local inference service, Analysis coordinator, Thai web chat, Evaluation harness

## Decision

The user explicitly accepted the deployment follow-up: **1. Accept private HTTP with a versioned contract for the POC**, with a private endpoint, minimal contract, explicit success/error responses, and timeout/unavailable-worker handling.

Use private HTTP between the modular application and the separate local inference process accepted in ADR 010. The browser calls the application's FastAPI API; it never calls inference directly. Internal application components retain Python interfaces under ADR 009. This decision does not require inference serving to use FastAPI or select a model-specific vendor API.

Use an explicitly identified v1 contract and one synchronous inference request/response operation. Agree its concrete URL and payload schema during detailed design. Each request identifies its contract version, correlation/attempt identity, requested inference task, authorized inputs, and bounded execution budget. Do not add a broker, streaming protocol, callback API, or durable worker queue through this decision.

### Success response

HTTP 200 returns the contract version, matching request identity, task result, and actual model/release identity. A result must match the requested task's agreed structure. The application validates version, identity, structure, and the active request before accepting it. HTTP success alone does not validate analytical evidence, complete a report, or approve publication. Empty, malformed, mismatched, or late responses are not successful stage outcomes.

### Error response

A worker-generated error returns a non-2xx status and a minimal envelope containing contract version, request identity where available, a stable error category, and whether retry may be appropriate. Do not include input contents, credentials, tracebacks, or implementation paths. Internal error categories are mapped by the application to Thai user-facing messages.

| HTTP status | Worker error category | Application behavior |
|---|---|---|
| 400 | Invalid request or unsupported contract/task | Stop this attempt; correct compatibility/input before retry rather than retrying unchanged. |
| 429 | Worker busy | Preserve saved state and expose a retryable incomplete outcome; no implicit queue or immediate retry loop. |
| 503 | Worker not ready or model unavailable | Preserve saved state, explain unavailability in Thai, and permit retry when ready. |
| 500 | Inference execution failed | Fail the attempt, preserve saved state, and offer retry; repeated failure requires evaluation rather than unlimited retries. |

### Timeout and unreachable worker

Bound connection and response waits; concrete values are configurable and must be measured against the POC workload rather than invented as acceptance targets. Connection refusal, connection loss, worker crash, and deadline expiry may produce no valid worker response. The application records a local unavailable/timeout outcome and request identity rather than assuming an error envelope will arrive.

On these failures, stop waiting, keep the application responsive when its resources permit, preserve saved screenshots and prior reports, display a Thai error, and offer retry. Treat the inference outcome as unknown or incomplete, never completed or approved. Ignore late results from expired/superseded attempts. A client timeout does not prove worker computation stopped: before retry, establish that the worker can accept the next job or terminate the abandoned attempt safely, preserving the single-active-job benchmark baseline. Re-execution may recompute; the application must not duplicate or overwrite completed report versions merely because it retried. Detailed durable jobs, automatic-retry policy, restart/cancellation mechanisms, and storage remain pending Decisions 6 and 7.

### Private access and open runtime choices

Configure the worker address on the application side; do not expose it in browser configuration, publish it to a LAN/public interface, or permit user-supplied arbitrary worker URLs. Prefer loopback binding when the selected placement supports application-to-worker connectivity. If Windows/WSL placement requires a different address, validate a private application-to-worker route and restrictive access before using it; do not assume loopback behavior across environments or bind broadly as a workaround. Placement-specific network/access configuration remains to be evaluated.

Inference runtime, model/backend, hardware acceleration, and Windows-versus-WSL placement remain open. If inference runs in WSL, measure memory available there before deciding model sizes and operational concurrency. Both component notebooks and reproducible integrated evaluation must exercise success, explicit worker errors, malformed/version-mismatched responses, timeout, worker loss, late results, privacy of the endpoint, and safe retry.

## Why

The user accepted an explicit private, minimal HTTP contract after re-confirming the separate-process boundary. It gives the application a testable success/failure boundary without coupling logical agents to network APIs or selecting an inference framework. Process communication adds failure modes, so lack of a valid response must be handled locally and cannot imply successful analysis. The original transport deferral is resolved; Decisions 4–9 remain reopened and are not accepted by this decision.

## Rejected

- Local process IPC for this POC boundary — the user chose private HTTP with an explicit versioned contract; IPC would require a separate mechanism matched to the eventual process placement.
- Browser-to-inference requests or a public inference endpoint — violates the accepted private application-to-worker boundary.
- Assuming every failure returns a worker error response — a crash, refused connection, or timeout may return no valid response.
- Automatic unbounded retries or treating a timeout as cancellation — can duplicate computation or overlap jobs while the prior attempt is still active.
- Selecting inference FastAPI, acceleration, or Windows/WSL placement through transport acceptance — these remain explicit evaluation questions.
