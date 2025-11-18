# BRANCHING\_STRATEGY.md

\# BRANCHING\_STRATEGY

Git Workflow for CephasOps



---



\## Main Branches



\- `main`

&nbsp; - Always deployable.

&nbsp; - Tag releases from here.

\- `develop`

&nbsp; - Integration branch.

&nbsp; - Features get merged into `develop` first.



---



\## Feature Branches



Naming:



\- `feature/orders-kpi-dashboard`

\- `feature/si-pwa-offline`



Process:



1\. Branch from `develop`.

2\. Commit changes.

3\. Open PR into `develop`.

4\. After review \& checks, merge using a squash or merge commit.



---



\## Hotfixes



\- `hotfix/<issue>` branch from `main`.

\- After fix:

&nbsp; - Merge into `main`.

&nbsp; - Also merge back into `develop` to keep them in sync.



---



\## Release Tags



\- Tag releases:

&nbsp; - `v1.0.0`, `v1.1.0`, etc.

\- Optionally use release branches if needed:

&nbsp; - `release/1.0.0`.



