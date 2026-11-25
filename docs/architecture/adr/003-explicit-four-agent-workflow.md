# ADR 003 — Explicit four-agent workflow

- **Status:** superseded by 012
- **Affects:** Analysis coordinator, Visual extraction agent, Insight analysis agent, Evidence validator, Thai report agent, Conversation and report service

## Decision

Use four specialized logical agents: visual extraction, insight analysis, evidence validation, and Thai report writing. An explicit coordinator controls stage order, context, clarification, revisions, and retries. Validate findings before writing and check final report claims before publishing a completed draft. Use deterministic calculation checks alongside evaluated model-assisted checks.

## Why

The user accepted decision 4. Explicit execution makes intermediate evidence and failures reproducible and limits unbounded agent activity. Role count does not fix model count: roles may share models where supported by evaluation, while the integrated workflow must use multiple specialized local models. The validator is not infallible or independent merely because it has a separate role. Unsupported findings are excluded or qualified, and failed validation cannot silently become completed work.

## Rejected

- Autonomous agents selecting delegation and execution order — harder to bound execution cost, trace evidence, and reproduce failures for the required evaluations.

