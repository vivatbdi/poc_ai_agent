# ADR 013 — Reconfirmed benchmark-led model selection

- **Status:** accepted
- **Affects:** Visual extraction agent, Insight analysis agent, Evidence validator, Thai report agent, Analysis coordinator, Local inference service, Evaluation harness

## Decision

Supersede ADR 004 with the user's explicit answer to reopened interview Decision 5: **1. Accept Decision 5**.

Select local open-source models and logical-role assignments through component benchmarks with human review and acceptance, followed by reproducible end-to-end POC evaluation and explicit human acceptance. Do not fix specific models or assignments through this strategy decision. The complete workflow must demonstrate multiple specialized local models as required by the PRD; logical roles may share a model where evaluation supports that choice, but sharing does not establish independent verification.

Evaluate suitable local vision-language and Thai-capable text candidates against representative public/anonymized business and public-sector screenshots and jointly human-validated reference interpretations. Measure extraction accuracy, supported findings, numerical correctness, the pre-writing source check and post-writing claim/number/certainty check from ADR 012, Thai explanation quality, latency, peak memory, resource contention, and model-switching cost. Include clarification and limitation-bearing reports where evidence is incomplete.

Retain the executable notebook requirement for every developed POC component, human acceptance before component integration, and a separate reproducible end-to-end evaluation with actual outputs, metrics, observed failures, limitations, and explicit human acceptance. Component benchmark success alone does not establish integrated POC acceptance. Record evaluated model/prompt/contract versions and the environment so acceptance applies to the combination actually evaluated.

Start with one inference job at a time as the benchmark baseline already accepted in ADR 010. Select model sizes and operational concurrency from measured results. Keep specific models, runtime/backend, hardware acceleration, and Windows/WSL inference placement open until evaluation. The host is Windows and development uses Ubuntu in WSL; if inference runs in WSL, measure memory available there before deciding model sizes and concurrency. Shared GPU memory is not additional host RAM, and the host's reported memory is not a measured WSL budget.

Establish numerical performance and analytical acceptance thresholds after baseline measurement and human agreement, as required by the PRD. This strategy grants no model, runtime, or integration acceptance in advance. Persistence, durable execution, production authorization, and lifecycle governance remain pending Decisions 6–9.

## Why

The user explicitly re-confirmed benchmark-led selection after the original answer was withdrawn. The accepted four logical roles and two evidence checks define what must be evaluated without prescribing model count per role. Hardware fit, Thai quality, evidence handling, and combined workflow behavior must be demonstrated in the actual execution environment. A single-job baseline provides a controlled starting workload, not proof of viable capacity. Human review at component level and separate integrated acceptance prevent isolated component results from being mistaken for a successful POC.

## Rejected

- Fixing model names, role assignments, acceleration, or Windows/WSL placement before evaluation — would commit the workflow to unverified fit and analytical quality.
- Requiring one model per logical role — the user accepted logical specialization, while evaluated model sharing remains possible.
- Treating component benchmarks as sufficient POC acceptance — the integrated workflow requires reproducible end-to-end evidence and explicit human acceptance.
- Treating host RAM or a single-job baseline as measured inference capacity — available memory and workload results must determine sizing and operational concurrency.
