# AI Dashboard Insight Agent — User Requirements Document

Status: Updated user requirements incorporating the requested final clarifications; awaiting the user's final review and approval. Technical design remains pending; open items are not implied decisions.

Source: `REQUIREMENTS.md` and the user clarifications recorded during the URD interview. This document records the approved user needs and distinguishes implementation preferences from decisions still to be made.

## Purpose & scope

Users currently interpret dashboards manually by examining visualizations and navigating between dashboard pages. They may overlook meaningful insights, including important trends, anomalies, unusual patterns, potential fraud indicators, and relationships between metrics.

The system will act as an analytical assistant that proactively identifies these observations, explains their significance in Thai, and helps users investigate them through follow-up conversations. It should support business, government and public-sector, and general-purpose dashboard domains.

Dashboard visualizations contain sufficient information for the intended interpretation process. Users may provide multiple screenshots from different dashboard pages so the system can analyze relationships between visualizations. Direct access to underlying databases, SQL queries, and dashboard data APIs is outside the defined input scope.

Development comprises two phases: a POC to evaluate feasibility, analytical quality, and resource efficiency, followed by production development informed by POC findings. The initial POC evaluation covers business and public-sector dashboards using public or anonymized screenshots.

Production scope includes continuous improvement through human-reviewed reports and feedback. RAG and potential model fine-tuning remain mechanisms for later evaluation, rather than mandatory POC deliverables.

## Stakeholders & user roles

- **Analysts:** Primary POC users and evaluators. They investigate dashboard metrics, provide context, correct findings, and assess analytical usefulness.
- **Human reviewers:** Review findings, request revisions, provide corrections, and explicitly approve reports before they are treated as finalized or approved for publication. During the POC, the analyst conducting an analysis may also approve its report.
- **Development team:** Develops and evaluates components under human control, makes intermediate behavior inspectable, and supports reproducible evaluation.
- **Joint evaluation responsibility:** Analysts and the development team provide representative screenshots and validate reference interpretations used to evaluate the AI.

Production must support multiple user roles and configurable report approval responsibilities. The specific role definitions, authorization model, and approval workflow remain open for PRD and system design.

## User needs

Analysts must be able to:

- Submit a screenshot through chat and receive an initial analysis without a separate Analyze action.
- Understand visible values, metrics, categories, comparisons, trends, anomalies, unusual patterns, and relationships within and across dashboard pages.
- Receive proactive observations about potential fraud indicators without treating suspicious patterns as confirmed fraud.
- Read detailed Thai Markdown reports that cover individual charts, explain significant observations and relationships, and identify evidence limitations.
- Distinguish information visible in screenshots, context explicitly supplied by users, and calculations or interpretations derived from those sources.
- Recognize estimates, assumptions, unreadable information, and interpretations requiring further evidence.
- Ask follow-up questions, request deeper analysis, supply metric definitions and business context, correct interpretations, and add screenshots within the same conversation.
- Receive relevant clarification questions when evidence is insufficient, without unnecessary questions when enough information is available.
- Obtain revised reports that incorporate new evidence, correct earlier interpretations, preserve valid findings, and remain consistent with conversation context.
- Decide when an explanation meets their needs and explicitly approve a report after human review.
- Reopen saved POC conversations after closing or restarting the application, including screenshots, messages, clarification exchanges, report revisions, and approval status.
- Retain saved screenshots and conversation histories until manually deleted, and be able to delete them manually.

For production, reviewers and authorized users need their reviewed reports, corrections, context, and approved interpretations to support more consistent and accurate future analysis. Approved knowledge reuse must respect the evidence boundaries of each new report. Knowledge and model improvements must remain under human oversight and be evaluated before deployment.

Production users need multiple user roles, configurable report approval responsibilities, appropriate access control, and protection of their data. The shared POC workspace and POC self-approval permission do not determine production access or approval policy. The specific authorization model and approval workflow remain open for PRD and system design.

Production users need a defined data lifecycle policy covering screenshots, conversations, approved reports, RAG knowledge, and potential training datasets. The policy must address how user-requested deletion affects information already reused for continuous improvement. Its detailed rules and implementation remain open for PRD and system design.

Developers and evaluators must be able to inspect intermediate results, reproduce component evaluations, and review actual outputs and limitations before human acceptance and integration.

## Usage scenarios

1. **Initial analysis:** An analyst submits a dashboard screenshot through web chat. The system returns a detailed Thai Markdown report covering individual charts, meaningful observations, relationships, and limitations.
2. **Cross-page investigation:** The analyst supplies screenshots from several dashboard pages. The system identifies relationships supported by visible information and explains where additional evidence is needed.
3. **Clarification and refinement:** A KPI definition or unit is unclear. The system asks a relevant question in Thai. The analyst answers, adds a screenshot, or corrects an interpretation, and the system revises its explanation using the conversation's evidence.
4. **Suspicious-pattern investigation:** The system identifies a potential fraud indicator, explains the supporting observations and uncertainty, and helps the analyst investigate through follow-up conversation. It does not present suspicion as confirmed fraud.
5. **Review and resumption:** An analyst corrects or approves a report, closes the application, and later reopens the saved conversation with its evidence, revisions, and approval status. Saved screenshots and conversation histories remain until manually deleted.
6. **Production improvement:** Human-reviewed reports and corrections are preserved for approved knowledge reuse or potential model improvement. Proposed improvements are evaluated and remain under human control before deployment. Reused knowledge must not introduce unsupported facts into another report.

## Constraints & assumptions

- All application-facing communication must be in Thai, including reports, chat, questions, notifications, errors, and review interactions. Necessary technical terms, proper nouns, KPI names, and units may retain their original language. This does not require internal code or development documentation to be in Thai.
- Factual evidence is limited to submitted screenshots, information explicitly supplied by users, and calculations or interpretations derived from those inputs. External information must not become factual evidence without explicit user authorization. Screenshot-only analysis cannot validate against an unavailable underlying dataset.
- The preferred development direction is to "operate the entire AI system locally using multiple small, specialized open-source AI models rather than depending on one large model or external cloud AI services."
- The user states: "The intended multi-agent architecture should allow different agents to use different local models according to their specialized responsibilities."
- The POC must investigate the feasibility and resource efficiency of this direction while maintaining acceptable dashboard interpretation accuracy and Thai-language analytical quality. Agent responsibilities must be inspectable and independently testable; their final number and model assignments are undecided.
- Existing source preferences include a "containerized microservices architecture", "Docker", and "Kubernetes is a preferred option for production orchestration", subject to assessment. These are recorded preferences, not selected deployment configurations. Logical agents and deployed services are distinct; one container per agent is not required.
- The POC requires a minimal web chat for screenshot uploads, Thai conversation, and Markdown reports. It uses a shared local workspace with shared evaluation history. Separate user accounts are deferred; production authentication, authorization, data protection, and secure storage remain considerations for later design.
- Saved screenshots and conversation histories are retained until manually deleted. Production must additionally define a data lifecycle policy covering screenshots, conversations, approved reports, RAG knowledge, and potential training datasets, including how user-requested deletion affects information reused for continuous improvement. Detailed lifecycle rules, storage, and deletion implementation remain open; this URD does not select a technical deletion mechanism.
- POC evaluation uses public or anonymized screenshots. Production handling of internal or sensitive data requires later definition.
- Every developed POC component requires a corresponding executable Jupyter Notebook documenting its purpose, inputs and outputs, test methodology, data, expected results, actual outputs and metrics, limitations, and human acceptance assessment.
- Humans control scope, architecture, component acceptance, and development progression. Components require review of their proposed behavior, development, notebook-based evaluation, and human approval before integration. Results and changes must remain traceable.
- Production continuous improvement includes human-reviewed feedback and approved knowledge reuse. RAG should be considered for retrieving authorized, relevant approved knowledge; potential training or fine-tuning should be evaluated separately using reviewed and approved examples. Neither is selected for implementation during this URD phase.
- Full Kubernetes deployment, RAG, fine-tuning, and complete production infrastructure are not mandatory POC deliverables.

Hardware, operating system, specific models, model sizes and assignments, deployment configuration, deadline, and time budget remain open. No particular specifications or configurations are assumed or selected here.

## Acceptance criteria

- Screenshot submission initiates analysis and produces a Thai Markdown report addressing individual charts, meaningful observations, relationships, and evidence limitations.
- Users can investigate trends, anomalies, unusual patterns, potential fraud indicators, and relationships across supplied screenshots through follow-up conversation.
- Numerical findings are checked against available visual evidence and derived calculations are checked. Reports distinguish supported observations from estimates, assumptions, and interpretations requiring further evidence.
- Unsupported values, business facts, and causal conclusions are not presented as verified findings. Suspicious patterns are described as potential fraud indicators, not confirmed fraud.
- Insufficient evidence leads to an explicit limitation or a relevant clarification question in Thai.
- Revised reports incorporate new screenshots, context, and corrections while retaining valid findings and conversation consistency.
- Human review, correction, explicit approval, conversation reopening, and manual deletion are supported. POC analysts may approve their own reports.
- Analysts and developers jointly provide representative business and public-sector screenshots and validate reference interpretations.
- POC evaluation compares manual and AI-assisted analysis for meaningful insights analysts missed, factual accuracy of observations, and reduction in analysis time. A baseline is measured first; quantitative acceptance targets are then agreed using the results.
- POC evaluation also assesses technical feasibility, resource efficiency, dashboard interpretation accuracy, and Thai analytical quality for multiple small local models. Hardware and numerical targets remain open.
- Each developed POC component has a reproducible evaluation notebook containing actual outputs, observed successes and failures, limitations, and a human acceptance assessment.
- For production, human-reviewed reports, corrections, and approved interpretations can support continuous improvement under human oversight. Knowledge or model changes are evaluated before deployment, and knowledge reuse respects each report's evidence boundaries. RAG and potential fine-tuning remain subject to later evaluation.
- Production supports multiple user roles and configurable report approval responsibilities, with access control and data protection. The specific authorization model, approval workflow, and detailed acceptance conditions remain open for PRD and system design; production does not inherit the shared POC access or self-approval policy.
- A production data lifecycle policy is defined for screenshots, conversations, approved reports, RAG knowledge, and potential training datasets. It explicitly addresses how user-requested deletion affects information reused for continuous improvement. Detailed policy rules and implementation remain open.

## Open questions

- What GPU, VRAM, system RAM, operating system, and other hardware resources will be available?
- Which local open-source models, sizes, and assignments should subsequent technical evaluation assess?
- What agent composition, service boundaries, communication mechanisms, and deployment configurations should follow from POC findings?
- What quantitative thresholds for insight discovery, factual accuracy, time savings, Thai analytical quality, and resource efficiency should be agreed after baseline evaluation?
- What development deadline and time budget apply?
- What specific production roles, authorization model, and configurable report approval workflow should PRD and system design define?
- What detailed production authentication, authorization, conversation visibility, data-protection, secure-storage, and sensitive-data handling policies are required?
- What lifecycle rules should govern screenshots, conversations, approved reports, RAG knowledge, and potential training datasets, and how should user-requested deletion affect information reused for continuous improvement? Detailed policy and implementation decisions remain open.
- Which approved feedback and knowledge should support production improvement, and whether and how should RAG or potential model fine-tuning be adopted after evaluation?

These items are intentionally unresolved. Finalization of this URD does not select their answers or authorize progression to PRD, technical design, or implementation.
