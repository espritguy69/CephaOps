📚 CephasOps Documentation



Welcome to the CephasOps full documentation index.

This folder contains architecture, specifications, APIs, diagrams, and UI storybooks that define how the platform behaves end-to-end.



🚀 How to Navigate This Documentation



To understand the system clearly and in the correct sequence, read the docs in this order:



1️⃣ Executive Summary (Start Here)



File: CSIOS\_EXEC\_SUMMARY.md

A high-level overview of CephasOps: goals, modules, multi-company context, workflows, and overall mission.



2️⃣ System Architecture



Folder: /architecture

These files explain how the system is built.



Read in this order:



ARCHITECTURE\_OVERVIEW.md



Clean architecture layers



Domain → Application → Infrastructure → API



Multi-company \& RBAC model



SYSTEM\_OVERVIEW.md (if present)



Logical architecture



Components \& services



SYSTEM\_DESIGN\_DIAGRAMS.md



Mermaid, flowcharts, logical diagrams (Parser → Orders → Scheduler → SI → Billing → PNL)



TECH\_STACK\_DECISION.md



Why .NET + React



PostgreSQL, EF Core, Docker, CI/CD



FOLDER\_STRUCTURE.md



Repository layout for backend, frontend, docs, modules



3️⃣ Storybook (Functional Requirements)



Folder: /storybook

Describes how users interact with CephasOps.



Read:



STORYBOOK\_V1.md



Order flows



Scheduler flows



SI on-site flows



Billing flows



PAGES.md



Every screen in Admin App \& SI App



WIREFRAMES.md



Text-based UI wireframes for frontend construction



USER\_FLOWS.md



End-to-end process flows for TIME activation, assurance, dockets, invoicing, etc.



4️⃣ Specifications (Backend Requirements)



Folder: /spec



Each module is documented as a standalone specification.



Include:



PHASE1\_DOMAIN\_MODELS.md



ORDERS\_MODULE.md



SCHEDULER\_MODULE.md



INVENTORY\_MODULE.md



INVOICING\_MODULE.md



DOCKETS\_MODULE.md



BUILDINGS\_SPLITTERS\_MODULE.md



PARTNERS\_INSTALLERS\_MODULE.md



PARSER\_RULES\_MODULE.md



REPORTING\_MODULE.md



RMA\_MODULE.md



SETTINGS\_MODULE.md



PNL\_COSTCENTRE\_MODULE.md



These define entities, use cases, rules, workflows, and data structures.



5️⃣ API Specification



Folder: /spec/api



Start with:



API\_BLUEPRINT.md

Complete list of endpoints across:



Orders



Scheduler



Buildings



Inventory \& RMA



Billing \& Invoices



Parser rules



Settings



Notifications



SI Mobile App



Auth



Followed by module-specific API files:

orders.api.md, scheduler.api.md, inventory.api.md, etc.



These are used by backend → frontend → SI App.



6️⃣ Database Specifications



Folder: /spec/database



Includes:



DATABASE\_SCHEMA.md



ERD.md



MIGRATIONS\_GUIDE.md



Defines PostgreSQL schema, relationships, and EF Core migrations strategy.



🗃 Archive / Legacy Files



Folder: /archive (formerly /old files/)



This folder contains:



Early drafts



Outdated versions



Emoji-heavy teaching documents



Older module specs



Deprecated wireframes



They are useful for history, but NOT canonical.

All new work should refer only to the files in /architecture, /spec, /spec/api, /spec/database, and /storybook.



🛠 Contribution Guidelines



See /docs/governance:



CODE\_OF\_CONDUCT.md



CONTRIBUTING.md



REVIEW\_CHECKLIST.md



BRANCHING\_STRATEGY.md



COMMIT\_CONVENTIONS.md



✔ Final Notes



This documentation set is the source of truth for the CephasOps platform.



All changes to backend, frontend, or infra MUST be reflected here.



Any outdated documents should be moved into /archive instead of deleted.

