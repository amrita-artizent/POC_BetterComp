# Feature: Compatibility Assessment for Required Platforms; As a user, I want to use Compatibility Assessment for Required Platforms so that I can benefit from its core functionality; As an Executive user, I want to ask plain-English questions against approved Gold layer data so that I can get governed manufacturing insights without using unapproved datasets (+190 more)
Status: NEW
Owner: Astra
Last Updated: 2026-09-23

## Summary
This feature delivers a governed Compatibility Assessment capability for required platforms within a web application using a microservices architecture. The capability allows authorized users to open a compatibility assessment view, assess compatibility for Microsoft Fabric, Azure OpenAI, and Slack APIs, and persist the resulting platform-by-platform statuses and overall outcome for later retrieval.

The feature also sits within a broader governed analytics context in which approved Microsoft Fabric Gold layer data, role-based access control, auditability, and safe error handling are required. The core business problem addressed by the primary in-scope user stories is the lack of a structured, governed workflow to evaluate required platform compatibility and to present saved assessment outcomes consistently.

Expected outcomes are:
- Authorized users can access the feature through the UI.
- The system displays the three required platforms in scope.
- Users can submit valid assessment inputs and receive a compatibility result per platform plus an overall assessment outcome.
- Invalid or unauthorized requests are blocked safely before external platform checks begin.
- Successful assessments are saved and restored when the feature is reopened.
- Failed and denied attempts are logged with structured observability data.

## Scope
### In Scope
Based on US 2040 and US 3352, this feature includes:
- A web-accessible Compatibility Assessment feature.
- Display of Microsoft Fabric, Azure OpenAI, and Slack APIs as the required platforms in scope.
- Submission and processing of compatibility assessment requests covering all three required platforms.
- Display of platform-level compatibility statuses and an overall assessment outcome.
- Validation that all required platforms are present in the assessment payload.
- Validation that platform status values are restricted to an approved enum.
- Persistence of assessment results including platform statuses, notes, timestamp, user identity, and overall outcome.
- Retrieval of the latest saved assessment result when reopening the feature.
- Role-based access control for assessment execution and retrieval.
- Denial of unauthorized or unauthenticated access without exposing assessment data.
- Structured logging of requests, validation failures, authorization denials, failed attempts, and completion outcomes with correlation IDs.
- Retry of retryable failed assessment attempts without requiring re-entry of unchanged valid inputs.
- Platform-specific error messaging for compatibility check failures that avoids exposing raw exceptions or secrets.

### Out of Scope
Explicitly out of scope from US 3352 and US 2040:
- Detailed remediation workflows for incompatible platforms.
- Advanced customization of assessment rules.
- User-specific settings.
- Automated external certification.
- Live integration testing against vendor-owned Microsoft Fabric, Azure OpenAI, or Slack environments.
- Full service-by-service decomposition beyond platform-level behavior for this consolidated specification pass.

## Application Type & Platform Context
- **Primary target application type:** web  
  - Source evidence: Feature metadata states “User-selected Application Types: web.”
- **Architecture style:** microservices  
  - Source evidence: Feature metadata states “User-selected Architecture Style: microservices.”
- **Delivery order context:** UI-first  
  - Source evidence: Feature metadata states “User-selected Delivery Order: ui-first.”

The user story acceptance criteria and interaction flow explicitly require UI access and a visible assessment experience, which confirms a web UI context. The technical considerations also imply backend/service support for validation, persistence, logging, and authorization.

## Actors and Permissions
### Actors
- **Authorized user / System User**
  - Can open the Compatibility Assessment feature.
  - Can view the assessment area.
  - Can submit a compatibility assessment request.
  - Can view the latest saved assessment result.
- **Unauthorized user / user without required role**
  - Must be denied access to view or execute the assessment.
  - Must not receive assessment data.
  - Must trigger an authorization event log entry.

### Permissions and Access Constraints
- Role-based access control is required for:
  - Assessment retrieval.
  - Assessment execution.
- Unauthenticated requests must be rejected.
- Unauthorized role access must be denied before external platform calls are initiated.
- Authorization denials must display an authorization message to the user but must not expose assessment data.
- Authorization denials must be recorded as authorization events.

### Open Permission Detail
The exact role name(s) allowed to execute or retrieve compatibility assessments are not specified in source material and require clarification.

## Feature Development Intent
This is feature-development work to implement the Compatibility Assessment for Required Platforms capability described in US 2040 and US 3352.

Behavior to be built or changed:
- Add a UI-accessible assessment area.
- Add request validation and submission handling for all required platforms.
- Add compatibility result generation and display for Microsoft Fabric, Azure OpenAI, and Slack APIs.
- Add persistence and retrieval of the latest assessment result.
- Add role-based access enforcement for assessment access and execution.
- Add retry handling for retryable failures.
- Add structured observability and error logging for all major request outcomes.

The delivered outcome must be a governed, testable compatibility assessment workflow that replaces informal interpretation with a structured result set and durable record.

## UI Design & Interaction Contract
### Required UI Entry and View
When an authorized user opens the Compatibility Assessment feature, the system shall display an assessment view that includes:
- Microsoft Fabric
- Azure OpenAI
- Slack APIs

These are the required platforms in scope and must be visible in the assessment view.

### Initial Load Behavior
When the feature is opened:
- The system loads the latest saved assessment if one exists.
- If no saved assessment exists, the system shows an empty assessment state.

### Submission Behavior
When the user submits a valid request covering all required platforms:
- The system displays a compatibility status for each required platform.
- The system displays an overall assessment outcome.
- The result view includes summary notes as supported by the story description.

### Validation Behavior
If required assessment inputs are missing or malformed:
- Submission is blocked.
- Specific field errors are shown.
- User-entered data is preserved.
- Validation occurs before any Microsoft Fabric, Azure OpenAI, or Slack API check starts.

If any required platform entry is missing:
- The system rejects the submission.
- The system preserves entered data.
- The system shows field-level validation messages.

If any platform contains an invalid status value:
- The system rejects the submission.
- The system preserves entered data.
- The system shows field-level validation messages.

### Error Behavior
If a compatibility check fails for Microsoft Fabric, Azure OpenAI, or Slack APIs:
- The UI displays a platform-specific error message.
- The message describes the failure category.
- The message must not expose raw exception details or secrets.

If a failure is retryable:
- The user is provided a retry action.
- A new assessment attempt is executed.
- Unchanged valid inputs do not need to be re-entered.

If the user lacks permission:
- The UI displays an authorization message.
- No external platform calls are initiated.

### UX/Content Constraints
- Controlled error and authorization messages are required.
- Raw backend exceptions, secrets, and sensitive diagnostic details must not be exposed to users.
- Edge cases must be handled gracefully.

### Accessibility
No explicit accessibility standard or requirement is stated in source context.

## API Contract
Source context supports backend/service behaviors but does not define named endpoints for this feature.

### Supported Service Operations
The system must support:
- Retrieval of the latest saved compatibility assessment result.
- Submission of a compatibility assessment request covering all required platforms.
- Validation of request completeness and status enum values.
- Authorization checks prior to retrieval or execution.
- Persistence of successful assessment results.
- Logging of request, validation, authorization, and failure outcomes.
- Retry execution for retryable failures.

### Request Inputs
Supported input elements from source:
- Assessment payload that includes all three required platforms:
  - Microsoft Fabric
  - Azure OpenAI
  - Slack APIs
- Platform-specific status values constrained to an approved enum.
- Notes.
- User identity derived from authenticated context.

### Outputs
For successful assessment execution:
- Compatibility status for each required platform.
- Overall assessment outcome.
- Persisted assessment record available for later retrieval.

For retrieval:
- Latest saved result, if available.

### Validation Errors
The system must reject requests when:
- Any required platform entry is missing.
- A platform status value is invalid.
- Required assessment inputs are missing.
- Inputs are malformed.

Validation errors must:
- Identify the specific field errors.
- Occur before any platform check starts.

### Authorization Errors
If the requester is unauthenticated or lacks the required role:
- The action is denied.
- No assessment data is returned.
- No external platform calls are initiated for execute attempts.
- An authorization event is recorded.

### Failure Handling
For platform check failures:
- The system returns a platform-specific failure category suitable for UI display.
- Raw exceptions and secrets are not exposed.

For retryable failures:
- A new assessment attempt may be executed without requiring re-entry of unchanged valid inputs.

### Idempotency
No explicit idempotency contract is provided in source context.

### External Integration Behavior
Source context references Microsoft Fabric, Azure OpenAI, and Slack APIs as required platforms in scope for compatibility assessment. However, detailed live integration behavior is explicitly out of scope where it would imply automated external certification or live vendor-environment testing.

## Business Logic & Rules
- The required platforms in scope are fixed for this feature:
  - Microsoft Fabric
  - Azure OpenAI
  - Slack APIs
- Every completed assessment must include explicit outcomes for all three required platforms.
- The system must calculate or determine:
  - Platform-specific compatibility outcomes.
  - Overall assessment outcome.
- Allowed platform status values are limited to:
  - compatible
  - partially compatible
  - incompatible
  - requires review
  Source support: story description says “such as,” but technical considerations require restriction to an approved enum. Exact enum labels need confirmation if different canonical values exist.
- Submission is blocked if:
  - Any required platform is absent from the payload.
  - Any status value is invalid.
  - Required inputs are missing or malformed.
- Validation occurs before any platform check starts.
- Only authorized users may retrieve or execute the assessment.
- Unauthorized access must fail closed and must not reveal assessment data.
- A successful assessment must be persisted with latest-result retrieval behavior on reopen.
- Failed attempts must produce structured error logs containing:
  - story feature context
  - target platform
  - failure type
  - timestamp
  - correlation identifier
- Retryable failures must support retry without re-entry of unchanged valid inputs.

## Data Model & Validation
### Assessment Record
Source-supported fields:
- `platformName`
- `compatibilityStatus`
- `assessmentNotes`
- `assessedAt`
- `assessedBy`
- `overallOutcome`

### Validation Rules
- All three required platforms must be represented in the assessment payload.
- `compatibilityStatus` must be restricted to an approved enum.
- Missing required inputs must be rejected.
- Malformed inputs must be rejected.
- Invalid status values must be rejected.
- User-entered data must be preserved on validation failure.

### Persistence Rules
- Successful assessment results must be stored in the application database.
- Latest saved result must be retrievable when the feature is reopened.

### Audit / Log Data
Structured logs and audit-like records for failed or denied attempts must include, where supported:
- correlationId
- timestamp
- feature/story context
- target platform
- failure type
- authorization outcome for denied access

### Data Retention
Retention policy is not specified in source context.

## Functional Requirements
1. The system shall provide a web-accessible Compatibility Assessment feature entry point for authorized users.  
2. The system shall display an assessment view containing Microsoft Fabric, Azure OpenAI, and Slack APIs as the required platforms in scope.  
3. When the feature is opened, the system shall load the latest saved assessment result if one exists; otherwise it shall display an empty assessment state.  
4. The system shall require a submitted assessment request to include entries for Microsoft Fabric, Azure OpenAI, and Slack APIs.  
5. The system shall validate required assessment inputs before starting any Microsoft Fabric, Azure OpenAI, or Slack platform check.  
6. The system shall reject a submission when any required platform entry is missing.  
7. The system shall restrict compatibility status values to an approved enum and reject any invalid status value.  
8. When validation fails, the system shall preserve user-entered data and return field-level validation messages identifying specific field errors.  
9. When a valid assessment request covering all required platforms is submitted, the system shall return a compatibility status for each required platform and an overall assessment outcome.  
10. The system shall display the returned platform statuses and overall assessment outcome in the UI.  
11. After a successful assessment, the system shall persist the assessment result including platform statuses, notes, timestamp, user identity, and overall outcome.  
12. The system shall make the latest saved assessment result available when the feature is reopened.  
13. The system shall enforce role-based access control for assessment retrieval and assessment execution.  
14. The system shall reject unauthenticated assessment retrieval or execution requests.  
15. If a user without the required role attempts to access or execute the assessment, the system shall deny the action, shall not expose assessment data, and shall record an authorization event.  
16. If a user lacks permission to run the assessment, the system shall deny the request before initiating any external platform calls.  
17. If a platform compatibility check fails, the system shall return a platform-specific failure category suitable for UI display without exposing raw exception details or secrets.  
18. The UI shall display a platform-specific error message when a platform compatibility check fails.  
19. When a failure is retryable, the system shall provide a retry path that executes a new assessment attempt without requiring re-entry of unchanged valid inputs.  
20. For every failed assessment attempt, the system shall write a structured error log containing feature context, target platform, failure type, timestamp, and correlation identifier.  
21. The system shall log assessment requests, validation failures, authorization denials, and completion outcomes with correlation IDs.  
22. The system shall support latest-assessment retrieval within 2 seconds under normal load.  
23. The system shall block unsupported or malformed requests gracefully and return controlled, non-secret-bearing error responses.  

## Testability Notes
Backend and service tests should verify:
- Required-platform completeness validation.
- Enum validation for compatibility status.
- Pre-check validation ordering before any platform check begins.
- Authorization denial before retrieval and before execute.
- No external platform-call initiation for unauthorized or invalid requests.
- Persistence of successful results and latest-result retrieval behavior.
- Structured logging content for failed and denied attempts.
- Retry execution behavior for retryable failures.
- Latest-result retrieval performance target under normal load.

## Non-Functional Requirements
### Performance
- Latest saved assessment retrieval shall complete within 2 seconds for normal load.
- Assessment summary shall return within agreed UI response targets. Exact non-retrieval target is not numerically defined in source.
- Historical retrieval of the latest result must be efficient.

### Security
- Enforce authentication and role-based access control for retrieval and execution.
- Reject unauthenticated requests.
- Deny unauthorized role access.
- Do not expose assessment data to unauthorized users.
- Do not expose raw exception details or secrets in user-facing messages.
- Do not initiate external platform calls for unauthorized execution attempts.

### Observability
- Log assessment requests, validation failures, authorization denials, and completion outcomes with correlation IDs.
- For every failed assessment attempt, capture structured error details including target platform and failure type.

### Reliability
- Edge cases must be handled gracefully.
- Retry must be supported for retryable failures.
- Failed validation must preserve user-entered data.

### Testing / Quality
- Feature must be properly tested.
- TDD-oriented coverage is required by generation mode and supported by source references to testing/QE guidance.

## Acceptance Scenarios
### Scenario 1: Authorized user opens assessment view
**Given** an authenticated user with the required role  
**When** the user opens the Compatibility Assessment feature  
**Then** the system displays an assessment view containing Microsoft Fabric, Azure OpenAI, and Slack APIs as required platforms in scope

### Scenario 2: Latest saved assessment is loaded
**Given** an authenticated authorized user  
**And** a prior successful assessment result exists  
**When** the user opens the Compatibility Assessment feature  
**Then** the system loads and displays the latest saved assessment result

### Scenario 3: Empty state on first open
**Given** an authenticated authorized user  
**And** no saved assessment exists  
**When** the user opens the Compatibility Assessment feature  
**Then** the system displays an empty assessment state

### Scenario 4: Valid assessment submission succeeds
**Given** an authenticated authorized user  
**And** the submitted request includes Microsoft Fabric, Azure OpenAI, and Slack APIs  
**And** all required inputs are valid  
**When** the user submits the assessment  
**Then** the system returns a compatibility status for each platform  
**And** returns an overall assessment outcome  
**And** displays those results to the user

### Scenario 5: Successful assessment is persisted
**Given** an authenticated authorized user  
**And** a valid assessment submission completes successfully  
**When** the system finishes processing  
**Then** it persists platform statuses, notes, timestamp, user identity, and overall outcome

### Scenario 6: Missing required platform blocks submission
**Given** an authenticated authorized user  
**And** the request omits one required platform entry  
**When** the user submits the assessment  
**Then** the system rejects the submission  
**And** preserves user-entered data  
**And** shows field-level validation identifying the missing platform entry  
**And** starts no platform check

### Scenario 7: Invalid status value blocks submission
**Given** an authenticated authorized user  
**And** one platform entry contains an invalid compatibility status  
**When** the user submits the assessment  
**Then** the system rejects the submission  
**And** preserves user-entered data  
**And** shows a field-level validation message for the invalid status  
**And** starts no platform check

### Scenario 8: Malformed input blocks submission before checks
**Given** an authenticated authorized user  
**And** the assessment request contains missing or malformed required inputs  
**When** the user submits the assessment  
**Then** the system blocks submission  
**And** identifies the specific field errors  
**And** does not start any Microsoft Fabric, Azure OpenAI, or Slack API check

### Scenario 9: Unauthorized access is denied
**Given** a user without the required role  
**When** the user attempts to open or execute the Compatibility Assessment feature  
**Then** the system denies the action  
**And** does not expose assessment data  
**And** records an authorization event

### Scenario 10: Unauthorized execute does not start external calls
**Given** a user without permission to run the assessment  
**When** the user attempts to execute an assessment  
**Then** the system displays an authorization message  
**And** does not initiate any external platform calls

### Scenario 11: Platform-specific failure is displayed safely
**Given** an authenticated authorized user  
**And** a platform compatibility check fails for one required platform  
**When** the system returns the failure outcome  
**Then** the UI displays a platform-specific error message describing the failure category  
**And** does not expose raw exception details or secrets

### Scenario 12: Retryable failure can be retried
**Given** an authenticated authorized user  
**And** an assessment attempt fails with a retryable failure  
**When** the user selects retry  
**Then** the system executes a new assessment attempt  
**And** preserves unchanged valid inputs without requiring re-entry

### Scenario 13: Failed attempt is logged
**Given** any assessment attempt fails  
**When** the failure is recorded  
**Then** the system writes a structured error log containing feature context, target platform, failure type, timestamp, and correlation identifier

## Traceability Matrix
| Source ID | Requirement | Acceptance Criteria | Test Coverage |
|---|---|---|---|
| US 2040 | FR-2, FR-9, FR-11, FR-13 | Assess and document compatibility with Microsoft Fabric, Azure OpenAI, Slack within approved architecture/governance | Service validation, persistence, authorization tests |
| US 3352 AC1 | FR-1, FR-2 | Assessment view displays Microsoft Fabric, Azure OpenAI, Slack APIs in scope | UI entry/API data contract verification |
| US 3352 AC2 | FR-9, FR-10 | Valid request returns status per platform plus overall outcome | Request-processing and response tests |
| US 3352 AC3 | FR-6, FR-7, FR-8 | Missing platform or invalid status rejects submission, preserves data, shows field-level validation | Validation tests |
| US 3352 AC4 | FR-11, FR-12 | Successful assessment persists result and latest saved result is available on reopen | Persistence and retrieval tests |
| US 3352 AC5 | FR-13, FR-15, FR-16 | Unauthorized access/execute is denied and authorization event is recorded | AuthN/AuthZ tests |
| US 3352 AC10 | FR-22 | Feature meets performance requirements | Retrieval performance tests |
| US 3352 AC11 | FR-5, FR-8 | Missing/malformed inputs block submission before platform checks start | Validation-ordering tests |
| US 3352 AC12 | FR-17, FR-18 | Platform-specific failure message without raw exception details or secrets | Failure mapping tests |
| US 3352 AC13 | FR-19 | Retry action executes a new attempt without re-entry of unchanged valid inputs | Retry-flow tests |
| US 3352 AC14 | FR-16 | Lack of permission blocks request before external calls | Authorization short-circuit tests |
| US 3352 AC15 | FR-20, FR-21 | Failed attempts write structured error logs with required metadata | Logging tests |

## Open Questions
1. What exact role or roles are authorized to view and execute the Compatibility Assessment feature?
2. What is the canonical approved enum for `compatibilityStatus`? The story gives examples but not a definitive enum contract.
3. What inputs, beyond platform entries and status values, are required for a “valid assessment request”?
4. How is the “overall assessment outcome” derived from platform-level statuses?
5. Are compatibility checks purely user-entered/documented statuses, system-derived evaluations, or a mixed workflow?
6. What specific failure categories must be used for platform-specific error messages?
7. What conditions classify a failure as retryable?
8. Is latest saved assessment scoped per user, per tenant, per environment, or globally for the feature?
9. Are notes required per platform, optional per platform, or only optional at assessment level?
10. What UI copy should be used for validation, authorization, retry, empty-state, and failure messages?
11. Is there a required audit-log retention period for authorization and failure events?
12. Are there any required accessibility standards for the web UI?
13. Are there dedicated APIs/endpoints already defined for retrieve, submit, and retry operations, or must they be introduced during implementation design?
14. What exact performance target applies to assessment execution response time, beyond the 2-second latest-result retrieval target?
15. Should historical assessments beyond the latest result be retrievable in this feature, or is only latest-result retrieval in scope?

## Source References
- **Feature ID:** 901191011
- **Feature Reference:** 901191011
- **Primary Feature Title:** Compatibility Assessment for Required Platforms
- **User Stories Used:**
  - US 2040 — Compatibility Assessment for Required Platforms
  - US 3352 — As a user, I want to use Compatibility Assessment for Required Platforms so that I can benefit from its core functionality
- **Golden Repo References Cited by Source Context:**
  - 01_Better_Companies_Architecture_Guideline.docx
  - 02_Better_Companies_Coding_Development_Guideline.docx
  - 03_Better_Companies_Review_Approval_Guideline.docx
  - 04_Better_Companies_Testing_QE_Guideline.docx
  - 06_Better_Companies_Security_Governance_Responsible_AI_Guideline.docx
  - Better_Companies_Requirement_Work_Item_Guideline.docx