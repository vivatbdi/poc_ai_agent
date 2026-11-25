# ADR 005 — SQLite and managed local artifacts

- **Status:** superseded by 014
- **Affects:** Conversation and report service, Analysis coordinator, Visual extraction agent, Insight analysis agent, Evidence validator, Thai report agent, Reviewed-example governance, Evaluation harness

## Decision

Use SQLite for structured POC state and managed local directories for screenshots and binary artifacts. Keep component-owned records behind their owning interfaces. Commit evidence before analysis, publish immutable report versions, bind approvals to exact completed versions, and track interrupted deletion durably. Reassess production storage against workload, authorization, and lifecycle policy.

## Why

The user accepted decision 6. Embedded storage avoids additional database and object-storage services for the shared local POC while supporting durable state transitions. File staging, metadata publication, orphan reconciliation, and deletion journals address the lack of a transaction spanning SQLite and the filesystem. Approval never transfers automatically to a new revision. This provides restart durability for committed content, not disk-loss recovery or a backup policy.

## Rejected

- PostgreSQL plus object storage immediately — adds infrastructure and operation before POC concurrency and production requirements demonstrate the need.

