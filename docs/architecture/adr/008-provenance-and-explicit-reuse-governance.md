# ADR 008 — Provenance and explicit reuse governance

- **Status:** superseded by 017
- **Affects:** Reviewed-example governance, Production policy controls, Conversation and report service, Analysis coordinator, Evaluation harness

## Decision

Track reviewed examples, immutable source/report versions, human review, separate permitted-use authorizations, and downstream lineage. Report approval and collection grant no reuse permission. Revocation or source deletion blocks further reuse and starts a durable owner-by-owner cleanup workflow under agreed policy. Expose pending cleanup and unresolved obligations; future improvement mechanisms require separate adoption and deployment decisions.

## Why

The user accepted decision 9. Explicit lineage lets source access, authorization, and deletion affect tracked downstream copies. Strong blocking decisions combine with eventual physical cleanup; no cross-component atomic deletion or model-unlearning claim is made. Production retention, backup expiry, exceptions, and consequences for future trained models remain unresolved. No training or RAG pipeline is adopted; collecting governed examples does not automatically change deployed behavior.

## Rejected

- A folder of approved reports with manually managed permissions — loses dependable links between source versions, permitted uses, authorization changes, and downstream copies.

