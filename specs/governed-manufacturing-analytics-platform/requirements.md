# Implementation Requirements Checklist

**Purpose**: Provide an implementation acceptance checklist that agents can execute one item at a time.  
**Feature**: Compatibility Assessment for Required Platforms and governed manufacturing analytics across approved Gold layer data, compatibility-layer RBAC preservation, responsible AI guardrails, Slack/voice/visual delivery, platform compatibility validation, deployment governance, and related audit/governance controls

## Functional Acceptance Criteria

- [ ] Implement the Compatibility Assessment UI-first flow so authorized users can open the feature, see Microsoft Fabric, Azure OpenAI, and Slack APIs in scope, submit an assessment, receive platform-by-platform statuses plus an overall outcome, and reopen the feature to retrieve the latest saved result
- [ ] Enforce Compatibility Assessment request validation so all three required platforms are present, status values are restricted to the approved enum, malformed inputs are blocked before any platform check starts, entered values are preserved, and field-level validation messages are shown
- [ ] Implement successful Compatibility Assessment persistence with explicit fields for platform name, compatibility status, assessment notes, assessedAt timestamp, assessedBy identity, and overallOutcome
- [ ] Implement Compatibility Assessment failure handling so platform-specific errors are shown by failure category, retryable failures expose a retry action, retries do not require re-entry of unchanged valid inputs, and raw exceptions or secrets are never shown
- [ ] Enforce Compatibility Assessment RBAC so unauthorized or unauthenticated access to view or execute the assessment is denied, no assessment data is exposed, and an authorization event is recorded
- [ ] Implement governed NLQ for Executive users so plain-English manufacturing questions are answered only when the resolved source is an approved Microsoft Fabric Gold layer dataset or semantic asset configured for the subject area
- [ ] Block NLQ answer generation when the request requires data outside the approved Gold layer allowlist, outside the requester’s role scope, from an unapproved source, from an unavailable mapped source, or from unsupported intent coverage
- [ ] Implement validation for conversational requests across web, Slack, and voice flows so empty prompts, whitespace-only prompts, overlength prompts, malformed requests, unsupported intents, unresolved metrics/entities, ambiguous follow-ups, invalid period references, and unsupported comparison constructs are rejected with governed validation or clarification responses
- [ ] Implement conversational answer generation for supported trend, variance, comparative, KPI, customer, account, team, and first-payment-rate use cases using approved Gold layer definitions only, with business-readable responses that include metric context, filter context, comparison basis, and reporting periods where required
- [ ] Preserve conversational context for supported follow-up questions so prior KPI, grouping, scope, or comparison context can be reused when source-supported, and provide reset behavior to start a new context-free conversation
- [ ] Implement Manager and Executive comparative analytics behaviors for month-over-month, quarter-over-quarter, and year-over-year questions with correct current value, prior value, absolute variance, and percentage variance when data is available
- [ ] Implement governed unsupported-question behavior so unsupported or partially supported questions return declined, qualified, insufficiency, unavailable, or governed non-answer states rather than speculative output
- [ ] Implement responsible-AI answer guardrails for Manager and similar governed AI-answer flows so answers are delivered only when grounding and policy checks pass, and suppressed with a governed refusal when unsupported claims, disallowed content, or out-of-scope data are detected
- [ ] Implement Slack as a primary delivery channel for supported Executive and Manager analytics stories so standard Slack requests are accepted in-channel, answered in-channel from approved Gold layer data, and not redirected to another interface for normal flows
- [ ] Implement voice input flows so authorized users can capture audio, receive transcription, handle permission/listening/transcription failures gracefully, edit transcripts where required, and submit valid transcripts through the same governed analytics path as typed input
- [ ] Implement voice output/playback flows so eligible approved responses can be spoken only when access checks pass, playback states are shown, replay is supported where required, and failures preserve the visible answer with a governed fallback message
- [ ] Implement visualization flows for NLQ/trend/variance responses so supported requests render visuals only from approved Gold layer lineage, unsupported or malformed visualization requests return controlled non-visual states, and no fabricated chart content is produced
- [ ] Implement trend and variance analytic views so authorized users can select approved KPIs, supported periods, and authorized filters; retrieve governed results; and see current/comparison values with directional context and unavailable states where source-supported
- [ ] Implement predictive insight gating so predictive outputs, combined historical+predictive views, and forward-looking labels are shown only for supported KPIs/horizons, using approved Gold layer-aligned sources and explicit response-type labeling
- [ ] Implement proposal/procurement/governance workflows in scope, including required proposal sections, vendor section drafting and readiness status, proposal incompleteness flagging, Gold-layer-readiness assumptions, dependency/time-commitment disclosure, reference-availability capture, commercial pricing/billing sections, SOW creation/review/signature gating, and project work-start blocking until SOW agreement and signature conditions are satisfied
- [ ] Implement delivery governance flows in scope, including phased delivery records, phase-gate checkpoints, steering review cadence, daily stand-up cadence rules, milestone tracking, governance evidence history, and blocking of unauthorized or invalid phase progression
- [ ] Implement compatibility and readiness validation capabilities in scope for Microsoft Fabric, Azure OpenAI, Slack APIs, semantic readiness, environment deployment configuration, release promotion, and Microsoft Fabric platform-constraint compliance, using persisted validation/audit outcomes and controlled failure states

## UI Acceptance Criteria

- [ ] Provide a web-accessible entry path for the Compatibility Assessment feature and related governed analytics screens required by source-supported stories
- [ ] Render the Compatibility Assessment screen with required platforms in scope, editable statuses/notes, latest saved result on reopen, submission states, retry actions, access-denied state, validation messages, and platform-specific error states
- [ ] Provide a GPT-style chat experience for governed NLQ with authenticated session context, text input, submit action, response area, validation messaging, unsupported-query messaging, and preserved conversation history where source-supported
- [ ] Provide conversational UI states for listening, transcribing, processing, success, delay, retry, completed playback, and error for voice-enabled stories without exposing technical stack traces
- [ ] Ensure Slack-oriented flows produce user-safe message formats with readable summaries, labeled metrics/comparisons, governed denial messages, and recoverable delivery-failure messaging
- [ ] Render trend, variance, comparative, and visualization outputs with clear labels for KPI, periods, comparison basis, values, variance, source/lineage context where required, no-data states, and unsupported-visualization states
- [ ] Preserve user-entered or transcribed content during validation failures, retriable failures, and delay/error states where source-supported
- [ ] Keep original governed answer content visible when visualization or voice playback fails, and do not replace it with empty, partial, or broken UI
- [ ] Provide admin/governance/review screens or views required by source-supported stories for policy management, audit review, governance evidence, routing rules, environment configuration, release validation, spend/consumption visibility, and model override management
- [ ] Ensure administrative secondary-channel UI loads from its dedicated route for authorized admins, shows only approved administrative elements, redirects unauthenticated users to sign-in, and displays retryable non-technical error states when config/content load fails
- [ ] Implement explicit blocked, denied, unavailable, and incomplete statuses in review/admin/proposal/delivery screens instead of blank sections or misleading success indicators
- [ ] Meet source-supported accessibility and clarity expectations for readable labels, inline validation, controlled messages, and state changes suitable for executive, manager, admin, reviewer, and support personas
- [ ] Follow existing design-system, responsive layout, and local UI interaction conventions for web delivery, including consistent formatting of forms, tables, timelines, audit views, chat surfaces, and analytics cards

## API and Integration Acceptance Criteria

- [ ] Implement backend operations needed for Compatibility Assessment create/read/execute/retry flows with validation, persistence, RBAC enforcement, and correlation-aware logging
- [ ] Implement governed conversational query APIs/services that validate identity, role, approved-source routing, supported intent, scope authorization, and response guardrails before any Gold layer retrieval or answer generation
- [ ] Ensure conversational services fail closed when RBAC context, source approval, dataset mapping, lineage validation, semantic readiness, or policy dependencies cannot be resolved
- [ ] Implement shared platform-level services appropriate for the microservices architecture, including source-resolution, authorization/compatibility-layer enforcement, guardrail evaluation, audit/event capture, and routing/configuration services where source-supported
- [ ] Implement Microsoft Fabric Gold layer integration only through approved mapped datasets, semantic models, views, and environment-specific bindings; block fallback to application tables, ad hoc files, unmanaged datasets, or non-Gold sources
- [ ] Implement Azure OpenAI integration compatibility handling so valid configuration supports a compatibility call and deterministic parsed result, while config errors, throttling, timeout, authorization, and schema failures map to controlled outcomes
- [ ] Implement Slack integration behaviors for approved message posting, request handling, signed-request verification, user mapping, response delivery, delivery failure classification, and compatibility validation where source-supported
- [ ] Implement voice integration endpoints/services for transcription and playback with authentication, input validation, session/request binding, and no unauthorized reuse of another user’s response/request identifiers
- [ ] Implement visualization endpoints/services with responseId lineage validation, visual type validation, dataset approval checks, and RBAC enforcement before render payload generation
- [ ] Implement routing-rule, model-selection, fallback-routing, and cost/performance telemetry services so routing decisions use only approved providers/targets, preserve user-facing model-agnostic behavior, and record rule/provider/outcome metadata
- [ ] Implement admin, governance, and audit retrieval APIs with least-privilege access, filtered retrieval where source-supported, append-only or immutable behavior where required, and no exposure of restricted response content by default
- [ ] Implement deployment/promotion/environment configuration APIs/services so Dev/QA/Production settings are separated, promotion order is enforced, hard-coded environment references are rejected, and release evidence/audit outcomes are captured
- [ ] Keep contracts backward-compatible unless a source-supported requirement explicitly requires a new or changed contract behavior

## Business Logic and Data Acceptance Criteria

- [ ] Persist Compatibility Assessment records with platformName, compatibilityStatus, assessmentNotes, assessedAt, assessedBy, overallOutcome, timestamping, and latest-result retrieval behavior
- [ ] Enforce approved enums, statuses, and allowed values for compatibility statuses, query outcomes, audit outcomes, phase-gate decisions, pricing model states, proposal readiness states, model override states, and delivery/review statuses where source-supported
- [ ] Implement Gold layer allowlist and semantic object governance so all in-scope analytics answers, visuals, and predictive outputs are traceable to approved Microsoft Fabric Gold layer assets
- [ ] Implement compatibility-layer RBAC preservation for Agent, Manager, and Executive roles so legacy baseline allow/deny/redaction behavior is preserved across NLQ, trend, variance, comparative, dashboard/detail, Slack, voice, and visual flows
- [ ] Enforce object-level, field-level, row-level, and scope-level restrictions where source-supported, including dataset/measure/dimension/row scope restrictions, Manager vs Executive differences, Agent compatibility constraints, and restricted customer/PII field handling
- [ ] Implement prompt-side and response-side protection for PII and restricted fields so configured excluded/masked fields do not appear in prompts, responses, charts, metadata, or audit payloads beyond approved masked/sanitized representations
- [ ] Enforce failure-closed behavior when a field is classified as PII but no valid masking rule exists, when RBAC or policy context is unresolved, or when sanitization/guardrail services fail
- [ ] Persist auditable records for successful, denied, blocked, declined, qualified, redacted, masked, retry, visualization, voice, Slack, routing, semantic, policy, and deployment events with the required metadata per source-supported story
- [ ] Keep audit/event stores append-only or immutable where source-supported, preserve version history for policy/config/governance changes, and prevent silent modification/deletion through application workflows
- [ ] Implement required commercial and governance data structures in scope, including policy/config entities, governance evidence records, phase checkpoints, proposal dependencies, billing rows, optional pricing items, SOW records, routing rules, environment bindings, semantic readiness records, and compatibility validation artifacts
- [ ] Enforce proposal and governance business rules in scope, including fixed-price-only core engagement submission, dependency statement/time-commitment requirements, required proposal sections/content, readiness/completeness gating, signature-before-work-start gating, and phase-gate blocking rules
- [ ] Handle edge cases explicitly, including missing latest assessment, no prior-year comparison data, empty result sets, missing lineage, missing approved mapping, duplicate active rules, invalid period pairs, invalid responseId/requestId, unmapped Slack identity, unsupported visualization structures, and provider fallback exhaustion

## Non-Functional Acceptance Criteria

- [ ] Enforce authentication, RBAC, least privilege, secure transport, sanitized errors, and protected storage for all governed analytics, admin, audit, proposal, governance, deployment, and compatibility workflows in scope
- [ ] Ensure unauthorized, denied, and blocked responses never expose secrets, raw exceptions, restricted dataset names where not permitted, restricted values, semantic object identifiers where prohibited, or hidden operational details
- [ ] Provide structured observability with correlation identifiers for request processing, authorization, source resolution, validation failure, provider routing, fallback attempts, delivery outcomes, audit writes, and deployment/configuration changes
- [ ] Meet source-supported response-time targets, including latest assessment retrieval within 2 seconds, conversational responses within 3 to 5 seconds where specified, voice flows within specified thresholds, admin views within specified thresholds, validation runs and deployment checks within specified thresholds, and trend/variance views within specified self-service windows
- [ ] Use efficient metadata caching, bounded retries, fallback routing, indexed retrieval, and environment-safe configuration refresh only where needed to satisfy performance targets without bypassing governance checks
- [ ] Ensure reliability behaviors cover retryable compatibility checks, controlled delay messages, replay support, safe degradation when policy/dependency services fail, and no silent request loss or duplicate-answer behavior under normal operating conditions
- [ ] Apply TDD-oriented implementation and verification for high-risk behaviors, especially RBAC preservation, approved-source enforcement, guardrails, PII masking, audit immutability, Slack/voice/visual governance, routing fallback, and deployment gating
- [ ] Follow applicable architecture, coding, review/approval, testing/QE, and security/responsible-AI guidance from the Golden Repo as implementation constraints where the source backlog already defines the product behavior they govern
- [ ] Do not invent undocumented per-service contracts, unsupported external certification/live checks, or additional product scope beyond the consolidated platform-level microservices and shared-service needs supported by source stories

## Traceability

- [ ] Every implemented change maps back to source-supported feature/user-story functional requirements, acceptance criteria, success metrics, user flows, or technical considerations in the provided context
- [ ] Every non-blocking Open Question that was implemented has a recorded decision + one-line rationale in specs/<slug>/assumptions.md (no Open Question is silently assumed)
- [ ] No BLOCKING Open Question was implemented as an assumption (a feature with an unresolved blocking question is held at needs-clarification, not completed)

## Notes

- Do not silently assume unresolved details such as exact approved enums, role matrices, baseline RBAC mappings, approved dataset identifiers, policy versions, latency thresholds where only described qualitatively, approved provider registries, environment IDs, or required audit payload schemas if they are not concretely defined in source context; record assumptions or hold for clarification as appropriate.
- Where multiple stories describe overlapping governed analytics behavior, implement one consistent shared enforcement path and verify that each persona/channel-specific acceptance criterion is still observable.
- Mark an item complete only after verifying actual implementation code and behavior.