# ADR 012 — Four-role workflow and two evidence checks

- **Status:** accepted
- **Affects:** Analysis coordinator, Visual extraction agent, Insight analysis agent, Evidence validator, Thai report agent, Conversation and report service, Evaluation harness

## Decision

Supersede ADR 003 with the user's explicit answer to reopened interview Decision 4: **2. Accept with changes**.

Keep four logical roles: visual extraction, insight analysis, evidence validation, and Thai report writing. An explicit Python coordinator within the modular application controls the workflow and passes context/results through Python interfaces. Model execution uses the separate local inference process and private HTTP contract accepted in ADRs 010 and 011. Logical roles are not separate processes or fixed model assignments.

The first evidence check validates candidate findings and calculations against their sources: supplied screenshots, explicit user context, and supported derivations. Deterministic calculation checks complement evidence checks within the limits of screenshot readability. Unsupported or unreadable inputs are not promoted to verified facts.

After Thai report writing, the final check specifically catches unsupported claims, changed numbers, or overstated certainty introduced during writing. Compare the draft with its validated findings, source references, calculations, and uncertainty/limitation statements. The same evidence-validation role can serve both checkpoints; this does not establish independent verification or require an additional model. A failed check prevents the affected unchecked claims from appearing as completed supported findings; it is not a silent pass.

When evidence is incomplete, the coordinator may ask a targeted clarification question in Thai, using prior context to avoid repetition, or produce a report that explicitly states the limitation. A limitation-bearing report may contain the supported findings that remain, must not fill gaps with invented facts, and must pass the final check for unsupported additions and overstated certainty. A validation-service failure is distinct from missing evidence and must be handled as a failed/incomplete attempt, not relabeled as an evidence limitation to bypass validation.

Model names, sizes, role assignments, and model sharing remain open for Decision 5. Durable job execution, persistence, detailed retry mechanisms, production authorization, and lifecycle governance remain pending Decisions 6–9. Retain executable component notebooks and reproducible integrated evaluation with human acceptance, including first-check failures, writer-introduced claim/number/certainty changes, targeted clarification, and limitation-bearing reports. Human report approval remains separate from successful evidence checks.

## Why

The user re-confirmed the four-role structure and explicit coordinator while distinguishing the purpose of each evidence checkpoint. Validating source findings before writing does not ensure the resulting Thai prose preserves numbers or uncertainty. The final check therefore guards changes introduced by writing. Missing evidence need not force an unanswered clarification loop when a useful report can explain its limits. Both checks remain inspectable, and neither model agreement nor a separate role proves factual correctness.

## Rejected

- Autonomous delegation and agent-selected execution order — conflicts with the retained explicit coordinator and makes execution/evidence tracing harder to reproduce.
- A single evidence check before report writing — does not catch unsupported additions, changed numbers, or stronger certainty introduced in the final prose.
- Always requiring clarification before any report when evidence is incomplete — the user permits an explicitly limited report using supported findings.
- Treating technical validation failure as a harmless evidence limitation — bypasses the requirement to check final claims.
- Assigning a fixed model to each logical role now — model assignments remain open for Decision 5.
