# 🧩 Contributing to CephasOps  
Thank you for contributing to the CephasOps Platform.  
This document outlines how developers should work, commit, review, and maintain quality across the entire repository.

---

# 1. Principles

1. **Architecture First**  
   All development must follow the designs in `/docs/architecture`.

2. **No Code Before Documentation**  
   Any new module, feature, or workflow must be documented before coding begins.

3. **Clean, Scalable, Maintainable**  
   - CQRS for backend  
   - Domain-driven entities  
   - Component-driven frontend  
   - Zero hard-coded secrets  

4. **Cursor AI Compliance**  
   Follow `.cursorrules` strictly when using Cursor for code generation.

---

# 2. Branching Strategy

Use the following formats:

| Type | Format | Example |
|------|--------|---------|
| Feature | `feat/<module>-short-desc` | `feat/orders-add-modification-flow` |
| Fix | `fix/<module>-short-desc` | `fix/email-parser-empty-attachment` |
| Documentation | `docs/<area>` | `docs/architecture-scheduler-update` |
| Chore / Cleanup | `chore/<area>` | `chore/repo-structure-cleanup` |

Avoid pushing directly to `main`.

---

# 3. Commit Standards

Follow **semantic commits** as documented in:  
`/docs/governance/SEMANTIC_COMMITS.md`

Examples:

```
feat(scheduler): add zone-based SI filtering
fix(inventory): correct router serial lookup
docs(specs): rewrite folder structure guidelines
chore(repo): move module files to docs/architecture
```

Do NOT use messages like:
```
update
final
fixing
changes
test
```

---

# 4. Pull Request Requirements

Every PR must:

- Use `/docs/governance/PR_TEMPLATE.md`
- Reference affected modules
- Include screenshots (for UI)
- Include API samples (for backend)
- Include DB migration notes (if applicable)
- Update architecture/spec docs if feature affects design

PRs cannot be merged until:
- CI passes (if configured)
- At least one reviewer approves
- Docs are updated
- Semantic commit standards met

---

# 5. Coding Standards

## Backend (.NET 8, CQRS, EF Core)

- Use `Application → Domain → Infrastructure` layering
- No business logic inside controllers
- Use domain events where appropriate
- Repositories should be interfaces in Domain, implementations in Infrastructure
- Use async/await everywhere

## Frontend (React + Vite)

- Use functional components only
- Use hooks for state and logic
- Use atomic component structure
- Do not duplicate logic; use shared utils
- Keep components small and reusable

---

# 6. Tests

- Unit tests must exist for core workflows  
- Test files go under:
  ```
  backend/tests/CephasOps.Tests/
  ```
- Frontend test setup can evolve later

---

# 7. Documentation Rules

Every new feature MUST include:

- Update to an architecture doc or spec doc
- API updates → `API_BLUEPRINT.md`
- Workflow changes → module file update
- New UI → screenshot added to PR

Architecture is always the **source of truth**.

---

# 8. Forbidden Actions

❌ No secrets committed  
❌ No modifying `/infra/environments/production`  
❌ No modifying `/secrets`  
❌ No changing `.cursorrules` without approval  
❌ No pushing to `main` directly  
❌ No undocumented changes  

---

# 9. Getting Help

Ask questions in the engineering group chat, referencing:

- Module
- File path
- What you have tried
- Screenshots or logs

---

# ✔ Thank you for helping build CephasOps!
