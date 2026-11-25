# ADR 017 — Reconfirmed Production V1 lifecycle and reuse governance

- **Status:** accepted
- **Affects:** Reviewed-example governance, Production policy controls, Conversation and report service, Analysis coordinator, Evaluation harness

## Decision

Supersede ADR 008 with the user's explicit answer to reopened interview Decision 9: **1. Accept Decision 9 for Production V1**.

Track source evidence, exact report versions, human review, separate authorization for each permitted use, and downstream artifacts. Preserve links from each reviewed example and derived artifact to its source/report versions, review, authorized purpose, and authorizing identity. Collection or report approval alone grants no knowledge or training reuse permission. Enforce the shared role/action/resource-scope authorization policy accepted in ADR 016 when accessing examples or authorizing/performing reuse.

When authorization is revoked or source deletion is requested, block affected reuse and record the downstream cleanup obligations. Track owner-by-owner cleanup to completion under the agreed policy, including interrupted work and unresolved obligations. Physical cleanup across components may complete asynchronously; it must remain pending and visible until the applicable obligations are satisfied, while affected reuse stays blocked. Do not mark cleanup complete merely because a request was queued, a source was removed, or one downstream artifact was deleted.

Retain source/dependency lineage needed to locate affected artifacts and finish cleanup, subject to the agreed policy. Do not invent a retention exception or authorize further reuse merely to preserve lineage. The precise records kept during cleanup, their eventual deletion, and any authorized exceptions require the detailed production policy. Apply lifecycle handling to tracked evaluation copies and other downstream material as well as reviewed examples.

Detailed retention periods, backup retention/expiry and restore consequences, and policies for future trained models remain open. This architecture does not promise automatic model unlearning, secure erasure, atomic deletion across all owners, or deletion from untracked external systems. The policy must define consequences and responsibilities before such uses are authorized.

RAG and training remain separate adoption decisions; neither is selected by this ADR. Prompt improvements and changes to deployed models or knowledge also retain the PRD's evaluation and explicit human approval requirements. Collecting governed examples must not automatically change deployed behavior. Any future adoption must evaluate evidence-boundary compliance, analytical quality, resources, authorization, and lifecycle consequences before deployment.

This is a Production V1 capability and does not expand the POC's shared local evaluation scope. Existing POC manual deletion, version-specific approval, restart recovery, and rejection of stale results remain as accepted in ADRs 014–015. Production deployment, detailed policy, and implementation are not authorized by this architecture decision alone.

Verify provenance completeness, separate per-use grants, denied unauthorized reuse, revocation/source-deletion blocking, dependency traversal, interrupted cleanup recovery, and prevention of premature completion in component and integrated evaluations. No such test is claimed as executed by this document.

## Why

The user explicitly re-confirmed production governance after withdrawing the original answer. Exact versions and downstream lineage make affected uses identifiable when authorization changes or deletion is requested. Blocking reuse immediately while tracking policy-governed cleanup avoids treating eventually completed file operations as an instantaneous cross-component deletion. Separate permission per use preserves the distinction between human report approval and authorization to reuse data. Keeping detailed retention, backup, trained-model policies, and adoption decisions open prevents governance collection from silently selecting RAG or training.

## Rejected

- A manually managed folder of approved examples without per-use grants and downstream lineage — cannot reliably trace authorization changes or cleanup obligations through derived artifacts.
- Treating report approval as blanket reuse/training permission — conflicts with the accepted separation of human decisions.
- Declaring deletion complete when cleanup is only queued or partly finished — hides unresolved downstream obligations.
- Automatically adopting RAG or training because examples are collected — these remain separate evaluation and adoption decisions.
- Promising trained-model deletion behavior before policy and a mechanism are defined — no unlearning or secure-erasure guarantee is established.
