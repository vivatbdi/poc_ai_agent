# AI Dashboard Insight Agent — Product Requirements Document

Status: Revised with the requested phase applicability, integrated POC evaluation, and continuous-improvement clarifications; awaiting the user's manual review and final approval. Existing priorities remain unchanged. Detailed production policies, technical decisions, and quantitative acceptance targets remain open.

Source: `docs/product/urd.md`, the user’s PRD interview decisions, and the user's targeted clarification request.

## Purpose & scope

The product is a conversational analytical assistant that interprets dashboard screenshots, proactively identifies meaningful observations, and explains findings through Thai chat and detailed Markdown reports.

It supports business, government and public-sector, and general-purpose dashboards. Analysis covers individual charts and relationships across screenshots supplied within a conversation.

Users can ask follow-up questions, supply context, correct interpretations, request revised reports, and explicitly approve reports after human review.

Phase 1 (POC) evaluates analytical usefulness, factual accuracy, Thai explanation quality, performance, and resource efficiency using multiple specialized local open-source models. It includes a minimal web chat, a shared local workspace, saved conversations, manual deletion, reproducible component evaluations, and an end-to-end evaluation of the integrated multi-agent workflow.

Phase 2 (Production V1) retains the POC's functional capabilities and additionally requires configurable access and approval controls, data lifecycle controls, and collection and governance of human-reviewed analytical examples for potential knowledge reuse and model improvement. POC-only shared access and self-approval permissions do not establish production policy.

Direct database access, SQL queries, and dashboard data APIs are outside the input scope. Screenshot analysis cannot validate against an unavailable underlying dataset. Potential fraud indicators are investigative observations, not confirmed fraud.

RAG, prompt improvements, and model fine-tuning are candidate improvement mechanisms to be evaluated separately. No particular improvement mechanism is mandatory for either phase without a separate adoption decision. Kubernetes remains an orchestration preference subject to assessment, not a mandatory deliverable in either phase. Complete production infrastructure is not a POC deliverable.

## Features

All features retain their confirmed priority of **must-have**. Phase applicability specifies where each requirement must be demonstrated; priority does not make a production-specific feature a POC deliverable.

- **Phase 1 (POC) and Phase 2 (Production V1):** The 13 features marked for both phases must be demonstrated in the POC and retained in Production V1. POC evaluation uses representative business and public-sector screenshots that are public or anonymized. Production access and lifecycle policies replace the POC's shared-workspace permissions where applicable.
- **Phase 2 (Production V1) only:** Human-controlled analytical improvement, Production access and approval controls, and Production data lifecycle controls are mandatory production-specific capabilities, not POC demonstrations.

Feature dependencies apply within the stated phase and do not pull production-specific controls or optional improvement mechanisms into the POC.

### Automatic screenshot analysis

Submitting a dashboard screenshot through web chat automatically starts analysis and returns an initial Thai Markdown report in the conversation.

- **Serves:** “Submit a screenshot through chat and receive an initial analysis without a separate Analyze action.”
- **Priority:** must-have
- **Phase:** Phase 1 (POC) demonstration required; retained in Phase 2 (Production V1)
- **Depends on:** Dashboard interpretation and insight discovery; Detailed Thai insight reports

### Dashboard interpretation and insight discovery

Interpret visible values, metrics, categories, and comparisons. Proactively identify meaningful trends, anomalies, unusual patterns, and relationships within individual charts and across screenshots in the conversation. Findings must be supported by the supplied evidence.

- **Serves:** “Understand visible values, metrics, categories, comparisons, trends, anomalies, unusual patterns, and relationships within and across dashboard pages.”
- **Priority:** must-have
- **Phase:** Phase 1 (POC) demonstration required; retained in Phase 2 (Production V1)
- **Depends on:** Evidence attribution; Uncertainty and evidence limitations

### Potential fraud indicator reporting

Proactively flag suspicious patterns supported by the supplied evidence. Explain why each pattern warrants investigation and state uncertainty and evidence limitations. Distinguish potential indicators from confirmed fraud.

- **Serves:** “Receive proactive observations about potential fraud indicators without treating suspicious patterns as confirmed fraud.”
- **Priority:** must-have
- **Phase:** Phase 1 (POC) demonstration required; retained in Phase 2 (Production V1)
- **Depends on:** Dashboard interpretation and insight discovery

### Detailed Thai insight reports

Present readable, detailed Thai Markdown reports covering individual charts, significant observations, relationships, and evidence limitations.

- **Serves:** “Read detailed Thai Markdown reports that cover individual charts, explain significant observations and relationships, and identify evidence limitations.”
- **Priority:** must-have
- **Phase:** Phase 1 (POC) demonstration required; retained in Phase 2 (Production V1)
- **Depends on:** Dashboard interpretation and insight discovery; Potential fraud indicator reporting; Evidence attribution; Uncertainty and evidence limitations

### Evidence attribution

Make clear whether a finding comes from screenshot evidence, explicitly supplied user context, or a derived calculation or interpretation. Identify supporting inputs and explain the calculation or reasoning for derived findings.

Check reported numerical values against available visual evidence and check derived calculations within the limits of that evidence.

- **Serves:** “Distinguish information visible in screenshots, context explicitly supplied by users, and calculations or interpretations derived from those sources.”
- **Priority:** must-have
- **Phase:** Phase 1 (POC) demonstration required; retained in Phase 2 (Production V1)
- **Depends on:** none

### Uncertainty and evidence limitations

Label estimates and assumptions, identify information that cannot be read reliably, and explain which interpretations require additional evidence. Unsupported values, business facts, and causal conclusions must not appear as verified findings.

- **Serves:** “Recognize estimates, assumptions, unreadable information, and interpretations requiring further evidence.”
- **Priority:** must-have
- **Phase:** Phase 1 (POC) demonstration required; retained in Phase 2 (Production V1)
- **Depends on:** none

### Context-aware follow-up conversation

Allow analysts to investigate findings through Thai chat by adding questions, metric definitions, business context, corrections, and screenshots. Responses use relevant evidence and context already supplied in the same conversation.

- **Serves:** “Ask follow-up questions, request deeper analysis, supply metric definitions and business context, correct interpretations, and add screenshots within the same conversation.”
- **Priority:** must-have
- **Phase:** Phase 1 (POC) demonstration required; retained in Phase 2 (Production V1)
- **Depends on:** Automatic screenshot analysis; Evidence attribution; Uncertainty and evidence limitations

### Targeted clarification

Ask relevant questions in Thai when missing information prevents a supported interpretation. Use existing conversation context to avoid repeated or unnecessary questions. Explain remaining limitations when clarification is unavailable.

- **Serves:** “Receive relevant clarification questions when evidence is insufficient, without unnecessary questions when enough information is available.”
- **Priority:** must-have
- **Phase:** Phase 1 (POC) demonstration required; retained in Phase 2 (Production V1)
- **Depends on:** Context-aware follow-up conversation

### Evidence-based report revision

Update reports using added screenshots, context, clarification answers, and corrections. Correct findings affected by new evidence, preserve findings that remain valid, and explain material changes while maintaining conversation consistency.

- **Serves:** “Obtain revised reports that incorporate new evidence, correct earlier interpretations, preserve valid findings, and remain consistent with conversation context.”
- **Priority:** must-have
- **Phase:** Phase 1 (POC) demonstration required; retained in Phase 2 (Production V1)
- **Depends on:** Detailed Thai insight reports; Context-aware follow-up conversation; Targeted clarification

### Human report review and approval

Allow reviewers to provide corrections, request revisions, and explicitly approve reports. Treat a report as finalized or approved for publication only after human approval.

POC analysts may approve their own reports. Production approval responsibilities must be configurable.

- **Serves:** “Decide when an explanation meets their needs and explicitly approve a report after human review.”
- **Priority:** must-have
- **Phase:** Phase 1 (POC) demonstration required; retained in Phase 2 (Production V1)
- **Depends on:** Detailed Thai insight reports; Evidence-based report revision

### Saved conversation resumption

Allow analysts to reopen saved conversations after closing or restarting the application. Restore screenshots, messages, clarification exchanges, report revisions, and approval status so investigation can continue using the saved context.

- **Serves:** “Reopen saved POC conversations after closing or restarting the application, including screenshots, messages, clarification exchanges, report revisions, and approval status.”
- **Priority:** must-have
- **Phase:** Phase 1 (POC) demonstration required; retained in Phase 2 (Production V1)
- **Depends on:** none

### Manual retention and deletion

Retain saved screenshots and conversation histories until a user explicitly deletes them. Provide controls for manual deletion.

Production consequences for information already reused in knowledge or training are governed by Production data lifecycle controls.

- **Serves:** “Retain saved screenshots and conversation histories until manually deleted, and be able to delete them manually.”
- **Priority:** must-have
- **Phase:** Phase 1 (POC) demonstration required; retained in Phase 2 (Production V1)
- **Depends on:** Saved conversation resumption

### Human-controlled analytical improvement

Production V1 must support collecting and governing human-reviewed analytical examples, including relevant reports, corrections, supplied context, and approved interpretations, for potential knowledge reuse and model improvement. Preserve the relationship between an example, its source evidence, human review, and permitted uses under the production access and lifecycle policies.

Report approval does not automatically authorize knowledge reuse or make a report training data. Inclusion in a knowledge resource or training dataset requires a separate human authorization decision under the agreed reuse policies; collection alone does not grant that authorization.

RAG, prompt improvements, and model fine-tuning remain candidate mechanisms evaluated separately for suitability, analytical quality, evidence-boundary compliance, and resource effects before adoption. Production V1's mandatory collection and governance capability does not require deploying any of those mechanisms.

Reused knowledge must respect each new report's evidence boundaries. Changes to knowledge, prompts, or models require evaluation and explicit human approval before deployment; collecting examples must not automatically change deployed analytical behavior.

- **Serves:** “For production, reviewers and authorized users need their reviewed reports, corrections, context, and approved interpretations to support more consistent and accurate future analysis. Approved knowledge reuse must respect the evidence boundaries of each new report. Knowledge and model improvements must remain under human oversight and be evaluated before deployment.”
- **Priority:** must-have
- **Phase:** Phase 2 (Production V1) only; not required for the POC
- **Depends on:** Human report review and approval; Production access and approval controls; Production data lifecycle controls; Inspectable component evaluation

> **Open:** Which reviewed feedback and approved knowledge may be reused, who may authorize each use, and whether RAG, prompt improvements, or model fine-tuning should be adopted after separate evaluation.

### Production access and approval controls

For production, restrict access to conversations, screenshots, reports, and review actions according to authorized roles. Allow report approval responsibilities to be configured and protect user data.

The shared POC workspace and POC self-approval permission do not establish production policy.

- **Serves:** “Production users need multiple user roles, configurable report approval responsibilities, appropriate access control, and protection of their data. The shared POC workspace and POC self-approval permission do not determine production access or approval policy. The specific authorization model and approval workflow remain open for PRD and system design.”
- **Priority:** must-have
- **Phase:** Phase 2 (Production V1) only; not required for the POC
- **Depends on:** none

> **Open:** Specific roles, authorization rules, conversation visibility, approval workflow, self-approval permissions, authentication requirements, and sensitive-data handling policies.

### Production data lifecycle controls

For production, apply defined retention, reuse, and deletion policies to screenshots, conversations, approved reports, reused knowledge, and potential training datasets. Address the consequences of user-requested deletion for information already reused in analytical improvement.

- **Serves:** “Production users need a defined data lifecycle policy covering screenshots, conversations, approved reports, RAG knowledge, and potential training datasets. The policy must address how user-requested deletion affects information already reused for continuous improvement. Its detailed rules and implementation remain open for PRD and system design.”
- **Priority:** must-have
- **Phase:** Phase 2 (Production V1) only; not required for the POC
- **Depends on:** Manual retention and deletion; Production access and approval controls

> **Open:** Detailed retention periods, reuse permissions, and deletion obligations for source material, derived knowledge, training datasets, and trained models.

### Inspectable component evaluation

Make intermediate results available for developer and evaluator review. Every developed POC component requires an executable Jupyter Notebook documenting its purpose, inputs and outputs, methodology, data, expected results, actual outputs, metrics, limitations, and human acceptance assessment.

Components require human approval before integration.

In addition to the mandatory component notebooks, Phase 1 requires a reproducible end-to-end evaluation of the integrated multi-agent workflow. Using representative public or anonymized business and public-sector screenshots and human-validated reference interpretations, the evaluation must cover:

- Screenshot submission through chat and automatic analysis initiation, including related screenshots from multiple dashboard pages.
- Thai Markdown report generation and display, covering individual charts and significant observations and relationships.
- Evidence validation throughout the workflow: visible values, derived calculations, attribution of sources, uncertainty, exclusion of unsupported findings, and prevention of potential fraud indicators being presented as confirmed fraud.
- Thai follow-up conversation, relevant clarification questions, and use of supplied context and corrections.
- Report refinement after new evidence or corrections, preserving valid findings and conversation consistency.
- Relevant failure handling, including unreadable or insufficient evidence and failed analysis or revision: Thai explanations, preservation of saved evidence and reports, retry, and prevention of incomplete work being treated as completed or approved.
- Human report review and explicit approval, conversation reopening after restart, and manual deletion, to demonstrate the remaining POC capabilities in an integrated setting.

Record evaluation inputs, expected behavior, actual outputs, metrics, observed failures, limitations, and the human acceptance decision. Apply the agreed baseline comparisons and subsequent quantitative targets to the integrated workflow as well as relevant components. Successful component tests alone do not establish POC acceptance: the integrated results require human review and explicit approval. Preserve these evaluation obligations when assessing Production V1 changes, including proposed analytical improvements; the executable notebook requirement remains mandatory for every developed POC component.

- **Serves:** “Developers and evaluators must be able to inspect intermediate results, reproduce component evaluations, and review actual outputs and limitations before human acceptance and integration.”
- **Priority:** must-have
- **Phase:** Phase 1 (POC) demonstration required; retained in Phase 2 (Production V1)
- **Depends on:** none

## Non-functional requirements

The following five requirements retain their confirmed intent. Performance, analytical quality, local operation and resource efficiency, and reliability and recovery must be demonstrated in Phase 1 (POC) and retained in Phase 2 (Production V1); production security and privacy is mandatory for Production V1. POC baseline and integrated evaluation obligations remain as specified below and in Inspectable component evaluation. Confirmation of a measurement process does not establish numerical acceptance thresholds.

### Performance

Measure initial-report and follow-up response times on representative business and public-sector screenshots using the available POC hardware. Establish a baseline first, then agree numerical acceptance targets before accepting POC performance.

> **Open:** Numerical response-time targets and the hardware and workload conditions under which they apply.

### Analytical quality

Analysts and developers jointly validate reference interpretations for representative business and public-sector screenshots.

Compare manual and AI-assisted analysis for factual accuracy, meaningful insights discovered, analysis time, and Thai explanation quality. Evaluate numerical correctness, evidence attribution, uncertainty handling, and potential fraud wording.

Agree quantitative acceptance thresholds after measuring the baseline.

> **Open:** Quantitative thresholds, scoring definitions, and evaluation dataset coverage.

### Local operation and resource efficiency

Evaluate the complete POC analysis workflow using multiple small, specialized, locally deployed open-source models without requiring external cloud AI services.

Measure memory use, processing time, and resource contention alongside analytical quality.

> **Open:** Available hardware, model assignments, and numerical resource limits, to be resolved through technical evaluation.

### Reliability and recovery

Saved conversation content and approval status must survive application restarts.

If analysis or report revision fails, preserve previously saved evidence and reports, explain the failure in Thai, and allow retry. Failed or incomplete work must not appear as a completed or approved report.

### Production security and privacy

Enforce authorized access to production screenshots, conversations, reports, and improvement data. Protect stored data and data in transit. Apply access restrictions to reused knowledge and evaluation or training datasets.

Production acceptance requires agreed access, sensitive-data handling, retention, and deletion policies. The shared POC workspace does not establish those policies.

> **Open:** Detailed production security, privacy, secure-storage, and data lifecycle policies and their acceptance conditions.

## Deferred & rejected

No user needs or proposed features were deferred or rejected during this interview.

The following boundaries were inherited from the URD:

- Separate POC user accounts are deferred because the POC uses a shared local evaluation workspace. Production access requirements remain mandatory.
- RAG, fine-tuning, full Kubernetes deployment, and complete production infrastructure are not mandatory POC deliverables. Their adoption requires later evaluation.

The targeted clarification further confirms that RAG, prompt improvements, model fine-tuning, and Kubernetes are not automatically mandatory Production V1 deliverables. Mandatory collection and governance of reviewed examples is distinct from selecting and deploying an improvement mechanism.

## Constraints & assumptions

- All application-facing communication must be in Thai, including reports, chat, clarification questions, notifications, errors, and review interactions. Necessary technical terms, proper nouns, KPI names, and units may retain their original language.
- Internal code and development documentation are not required to be in Thai.
- Factual evidence is limited to submitted screenshots, explicitly supplied user information, and calculations or interpretations derived from those inputs.
- External information must not become factual evidence without explicit user authorization.
- Screenshot-only analysis cannot verify findings against an unavailable underlying dataset.
- The intended direction uses multiple small, specialized local open-source models. Agent responsibilities must be inspectable and independently testable; their final composition and model assignments remain undecided.
- Containerized microservices, Docker, and production Kubernetes are recorded architectural preferences subject to assessment. Logical agents and deployed services are distinct; one container per agent is not required.
- The POC uses a minimal web chat and shared local evaluation history. Evaluation screenshots must be public or anonymized.
- Saved screenshots and conversation histories remain until manually deleted. Production reuse and deletion consequences require additional policy.
- Every developed POC component requires a reproducible executable evaluation notebook with actual results and human acceptance assessment.
- The integrated POC multi-agent workflow also requires reproducible end-to-end evaluation with actual outputs, limitations, and explicit human acceptance; component notebook results alone are insufficient.
- Production V1 must collect and govern human-reviewed analytical examples, but report approval is not automatic authorization for knowledge reuse or training. Candidate mechanisms and changes to deployed knowledge, prompts, or models require separate evaluation and human approval.
- Humans control scope, architecture, component acceptance, and development progression. Proposed component behavior and implementation require review; evaluated components require human approval before integration.
- Evaluation data and reference interpretations are jointly provided and validated by analysts and developers.
- Production design must be informed by POC findings.
- This PRD does not select a technology stack, storage mechanism, model, service boundary, communication protocol, or deployment configuration.
- Feature priority does not establish implementation order or authorize implementation.

## Open questions

- What GPU, VRAM, system RAM, operating system, and other hardware resources will be available?
- Which local open-source models, sizes, and assignments should technical evaluation assess?
- What agent composition, service boundaries, communication mechanisms, and deployment configurations should follow from POC findings?
- What numerical performance targets and workload conditions should follow from the baseline?
- What evaluation scoring definitions, dataset coverage, and quantitative thresholds should apply to insight discovery, factual accuracy, analysis-time savings, Thai quality, and resource efficiency?
- What development deadline and time budget apply?
- Which production roles, authentication requirements, authorization rules, conversation visibility rules, approval responsibilities, and self-approval permissions are required?
- What production data-protection, secure-storage, and sensitive-data handling policies and acceptance conditions are required?
- What retention periods and reuse permissions apply to screenshots, conversations, approved reports, reused knowledge, and potential training datasets?
- How must user-requested deletion affect derived knowledge, training datasets, and trained models?
- Which reviewed feedback and approved knowledge may support production improvement?
- Should RAG or potential model fine-tuning be adopted, and what evidence and approval are required before deployment?
- Which collected examples may be authorized for knowledge reuse or training, who may authorize each use, and how is that authorization distinguished from report approval under the production governance policy?
- Should prompt improvements be adopted as an improvement mechanism, and what evaluation evidence and human approval are required before deployment?

Coverage check: All 16 URD user needs are represented in feature `Serves:` entries. None remain uncovered.
