# ADR 006 — Durable jobs and bounded background execution

- **Status:** superseded by 015
- **Affects:** Thai web chat, Conversation and report service, Analysis coordinator, Local inference service

## Decision

Persist analysis jobs in SQLite and execute them using one bounded background worker inside the application process. Return a durable job reference and use browser polling for progress. Record stage outcomes, bounded attempts, and deadlines; mark interrupted work incomplete after restart and allow retry. Reconcile a transactionally persisted enqueue intent into the coordinator-owned job using a stable identity.

## Why

The user accepted decision 7. Durable job state decouples long inference from browser request lifetime without a broker. The enqueue intent closes the evidence-save/job-creation crash gap without allowing two components to write one entity. Retry may recompute inference but must not duplicate report publication, overwrite approved history, or resurrect tombstoned sources. Check source revision and permissions again at execution and publication.

## Rejected

- A dedicated broker and worker service immediately — adds another service before the POC requires distributed or multiple-worker execution.

