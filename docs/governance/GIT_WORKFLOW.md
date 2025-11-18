# ✅ GitHub Push Checklist – CephasOps

\# Git Workflow – CephasOps



\## Branching Strategy



\- `main`  

&nbsp; - Always \*\*stable\*\*, production-ready.

&nbsp; - No direct commits. Only merge via Pull Request (PR).



\- `develop` (optional)  

&nbsp; - Integration branch for upcoming features.

&nbsp; - PRs from feature branches can target `develop` first.



\- `feature/\*`  

&nbsp; - One branch per task or small group of related tasks.

&nbsp; - Examples:

&nbsp;   - `feature/phase1-backend-orders`

&nbsp;   - `feature/phase1-frontend-orders-page`

&nbsp;   - `feature/si-pwa-today-jobs`



\## Rules for Human \& AI Contributors



1\. \*\*Never commit directly to `main`.\*\*  

&nbsp;  - Always create a `feature/\*` branch.



2\. \*\*Every change must go through a PR:\*\*

&nbsp;  - PR title: short but clear (`Phase1 – Orders API`, etc.)

&nbsp;  - PR description:

&nbsp;    - What was done

&nbsp;    - Related docs updated (if any)

&nbsp;    - Any breaking changes



3\. \*\*Use the Review Checklist:\*\*

&nbsp;  - Before merging, apply `/docs/governance/REVIEW\_CHECKLIST.md`.



4\. \*\*Reverts:\*\*

&nbsp;  - If a feature breaks something:

&nbsp;    - Use GitHub “Revert PR” to undo.

&nbsp;    - Open a new branch to fix and re-apply.



5\. \*\*AI (Cursor) Special Rules:\*\*

&nbsp;  - Cursor works ONLY on `feature/\*` branches.

&nbsp;  - Human reviews every PR before merge.

&nbsp;  - AI must reference relevant docs in each PR description.



\## Typical Flow



1\. Create a new branch:

&nbsp;  - `git checkout -b feature/phase1-backend-orders`



2\. Let Cursor implement code \*\*on this branch only\*\*.



3\. Push and open PR to `develop` or `main`.



4\. Run through:

&nbsp;  - Code review

&nbsp;  - Review checklist

&nbsp;  - Tests



5\. Merge PR.



6\. Tag release if appropriate.



