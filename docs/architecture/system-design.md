# AI Dashboard Insight Agent — System Design

Approval: Approved by the user on 2026-11-25 as the architecture baseline for POC planning, including the phase-specific dependency and POC evaluation-artifact deletion clarifications. This approval covers planning against this baseline; implementation and Production V1 each require separate client approval. Accepted ADRs and the current URD/PRD remain in force, and the open evaluation choices and production policies listed below remain unresolved.

Status: System-design interview complete. Hardware/environment facts remain confirmed, revised Decision 2 is accepted in ADR 009, and reopened Decisions 3–9 have each received a new explicit answer, recorded in ADRs 010 and 012–017. The private HTTP transport follow-up is accepted in ADR 011. ADRs 001–008 preserve the prior decisions and are superseded. Detailed evaluation choices and production policies remain open. Documentation only; implementation, component integration, and production release require their own human approvals.

Source: `docs/product/prd.md`, `REQUIREMENTS.md`, project instructions in `AGENT.md`, the original nine interview answers, the clarification of Questions 1 and 2, and the user's subsequent withdrawal of answers to Decisions 3–9. The original answers are historical reference, not current acceptance. Decision 2 remains **2. Accept with changes**, recorded in ADR 009 superseding ADR 001. Decision 3 was subsequently explicitly re-confirmed: one modular FastAPI application process and one separate local inference process, recorded in ADR 010. The subsequent inference communication follow-up is accepted in ADR 011: private HTTP with a minimal versioned contract for the POC.

Current authority: PRD requirements, confirmed hardware/environment facts, and accepted ADRs 009–017 govern this design. The Decisions section maps interview topics to ADR identities and preserves withdrawn answers alongside their explicit replacements. Component boundaries, ownership, paths, contracts, and fitness rules below express the completed design, not implemented code or tested behavior. Models, inference runtime/acceleration/placement, actual storage paths, numerical acceptance targets, and detailed production policies remain open as listed in Open questions. Earlier accepted ADRs are preserved verbatim except for their supersession status; their then-open questions or prior acceptance references describe the state at the time and must be read with later ADRs.

## Purpose & scope

Build a conversational analytical assistant that interprets dashboard screenshots through specialized local models and returns evidence-supported Thai Markdown reports. Support follow-up questions, clarification, corrections, report revisions, human review, saved conversations, and manual deletion.

The POC demonstrates the 13 shared features and reproducible component and integrated evaluations. Production additionally requires access controls, lifecycle policies, and governance of reviewed analytical examples. Production components below describe required boundaries, not authorization to implement production infrastructure during the POC.

The POC can start, run all 13 features, and complete its reproducible end-to-end evaluation without implementing or configuring production identity, authorization, or reviewed-example governance. Its shared local workspace includes human report review and explicit version-specific approval (including POC self-approval), saved conversations, manual deletion, component notebooks, and integrated human acceptance. Production policy controls and Reviewed-example governance remain in the Production V1 design under accepted ADRs 016–017; production work is subject to separate client approval after the POC.

No database, SQL, or dashboard API is an analytical input. No external information becomes factual evidence without explicit authorization. RAG, prompt-improvement deployment, fine-tuning, and Kubernetes are not selected. No training pipeline or retrieval service is designed here. Production deployment and storage must be reconsidered using POC evidence.


POC feature coverage below uses only components marked POC and Production V1; their POC dependency closure excludes both production-only components.

| POC feature (PRD name) | POC components serving it |
|---|---|
| Automatic screenshot analysis | Thai web chat; Analysis coordinator; Conversation and report service |
| Dashboard interpretation and insight discovery | Visual extraction agent; Insight analysis agent; Evidence validator |
| Potential fraud indicator reporting | Insight analysis agent; Evidence validator; Thai report agent |
| Detailed Thai insight reports | Thai report agent; Thai web chat |
| Evidence attribution | Visual extraction agent; Evidence validator; Conversation and report service |
| Uncertainty and evidence limitations | Evidence validator; Thai report agent; Analysis coordinator |
| Context-aware follow-up conversation | Analysis coordinator; Conversation and report service; Thai web chat |
| Targeted clarification | Analysis coordinator; Thai report agent |
| Evidence-based report revision | Analysis coordinator; Conversation and report service; Insight analysis agent; Evidence validator; Thai report agent |
| Human report review and approval | Thai web chat; Conversation and report service |
| Saved conversation resumption | Thai web chat; Conversation and report service |
| Manual retention and deletion | Thai web chat; Analysis coordinator; Conversation and report service |
| Inspectable component evaluation | Evaluation harness and the POC components it exercises |

## Context & constraints

- Repository survey: requirements and product documents only; no application source, dependency manifest, system design, ADR directory, or threat model existed. This is a greenfield design. `AGENT.md` exists; its reference to `AGENTS.md` names a file that is absent. No implementation or Git operations were performed by this design run.
- The approved PRD is the product scope for this design. Its later clarifications distinguish mandatory production governance from optional RAG/training and treat containerization and Kubernetes as preferences. The older summary in `REQUIREMENTS.md` must not silently promote those optional mechanisms to deliverables.
- Hardware supplied by the user: Intel Core Ultra 7 155U, 12 cores, 14 logical processors, 16 GB DDR5 RAM, 15.5 GB usable; integrated Intel Graphics with a displayed 8.8 GB of shared system memory; Intel AI Boost NPU. Shared GPU memory is part of system RAM. The host OS is Windows; development uses Ubuntu in WSL. Storage capacity and runtime compatibility remain unknown. The inference runtime, execution location, and hardware acceleration remain open. If inference runs in WSL, measure memory available there before deciding model sizes and concurrency; host usable RAM is not an established WSL budget.
- Accepted POC runtime (Decision 2, Accept with changes): Python, FastAPI for the browser-facing API, pytest, executable Jupyter evaluation notebooks, and minimal HTML/CSS/JavaScript served by the application. Same-process components use Python interfaces. Service-to-service APIs are decided separately with deployment boundaries; this stack choice does not mandate FastAPI or HTTP for inference serving. Dependency and runtime versions will be pinned during implementation after compatibility evaluation; none are installed by this document.
- Accepted POC deployment (re-confirmed interview Decision 3 / ADR 010): one modular FastAPI application process and one separate local inference process; internal application components use Python interfaces. Preserve saved screenshots and prior reports if inference fails, show a Thai error, and allow retry. Start with one inference job at a time as a benchmark baseline. Both processes share the laptop memory budget. Inference runtime, placement, acceleration, and container packaging remain open; private HTTP transport is accepted in ADR 011; measure WSL memory before model sizing/concurrency if inference runs there.
- The PRD requires multiple specialized local open-source models. Model names, sizes, licenses, assignments, quantization, and Intel acceleration support are not settled. Benchmark-led model and role selection with component human review followed by reproducible end-to-end POC evaluation and explicit human acceptance is accepted in ADR 013. Specific models, runtime/backend, acceleration, and Windows/WSL placement remain open until evaluation. A single inference job at a time is now the accepted benchmark baseline under re-confirmed Decision 3 / ADR 010; operational capacity remains unmeasured. Do not assume models fit in RAM together; the confirmed WSL measurement condition still applies.
- All application-facing text is Thai, including errors, questions, status, review controls, and reports. Necessary technical terms, proper nouns, KPI names, and units may retain their original language.
- Evidence consists of screenshots, explicit user context, and derivations from those inputs. Model knowledge is not new factual evidence. Fraud indicators remain investigative observations, never confirmed fraud.
- POC access is a shared local evaluation workspace using public or anonymized screenshots. Bind local services to the local host by default; broader access requires a separate deployment decision. POC access does not establish production policy.
- Saved material persists until manual deletion. Committed evidence, previous reports, and approval state survive application restart. Failed or incomplete work is not a completed or approved report.
- Every developed POC component requires an executable evaluation notebook containing actual outputs, methodology, metrics, limitations, and human acceptance. Components require approval before integration; integrated acceptance requires its own reproducible evaluation and explicit human approval.
- Performance and analytical thresholds follow baseline measurement and later human agreement. No numerical acceptance thresholds are invented here. Team capacity, deadline, and development time budget remain open.
- Paths below are proposed future boundaries, not claims that code, tests, notebooks, or commands already exist. Every component has a notebook path even where production-only applicability means the POC need not develop that component.

## Components

Phase applicability is explicit in each component's Phase and Depends on lines. A dependency labeled **POC and Production V1** is required in the POC and retained in Production V1; one labeled **Production V1 only** is excluded from POC startup, execution, review, deletion, and evaluation. A shared component with only Production V1 dependencies has no component dependency for its POC behavior. POC composition must not import or initialize unimplemented production components or require identity settings, authorization policy configuration, reviewed-example stores, or production test fixtures. Shared-workspace operations are handled by the POC components directly. When Production V1 is separately approved, its declared dependencies become mandatory and the accepted deny-unless-permitted checks apply.

Four logical roles and an explicit Python coordinator in the application are accepted under ADR 012. The role definitions below retain their paths and interfaces; model assignments remain open for evaluation under accepted ADR 013, and SQLite/local artifact persistence and its recovery/approval/deletion behavior are accepted under ADR 014; durable SQLite jobs, one background coordinator inside the application, polling, and safe restart/retry execution are accepted under ADR 015; a shared Production V1 authorization policy with service enforcement is accepted under ADR 016, and Production V1 lifecycle/reviewed-example governance is accepted under ADR 017. Decision 3 now establishes a separate local inference process under ADR 010. Application components in the same process use Python interfaces under ADR 009, and FastAPI exposes the browser-facing API. Communication to inference crosses the accepted process boundary using private HTTP and a minimal versioned contract under ADR 011. Validate the private route against the eventual Windows/WSL placement; that placement remains open. The ownership rule keeps each component behind its named contract rather than directly accessing another's records. An analysis-stage result is not an approved report.

### Thai web chat

Presents Thai conversation, screenshot submission, Markdown reports, review actions, and deletion controls.

- **Phase:** POC and Production V1
- **Serves:** Automatic screenshot analysis; Detailed Thai insight reports; Context-aware follow-up conversation; Targeted clarification; Evidence-based report revision; Human report review and approval; Saved conversation resumption; Manual retention and deletion
- **Depends on:** Analysis coordinator (POC and Production V1), Conversation and report service (POC and Production V1), Production policy controls (Production V1 only), Reviewed-example governance (Production V1 only)
- **Paths:** app/web/, tests/web/, notebooks/web.ipynb
- **Tests:** pytest tests/web/
- **Interface:** Browser HTTP API v1 for commands and polling; served HTML/CSS/JavaScript; user actions carry the current resource/report version.
- **Owns:** none

In the POC, the web chat exposes shared local history, human correction/review, explicit report-version approval with self-approval allowed, and manual deletion. These use the coordinator and conversation/report interfaces without production identity, policy, or reviewed-example screens/services. Production role-scoped controls and governed-example interactions use the Production V1 only dependencies.

Render Markdown with unsafe HTML disabled and sanitize links; treat screenshots, Markdown, and user content as data, never executable instructions. Unit tests isolate dependencies; real browser behavior belongs to integrated evaluation. Failed requests show Thai errors while preserving the user's current context; retry mutations with their original operation identity.

### Conversation and report service

Maintains durable conversation evidence, messages, immutable report versions, reviews, and approval state.

- **Phase:** POC and Production V1
- **Serves:** Automatic screenshot analysis; Evidence attribution; Context-aware follow-up conversation; Evidence-based report revision; Human report review and approval; Saved conversation resumption; Manual retention and deletion
- **Depends on:** Production policy controls (Production V1 only)
- **Paths:** app/conversations/, tests/conversations/, notebooks/conversations.ipynb
- **Tests:** pytest tests/conversations/
- **Interface:** Versioned Python contract for conversation snapshots, evidence access, versioned report publication, reviews, approval, deletion tombstones, managed source-linked artifact registration/read, and source-artifact cleanup by source/conversation ID; HTTP exposure is through the application boundary.
- **Owns:** Conversation, Message, Source evidence, Report version, Review action, Source deletion journal, Managed source-linked artifact

In the POC, shared local users can reopen conversations, review reports, explicitly approve a completed version (including their own), and manually delete data without calling Production policy controls. The service still enforces report/version validity, durable writes, and deletion state; these checks do not require a production authorization implementation. Production V1 adds the shared authorization policy before protected operations.

Use SQLite for owned structured records and a managed local artifact directory for screenshots. Validate image type, size, decoded dimensions, and path confinement. Save evidence before scheduling analysis. A publication succeeds only against the expected live conversation and input revision; approval names an exact completed report version. Revisions do not inherit approval. Committing a validated draft does not itself approve it. Write failures never replace previously committed reports. Deletion first makes the source inaccessible, then resumes tracked cleanup after interruption.

The Conversation and report service also owns managed evaluation copies containing conversation content, including persisted notebook output cells, rendered exports, or a saved notebook containing such cells. The harness registers these through the existing service boundary with every contributing source/conversation ID and the exact source/output/report version references; it receives an artifact ID rather than owning a separate unmanaged file. Registration/read checks reject tombstoned sources, and persistence uses the same managed file publication and source deletion journal as other source-linked artifacts. Source-artifact cleanup enumerates and removes these copies by source/conversation ID, including any managed notebook container and its content-bearing exports. This adds no dependency on the Evaluation harness.

### Analysis coordinator

Controls durable analysis, context selection, clarification, revisions, retries, and deletion execution.

- **Phase:** POC and Production V1
- **Serves:** Automatic screenshot analysis; Context-aware follow-up conversation; Targeted clarification; Evidence-based report revision; Manual retention and deletion; Production data lifecycle controls; Inspectable component evaluation
- **Depends on:** Conversation and report service (POC and Production V1), Visual extraction agent (POC and Production V1), Insight analysis agent (POC and Production V1), Evidence validator (POC and Production V1), Thai report agent (POC and Production V1), Production policy controls (Production V1 only), Reviewed-example governance (Production V1 only)
- **Paths:** app/coordinator/, tests/coordinator/, notebooks/coordinator.ipynb
- **Tests:** pytest tests/coordinator/
- **Interface:** Versioned Python submission, job-status, retry, and deletion contracts; the browser receives a durable job reference and polls via HTTP API v1.
- **Owns:** Analysis job, Workflow stage record, Lifecycle execution

One bounded worker inside the application process executes persisted jobs. Commit submission and an enqueue intent atomically in the conversation service; reconcile that intent idempotently into a coordinator-owned job before acknowledging job availability. Conversation service never writes job records. This avoids a crash gap between durable evidence and scheduling without sharing entity ownership.

Run visual extraction, insight analysis, the first evidence check, Thai report writing, and the final check under accepted ADR 012. The first check validates findings and calculations against their screenshot/user-context sources. The final check catches unsupported claims, changed numbers, or overstated certainty introduced during Thai report writing. If evidence is incomplete, ask a targeted Thai clarification question using existing context or produce a report explicitly stating the limitation while retaining supported findings. Do not invent missing facts, and do not bypass a failed validation service by labeling it an evidence limitation. Revisions select an explicit evidence/context snapshot, retain valid findings, and explain material changes. Preserve inspectable stage outputs without hidden model chain-of-thought; SQLite/local artifact persistence is accepted under ADR 014; durable SQLite jobs, one background coordinator inside the application, browser polling, and restart/retry safeguards are accepted under ADR 015, with concrete transition and stop/status mechanisms left to detailed design. Model assignments remain open for evaluation under the accepted Decision 5 strategy in ADR 013.

Record bounded attempts and timeouts; no unbounded automatic agent loop. On restart mark abandoned running work interrupted and allow explicit retry. Retry identity prevents duplicate publication. In both phases, check the live source revision, tombstone, job/attempt identity, and report state before accepting results or publication. Production V1 additionally rechecks resource authorization before protected execution, result return, and publication. POC execution and manual deletion do not call production policy or reviewed-example governance; cleanup covers the POC-owned evidence, reports, and artifacts. Production V1 additionally propagates deletion/revocation to governed examples and downstream uses under the agreed policy. Deletion cancels work, blocks stale publications, and calls the applicable owner's cleanup interface; delayed inference results are discarded. If cleanup cannot complete, lifecycle execution remains visibly pending. Jobs needing production permissions are reauthorized at execution time.

For POC evaluation copies, the coordinator calls the Conversation and report service's existing source-artifact cleanup interface by source/conversation ID. That owner removes its managed source-linked evaluation/notebook artifacts and reports pending or complete cleanup; the coordinator tracks the result in Lifecycle execution. Existing stage owners remain responsible for their original analytical outputs through their declared cleanup interfaces. The coordinator neither calls nor depends on the Evaluation harness. After tombstoning, references stop resolving even while file removal is pending; failed or interrupted removal stays pending, and restart reconciliation resumes the source deletion journal and lifecycle execution idempotently. Completion requires removal of all registered managed copies covered by the deletion. Governed examples and downstream reuse artifacts are handled only in Production V1 through its separately labeled governance dependency.

### Visual extraction agent

Produces inspectable observations tied to screenshot regions and visible evidence.

- **Phase:** POC and Production V1
- **Serves:** Dashboard interpretation and insight discovery; Evidence attribution; Uncertainty and evidence limitations; Inspectable component evaluation
- **Depends on:** Local inference service (POC and Production V1)
- **Paths:** app/vision/, tests/vision/, notebooks/vision.ipynb
- **Tests:** pytest tests/vision/
- **Interface:** Versioned observation contract with source identities, chart/region references, visible values and labels, readability limits, and uncertainty; cleanup by source reference.
- **Owns:** Visual observation

Unreadable or ambiguous values remain unreadable or estimated. An unavailable model or malformed response fails the stage with a bounded timeout; no fabricated replacement values. An observation is an extraction claim, not independent proof of screenshot truth.

### Insight analysis agent

Proposes chart-level and cross-screenshot findings from supplied observations and user context.

- **Phase:** POC and Production V1
- **Serves:** Dashboard interpretation and insight discovery; Potential fraud indicator reporting; Evidence attribution; Uncertainty and evidence limitations; Evidence-based report revision; Inspectable component evaluation
- **Depends on:** Local inference service (POC and Production V1)
- **Paths:** app/analysis/, tests/analysis/, notebooks/analysis.ipynb
- **Tests:** pytest tests/analysis/
- **Interface:** Versioned findings contract accepting explicit observation/context snapshots and returning evidence references, derivations, limitations, and clarification needs; cleanup by source reference.
- **Owns:** Candidate finding

The coordinator passes observations by contract; the agent does not read extraction storage. Potential fraud wording describes supported reasons to investigate. Unsupported causation and outside business facts are excluded. Model failures or invalid contracts fail the stage and leave saved reports unchanged.

### Evidence validator

Checks candidate findings and final report claims against supplied evidence, calculations, and evidence-boundary rules.

- **Phase:** POC and Production V1
- **Serves:** Evidence attribution; Uncertainty and evidence limitations; Potential fraud indicator reporting; Detailed Thai insight reports; Evidence-based report revision; Inspectable component evaluation
- **Depends on:** Local inference service (POC and Production V1)
- **Paths:** app/validation/, tests/validation/, notebooks/validation.ipynb
- **Tests:** pytest tests/validation/
- **Interface:** Versioned validation contract accepting findings or report claims with authorized screenshot/context inputs and returning supported, uncertain, rejected, or needs-clarification outcomes; cleanup by source reference.
- **Owns:** Validation result

Under ADR 012, the first check validates candidate findings and calculations against supplied sources using deterministic arithmetic/reference checks and evaluated model-assisted checks where appropriate. The final check compares the Thai draft with validated findings and sources to catch unsupported claims, changed numbers, or overstated certainty introduced during writing. The same validation role may perform both checks; no separate model assignment is implied. Reusing a model across roles is not independent verification, and agreement between models does not establish ground truth. Incomplete evidence can lead to targeted clarification or an explicitly limited report, but supported remaining claims and limitation wording must pass the final check. A validator timeout or technical failure remains a failed/incomplete check, never an implicit pass or a limitation-based bypass.

### Thai report agent

Expresses validated findings, questions, and material revisions in Thai Markdown.

- **Phase:** POC and Production V1
- **Serves:** Detailed Thai insight reports; Targeted clarification; Evidence-based report revision; Uncertainty and evidence limitations; Potential fraud indicator reporting; Inspectable component evaluation
- **Depends on:** Local inference service (POC and Production V1)
- **Paths:** app/reporting/, tests/reporting/, notebooks/reporting.ipynb
- **Tests:** pytest tests/reporting/
- **Interface:** Versioned Thai draft contract accepting validated findings, relevant context, and revision history, returning Markdown plus claim/evidence references; cleanup by source reference.
- **Owns:** Report draft

No new factual claims are authorized by writing a report. Preserve provenance and limitations; final validation precedes publication. Output failures leave the draft incomplete and preserve the previous saved report. Clarification does not repeat questions already answered in the supplied context.

### Local inference service

Runs evaluated local models with bounded resource use behind a replaceable serving contract.

- **Phase:** POC and Production V1
- **Serves:** Local operation and resource efficiency constraint; Dashboard interpretation and insight discovery; Detailed Thai insight reports; Inspectable component evaluation
- **Depends on:** none
- **Paths:** app/inference/, tests/inference/, notebooks/inference.ipynb
- **Tests:** pytest tests/inference/
- **Interface:** Accepted private HTTP contract v1 under ADR 011 for synchronous task requests and structured success/error responses with request and model/release identity. Inference runs in a separate local process under ADR 010. Internal application components use Python interfaces. Concrete URL/schema and backend adapters are detailed-design work; runtime and placement remain open.
- **Owns:** Inference release, Inference request record

The inference runtime and acceleration remain open. CPU execution remains a provisional evaluation approach, not a selected backend. One inference job at a time is the accepted benchmark baseline under ADR 010, not proof of measured operational capacity. If inference runs in WSL, first measure memory available there before deciding model sizes and concurrency. GPU/NPU support and container compatibility require measurement. Select a vision-language model and separate Thai-capable text model through benchmarks; exact assignments remain open. Load together only if measured memory headroom permits. Return busy, unavailable, invalid, or failed responses explicitly. Inference retries may recompute; callers deduplicate stage acceptance. Do not log screenshots or prompts by default, and do not retain request payload caches after request completion. External cloud inference is not required or enabled by this design.

### Production policy controls

Decides access, approval eligibility, retention constraints, and permitted reuse under explicit production policies.

- **Phase:** Production V1 only
- **Serves:** Production access and approval controls; Production data lifecycle controls; Human report review and approval; Human-controlled analytical improvement
- **Depends on:** none
- **Paths:** app/policies/, tests/policies/, notebooks/policies.ipynb
- **Tests:** pytest tests/policies/
- **Interface:** Versioned authorization and lifecycle-decision contract accepting a trusted identity, action, and resource scope; returns grant/deny and applicable policy revision.
- **Owns:** Policy revision, Role and scope assignment

This component is Production V1 only. It is not a POC startup dependency, POC configuration requirement, or provider of POC review/deletion permissions. The POC shared workspace does not require a permissive policy implementation or a mock production identity.

Under accepted ADR 016 for Production V1, application services enforce one shared authorization policy evaluating the authenticated user role, requested action, and scope for the specific resource. Deny unless expressly permitted, including when identity or policy cannot be validated. Recheck access when delayed jobs execute and before returning or publishing protected results; enqueue-time permission is not a permanent grant. Report approval and knowledge/training reuse authorization remain separate. Exact roles, identity provider/authentication integration, visibility rules, approval responsibilities, and self-approval policy remain open. The architecture supports configurable self-approval without deciding whether production permits it. Shared local POC access does not establish production policy. This component decides policy; artifact owners execute deletion.

### Reviewed-example governance

Collects reviewed examples and tracks their provenance, permitted uses, authorizations, and revocations.

- **Phase:** Production V1 only
- **Serves:** Human-controlled analytical improvement; Production data lifecycle controls; Production access and approval controls
- **Depends on:** Conversation and report service (Production V1 only), Production policy controls (Production V1 only)
- **Paths:** app/governance/, tests/governance/, notebooks/governance.ipynb
- **Tests:** pytest tests/governance/
- **Interface:** Versioned example collection, authorization, permitted-use selection, revocation, and cleanup contract; reads source versions through the conversation interface.
- **Owns:** Reviewed example, Reuse authorization, Derived-use lineage

This component is Production V1 only. POC reports, review comments, approvals, and evaluation records remain in their POC owners; they do not require collection into governed reuse examples or configuration of reuse authorizations. Production governed-example collection is a separate capability.

Under accepted ADR 017 for Production V1, track source evidence, exact report versions, human review, authorizing identity, separate authorization for each permitted use, and downstream artifacts. Collection and report approval grant no reuse permission. Enforce ADR 016 and revalidate policy, source availability, and per-use authorization before reuse. On revocation or source-deletion request, block affected reuse and track downstream cleanup to completion under the agreed policy. Keep interrupted work and unresolved obligations visible; a queued or partially completed operation is not completed cleanup. Governance does not copy source content without tracked lineage and lifecycle coverage. Detailed retention, backup, and future trained-model policies remain open. RAG and training remain separate adoption decisions; collection does not automatically change deployed prompts, models, or knowledge.

### Evaluation harness

Reproduces component and integrated evaluations with human-validated references and human acceptance records.

- **Phase:** POC and Production V1
- **Serves:** Inspectable component evaluation; Human-controlled analytical improvement; Performance, Analytical quality, and Local operation and resource efficiency requirements
- **Depends on:** Thai web chat (POC and Production V1), Conversation and report service (POC and Production V1), Analysis coordinator (POC and Production V1), Visual extraction agent (POC and Production V1), Insight analysis agent (POC and Production V1), Evidence validator (POC and Production V1), Thai report agent (POC and Production V1), Local inference service (POC and Production V1), Production policy controls (Production V1 only), Reviewed-example governance (Production V1 only)
- **Paths:** evaluation/, tests/evaluation/, notebooks/evaluation.ipynb
- **Tests:** pytest tests/evaluation/
- **Interface:** Versioned evaluation manifests, reproducible notebook invocation, result export, and acceptance recording; isolated tests use stubs for other components.
- **Owns:** Evaluation case, Evaluation run, Acceptance record

The POC harness exercises the nine POC-and-Production-V1 components and all 13 POC features without importing, initializing, or configuring the two Production V1 only components. Dedicated public or anonymized benchmark fixtures and human-validated references are managed by the harness directly; reference validation and acceptance are not production reuse authorization. Fixture-based runs provide reproducible retained results independently of a deletable conversation. A conversation-derived copy must not be relabeled as an independent fixture to evade source deletion. Production authorization/governance scenarios and their fixtures are scoped to Production V1 evaluation after separate client approval.

Each developed POC component has the notebook named in its Paths, with purpose, inputs/outputs, data and methodology, expectations, executable scenarios, actual results, metrics, limitations, and a human acceptance assessment. The harness owns evaluation records; component notebooks own their scenario definitions. Integrated evaluation is separate under `notebooks/integrated-workflow.ipynb` and `tests/integration/`, outside all component Paths, and exercises actual component interactions.

Cover all 13 POC features, multi-page screenshots, baseline comparisons, failure/retry, restart, stale approvals, revision, and deletion. Record model/prompt/contract versions, hardware, resource measurements, scoring definitions, reference provenance, and explicit human decisions. No component or POC acceptance is inferred from unit tests alone. Failed or incomplete evaluations cannot appear accepted.

For conversation-backed evaluation, reference existing screenshots and source-linked analytical/report outputs by stable IDs wherever possible instead of copying their content into notebook cells or evaluation files. The harness resolves IDs through its existing conversation/owning-component interfaces and checks source availability before displaying results. If a notebook must persist conversation-bearing output, the Conversation and report service owns it as a Managed source-linked artifact registered against all contributing sources; the harness keeps only its ID in evaluation records. Conversation text, screenshots, report excerpts, plots, outputs, or review comments embedded in a saved notebook/export are content-bearing copies, not an exemption from deletion. Do not leave those bytes in unmanaged notebook outputs, autosaves, exports, or caches. The durable saved notebook/output container must be registered with that owner before it is exposed as persisted evidence; reject or reconcile incomplete publication under the existing managed-artifact recovery workflow.

Keep the required executable notebooks, actual results, metrics, limitations, and human acceptance records. Dedicated public/anonymized fixture runs retain actual outputs for reproducibility. Conversation-backed runs retain IDs and content-free evaluation/acceptance metadata; any result, metric, explanation, or review text that contains conversation content follows the managed-artifact rule rather than being retained as free-standing metadata. After source deletion, mark the affected references/results unavailable without reproducing deleted content or claiming that run can still be replayed. Content-free run metrics and acceptance decisions can remain with that availability annotation; fixture-based notebook results and acceptance records remain reproducible. This clarifies evaluation artifact ownership without adopting a production retention policy.

Planned POC deletion scenario for component notebooks and integrated evaluation:

1. Evaluate a saved conversation using screenshot/output IDs and persist one necessary conversation-bearing notebook result through the Conversation and report service as a managed artifact; record metrics and the human acceptance assessment.
2. Delete the source through the coordinator. Confirm that source/output/artifact references stop resolving immediately, stale result publication or artifact registration is rejected, and source-artifact cleanup includes the managed notebook result/container and registered exports without calling the harness.
3. Interrupt removal while a managed copy remains. Confirm deletion stays visibly pending; restart and resume the existing deletion journal/lifecycle execution until every affected registered copy is removed. Only then may cleanup be complete.
4. Confirm the retained evaluation record reports unavailable source-linked results without leaking content, while content-free metrics/acceptance records and independently sourced public/anonymized fixture notebook results remain available. Run the scenario with no production identity, authorization, or reviewed-example governance component configured.

## Data model

Persistence status: SQLite structured state and managed artifact files with restart reconciliation, version-specific approval, and deletion/late-result protection are accepted under ADR 014. Actual storage paths await Windows/WSL placement and filesystem checks. Job execution is accepted under ADR 015; the Production V1 role/action/resource-scope authorization boundary is accepted under ADR 016, and lifecycle governance is accepted under ADR 017, while detailed access, retention, backup, and trained-model policies remain open; benchmark-led model selection is accepted under ADR 013 while actual models/assignments remain unevaluated; the four-role workflow and two checks are accepted in ADR 012; PRD durability, approval, evidence, and evaluation requirements remain mandatory.

Each entity has exactly one writer named by its component's Owns line. Strong consistency means an authoritative transactional decision within the owner, not a claim of distributed transactions across owners. Cross-owner references use immutable identities and contract versions; tombstones and reauthorization prevent stale work from restoring deleted data.

- **Conversation** — saved investigation and current input revision. Owns messages and links to evidence and reports. Consistency: strong; deletion and publication coordinate against the same live revision.
- **Message** — user text, clarification exchanges, and application responses in one conversation. Consistency: strong append and ordered retrieval.
- **Source evidence** — screenshot artifact or explicit user-supplied context with source identity and conversation association. Consistency: strong metadata visibility after artifact durability; inaccessible after tombstoning.
- **Report version** — immutable completed report and supporting input/validation references within a conversation. Consistency: strong publication against expected live inputs; historical versions are not overwritten.
- **Review action** — corrections, revision requests, or approval of an exact report version by a human. Consistency: strong eligibility and report-state checks at approval time.
- **Source deletion journal** — durable cleanup state for conversation-owned records/files, enqueue intents, and managed source-linked evaluation copies. Consistency: strong tombstone before cleanup; physical cleanup eventually completes and remains observable meanwhile, including after restart.
- **Managed source-linked artifact** — a persisted content-bearing evaluation output, notebook container, or export linked to every contributing source/conversation and exact version; owned only by Conversation and report service. Consistency: strong registration/read checks against live sources; references stop resolving on source deletion and physical cleanup remains tracked until every affected registered copy is removed.
- **Analysis job** — durable execution identity, requested input revision, status, and bounded attempt history. Consistency: strong claim and state transitions under a single worker; retry deduplicates publication.
- **Workflow stage record** — inspectable result references and execution versions for a job. Consistency: strong stage acceptance for the current attempt; partial work never implies completion.
- **Lifecycle execution** — owner-by-owner deletion/revocation progress and pending obligations. Consistency: strong blocking state; cleanup across owners is eventual and retryable.
- **Visual observation** — extracted chart content and screenshot-region provenance for a job. Consistency: immutable result per accepted stage attempt, gated by current source availability.
- **Candidate finding** — proposed trend, anomaly, relationship, or investigative indicator with derivations. Consistency: immutable per accepted analysis attempt; no publication authority.
- **Validation result** — evidence/claim checks and limitations for an input snapshot or draft. Consistency: immutable per validated version; cannot validate a different draft implicitly.
- **Report draft** — Thai prose and claim references before final validation. Consistency: immutable draft revision tied to an analysis attempt; never implicitly approved.
- **Inference release** — evaluated model artifacts, serving configuration, and version identities. Consistency: strong release selection; each request records its actual version.
- **Inference request record** — non-content execution metadata for a serving attempt. Consistency: completion may be unknown after process loss; no exactly-once inference claim.
- **Policy revision** — versioned access, approval, retention, and reuse rules. Consistency: strong activation; unavailable or unknown policy denies production actions.
- **Role and scope assignment** — identity-linked roles, accessible resources, and actions. Consistency: strong authorization reads; do not rely on stale job-time grants.
- **Reviewed example** — governed references to reviewed source, context, corrections, and report versions. Consistency: strong collection/provenance; existence conveys no reuse grant.
- **Reuse authorization** — explicit human permission for an example's named use, independent of report approval. Consistency: strong grant/revocation before permitted-use selection.
- **Derived-use lineage** — relationship between authorized sources and tracked downstream copies or future uses. Consistency: strong registration before use; physical downstream cleanup is eventual and tracked.
- **Evaluation case** — versioned public/anonymized input references and analyst/developer-validated expectations. Consistency: immutable case version; provenance follows source lifecycle.
- **Evaluation run** — result/reference IDs, environment/version manifest, resource measurements, failures, limitations, and actual fixture-run results; conversation-bearing result copies belong to Conversation and report service, not this record. Consistency: durable run states; incomplete results remain incomplete and deleted-source references resolve as unavailable.
- **Acceptance record** — explicit human acceptance or rejection of a component, integrated workflow, or proposed improvement, bound to evaluated versions. Consistency: strong; changing evaluated versions does not transfer acceptance.

Use component-owned, versioned SQLite schema migrations; no owner bypasses another's interface even within the same database. Stage artifacts and evaluation records may use managed files with durable metadata. Create artifacts before exposing references; track staging and reconcile orphan files after interruption. Never delete unrecognized files outside managed roots. Application and inference ephemeral buffers are not durable stores.

POC conversation data, dependent stage artifacts, and source-linked evaluation copies persist until manual deletion unless explicitly ephemeral. Evaluation references do not create copies or grant indefinite retention of conversation content. Source-linked notebook outputs/containers are owned by Conversation and report service and included in its source-artifact cleanup; evaluation metadata holds their IDs. Deletion blocks reference resolution/access first and cascades through the existing owner cleanup interfaces; interrupted cleanup resumes after restart and stays pending until affected registered files are removed; preserve only the minimum non-content cleanup metadata needed to finish, then remove it under the applicable policy. Production retention periods, legal obligations, backup expiry, and consequences for future trained models remain open. No backup-based recovery guarantee or secure-erasure guarantee is established.

## Integrations

Integration status: SQLite and managed local artifact storage are accepted under ADR 014, with actual paths deferred until Windows/WSL placement and filesystem behavior are confirmed. Browser polling and durable execution by one background coordinator inside the application process are accepted under ADR 015. The separate inference process is accepted under ADR 010, with private HTTP and a minimal versioned contract accepted under ADR 011. Browser-facing FastAPI and same-process Python interfaces remain accepted under ADR 009.

- **Browser/application** — inbound HTTP API v1, synchronous commands and asynchronous job polling; local loopback for POC, HTTPS required at a future production boundary. Contract versions are explicit and changes are compatibility-tested. Mutations carry stable operation identity and expected resource versions. Unavailable: Thai retry/status presentation; previously saved content remains durable.
- **Application/inference process boundary** — outbound private HTTP, synchronous inference calls using explicit contract v1 (ADR 011) across the separate process boundary (ADR 010). The browser never calls the worker. HTTP 200 carries matching request/contract identity, a task result, and actual model/release identity; validate the response before accepting it. Worker errors carry a stable category and retry indication: 400 invalid/unsupported requests, 429 busy, 503 not ready/model unavailable, 500 execution failure. Map errors to Thai in the application without leaking input contents or internal diagnostics. Connection loss, refusal, crash, or timeout may return no envelope: bound connection/response waits, record an incomplete unavailable/timeout outcome locally, preserve saved screenshots and reports, show a Thai error, and allow retry. Ignore late results; after timeout confirm that the previous attempt has finished or been safely stopped before dispatching any other job, as accepted in ADR 015. Worker readiness alone does not establish termination, and timeout alone does not cancel computation. Apply the same gate after application restart if the inference process may still be executing abandoned work. Never infer completed/approved work or duplicate report publication from a retry. Keep the endpoint private to the application through placement-appropriate binding/access controls; verify the Windows/WSL route before use. Concrete URL/schema, numerical timeout settings, and network configuration remain detailed-design/evaluation work. Durable jobs, incomplete interrupted states, and explicit retry are accepted under ADR 015. Concrete bounded retry limits and backend-specific status/stop mechanisms require detailed design; no retry may bypass prior-attempt resolution. SQLite/local artifact persistence is accepted under ADR 014, with paths still deferred. Runtime, placement, acceleration, and production service authentication remain open.
- **Local model artifacts/backends** — inference process reads locally provisioned, version-pinned open-source model artifacts. Runtime backend, artifact identity, license, configuration, and acceleration compatibility are recorded per evaluated release. Offline provisioning is separate from runtime analysis; no user evidence is sent to download sources. Missing or incompatible artifacts fail readiness and jobs clearly. No particular vendor API compatibility is assumed.
- **SQLite and artifact filesystem** — owner-local embedded database/file interfaces; versioned schemas and artifact manifests, not public analytical-data integrations. Unavailable storage rejects mutations and new execution; never acknowledge unsaved input as durable. Cleanup is idempotent and restartable.
- **Production identity provider (Production V1 only)** — required production integration boundary, provider and protocol not yet chosen. It is absent from POC startup, execution, and end-to-end evaluation; no provider, credentials, or role/scope policy configuration is required for the shared local POC. Its authenticated identity feeds policy controls; token/assertion validation and versioning must be designed before production. Unavailable or unvalidated identity denies production access. No credential store or identity dependency is selected here.

No dashboard database/API, cloud AI provider, message broker, vector database, retrieval system, or training platform is integrated by this design.

## Quality attributes

- **Latency** — not targeted numerically: PRD requires baseline initial-report and follow-up timing on representative inputs before agreeing thresholds. Record queue, model-load, inference, validation, and total time separately.
- **Throughput** — not targeted: workload volume and acceptable queue delay are unknown; measure completed analyses and queue growth under a declared workload.
- **Concurrency** — accepted benchmark baseline: one inference job at a time, explicitly re-confirmed in Decision 3 / ADR 010. Operational concurrency and model sizes follow measurement; this is not a demonstrated capacity limit. One background coordinator inside the application process is accepted under ADR 015; this adds no third process. After timeout, prior-attempt completion or safe stopping is required before any further dispatch. If inference runs in WSL, measure memory available there before deciding model sizes and inference concurrency, as confirmed by the user. Multi-user capacity and simultaneous model residency remain untargeted.
- **Availability** — not targeted: this local evaluation POC has no uptime SLO; production availability requirements remain open.
- **Recovery (RTO)** — not targeted numerically: PRD requires restart recovery and visible retry; recovery duration will be measured. No automatic regional or host-failure recovery is promised.
- **Recovery (RPO)** — application-restart objective: no loss of acknowledged committed conversation content, report versions, or approval state. Source: PRD Reliability and recovery. Host/disk-loss and backup RPO remain not targeted.
- **Analytical quality** — no numerical threshold yet. PRD requires human-validated references and manual-versus-assisted comparisons for accuracy, meaningful insights, time, and Thai quality, including numerical correctness, evidence attribution, uncertainty, and fraud wording.
- **Memory and resource efficiency** — Windows host reports 15.5 GB usable shared system RAM; Ubuntu in WSL is the development environment. This is not a measured WSL or inference memory budget. If inference runs in WSL, measure memory available there before deciding model sizes and concurrency. Application/model budgets and acceptance headroom remain unconfirmed. Source: user hardware/environment clarification and PRD baseline process. Measure peak RAM, resource contention, and model-switching cost without treating the shared GPU figure as extra capacity.
- **Privacy and security** — POC inputs must be public or anonymized; no cloud AI dependency. Production requires scoped access and protection at rest/in transit, but policies, encryption/key management, identity integration, and acceptance criteria are open. Source: PRD.
- **Maintainability and reproducibility** — each developed POC component has isolated tests and an executable evaluation notebook; explicit human acceptance precedes integration. Integrated workflow requires separate acceptance. Source: PRD.
- **Thai communication** — all application-facing reports, messages, questions, errors, notifications, and review interactions use Thai with the PRD's named exceptions. Source: PRD.

## Operations

Accepted POC process boundary: one modular application and one separate inference process (ADR 010). If inference fails, preserve saved screenshots and prior reports, keep the application responsive when its own resources permit, display a Thai error, and allow retry. Shared memory exhaustion can still affect both processes. Private HTTP success/error and bounded-wait behavior are accepted under ADR 011. One background coordinator, durable SQLite jobs, browser polling, interrupted states after restart, and safe retry are accepted under ADR 015. Detailed bounded retry limits, polling cadence, attempt status/stop mechanisms, packaging, placement-specific storage rollout, and production lifecycle operations remain detailed-design or open policy work. Never dispatch after a timeout until the previous attempt is confirmed finished or safely stopped.

Observe execution using correlation identities for conversation, job, attempt, stage, and release. Logs contain metadata by default, not screenshot contents, report text, credentials, or prompts. Authorized evaluators access necessary intermediate artifacts through component interfaces.

| Component | Failure visibility and response |
|---|---|
| Thai web chat | HTTP errors, browser error telemetry in evaluation, Thai status/error display; mutation retry retains operation identity. |
| Conversation and report service | Storage/commit failures, rejected stale writes, missing artifacts, and deletion backlog; reject unsaved changes and preserve committed versions. |
| Analysis coordinator | Queue depth, stage duration, attempts, interruption, stale-publication rejection, and lifecycle backlog; bounded retry and explicit incomplete status. |
| Visual extraction agent | Extraction failures, unreadable observations, and source-reference errors; fail or expose limitations. |
| Insight analysis agent | Invalid findings, missing evidence references, and clarification outcomes; fail the stage without changing reports. |
| Evidence validator | Rejected/uncertain claims, arithmetic mismatches, and validator failures; failed checks never pass by default. |
| Thai report agent | Draft/contract failures, language-quality results, and final-check rejection; preserve the previous report. |
| Local inference service | Readiness, active request count, model-load failures, request timeouts, memory high-water marks, and process exit; bound load and return explicit errors. |
| Production policy controls | Denied actions and policy/identity unavailability without exposing sensitive content; deny production action on failure. |
| Reviewed-example governance | Missing provenance, revoked grants, blocked uses, and pending cleanup; prevent reuse until eligible. |
| Evaluation harness | Incomplete runs, missing outputs, reproducibility failures, and missing acceptance; never infer human acceptance. |

Deploy pinned application and inference releases with separate configuration for local POC, test/evaluation, and future production. Keep runtime evidence and model artifacts outside image layers and source code. Keep secrets out of committed configuration. Compose may provide the POC process wiring if the chosen backend is compatible; otherwise document the equivalent native local processes and versioned configuration. No implementation packaging exists yet.

For change rollout, stop new job claims, drain or mark active work interrupted, back up owned data as a coordinated consistent snapshot when a backup policy exists, apply compatible schema migrations, and verify readiness before resuming. Prefer additive migrations across the rollback window. Roll back application, model, prompt, and contract releases to the last accepted compatible combination. If a migration prevents compatibility, stop and restore a verified pre-migration snapshot under an explicit recovery procedure; do not silently discard newly written user data. Retention/deletion obligations must cover any backups; no routine POC backup copies are introduced before their lifecycle is defined.

Cost drivers are RAM and CPU inference time, repeated model loading, storage for screenshots/intermediates/evaluation copies, development of notebooks and contracts, and human reference labeling/review. Avoid a broker, frontend build framework, per-agent containers, vector database, and Kubernetes until evidence justifies adoption. Production infrastructure and operational staffing costs cannot yet be estimated.

## Fitness functions

The architecture checks below guard the accepted design and PRD evaluation obligations. They are requirements for future implementation, not executed checks or evidence of human acceptance.

These are required future checks, not implemented or executed checks. Build each when work first touches a guarded component in the applicable phase. POC gates cover only POC-active components/dependencies; production permission/reuse checks and Production V1 only guards do not block POC acceptance or require production configuration. Shared restart, evidence, report approval, and manual-deletion checks remain mandatory for the POC. Production V1 evaluation adds authorization, governed reuse, and downstream cleanup checks without weakening their accepted requirements. Tests exercising multiple components belong only in the full suite, outside component Paths; isolated component tests replace dependency interfaces with stubs. Full-suite command: `pytest`; actual browser and notebook execution additionally form integrated evaluation.

### Imports follow the declared dependencies

- **Guards:** Thai web chat, Conversation and report service, Analysis coordinator, Visual extraction agent, Insight analysis agent, Evidence validator, Thai report agent, Local inference service, Production policy controls, Reviewed-example governance, Evaluation harness
- **Rule:** Code under one component's Paths imports another component only where Depends on names it for the applicable phase, and only through its declared interface; POC startup and evaluation do not require imports of Production V1 only components.
- **Checked by:** lint rule
- **Run with:** python -m tools.check_import_boundaries

### Owners exclusively mutate their entities

- **Guards:** Conversation and report service, Analysis coordinator, Visual extraction agent, Insight analysis agent, Evidence validator, Thai report agent, Local inference service, Production policy controls, Reviewed-example governance, Evaluation harness
- **Rule:** Storage mutations affect only the invoking component's owned entities and managed artifacts.
- **Checked by:** test

### Evidence and final prose remain attributable

- **Guards:** Visual extraction agent, Insight analysis agent, Evidence validator, Thai report agent, Analysis coordinator
- **Rule:** The first check rejects unsupported source findings/calculations; the final check catches writer-introduced unsupported claims, changed numbers, and overstated certainty; incomplete evidence leads to targeted clarification or an explicitly limited report without bypassing either check.
- **Checked by:** test

### Restart and retry preserve report state

- **Guards:** Conversation and report service, Analysis coordinator
- **Rule:** Crash/retry injection cannot lose acknowledged committed content, duplicate publication, or approve an incomplete or different report version.
- **Checked by:** test

### Deletion prevents resurrection and further reuse

- **Guards:** Conversation and report service, Analysis coordinator, Visual extraction agent, Insight analysis agent, Evidence validator, Thai report agent, Reviewed-example governance, Evaluation harness
- **Rule:** In the POC, source deletion invalidates evaluation references, blocks stale publication/registration, and stays pending until the Conversation and report service removes all affected managed notebook/output copies through its source-artifact cleanup, including recovery after restart without a coordinator-to-harness call; Production V1 additionally blocks revoked reuse and tracks governed-example/downstream cleanup under its agreed policy.
- **Checked by:** test

### Production permissions distinguish approval from reuse

- **Guards:** Production policy controls, Conversation and report service, Analysis coordinator, Reviewed-example governance
- **Rule:** Unauthorized cross-scope access, invalid approval, and reuse without a separate live authorization are denied, including in delayed jobs.
- **Checked by:** test

### Runtime contracts remain compatible

- **Guards:** Thai web chat, Local inference service, Analysis coordinator
- **Rule:** Browser and inference contract compatibility tests reject unsupported versions and malformed responses before state changes.
- **Checked by:** test

### Evaluation and acceptance bind to exact versions

- **Guards:** Evaluation harness, Local inference service, Analysis coordinator, Reviewed-example governance
- **Rule:** Integration or deployment eligibility requires completed reproducible evaluation artifacts and explicit human acceptance for the actual component and integrated release versions.
- **Checked by:** CI step

### Thai interface and safe Markdown

- **Guards:** Thai web chat, Thai report agent, Analysis coordinator
- **Rule:** Representative user flows use Thai text with documented exceptions and render untrusted Markdown without executable HTML or unsafe links.
- **Checked by:** test

## Decisions

- [ADR 001 — Python application and evaluation stack](adr/001-python-application-and-evaluation-stack.md) — superseded by 009
- [ADR 002 — Modular application and separate local inference](adr/002-modular-application-and-local-inference.md) — superseded by 010
- [ADR 003 — Explicit four-agent workflow](adr/003-explicit-four-agent-workflow.md) — superseded by 012
- [ADR 004 — Benchmark-led model selection](adr/004-benchmark-led-model-selection.md) — superseded by 013
- [ADR 005 — SQLite and managed local artifacts](adr/005-sqlite-and-managed-local-artifacts.md) — superseded by 014
- [ADR 006 — Durable jobs and bounded background execution](adr/006-durable-jobs-and-background-execution.md) — superseded by 015
- [ADR 007 — Scoped centralized production authorization](adr/007-scoped-production-authorization.md) — superseded by 016
- [ADR 008 — Provenance and explicit reuse governance](adr/008-provenance-and-explicit-reuse-governance.md) — superseded by 017
- [ADR 009 — Clarified POC runtime and evaluation stack](adr/009-clarified-poc-runtime-and-evaluation-stack.md) — accepted
- [ADR 010 — Reconfirmed POC deployment boundaries](adr/010-reconfirmed-poc-deployment-boundaries.md) — accepted
- [ADR 011 — Private HTTP inference contract for the POC](adr/011-private-http-inference-contract.md) — accepted
- [ADR 012 — Four-role workflow and two evidence checks](adr/012-four-role-workflow-and-two-evidence-checks.md) — accepted
- [ADR 013 — Reconfirmed benchmark-led model selection](adr/013-reconfirmed-benchmark-led-model-selection.md) — accepted
- [ADR 014 — Reconfirmed POC persistence and recovery](adr/014-reconfirmed-poc-persistence-and-recovery.md) — accepted
- [ADR 015 — Reconfirmed durable analysis execution](adr/015-reconfirmed-durable-analysis-execution.md) — accepted
- [ADR 016 — Reconfirmed Production V1 authorization policy](adr/016-reconfirmed-production-authorization-policy.md) — accepted
- [ADR 017 — Reconfirmed Production V1 lifecycle and reuse governance](adr/017-reconfirmed-production-lifecycle-and-reuse-governance.md) — accepted

Reopening record: the user explicitly withdrew the previous answers below pending re-confirmation. Only the Status lines of ADRs 002–008 changed; their original decisions, reasons, and rejected alternatives remain intact as historical reference. Statements in their Why sections that the user accepted them describe the previous interview state. These are no longer current approvals. ADR 001 remains superseded by 009; ADR 009 remains accepted and unchanged. A new explicit answer is required for each reopened topic; acceptance of one does not accept a dependent topic. If a proposal changes, preserve the old text and record the revised decision in a new ADR with supersession rather than rewriting historical reasoning.

| Interview decision | Topic | ADR | Previous answer (historical) | Current disposition |
|---|---|---|---|---|
| 1 | Hardware and environment | Recorded constraints / ADR 009 clarification | Hardware facts, later Windows host and Ubuntu in WSL clarification | Confirmed; inference runtime/acceleration open; measure WSL memory before model sizing/concurrency if inference runs there |
| 2 | Application runtime and test stack | 001 superseded by 009 | 1. Accept, revised to 2. Accept with changes | ADR 009 accepted; browser FastAPI, same-process Python interfaces, both evaluation requirements retained |
| 3 | Deployment boundaries | 002 superseded by 010 | Original 1. Accept withdrawn; subsequently explicit 1. Accept for the POC | ADR 010 accepted: modular application plus separate local inference, recovery behavior and single-job benchmark baseline; private HTTP follow-up accepted in ADR 011 |
| 4 | Agent composition and control flow | 003 superseded by 012 | Original 1. Accept withdrawn; subsequently 2. Accept with changes | ADR 012 accepted: four logical roles, explicit Python coordinator, source check before writing, writing-drift check after writing, targeted clarification or explicit limitations; model assignments open |
| 5 | Model selection strategy | 004 superseded by 013 | Original 1. Accept withdrawn; subsequently explicit 1. Accept Decision 5 | ADR 013 accepted: component benchmarks and human review, reproducible integrated evaluation and explicit human acceptance; single-job baseline; models/runtime/acceleration/placement open |
| 6 | POC persistence | 005 superseded by 014 | Original 1. Accept withdrawn; subsequently explicit 1. Accept Decision 6 | ADR 014 accepted: SQLite plus managed local artifacts, restart recovery, version-specific approval, deletion/late-result protection; actual paths await Windows/WSL placement |
| 7 | Durable analysis execution | 006 superseded by 015 | Original 1. Accept withdrawn; subsequently explicit 1. Accept Decision 7 | ADR 015 accepted: SQLite jobs, one in-application coordinator, browser polling, incomplete interrupted jobs with retry, publication/stale-result safeguards, prior-attempt resolution before dispatch after timeout |
| 8 | Production authorization boundary | 007 superseded by 016 | Original 1. Accept withdrawn; subsequently explicit 1. Accept Decision 8 for Production V1 | ADR 016 accepted: shared role/action/resource-scope policy enforced by services, deny unless expressly permitted, separate approval/reuse permissions, checks at delayed execution and before protected return/publication; detailed policies open |
| 9 | Lifecycle and reviewed-example governance | 008 superseded by 017 | Original 1. Accept withdrawn; subsequently explicit 1. Accept Decision 9 for Production V1 | ADR 017 accepted: exact source/report/review provenance, separate per-use grants, downstream artifact tracking, blocked affected reuse and cleanup tracked to completion; retention/backup/trained-model policies open |

Resume order: preserve the original transport deferral as history. Decision 3 was explicitly re-confirmed in ADR 010. The user then explicitly answered **1. Accept private HTTP with a versioned contract for the POC**, recorded in ADR 011, requesting private access, a minimal contract, explicit success/error responses, and timeout/unavailable-worker handling. The user subsequently answered Decision 4 with **2. Accept with changes**, recorded in ADR 012, distinguishing source validation from post-writing claim/number/certainty checks and permitting targeted clarification or explicit limitations. The user then explicitly answered **1. Accept Decision 5**, recorded in ADR 013: component benchmarks with human review, followed by reproducible end-to-end POC evaluation and explicit human acceptance; one inference job at a time is the baseline, and specific models/runtime/acceleration/placement remain open. The user then explicitly answered **1. Accept Decision 6**, recorded in ADR 014: SQLite plus managed local screenshots/artifacts, restart recovery, version-specific approval, and deletion handling; actual paths await Windows/WSL placement. The user then explicitly answered **1. Accept Decision 7**, recorded in ADR 015: durable SQLite jobs, one background coordinator inside the application, browser polling, incomplete interrupted jobs after restart, explicit retry, prior-report protection, duplicate-publication prevention, and rejection of late results for deleted/superseded inputs. After timeout, confirm prior-attempt completion or safe stopping before any new dispatch. The user then explicitly answered **1. Accept Decision 8 for Production V1**, recorded in ADR 016: shared authorization policy enforced by application services, resource-specific role/action/scope checks, deny unless expressly permitted, separate report approval and knowledge/training reuse authorization, and access rechecks at delayed execution and before protected result return/publication; detailed roles, identity, visibility, and self-approval policy remain open. The user then explicitly answered **1. Accept Decision 9 for Production V1**, recorded in ADR 017: source evidence, exact report versions, human review, separate authorization per permitted use, downstream artifacts, and blocked reuse plus policy-governed cleanup tracked to completion. Detailed retention, backup, and trained-model policies remain open; RAG and training require separate adoption decisions. Interview status: complete; no pending interview decision or transport question remains. No actual model assignment or implementation is accepted before its required evaluation/review.

## Open questions

- The interview is complete: Decisions 3–9 were individually re-confirmed in ADRs 010 and 012–017, and the transport follow-up in ADR 011. No ADR remains proposed. Model names, sizes, sharing, and assignments remain for evaluation under ADR 013; acceptance of the selection strategy does not accept a model.
- Deployment follow-up — inference communication is resolved by accepted ADR 011: private HTTP with a minimal versioned POC contract and explicit success/error/failure handling. Concrete payload/URL details, measured timeout values, and the private Windows/WSL route still require detailed design/evaluation. Runtime, acceleration, placement, and detailed production lifecycle policies remain open.

- Windows host and Ubuntu-in-WSL development are confirmed. Available disk capacity, actual inference execution location/runtime, memory budget/headroom, runtime compatibility, and Intel GPU/NPU acceleration remain unverified. If inference runs in WSL, measure memory available there before deciding model sizes and concurrency. The supplied host hardware specification is not a WSL benchmark.
- Validate private endpoint connectivity/access controls for the eventual Windows/WSL placement and selected inference backend. Private HTTP is accepted under ADR 011; browser-facing FastAPI and internal Python interfaces remain accepted under ADR 009. Inference need not use FastAPI. Runtime and execution placement remain open.
- Select model names, sizes, licenses, quantization, serving backend, assignments, concurrency, and residency from component and integrated measurements. Determine whether the inference process and Docker Compose support the target environment.
- Establish performance workload conditions, numerical response-time/resource limits, throughput expectations, and queue bounds after baseline measurement. Establish scoring definitions, representative dataset coverage, and quantitative thresholds for factual accuracy, insight discovery, Thai quality, analysis-time savings, and integrated acceptance.
- Confirm development deadline, time budget, team capacity, and responsibility for ongoing evaluation and operations.
- Production roles, identity provider/protocol, authentication, scope/visibility rules, approval workflow, self-approval policy, and reuse-authorizing authorities remain open. The production identity integration protocol and contract versioning remain unspecified (architecture-checklist interoperability gap). The shared service-enforced role/action/resource-scope boundary is accepted for Production V1 under Decision 8 / ADR 016; detailed production policy and its validation remain open.
- Production sensitive-data handling, secure storage, encryption/key management, service authentication, data residency, retention periods, backup expiry, deletion obligations, and acceptance conditions remain open.
- Define how deletion/revocation affects future derived knowledge, training datasets, deployed trained models, and any obligations that cannot be automatically fulfilled. Do not claim model unlearning or secure erasure.
- Decide which reviewed feedback or knowledge may be reused, by whom and for what purpose, and how pending obligations and authorized exceptions are communicated.
- RAG, prompt improvements, and fine-tuning require separate suitability, evidence-boundary, resource, evaluation, and human deployment decisions. No such mechanism is adopted by these ADRs.
- Production storage, service boundaries, scaling, deployment configuration, availability SLOs, host/disk-loss RTO/RPO, and backup/restore policies must follow POC findings and agreed production policy. SQLite and local files are accepted for the POC under Decision 6 / ADR 014 only; production storage and lifecycle policies remain open. Actual database/artifact paths await confirmed Windows/WSL placement and filesystem checks.
- Run `prince-guardrails:threat-model` on this design before implementation security decisions are treated as sufficient; no threat-model document exists. Treat image content, user text, and model responses as untrusted data, and assess prompt injection, upload handling, access, deletion, and inference boundaries there.
- Resolve the project instruction filename mismatch (`AGENT.md` references absent `AGENTS.md`) before implementation. The user invoked design explicitly; no implementation was authorized or begun.
- All 16 PRD features map to the 11 components above. Current decisions are accepted in ADRs 009–017; ADRs 001–008 retain their historical bodies with supersession links. No optional deferred mechanism is selected. Acceptance covers the architecture and evaluation strategy, not unmeasured model/runtime choices, detailed production policies, implementation, or deployment.
