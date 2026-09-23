# Change Map - Compatibility Assessment for Required Platforms; As a user, I want to use Compatibility Assessment for Required Platforms so that I can benefit from its core functionality; As an Executive user, I want to ask plain-English questions against approved Gold layer data so that I can get governed manufacturing insights without using unapproved datasets (+190 more)

> Advisory implementation map for IDE code generation. Verify all paths and ownership in the actual workspace before editing.

- Feature slug: `governed-manufacturing-analytics-platform`
- Suggested owner repo type: `ui`
- Impacted repo types: `ui`, `api`, `worker`, `shared-lib`
- Confidence: 0.55

## Read First

- `specs/.devx/project-context.md`
- `specs/.devx/current-state.md`
- `specs/.devx/implementation-check.md`
- `specs/.devx/change-maps/governed-manufacturing-analytics-platform.md`
- `specs/.devx/guidance/golden-repo-guidelines.md`
- `specs/governed-manufacturing-analytics-platform/specs.md`
- `specs/governed-manufacturing-analytics-platform/requirements.md`
- `specs/governed-manufacturing-analytics-platform/tdd-tests.md`
- `specs/governed-manufacturing-analytics-platform/prompt.md`

## Candidate Areas

- UI screens/components
- API routes/services
- Data model/persistence
- Authentication/authorization
- Background work/events
- Tests

## Existing Code Search Hints

- Search the discovered workspace for existing ui screens/components before creating new code.
- Search the discovered workspace for existing api routes/services before creating new code.
- Search the discovered workspace for existing data model/persistence before creating new code.
- Search the discovered workspace for existing authentication/authorization before creating new code.
- Search the discovered workspace for existing background work/events before creating new code.
- Search the discovered workspace for existing tests before creating new code.

## Tests To Inspect

- Existing unit tests near candidate implementation files
- Existing integration/API tests for related routes or services
- Existing UI/e2e tests for related screens or flows

## Constraints

- Verify every suggested path in the actual IDE workspace before editing.
- Prefer extending existing modules over creating parallel implementations.
- Existing code patterns override generic guidance when they conflict.
- Do not create a new repo or major folder structure unless the spec explicitly requires it.

## Routing Rationale

- Specs home defaults to the ui repo because delivery order is UI-first.
- Feature includes both UI and backend signals, so the ui repo is the default owner and the other side is marked as impacted.
