# ADR 015 — Reconfirmed durable analysis execution

- **Status:** accepted
- **Affects:** Thai web chat, Conversation and report service, Analysis coordinator, Local inference service, Evaluation harness

## Decision

Supersede ADR 006 with the user's explicit answer to reopened interview Decision 7: **1. Accept Decision 7**.

Persist analysis jobs and stage outcomes in SQLite. Use one bounded background coordinator inside the existing application process; keep model execution in the separate local inference process through private HTTP v1 under ADRs 010–011. This does not add another application-worker process, broker, or scheduler service. Internal application components use Python interfaces under ADR 009.

Save evidence and a durable scheduling intent before acknowledging submission for analysis. Reconcile the intent idempotently into the coordinator-owned job using a stable operation identity; conversation storage and coordinator job records retain separate ownership. This closes the crash gap between saving evidence and scheduling work. Return a durable job reference so the browser can poll status without keeping the inference request attached to a browser connection.

Record stage outcomes, attempts, and incomplete/failed states durably. After application restart, interrupted jobs appear incomplete and allow explicit retry; do not silently mark them completed, approved, or automatically resumed. Preserve saved screenshots, prior reports, and their version-specific approval state. Repeated submission/retry must not duplicate completed report publication. Before accepting results or publishing a report, verify the job/attempt identity and current source revision; reject results for deleted or superseded inputs and expired/superseded attempts.

Start with one inference job at a time. After a timeout, **confirm the previous attempt has finished or been safely stopped before dispatching another job**, whether retrying the same analysis or starting different work. A generic worker-ready response or an expired client timeout alone is not proof that the previous computation ended. Apply this check after application restart as well if the separate inference process may still be running an abandoned attempt. Until its outcome is resolved, keep the attempt visibly incomplete and block further dispatch; preserve user data and explain the condition in Thai.

Use bounded waits and attempts rather than an unbounded retry loop. Detailed worker status/stop mechanisms, attempt limits, timeout values, polling intervals, and crash-safe transitions require detailed design and testing against the selected inference runtime; this ADR does not invent values or assume a backend cancellation capability. User retry is supported; no automatic recovery may bypass attempt resolution or stale-result checks.

Retain executable component notebooks and reproducible integrated evaluation. Exercise interrupted submission/scheduling, inference/application process failure, polling after restart, safe retry, timeout with continuing computation, duplicate publication prevention, and late results after source deletion or revision. Human acceptance is required; documenting these checks is not evidence that they have run.

Model/backend, acceleration, Windows/WSL placement, storage paths, and measured operating capacity remain open under the existing evaluation decisions. Production authorization and reviewed-example lifecycle mechanisms remain pending Decisions 8–9.

## Why

The user explicitly re-confirmed durable SQLite jobs, one background coordinator, browser polling, interrupted-job recovery, and publication safeguards. Durable state decouples analytical work from browser request lifetime while fitting the accepted two-process POC. A timeout can leave computation running in the separate process, so confirmed completion or safe stopping must precede every subsequent dispatch. Strong result/version checks protect saved reports and prevent a delayed response from restoring deleted evidence.

## Rejected

- A dedicated broker and separate application-worker service for the POC — adds infrastructure and a third process before workload evidence requires it.
- Browser-request-only or memory-only job state — cannot provide the accepted restart visibility and durable retry behavior.
- Treating timeout or worker readiness as proof of prior-attempt termination — can overlap inference jobs and accept stale results.
- Automatically completing interrupted work or publishing every retried result — risks duplicate reports, invalid approval state, and recreation of deleted data.
