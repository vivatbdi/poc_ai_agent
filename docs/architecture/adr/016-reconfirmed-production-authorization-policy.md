# ADR 016 — Reconfirmed Production V1 authorization policy

- **Status:** accepted
- **Affects:** Production policy controls, Thai web chat, Conversation and report service, Analysis coordinator, Reviewed-example governance, Evaluation harness

## Decision

Supersede ADR 007 with the user's explicit answer to reopened interview Decision 8: **1. Accept Decision 8 for Production V1**.

Use a shared authorization policy enforced by application services. Evaluate the authenticated user's role, requested action, and scope for the specific resource. Deny the action unless expressly permitted; an unknown/unvalidated identity or unavailable policy cannot grant access. UI controls do not replace service enforcement, and components must not bypass it through direct data access.

Apply the boundary to protected conversations, screenshots, reports, review actions, delayed jobs, and reviewed examples. Recheck access when delayed jobs execute and before returning or publishing protected results. A job's enqueue-time permission is not a permanent grant. If access is no longer granted, do not return or publish protected material; prevent unauthorized execution and expose only an appropriate non-sensitive Thai status/error.

Keep report approval and knowledge or training reuse authorization separate. Approval of a report alone grants no reuse permission. The architecture must support an explicit self-approval policy, but this decision does not permit or prohibit production self-approval or decide the rules.

Leave exact roles, identity provider and authentication integration, resource visibility rules, approval responsibilities, self-approval policy, and authorities allowed to grant reuse permission open. Do not select an authentication library, policy engine dependency, or identity protocol through this decision. Detailed rule versions, revocation handling, and enforcement checks are design/evaluation work before production acceptance.

This decision applies to Production V1. The POC retains its shared local evaluation workspace and does not establish production permissions. It does not authorize production deployment or bring production-specific access infrastructure into the POC scope. Production lifecycle/reviewed-example governance remains pending Decision 9.

Verify permitted and denied actions across resource scopes, approval versus reuse separation, unavailable/invalid identity or policy, and revocation between enqueue, execution, return, and publication. Retain human evaluation and explicit acceptance requirements; no production access policy is considered validated merely because this ADR exists.

## Why

The user explicitly re-confirmed a shared policy with service-level enforcement, resource-specific role/action/scope decisions, and denial unless expressly permitted. Shared rules prevent inconsistent decisions across application services, while repeated checks protect delayed operations from relying on stale permissions. Keeping report approval separate from reuse authorization preserves the PRD's human-control boundary without inventing detailed production roles or policies.

## Rejected

- Role checks without action and resource scope — cannot express the accepted resource-specific access boundary.
- UI-only authorization or a check only at enqueue time — can be bypassed by service calls or become stale before delayed execution and result publication.
- Treating report approval as knowledge/training permission — merges distinct human authorization decisions.
- Inferring production visibility or self-approval policy from shared POC access — the user explicitly leaves those production policies open.
