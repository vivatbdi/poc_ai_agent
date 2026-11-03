# AI Dashboard Insight Agent — Project Requirements Summary

## 1. Project Objective

Develop a **conversational, multi-agent AI system** that acts as a business analyst, analyzing dashboard screenshots and generating detailed explanations and insights in **Thai language using Markdown format**.

The system should be capable of:

- Understanding information presented in dashboard screenshots.
- Describing charts, KPIs, and visualizations.
- Identifying trends, anomalies, comparisons, and relationships between charts.
- Generating structured Thai Markdown insight reports.
- Interacting with users entirely in Thai through a chat-based web application.
- Asking clarifying questions when additional information is required.
- Refining reports based on user explanations or additional screenshots.
- Improving its analytical effectiveness through human-reviewed feedback and knowledge reuse.

The project will follow a **two-phase development approach**:

1. **Phase 1 — Proof of Concept (POC):** Validate technical feasibility, analytical quality, efficiency, and multi-agent workflow design.
2. **Phase 2 — Production:** Develop a stable, reliable, maintainable system using containerized microservices, incorporating conversational capabilities and continuous improvement.

Throughout both phases, humans must retain full control over the development process, including when Codex is used as an AI developer.

---

## 2. Dashboard Scope

The AI agent should support multiple dashboard domains, including:

- Business KPI and performance dashboards
- Government and public-sector statistical dashboards
- General-purpose dashboards across different domains

The system should not be restricted to a specific business domain.

Its analytical approach should be sufficiently flexible to interpret different dashboard structures and visualization types.

## 3. Input Requirements

### 3.1 Primary Input

The primary input consists of **dashboard screenshots or images**.

- No direct access to underlying databases is required.
- No SQL queries or dashboard data APIs are required.
- The agent must interpret information visible in the screenshot.

### 3.2 Additional Conversational Input

During a conversation, users may provide:

- Additional explanations about the dashboard.
- Business context or definitions of metrics.
- Clarifications regarding chart elements.
- Additional dashboard screenshots.
- Questions requesting deeper analysis.
- Corrections to previous interpretations.

The AI agent must combine newly supplied information with previously provided screenshots to refine its analysis.

### 3.3 Input Boundaries

The system must distinguish between:

1. Information directly visible in screenshots.
2. Additional information explicitly supplied by the user.
3. Interpretations or calculations derived from those inputs.

The system must not introduce external information as factual evidence for its analysis unless the user has explicitly provided it.

---

## 4. Analytical Capabilities

The AI agent should support the following capabilities:

1. **Basic Description:** Explain visible values, metrics, categories, and comparisons.
2. **Trend Analysis:** Identify increases, decreases, changes, and patterns over time.
3. **Anomaly Detection:** Identify unusual spikes, drops, and abnormal patterns.
4. **Cross-Chart Analysis:** Identify meaningful relationships between multiple KPIs and visualizations.
5. **Contextual Analysis:** Use additional information provided by users to improve interpretation.
6. **Iterative Analysis:** Revise previous explanations when new evidence or corrections become available.

The AI agent must distinguish between observations supported by evidence and interpretations that cannot be verified from the available information.

## 5. Output and Language Requirements

### 5.1 Thai-Only User Communication

**All communication between the AI system and its users must be in Thai.**

This requirement applies to:

- Initial dashboard insight reports.
- Chat responses and follow-up explanations.
- Agent-generated clarification questions.
- Requests for additional information.
- Report revisions and summaries.
- User-facing notifications, warnings, and error messages.
- Human review and approval interactions presented in the application.

The AI agent must understand user inputs in Thai and generate appropriate responses in Thai.

Technical terms, proper nouns, KPI names, and units may retain their original language where necessary for accuracy.

This restriction applies to the **user-facing application**, not necessarily to internal code, system prompts, API messages, logs, or development documentation.

### 5.2 Initial Insight Report

After receiving a dashboard screenshot, the system should produce a detailed Thai Markdown report that:

- Covers individual charts within the screenshot.
- Describes important values and observations.
- Explains trends, anomalies, and comparisons.
- Highlights meaningful relationships between charts.
- Identifies limitations or missing information.
- Uses clear, understandable Thai.

### 5.3 Refined Insight Report

When users request additional analysis or provide new information, the AI agent should update the report.

The revised report must:

- Incorporate relevant new information.
- Preserve previously validated findings where appropriate.
- Correct earlier interpretations when new evidence requires it.
- Distinguish screenshot-derived findings from user-provided context.
- Maintain consistency with the conversation and available evidence.

### 5.4 Output Restrictions

The generated explanation must not include unsupported facts, fabricated values, or conclusions unrelated to the dashboard and user-supplied information.

When sufficient information is unavailable, the agent should acknowledge the limitation or ask the user for clarification.

---

## 6. Conversational Execution and Interaction

The system must support a **chat-based, on-demand analytical workflow**.

### 6.1 Initial Trigger

The AI agent is triggered when a user sends a dashboard screenshot through the chat interface.

The expected process is:

1. User uploads a dashboard screenshot.
2. AI agents analyze the visual information.
3. The system generates a detailed Thai Markdown insight report.
4. The report is displayed in the chat interface.
5. The conversation remains active for further questions and refinement.

A separate Analyze button is not required.

### 6.2 Follow-Up Conversation

After receiving the report, users should be able to:

- Ask questions about individual charts.
- Request deeper explanations.
- Ask for comparisons between different metrics.
- Provide additional business context.
- Correct previous assumptions or interpretations.
- Upload additional screenshots.
- Request an updated or expanded report.

The AI agent should maintain relevant conversational context throughout the session.

### 6.3 Clarification Questions

When the available information is insufficient, the AI agent should proactively generate relevant questions in Thai.

For example, it may ask for:

- The definition of an unfamiliar KPI.
- A chart's measurement units.
- Context required to interpret an unusual trend.
- An additional screenshot containing missing information.

The AI agent must use the user's answers together with available screenshots to improve its analysis.

It should avoid unnecessary questions when sufficient information is already available.

### 6.4 Conversation Completion

The analytical conversation should continue until the user is satisfied with the explanation.

The user remains in control of when the report is considered complete.

---

## 7. Multi-Agent Architecture Requirements

The system should use a **network of specialized AI agents**, rather than relying on one local model for the entire process.

### 7.1 Multi-Agent Design Principles

- Each agent should have a clearly defined responsibility.
- Agents should specialize in tasks for which their models or tools perform effectively.
- Different agents may use different local AI models.
- Model selection should be based on task suitability, accuracy, and efficiency.
- Agents should communicate through well-defined interfaces.
- Intermediate results should be inspectable and testable.
- Individual agents should be replaceable or improvable without redesigning the entire system.

### 7.2 Conceptual Agent Responsibilities

Potential specialized responsibilities include:

1. **Visual Understanding:** Interpret dashboard screenshots.
2. **Information Extraction:** Extract chart labels, KPI values, categories, and visible information.
3. **Insight Analysis:** Identify trends, anomalies, comparisons, and relationships.
4. **Evidence Validation:** Check numerical values, calculations, and supporting evidence.
5. **Report Generation:** Produce structured Thai Markdown explanations.
6. **Conversation Management:** Interpret follow-up questions and maintain context.
7. **Clarification Management:** Determine when additional information is required.
8. **Report Refinement:** Update explanations using new information.
9. **Knowledge Retrieval:** Retrieve relevant approved knowledge subject to guardrails.

These responsibilities are conceptual and do not establish the final number of agents.

### 7.3 Model Independence

The architecture must not depend on a single AI model.

Different specialized agents may use vision-language models, text-based language models, OCR models, analytical models, or deterministic Python tools.

The final model assignments will be determined during technical design.

---

## 8. Microservices and Containerized System Architecture

The system should preferably follow a **containerized microservices architecture**.

### 8.1 Architectural Principles

- The overall system should consist of multiple independently deployable services.
- Each service should have a clearly defined responsibility.
- Services should run in isolated Docker containers.
- Communication between services should use well-defined APIs or suitable service-to-service communication mechanisms.
- Components should be independently testable, maintainable, and replaceable.
- The architecture should support future scaling and production deployment.
- Internal implementation details should remain isolated behind service interfaces.

### 8.2 Service Communication

The system should support reliable communication between components using appropriate interfaces.

Potential mechanisms include:

- REST APIs
- Other suitable API-based communication methods
- Asynchronous messaging when required by workload characteristics

The selection of communication protocols will be determined during technical design.

### 8.3 Container Orchestration

**Kubernetes is a preferred option for production orchestration**, subject to technical and operational assessment.

The system should consider:

- Container deployment and service discovery.
- Configuration and secret management.
- Service health checks.
- Restart and recovery mechanisms.
- Resource allocation and scaling.
- Monitoring and logging.
- Service communication and network security.

Simpler approaches, such as Docker Compose, may be used during the POC when appropriate.

### 8.4 Multi-Agent and Microservice Relationship

The system must distinguish between:

- **AI agents:** Specialized logical components responsible for analytical tasks.
- **Microservices:** Independently deployed software components responsible for providing system capabilities.

The architecture may implement one or more agents within a microservice, depending on technical requirements.

The system should not require a separate Docker container for every logical agent unless this provides a meaningful technical or operational benefit.

### 8.5 POC and Production Alignment

Although the POC should remain lightweight, its component interfaces and boundaries should be designed with future microservice deployment in mind.

This should reduce unnecessary rework when transitioning from the POC to production.

The final deployment architecture remains subject to POC findings.

---

## 9. AI Model Requirements

The system should primarily use **locally deployed open-source AI models**.

Potential model families include:

- Qwen
- Llama
- Other suitable open-source models

The system should support multiple models operating as specialized components within the agent network.

Model selection should consider:

- Task-specific capability
- Dashboard understanding accuracy
- Thai-language explanation quality
- Reasoning ability
- Response latency
- Hardware requirements
- Resource efficiency
- Compatibility with the multi-agent workflow

Specific models, sizes, and deployment configurations remain undecided.

---

## 10. Accuracy, Evidence, and Guardrails

### 10.1 Evidence-Based Analysis

The system must prioritize factual accuracy and reliable interpretation.

Required capabilities include:

- Validating reported numerical values against available visual evidence.
- Checking calculations derived from extracted values.
- Providing supporting evidence for analytical findings.
- Distinguishing verified observations from estimates and assumptions.
- Identifying missing or insufficient evidence.

**Important constraint:** Screenshot-only input does not provide access to the original underlying dataset. Therefore, validation must operate within the limitations of available visual information.

### 10.2 Analytical Guardrails

The system must restrict analytical content to authorized evidence.

Permitted information sources are:

1. Dashboard screenshots provided by the user.
2. Additional explanations and information explicitly provided by the user.
3. Calculations and interpretations directly derived from those sources.

The AI agent must not:

- Invent numerical values or dashboard metrics.
- Introduce unsupported business facts.
- Present assumptions as verified findings.
- Generate unrelated analytical context.
- Claim causal relationships without sufficient evidence.
- Use external information to establish report findings without user authorization.

### 10.3 Conversational Guardrails

The same restrictions must apply during follow-up conversations.

If a user asks a question that cannot be answered from available evidence, the agent should:

1. Identify missing information.
2. Explain the limitation in Thai.
3. Ask the user for relevant additional information.
4. Refine the analysis after receiving evidence.

### 10.4 Human Review

Human review and approval must be supported before reports are treated as finalized or approved for publication.

The system should allow reviewers to correct findings, provide explanations, and request revisions.

---

## 11. Continuous Improvement, RAG, and Feedback Learning

The system should improve its dashboard interpretation capabilities over time through human-reviewed reports and feedback.

This capability may not be implemented during the POC but must be considered in the overall development plan.

### 11.1 Human Feedback Collection

Every report submitted for human review should be eligible for inclusion in the feedback process.

Relevant information may include:

- Original dashboard screenshots.
- Initial AI-generated reports.
- Human comments and corrections.
- Additional context supplied during review.
- Revised reports.
- Final approved interpretations.

### 11.2 Feedback Loop

The intended improvement process is:

1. AI agents analyze a screenshot.
2. The system generates an initial report.
3. A human reviews the report.
4. The reviewer approves, corrects, or provides additional information.
5. Relevant feedback and approved results are preserved.
6. Approved information is prepared for knowledge reuse or model improvement.
7. Improvements are evaluated before use in future analyses.

### 11.3 Retrieval-Augmented Generation (RAG)

The production design should consider using RAG to retrieve relevant previously approved analytical knowledge.

Potential knowledge sources include:

- Human-approved reports.
- Corrected interpretations.
- Definitions of dashboard metrics.
- Approved examples of chart analysis.
- Documented analytical rules and explanations.

RAG should help agents generate more consistent and accurate interpretations without necessarily changing model weights.

**Guardrail requirement:** Retrieved knowledge must not introduce unsupported factual claims into a new report. It may guide analytical methods or provide reusable context only when authorized and relevant.

### 11.4 Model Training and Fine-Tuning

The development plan should consider using human-reviewed examples to improve local models through training or fine-tuning.

Potential objectives include:

- Improving chart interpretation accuracy.
- Reducing repeated analytical errors.
- Improving numerical reasoning.
- Improving Thai explanations.
- Adapting models to recurring dashboard formats.

RAG and fine-tuning should be evaluated as complementary but distinct mechanisms.

Training must use appropriately reviewed and approved data.

### 11.5 Improvement Controls

Knowledge updates and model changes must remain under human oversight.

Any updated model or knowledge repository should be evaluated before deployment.

---

## 12. Final Application Requirements

The intended production deliverable is a **Thai-language chat-based web application**.

### 12.1 Application Concept

The AI agent should behave like a business analyst who helps users understand dashboards.

The main interaction follows:

**User uploads screenshot → AI generates Thai Markdown report → User asks questions or provides context → AI refines the report → Conversation continues until the user is satisfied.**

### 12.2 Required User Capabilities

The web application should support:

- Dashboard screenshot upload.
- Multiple screenshot uploads within one conversation.
- Thai-language chat interaction.
- Thai Markdown report rendering.
- Follow-up analysis and explanations.
- Agent-generated clarification questions in Thai.
- User-provided corrections and contextual information.
- Iterative report refinement.
- Human review and approval.

### 12.3 Conversation State

The system must maintain relevant information across a conversation, including:

- Uploaded screenshots.
- Previously generated insights.
- User-provided explanations.
- Clarification questions and answers.
- Report revisions.
- Review and approval status.

The detailed conversation storage mechanism will be defined during technical design.

---

## 13. Development Phases

The project must consist of two phases.

### Phase 1 — Proof of Concept (POC)

**Objective:** Validate the technical feasibility, efficiency, and analytical quality of the proposed multi-agent system.

The POC should focus on:

- Evaluating suitable local AI models.
- Testing specialized agent responsibilities.
- Designing and testing agent communication.
- Testing screenshot extraction and interpretation.
- Testing chart analysis and numerical reasoning.
- Testing Thai Markdown report generation.
- Testing Thai-language conversational interaction.
- Evaluating analytical guardrails.
- Testing conversational refinement using additional inputs.
- Assessing accuracy, latency, and resource efficiency.
- Validating component interfaces for future microservice deployment.
- Identifying limitations and improvements required for production.

The POC should prioritize experimentation, reproducibility, transparency, and human verification.

A lightweight Docker-based implementation may be used where beneficial. Full Kubernetes deployment, RAG, fine-tuning, and complete production infrastructure are not mandatory POC deliverables.

### Phase 2 — Production

**Objective:** Convert the validated POC into a stable, secure, reliable, maintainable, and extensible application.

The Production phase should consider:

**Application and Integration**
- Thai-language chat-based web application.
- Multi-screenshot conversation handling.
- Thai Markdown rendering.
- Report revision and approval.
- Dashboard application integration where required.

**AI Agent Infrastructure**
- Multi-agent orchestration.
- Local model deployment and serving.
- Model selection and resource management.
- Agent-level error handling and recovery.
- Agent communication and observability.

**Microservices Infrastructure**
- Independently deployed Docker services.
- API-based service integration.
- Kubernetes or another suitable orchestration solution.
- Container health checks and recovery.
- Service discovery and networking.
- Configuration management.
- Monitoring and logging.
- Resource allocation and scaling.

**Quality and Security**
- Analytical guardrails.
- Numerical and evidence validation.
- User authentication and authorization.
- Data protection and secure storage.
- Testing and quality assurance.
- Operational monitoring.

**Continuous Improvement**
- Human feedback collection.
- Approved report repository.
- Retrieval-Augmented Generation.
- Model fine-tuning evaluation.
- Model and knowledge version management.
- Evaluation before deploying improvements.

The production architecture must be informed by evidence collected during the POC.

---

## 14. Human-Controlled Development and Codex Usage

The development process must follow a **human-controlled, Codex-assisted development approach**.

Codex may perform implementation, experimentation, debugging, and documentation tasks. However, humans must retain authority over development decisions.

### 14.1 Human Control Principles

- Humans control requirements, scope, and architecture.
- Codex operates within approved task boundaries.
- Tasks should be small enough to inspect and debug.
- Major technical decisions require human review and approval.
- Components must be independently testable.
- Test results must be available for human inspection.
- Changes affecting accepted components should undergo regression testing.
- Humans determine component acceptance.

### 14.2 Mandatory POC Component Testing

During Phase 1, **every developed component must have a corresponding Jupyter Notebook (`.ipynb`) for testing and verification**.

Each notebook must contain:

1. **Component Overview**
   - Component purpose
   - Expected inputs and outputs
   - Responsibilities within the agent network

2. **Test Objective and Methodology**
   - Test objectives
   - Test conditions
   - Test data
   - Expected results

3. **Executable Test Code**
   - Python code invoking the component
   - Reproducible test scenarios
   - Clearly organized execution steps

4. **Test Results**
   - Actual outputs
   - Relevant metrics
   - Comparisons with expected results
   - Observed successes and failures

5. **Markdown Explanation and Evaluation**
   - Explanation of testing procedures
   - Interpretation of results
   - Identified problems and limitations
   - Recommendations for improvement
   - Component acceptance assessment

These notebooks must combine executable tests and human-readable Markdown documentation.

### 14.3 Component Development and Approval Process

Each POC component must follow a controlled development cycle:

1. Define requirements and expected behavior.
2. Review and approve the proposed implementation.
3. Allow Codex to develop the component.
4. Execute the component's Jupyter Notebook tests.
5. Review outputs and documented results.
6. Approve the component or request revisions.
7. Integrate the approved component into the agent network.

### 14.4 Development Traceability

Development records should make it possible to understand:

- What Codex was instructed to implement.
- What code or configuration was produced.
- What tests were performed.
- What results were observed.
- What issues were identified.
- What modifications were made.
- Who approved the component and its results.

The implementation of these traceability mechanisms will be determined during development planning.

---

## 15. Current Project Definition

| Category | Requirement |
|---|---|
| Project Type | Conversational multi-agent dashboard insight system |
| Final Application | Chat-based web application |
| User-Facing Language | Thai only, with necessary technical terms retained |
| Primary Input | Dashboard screenshots |
| Additional Input | User questions, explanations, corrections, and screenshots |
| Trigger | User submits a screenshot |
| Core Functions | Visual extraction, insight analysis, evidence validation, report generation |
| Analytical Scope | Descriptions, trends, anomalies, cross-chart relationships |
| Output | Detailed Thai Markdown reports |
| Conversation | Interactive analysis and iterative report refinement |
| AI Architecture | Network of specialized task-focused agents |
| AI Models | Multiple locally deployed open-source models |
| System Architecture | Containerized microservices with API-based communication |
| Container Technology | Docker |
| Preferred Production Orchestration | Kubernetes, subject to assessment |
| Guardrails | Evidence limited to screenshots and user-provided information |
| Quality Control | Evidence validation and human review |
| Continuous Improvement | Human feedback, RAG, and potential model fine-tuning |
| Development Approach | Human-controlled, Codex-assisted |
| Development Phases | Phase 1: POC; Phase 2: Production |
| POC Testing | Mandatory `.ipynb` notebook for every component |
| Component Acceptance | Human review and approval |
| Production Priority | Stability, security, maintainability, scalability, and continuous improvement |
| Current Status | Requirements Definition — Technical Design Pending |