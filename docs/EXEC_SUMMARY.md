\# CephasOps – Executive Summary (Phase 1)



\## 1. What is CephasOps?



CephasOps is an internal operations platform designed to manage:



\- ISP service orders (TIME, Celcom, etc.)

\- Service installer (SI) scheduling \& job flows

\- Building \& splitter management

\- Inventory \& material movements

\- Dockets → Invoices → Submission IDs

\- RMA / MRA tracking

\- Multi-company PNL (Cephas Trading, Cephas Sdn. Bhd., Kingsman, Menorah)



The system is \*\*multi-company\*\* from day one and supports:



\- Multiple operating companies under one group

\- Strict separation of data by `CompanyId`

\- Director-level cross-company views



---



\## 2. Phase 1 Scope (ISP Vertical Only)



Phase 1 focuses on \*\*ISP operations\*\*, especially TIME orders:



\- TIME activation orders (FTTH / FTTO / FTTR / SDU / RDF POLE)

\- Assurance / troubleshooting (TTKT + AWO)

\- Scheduler calendar for SIs (in-house + subcon)

\- SI mobile PWA (On The Way → Met Customer → Completed)

\- Building \& splitter management (including standby port rules)

\- Inventory:

&nbsp; - Warehouse → SI → Customer → RMA

\- Docket reception \& upload

\- Invoice creation, portal Submission ID, basic aging

\- RMA \& MRA document linkage

\- Multi-company context:

&nbsp; - Cephas Sdn. Bhd

&nbsp; - Cephas Trading \& Services

\- Notifications \& KPIs (dockets, on-time, etc.)

\- Basic PNL using cost centres



Barbershop and Travel verticals are \*\*future phases\*\*, not Phase 1.



---



\## 3. Architecture at a Glance



CephasOps follows a \*\*clean / layered architecture\*\*:



\- \*\*API\*\* – ASP.NET Core Web API (controllers, DTOs, auth)

\- \*\*Application\*\* – use cases, services, orchestration

\- \*\*Domain\*\* – entities, enums, value objects, domain rules

\- \*\*Infrastructure\*\* – EF Core, PostgreSQL, email, file storage, integrations



Key cross-cutting concerns:



\- Multi-company scoping (`CompanyId` everywhere)

\- RBAC (per company, per role)

\- Auditing (`CreatedAt`, `UpdatedAt`, etc.)

\- Background jobs (email parsing, billing cycles)

\- Logging (Serilog)



Details in: `/docs/architecture/ARCHITECTURE\_OVERVIEW.md`



---



\## 4. Frontends



CephasOps has two main frontends:



1\. \*\*Admin Web App (React + Vite + TS)\*\*

&nbsp;  - For Admin, Scheduler, Warehouse, Finance, Director

&nbsp;  - Modules:

&nbsp;    - Dashboard

&nbsp;    - Orders

&nbsp;    - Scheduler

&nbsp;    - Inventory

&nbsp;    - Billing / Invoices

&nbsp;    - PNL

&nbsp;    - Settings



2\. \*\*Service Installer PWA (React PWA)\*\*

&nbsp;  - For SIs (in-house \& subcon)

&nbsp;  - Features:

&nbsp;    - Today’s jobs

&nbsp;    - Job details

&nbsp;    - Status updates (OnTheWay, MetCustomer, Completed)

&nbsp;    - GPS capture on status changes

&nbsp;    - Photos (site completion, ONU/router, fibre tray)

&nbsp;    - Materials used

&nbsp;    - Offline-first behaviour



Exact pages \& routes: `/docs/storybook/PAGES.md`



---



\## 5. Data \& Domain



The central Phase 1 domain models are:



\- Company, User, Role, Permission

\- ServiceInstaller

\- Partner

\- Order, OrderStatusLog

\- Building, Splitter, SplitterPort

\- Material, MaterialStock, MaterialMovement

\- RmaRequest

\- Invoice, InvoiceLine

\- ParserRule

\- NotificationSetting

\- CostCentre



Each entity includes:



\- `CompanyId`

\- Audit fields (`CreatedAt`, `UpdatedAt`, `CreatedBy`, `UpdatedBy`)



Full spec: `/docs/spec/database/DATABASE\_SCHEMA.md`  

and `/docs/spec/PHASE1\_DOMAIN\_MODELS.md` (or equivalent file name in spec).



---



\## 6. Tech Stack (Phase 1)



\- \*\*Backend\*\*

&nbsp; - ASP.NET Core 8 Web API

&nbsp; - Entity Framework Core + PostgreSQL

&nbsp; - JWT Auth + role-based authorization

&nbsp; - Serilog logging

&nbsp; - Hangfire / Quartz for background jobs



\- \*\*Frontend Admin\*\*

&nbsp; - React + TypeScript

&nbsp; - Vite

&nbsp; - TailwindCSS

&nbsp; - TanStack Query (React Query)

&nbsp; - React Router



\- \*\*SI PWA\*\*

&nbsp; - React PWA (mobile-first)

&nbsp; - Camera \& GPS APIs

&nbsp; - IndexedDB / local storage for offline mode



\- \*\*General\*\*

&nbsp; - Docker \& Docker Compose for local dev

&nbsp; - GitHub Actions for CI/CD



Details in: `/docs/architecture/TECH\_STACK\_DECISION.md`



---



\## 7. How to Read the Docs (for Humans \& AI)



\*\*Recommended reading order:\*\*



1\. `EXEC\_SUMMARY.md` (this file)

2\. `/docs/architecture/ARCHITECTURE\_OVERVIEW.md`

3\. `/docs/storybook/STORYBOOK.md`

4\. `/docs/storybook/PAGES.md`

5\. `/docs/spec/api/API\_BLUEPRINT.md`

6\. `/docs/spec/database/DATABASE\_SCHEMA.md`

7\. `/docs/ai/CURSOR\_ONBOARDING.md`

8\. `/docs/ai/PHASE1\_BACKLOG.md`

9\. `/docs/governance/AI\_RULES\_FOR\_CURSOR.md`

10\. `/docs/governance/REVIEW\_CHECKLIST.md`



---



\## 8. Governance \& AI Usage



\- AI-generated code is allowed only under:

&nbsp; - Clean Architecture rules

&nbsp; - Multi-company + RBAC rules

&nbsp; - Governance docs



Key governance files:



\- `/docs/governance/CODE\_OF\_CONDUCT.md`

\- `/docs/governance/REVIEW\_CHECKLIST.md`

\- `/docs/governance/GIT\_WORKFLOW.md`

\- `/docs/governance/AI\_RULES\_FOR\_CURSOR.md`

\- `/docs/ai/CURSOR\_MASTER\_SCRIPT.md`



\*\*Git is the source of truth\*\*.  

\*\*Docs define behaviour\*\*.  

\*\*AI + humans implement under those rules.\*\*



