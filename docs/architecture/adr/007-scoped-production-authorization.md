# ADR 007 — Scoped centralized production authorization

- **Status:** superseded by 016
- **Affects:** Production policy controls, Thai web chat, Conversation and report service, Analysis coordinator, Reviewed-example governance

## Decision

Centralize production authorization using authenticated identity, role, resource scope, and explicit action permissions. Enforce it in application services, including delayed jobs. Deny when no rule grants access or policy evaluation is unavailable. Keep report approval and reuse authorization distinct; make self-approval configurable. Preserve the shared local POC mode as a separate explicit policy mode.

## Why

The user accepted decision 8 as an architectural boundary. Role checks alone cannot decide which conversations, screenshots, or examples someone may access. Central policy permits consistent enforcement while owners retain mutation responsibility. Actual production roles, identity provider/protocol, visibility, self-approval, authorizing authorities, and sensitive-data rules remain unresolved. This ADR neither selects an authentication library nor establishes production readiness.

## Rejected

- Role checks alone — insufficient to express resource-specific visibility and separate actions without additional resource-scoping rules.

