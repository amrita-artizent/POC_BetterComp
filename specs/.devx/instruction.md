# Development Instructions

> Last updated: 2026-09-23
> This file is regenerated during specs generation. Keep inventory updates in version control and restore any manual edits after regeneration.

## Active Configuration

This project is configured as **Microservices** with **UI-first** specs home.

## Architecture Mode Rules

- **Monolithic**: Existing specs-to-code flow remains unchanged. All feature specs stay under `specs/<feature-slug>/` in the implementation repo.
- **Microservices / Multi-repo**: All generated specs still live in one selected specs home repo. Other repos should reference these specs; do not fork spec sources.

## Specs Home by Delivery Order

- Specs home is the **UI repository** (`specs/` + `specs/.devx/`).
- API and other service repos consume specs from the UI-first source of truth.

## Workspace Discovery Files

- `specs/.devx/discover-workspace.sh` — run this in the cloned target workspace
- `specs/.devx/feature-routing.json` — generated feature-level owner and impacted repo suggestions
- `specs/.devx/workspace-repos.json` — discovered repositories, capabilities, and existing-code context
- `specs/.devx/workspace-context.md` — human-readable workspace summary
- `specs/.devx/repo-plans.json` — proposed or approved future repositories

### Recommended sequence

1. Push generated specs to the chosen specs-home repo
2. Clone or open the target implementation workspace locally
3. Run `bash specs/.devx/discover-workspace.sh`
4. Review `specs/.devx/feature-routing.json` against the discovered workspace inventory
5. Update repo ownership or add repo plan proposals before implementation starts
## Repository Inventory

| Repository | Purpose | URL | Default Branch | Notes |
| --- | --- | --- | --- | --- |
| _Populate by running discover-workspace.sh in the target workspace_ | _TBD_ | _TBD_ | _TBD_ | _TBD_ |

## Existing APIs

| API / Route | Method | Owning Service/Repo | Notes |
| --- | --- | --- | --- |
| _Populate by running discover-workspace.sh in the target workspace_ | _TBD_ | _TBD_ | _TBD_ |

## Existing UI Screens

| Screen | Route/Path | Owning Repo | Notes |
| --- | --- | --- | --- |
| _Populate by running discover-workspace.sh in the target workspace_ | _TBD_ | _TBD_ | _TBD_ |

## New Feature Routing Decision

When a new feature is requested in microservices mode:

1. Check this inventory and current specs to identify the nearest existing microservice.
2. Decide whether to:
   - Extend an existing microservice with additional APIs/contracts, or
   - Create a new microservice when ownership, deployability, and domain boundaries require it.
3. Record the decision in the feature `specs.md` and linked work item before implementation starts.
