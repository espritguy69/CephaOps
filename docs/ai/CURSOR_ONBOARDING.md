
# CURSOR_ONBOARDING.md – Full Version
## Purpose
This document instructs Cursor AI (and any AI coding agent) EXACTLY how to read, understand, and modify the CephasOps codebase.

## 1. How Cursor should read the repository
1. Start at `/docs/EXEC_SUMMARY.md`
2. Then read:
   - `/docs/architecture/ARCHITECTURE_OVERVIEW.md`
   - `/docs/storybook/STORYBOOK.md`
   - `/docs/spec/api/API_BLUEPRINT.md`
   - `/docs/spec/database/DATABASE_SCHEMA.md`
3. Only after understanding these, proceed to backend/frontend code.

## 2. Architecture Expectations
- Clean Architecture (API → Application → Domain → Infrastructure)
- Multi-company tenancy: Every request must include authenticated company scope.
- RBAC: Backend must reject actions outside user’s role.
- Event-driven behaviors: order status → inventory → invoice linkages.

## 3. Coding Rules for AI
- NEVER write inline SQL—only EF Core with proper configurations.
- Every controller must:
  - Validate `companyId`
  - Apply role-based authorization attributes
- No business logic in controllers—only in Application Services.
- Domain entities must remain pure C# classes without EF/persistence logic.

## 4. End-to-End Build Instructions (AI Workflow)
1. Scaffold EF entities from `/docs/spec/database/DATABASE_SCHEMA.md`
2. Build repositories
3. Build services (OrderService, SchedulerService, BillingService, etc.)
4. Build controllers
5. Build frontend pages following `/docs/spec/frontend/PAGES.md`
6. Connect SI PWA using `/docs/storybook/STORYBOOK.md`

## 5. Common AI Mistakes to Avoid
- Mixing domain & infrastructure namespaces.
- Ignoring multi-company scoping.
- Forgetting audit fields.
- Forgetting to update Swagger after new endpoints.

## 6. Done Checklist Before Commit
- All new APIs added to API_BLUEPRINT.md
- EF migrations updated
- Unit tests created
- Frontend API hook created
- Docs updated

