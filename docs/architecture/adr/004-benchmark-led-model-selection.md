# ADR 004 — Benchmark-led model selection

- **Status:** superseded by 013
- **Affects:** Visual extraction agent, Insight analysis agent, Evidence validator, Thai report agent, Local inference service, Evaluation harness

## Decision

Select model names, sizes, assignments, and serving backends through component and integrated evaluations rather than fixing them now. Evaluate a small vision-language model and a separate small Thai-capable text model. Begin CPU measurements and evaluate Intel GPU/NPU execution only where supported by the chosen backend. Begin with one active inference request and load models together only when measured memory headroom permits.

## Why

The user accepted decision 5. Actual local-model quality, RAM fit, Thai performance, switching costs, and acceleration compatibility have not been demonstrated. Measure factual accuracy, evidence checks, Thai quality, latency, peak memory, and resource contention using human-validated references. Human component acceptance precedes integration, and integrated evaluation requires its own acceptance. Numerical thresholds follow the baseline as the PRD requires; accepted strategy is not acceptance of any model.

## Rejected

- Fix specific models and acceleration backends before benchmarks — would commit multiple agents to unverified hardware fit and analytical quality.

