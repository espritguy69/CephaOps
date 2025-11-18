# CONTRIBUTING.md

\# CONTRIBUTING

How to Contribute to CephasOps



Thank you for contributing to CephasOps. This document describes how to work with this repository.



---



\## 1. Branching



\- `main` – production-ready code

\- `develop` – integration branch for ongoing work

\- Feature branches:

&nbsp; - `feature/<short-description>`

\- Bugfix branches:

&nbsp; - `fix/<issue-id>-<short-description>`



See `governance/BRANCHING\_STRATEGY.md` for details.



---



\## 2. Issues



\- Use GitHub Issues (or your internal tracker).

\- Clearly describe:

&nbsp; - Problem statement

&nbsp; - Steps to reproduce (if bug)

&nbsp; - Expected vs actual behaviour

&nbsp; - Proposed solution (if known)



---



\## 3. Pull Requests



\- Keep PRs focused and small when possible.

\- Link to related issue(s).

\- Include:

&nbsp; - Summary of changes

&nbsp; - Testing steps

&nbsp; - Any DB schema changes (link to migration)



PRs should pass:



\- Backend tests

\- Frontend linting / build

\- Any relevant integration tests



---



\## 4. Coding Standards



\- Backend:

&nbsp; - C# style aligned with .NET conventions.

&nbsp; - Use async/await for I/O.

&nbsp; - Put business logic in Application/Domain, not controllers.

\- Frontend:

&nbsp; - React + TypeScript.

&nbsp; - Prefer function components and hooks.

&nbsp; - Keep components small and composable.



---



\## 5. Documentation



For new features:



\- Update:

&nbsp; - `docs/spec/\*.md` for domain/module changes

&nbsp; - `docs/spec/api/\*.md` for API changes

&nbsp; - `docs/storybook/\*.md` for UI/flow changes



---



\## 6. Security \& Data



\- Do not commit credentials or secrets.

\- Use environment variables and secret stores.

\- Treat real customer data as confidential and \*\*do not\*\* put it in examples or logs.



