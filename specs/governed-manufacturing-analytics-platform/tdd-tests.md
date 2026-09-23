# TDD Test Specifications: Compatibility Assessment for Required Platforms

## Overview
These tests define the backend-first TDD plan for Feature 901191011, focused on:
- compatibility assessment APIs and service logic for Microsoft Fabric, Azure OpenAI, and Slack
- governed NLQ/analytics access using approved Gold layer data only
- RBAC, auditability, validation, source allowlisting, and controlled failure handling
- shared platform-level services in a microservices architecture

TDD approach:
1. Write failing tests per acceptance criterion.
2. Implement the minimum code to pass.
3. Refactor only after green, preserving behavior and audit/security constraints from Golden Repo guidance.

## Unit Test Specifications

### Compatibility Assessment Validation & Domain Rules
- **Test:** rejects assessment submission when any required platform is missing
  - **Given:** an authorized user submits an assessment payload missing one of Microsoft Fabric, Azure OpenAI, or Slack
  - **When:** the request is validated
  - **Then:** validation fails with field-level errors and no platform check starts
  - **Priority:** High
  - **TDD Phase:** Red: failing validator test for missing required platform set; Green: add required-platform completeness rule; Refactor: extract shared enum/platform-set validator if reused 3+ times

- **Test:** rejects invalid compatibility status values
  - **Given:** an assessment payload contains a status outside the approved enum
  - **When:** validation runs
  - **Then:** validation fails for the specific platform field and processing stops before external checks
  - **Priority:** High
  - **TDD Phase:** Red: failing enum validation test; Green: add approved status rule; Refactor: centralize status enum mapping

- **Test:** computes overall assessment outcome from platform statuses
  - **Given:** valid statuses for all three required platforms
  - **When:** the assessment service evaluates the request
  - **Then:** it returns per-platform statuses and a deterministic overall outcome
  - **Priority:** High
  - **TDD Phase:** Red: failing service rule test for outcome derivation; Green: implement minimal outcome logic; Refactor: isolate outcome policy object

- **Test:** preserves unchanged valid inputs for retryable failed platform checks
  - **Given:** a prior valid assessment request with one retryable platform failure
  - **When:** a retry is requested
  - **Then:** the new attempt reuses unchanged valid inputs without requiring re-entry
  - **Priority:** Medium
  - **TDD Phase:** Red: failing retry state test; Green: implement input reuse; Refactor: separate attempt-state handling

### Compatibility Assessment Authorization & Logging
- **Test:** denies assessment access for unauthenticated or unauthorized users
  - **Given:** a request from a user missing the required role or session
  - **When:** access is evaluated
  - **Then:** access is denied, no assessment data is returned, and no external platform calls start
  - **Priority:** High
  - **TDD Phase:** Red: failing auth guard test; Green: implement deny-before-execute; Refactor: unify shared RBAC guard contract

- **Test:** creates structured authorization or failure audit/log event for failed assessment attempts
  - **Given:** an unauthorized request or platform failure
  - **When:** the attempt is handled
  - **Then:** a structured event is produced with feature context, target platform, failure type, timestamp, and correlationId
  - **Priority:** High
  - **TDD Phase:** Red: failing audit payload test; Green: emit required fields; Refactor: extract audit event factory

- **Test:** sanitizes platform-specific error messages
  - **Given:** a platform check throws a raw exception
  - **When:** the error is mapped for response
  - **Then:** the message exposes only failure category, not raw exception text or secrets
  - **Priority:** High
  - **TDD Phase:** Red: failing error-sanitization test; Green: implement category mapper; Refactor: reuse controlled error model

### Compatibility Assessment Persistence
- **Test:** persists successful assessment with required fields
  - **Given:** a successful assessment result
  - **When:** persistence is invoked
  - **Then:** platformName, compatibilityStatus, assessmentNotes, assessedAt, assessedBy, and overallOutcome are stored
  - **Priority:** High
  - **TDD Phase:** Red: failing repository contract test; Green: persist required fields only; Refactor: isolate persistence mapper

- **Test:** returns latest saved assessment on reopen
  - **Given:** multiple prior assessment records exist
  - **When:** latest assessment retrieval is requested
  - **Then:** the most recent saved result is returned efficiently
  - **Priority:** High
  - **TDD Phase:** Red: failing latest-record selection test; Green: implement latest-by-timestamp retrieval; Refactor: optimize query abstraction

### Approved Gold Layer Source Resolution
- **Test:** allows answer generation only when resolved dataset is in approved Gold layer allowlist
  - **Given:** a valid NLQ request whose subject resolves to an approved Gold layer dataset
  - **When:** source resolution runs
  - **Then:** the request is allowed to continue with the approved dataset identifier
  - **Priority:** High
  - **TDD Phase:** Red: failing allowlist resolution test; Green: implement approved-source check; Refactor: encapsulate allowlist policy

- **Test:** blocks answer generation when query requires unapproved or non-Gold source
  - **Given:** a request resolving to an unapproved, unknown, or non-Gold dataset
  - **When:** source policy is evaluated
  - **Then:** the request is denied before query execution and returns a governed message without source leakage
  - **Priority:** High
  - **TDD Phase:** Red: failing deny-path test; Green: add fail-closed source check; Refactor: share denial response builder

- **Test:** fails closed when source registry or mapping cannot be evaluated
  - **Given:** source allowlist metadata is unavailable or unresolved
  - **When:** request validation runs
  - **Then:** execution is blocked and a controlled failure outcome is returned
  - **Priority:** High
  - **TDD Phase:** Red: failing dependency-unavailable test; Green: implement fail-closed behavior; Refactor: separate registry gateway from policy logic

### NLQ Request Validation & Intent Handling
- **Test:** rejects empty, whitespace-only, or overlength plain-English questions
  - **Given:** an NLQ request with blank or over-limit text
  - **When:** request validation runs
  - **Then:** validation fails and no Gold layer query or model request is executed
  - **Priority:** High
  - **TDD Phase:** Red: failing input-boundary tests; Green: implement trim and length rules; Refactor: centralize NLQ input validator

- **Test:** rejects unsupported or unmappable analytics intent
  - **Given:** a query that cannot be mapped to supported KPI, trend, variance, or comparative intent
  - **When:** intent resolution runs
  - **Then:** a validation or clarification result is returned and no downstream query is executed
  - **Priority:** High
  - **TDD Phase:** Red: failing unsupported-intent test; Green: implement supported-intent gate; Refactor: isolate intent classifier interface

- **Test:** classifies supported comparative phrases consistently
  - **Given:** month-over-month, quarter-over-quarter, or year-over-year phrasing
  - **When:** intent classification runs
  - **Then:** the correct comparison type is resolved for downstream analytics
  - **Priority:** Medium
  - **TDD Phase:** Red: failing comparative phrase mapping tests; Green: add supported mappings; Refactor: normalize comparison phrase catalog

### RBAC Compatibility Layer
- **Test:** resolves preserved role mapping before NLQ or analytics execution
  - **Given:** an authenticated Agent, Manager, or Executive request
  - **When:** compatibility-layer authorization runs
  - **Then:** the legacy/baseline role mapping is resolved before any Gold layer query
  - **Priority:** High
  - **TDD Phase:** Red: failing role-resolution test; Green: implement pre-query role mapping; Refactor: decouple identity resolution from policy evaluation

- **Test:** denies restricted metrics, dimensions, or scopes without leaking restricted metadata
  - **Given:** a request for Manager-only or Executive-only content by a lower-tier role
  - **When:** policy evaluation runs
  - **Then:** the request is denied or redacted without returning restricted values, field names, or semantic object identifiers
  - **Priority:** High
  - **TDD Phase:** Red: failing non-leakage authorization test; Green: implement governed deny/redact behavior; Refactor: share restricted-content sanitizer

- **Test:** produces consistent authorization outcomes across equivalent channels
  - **Given:** equivalent dashboard, NLQ, Slack, voice, or visual requests for the same governed resource
  - **When:** authorization is evaluated
  - **Then:** the same allow/deny decision is produced for the same role and scope
  - **Priority:** High
  - **TDD Phase:** Red: failing parity test across channels; Green: route all channels through shared policy; Refactor: eliminate duplicated channel-specific auth logic

### Auditability & Immutable Governance Events
- **Test:** writes audit record for each NLQ request with required metadata
  - **Given:** an answered or denied NLQ request
  - **When:** audit persistence runs
  - **Then:** user identity, role, original/normalized query, resolved dataset, timestamp, correlationId, and final outcome are stored
  - **Priority:** High
  - **TDD Phase:** Red: failing audit schema test; Green: persist required fields; Refactor: common audit DTO builder

- **Test:** omits restricted prompt/response content from security/governance audit records
  - **Given:** a denied, blocked, redacted, or sensitive request
  - **When:** audit payload is generated
  - **Then:** raw restricted content and secrets are excluded or sanitized
  - **Priority:** High
  - **TDD Phase:** Red: failing sanitization test; Green: strip disallowed fields; Refactor: use shared sanitization policy

- **Test:** prevents duplicate or conflicting audit/result records where uniqueness is required
  - **Given:** the same unique business key is submitted twice
  - **When:** persistence is attempted
  - **Then:** duplicate conflicting records are rejected or deduplicated according to requirement
  - **Priority:** Medium
  - **TDD Phase:** Red: failing uniqueness test; Green: add persistence guard; Refactor: repository-level uniqueness abstraction

## Integration Test Specifications

### Compatibility Assessment API + Service + Repository
- **Test:** GET latest assessment returns required platforms in scope and latest persisted result
  - **Given:** an authorized user and existing saved assessment
  - **When:** the feature retrieval endpoint is called
  - **Then:** the response includes Microsoft Fabric, Azure OpenAI, Slack, and the latest saved assessment data
  - **Priority:** High

- **Test:** POST assessment with valid payload returns per-platform result and persists assessment
  - **Given:** an authorized user and a valid all-platform request
  - **When:** the assessment endpoint is executed
  - **Then:** the response returns platform statuses plus overall outcome, and the result is stored with timestamp and user identity
  - **Priority:** High

- **Test:** POST assessment with invalid payload returns field-level validation and skips platform integrations
  - **Given:** a malformed or incomplete assessment request
  - **When:** submission is attempted
  - **Then:** a validation response is returned, user-entered values are preserved in the response model where applicable, and no platform check adapter is invoked
  - **Priority:** High

### NLQ Governance Flow
- **Test:** governed NLQ request routes only through approved Gold layer source and returns auditable answer
  - **Given:** an authenticated authorized Executive or Manager with a supported question
  - **When:** the NLQ endpoint is called
  - **Then:** the request resolves to an approved Gold layer dataset, returns a governed answer, and creates an audit record
  - **Priority:** High

- **Test:** out-of-scope or unapproved-source NLQ request is blocked before query execution
  - **Given:** a request requiring unapproved data or unsupported subject matter
  - **When:** the NLQ endpoint is called
  - **Then:** the response is governed denial/unsupported, no query is executed, and a denial audit record is stored
  - **Priority:** High

- **Test:** unauthorized role request is denied consistently across text, Slack, and voice channels
  - **Given:** equivalent requests from a user lacking required scope
  - **When:** each channel-specific endpoint is called
  - **Then:** each request is denied before data retrieval and a consistent authorization event is recorded
  - **Priority:** High

### Compatibility Layer + Baseline RBAC Validation
- **Test:** validation run compares actual authorization outcomes to approved baseline fixtures
  - **Given:** an admin-authorized validation run request with environmentId, baselineVersion, and fixtureSetId
  - **When:** the validation execution endpoint runs
  - **Then:** Agent, Manager, and Executive outcomes across NLQ/trend/variance/comparative are compared to baseline and persisted in a validation report
  - **Priority:** High

- **Test:** critical RBAC mismatches block run status and deployment recommendation
  - **Given:** a validation run with at least one critical discrepancy
  - **When:** report finalization occurs
  - **Then:** the run status is blocked and deployment recommendation indicates remediation required
  - **Priority:** High

### Source Configuration & Runtime Enforcement
- **Test:** approved Gold layer environment configuration saves and is used at runtime
  - **Given:** a valid source configuration with workspaceId, itemId, and approved mappings
  - **When:** configuration is saved and a governed request is executed
  - **Then:** runtime source resolution uses the saved Gold layer mapping and logs the resolution outcome
  - **Priority:** High

- **Test:** invalid source configuration fails save and prevents runtime fallback
  - **Given:** a configuration pointing to a non-Gold or unmapped source
  - **When:** configuration save or deployment validation is attempted
  - **Then:** validation fails with specific field errors and runtime requests are blocked rather than falling back
  - **Priority:** High

## Acceptance Test Scenarios

### US 3352 / US 2040 Compatibility Assessment for Required Platforms
- **Scenario:** authorized user opens feature and sees required platforms in scope
  - **Given:** an authorized user with feature access
  - **When:** the compatibility assessment is opened
  - **Then:** Microsoft Fabric, Azure OpenAI, and Slack are included in scope and latest saved result is returned if present

- **Scenario:** valid assessment submission returns statuses and persists latest result
  - **Given:** an authorized user submits a valid assessment for all required platforms
  - **When:** the request is processed
  - **Then:** per-platform status and overall outcome are returned and persisted with notes, timestamp, and user identity

- **Scenario:** invalid assessment payload is rejected before any platform checks start
  - **Given:** a request missing a required platform or containing invalid status
  - **When:** submission is attempted
  - **Then:** the request is rejected with field-level validation errors and no platform checks are started

- **Scenario:** unauthorized user is denied without data exposure
  - **Given:** a user without required role
  - **When:** the user attempts to access or execute the assessment
  - **Then:** the action is denied, no assessment data is exposed, and an authorization event is recorded

- **Scenario:** retryable platform failure produces controlled platform-specific error and retry path
  - **Given:** a valid request where one platform check fails with a retryable category
  - **When:** the failure is returned
  - **Then:** a sanitized platform-specific error is returned, unchanged valid inputs remain reusable, and the failed attempt is logged with correlationId

### US 2155 / US 2635 / US 2245 / US 2365 Governed Executive NLQ
- **Scenario:** Executive gets answer only from approved Gold layer dataset
  - **Given:** an authenticated Executive submits a supported manufacturing question
  - **When:** the query is resolved
  - **Then:** the answer is generated only from an approved Gold layer dataset and the audit record captures source identifier and outcome

- **Scenario:** unapproved or out-of-scope source is blocked
  - **Given:** a question requiring unapproved or non-Gold data
  - **When:** the request is evaluated
  - **Then:** answer generation is blocked, a governed denial is returned, and no unapproved source details are exposed

- **Scenario:** invalid or unsupported Executive question is rejected before query execution
  - **Given:** an empty, overlength, or unsupported-intent question
  - **When:** the request is submitted
  - **Then:** validation fails and no Gold layer query is executed

### US 2166 / US 2253 Responsible AI Guardrails for Manager Answers
- **Scenario:** Manager receives answer only when guardrails pass
  - **Given:** an authenticated Manager submits a supported trend, variance, or comparative question
  - **When:** the answer pipeline and guardrails execute
  - **Then:** a governed answer is returned only if grounding and policy checks pass, with lineage metadata included

- **Scenario:** unsupported or partially supported question is declined or qualified
  - **Given:** a Manager question with insufficient or partial Gold layer evidence
  - **When:** support coverage is evaluated
  - **Then:** the system returns a declined or qualified response with no fabricated claims and writes an auditable guardrail event

### US 3320 / US 3328 / US 2216 / US 2226 / US 2223 Preserved RBAC
- **Scenario:** compatibility layer preserves baseline role behavior
  - **Given:** Agent, Manager, or Executive submits an analytics request
  - **When:** the compatibility layer resolves permissions
  - **Then:** only baseline-approved datasets, measures, dimensions, and scopes are permitted for that role

- **Scenario:** restricted content request is denied without leakage
  - **Given:** a role requests higher-tier metrics or out-of-scope entities
  - **When:** authorization runs
  - **Then:** the system denies or redacts the response without exposing restricted values or semantic identifiers

### US 3337 / US 3345 RBAC Validation and Regression
- **Scenario:** admin runs RBAC baseline validation
  - **Given:** an authenticated admin provides valid validation run inputs
  - **When:** the run is executed
  - **Then:** results are compared to baseline, mismatches are classified, reports are persisted, and critical discrepancies block promotion

### US 3024 / US 3033 Approved Gold Layer Configuration and Runtime Use
- **Scenario:** configured Gold layer source is used for governed analytics
  - **Given:** a valid environment-specific Gold layer mapping
  - **When:** a governed analytics request is processed
  - **Then:** the request resolves to the configured Gold layer asset only, with lineage/audit metadata recorded

- **Scenario:** missing or invalid Gold layer mapping blocks runtime execution
  - **Given:** no valid mapping exists for the requested subject area
  - **When:** runtime resolution is attempted
  - **Then:** execution is blocked with governed failure and no fallback to alternate datasets occurs

## Test-First Development Guidelines
1. Write validation failure tests first for compatibility assessment payloads and NLQ requests.
2. Write authorization-denial tests next to prove fail-closed behavior before business logic.
3. Write approved-source allowlist tests before any query execution logic.
4. Write success-path compatibility assessment tests for valid all-platform submissions.
5. Write audit/log persistence tests for allow, deny, validation-fail, and platform-fail outcomes.
6. Write compatibility-layer baseline RBAC tests across Agent/Manager/Executive.
7. Write integration tests that prove no external/platform/query call occurs on validation/auth failure.
8. Write regression validation report tests for admin-run baseline comparisons.

Implementation sequence recommendations:
1. Request validators
2. RBAC/identity resolution guard
3. Gold layer/source approval policy
4. Compatibility assessment domain service
5. Audit event builder and persistence
6. Latest-result retrieval/persistence
7. Shared compatibility-layer authorization service
8. Validation-run/reporting service

Refactoring considerations:
- Apply fail-closed patterns for security, source validation, and routing.
- Keep business rules in domain services, not controllers.
- Extract shared validators/policies only after the Rule of Three.
- Keep audit payload construction centralized to avoid field drift.
- Re-run full suite after each refactor step; do not refactor on red.

## Edge Cases & Boundary Tests
- Boundary condition tests
  - Maximum allowed NLQ length accepted; one character over rejected
  - Exactly three required compatibility platforms accepted; any missing/extra malformed platform entry rejected
  - Latest-assessment retrieval with no prior records returns empty/default governed state
  - Invalid comparison period or unsupported comparison type rejected before analytics execution

- Error handling tests
  - Source registry unavailable causes fail-closed denial
  - RBAC service unavailable causes fail-closed denial
  - External platform compatibility adapter timeout/error maps to sanitized platform-specific category
  - Audit write missing mandatory fields is rejected and surfaced through operational error handling
  - Missing baseline fixtures or invalid validation-run inputs return validation error and no run starts

- Concurrency/timing tests (if applicable)
  - Concurrent retries for the same assessment do not create conflicting latest-result state
  - Duplicate validation-run submission with same correlation/request key is safely handled once or rejected deterministically
  - Latest-result retrieval under normal load meets 2-second target
  - Authorized NLQ summary response path stays within stated seconds-level target using cached metadata where applicable