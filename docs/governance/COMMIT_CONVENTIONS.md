# COMMIT_CONVENTIONS
Commit Message Style

We use a simple conventional style to keep history readable:

Format:

```text
<type>: <short description>

[optional body]
Common type values:

feat – new feature

fix – bug fix

chore – tooling, configs, etc.

docs – documentation only

refactor – refactoring without behaviour change

test – adding or updating tests

Examples:

feat: add SI KPI summary to profile endpoint

fix: correct splitter standby validation

docs: expand API blueprint for invoices

Keep the first line under ~72 characters when possible.

markdown
Copy code

---

### `REVIEW_CHECKLIST.md`

```markdown
# REVIEW_CHECKLIST
PR Review Guidelines

Before approving a PR, check:

## 1. Correctness

- [ ] Does the code do what the description claims?
- [ ] Are edge cases covered?
- [ ] Are errors handled gracefully?

## 2. Architecture & Design

- [ ] Is business logic in Application/Domain, not in controllers?
- [ ] Are new entities consistent with `PHASE1_DOMAIN_MODELS.md`?
- [ ] Does it respect multi-company scoping?

## 3. Tests

- [ ] Are there unit tests or integration tests where appropriate?
- [ ] Do existing tests still pass?

## 4. Security & Data

- [ ] No secrets committed.
- [ ] Logs and errors don’t leak sensitive customer info.

## 5. Documentation

- [ ] API changes reflected in `spec/api/*.md`.
- [ ] Domain/module changes reflected in `spec/*.md`.
- [ ] UI behaviour changes reflected in `storybook/*.md`.

Use this checklist as guidance, not bureaucracy. The goal is safe, maintainable progress.



