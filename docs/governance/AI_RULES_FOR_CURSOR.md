
# AI_RULES_FOR_CURSOR.md – Full Version
## Purpose
Defines strict rules for Cursor AI and any automated agent modifying CephasOps repo.

## Core Principles
1. **Follow Clean Architecture** exactly as defined in /docs/architecture.
2. Never add business logic to controllers.
3. Always enforce `companyId` scoping.
4. Respect RBAC — deny actions outside role permissions.
5. Document every new endpoint in API_BLUEPRINT.md.
6. Update Storybook only when UI flows change.
7. Keep frontend and backend in sync.
8. Never delete audit fields.

## Required Steps Before Every Commit
- Update API hooks
- Add EF migration
- Update Swagger
- Run test suite
- Update relevant docs

## Behavior Rules for Multi-Agent AI
- Lead agent reads /docs/EXEC_SUMMARY first.
- Code agent only modifies code.
- Doc agent updates docs after code changes.
