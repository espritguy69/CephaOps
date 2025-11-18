# REVIEW\_CHECKLIST.md

\# REVIEW\_CHECKLIST

PR Review Guidelines



Before approving a PR, check:



\## 1. Correctness



\- \[ ] Does the code do what the description claims?

\- \[ ] Are edge cases covered?

\- \[ ] Are errors handled gracefully?



\## 2. Architecture \& Design



\- \[ ] Is business logic in Application/Domain, not in controllers?

\- \[ ] Are new entities consistent with `PHASE1\_DOMAIN\_MODELS.md`?

\- \[ ] Does it respect multi-company scoping?



\## 3. Tests



\- \[ ] Are there unit tests or integration tests where appropriate?

\- \[ ] Do existing tests still pass?



\## 4. Security \& Data



\- \[ ] No secrets committed.

\- \[ ] Logs and errors don’t leak sensitive customer info.



\## 5. Documentation



\- \[ ] API changes reflected in `spec/api/\*.md`.

\- \[ ] Domain/module changes reflected in `spec/\*.md`.

\- \[ ] UI behaviour changes reflected in `storybook/\*.md`.



Use this checklist as guidance, not bureaucracy. The goal is safe, maintainable progress.



