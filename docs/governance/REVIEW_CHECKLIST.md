
# Engineering Review Checklist

## 1. API Layer
- [ ] Controller has no business logic
- [ ] Request DTO validated
- [ ] Auth & RBAC applied
- [ ] CompanyId enforced
- [ ] Endpoint added to API_BLUEPRINT.md

## 2. Service Layer
- [ ] Business logic isolated
- [ ] Domain rules respected
- [ ] No circular dependencies

## 3. Domain Layer
- [ ] Entities remain pure
- [ ] Audit fields included
- [ ] No EF attributes in Domain

## 4. Infrastructure
- [ ] Repository follows tenant rules
- [ ] EF config matches DATABASE_SCHEMA
- [ ] Migrations updated

## 5. Frontend
- [ ] Uses React Query
- [ ] Uses API hooks
- [ ] Follows PAGES.md layout

## 6. Documentation
- [ ] Relevant docs updated
- [ ] Changelog entry added

