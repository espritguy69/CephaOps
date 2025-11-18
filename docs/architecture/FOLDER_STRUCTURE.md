# FOLDER\_STRUCTURE.md



\# FOLDER\_STRUCTURE

CephasOps – Repository Layout



This document standardises the folder layout for the CephasOps GitHub repository.



---



\## Root Layout



```text

cephasops/

├── README.md

├── .gitignore

├── docker-compose.yml

├── backend/

├── frontend/

├── docs/

└── .github/

&nbsp;   └── workflows/



Backend

backend/

&nbsp; CephasOps.Api/

&nbsp;   Controllers/

&nbsp;   DTOs/

&nbsp;   Middleware/

&nbsp;   Filters/

&nbsp;   Mapping/

&nbsp;   Program.cs

&nbsp; CephasOps.Application/

&nbsp;   Orders/

&nbsp;   Scheduler/

&nbsp;   Inventory/

&nbsp;   Invoices/

&nbsp;   Buildings/

&nbsp;   Splitters/

&nbsp;   Partners/

&nbsp;   Installers/

&nbsp;   Dockets/

&nbsp;   Settings/

&nbsp;   Reporting/

&nbsp;   Auth/

&nbsp;   Common/

&nbsp; CephasOps.Domain/

&nbsp;   Common/

&nbsp;   Entities/

&nbsp;   Enums/

&nbsp;   ValueObjects/

&nbsp; CephasOps.Infrastructure/

&nbsp;   Persistence/

&nbsp;     CephasOpsDbContext.cs

&nbsp;     Configurations/

&nbsp;   Repositories/

&nbsp;   Email/

&nbsp;   FileStorage/

&nbsp;   Jobs/

&nbsp;   Identity/

&nbsp; CephasOps.Tests/

&nbsp;   ApiTests/

&nbsp;   ApplicationTests/

&nbsp;   DomainTests/



Frontend

frontend/

&nbsp; app/        # Admin / Ops web app

&nbsp;   src/

&nbsp;     pages/

&nbsp;     components/

&nbsp;     hooks/

&nbsp;     api/

&nbsp;     types/

&nbsp;     utils/

&nbsp;   vite.config.ts

&nbsp;   package.json



&nbsp; si-app/     # Service Installer PWA

&nbsp;   src/

&nbsp;     pages/

&nbsp;     components/

&nbsp;     hooks/

&nbsp;     api/

&nbsp;     utils/

&nbsp;   vite.config.ts

&nbsp;   package.json



Documentation

docs/

&nbsp; architecture/

&nbsp; architecture/diagrams/

&nbsp; governance/

&nbsp; spec/

&nbsp; spec/api/

&nbsp; spec/database/

&nbsp; storybook/





See architecture/ and spec/ for exact file breakdown and responsibilities.



This structure is optimised for:



AI assistants (Cursor) to understand modules cleanly.



Clear ownership per module and per layer.



Easy onboarding for new developers.





---



\## `/docs/governance`



I’ll keep these concise but real and usable.



\### `CODE\_OF\_CONDUCT.md`



```markdown

\# CODE\_OF\_CONDUCT

CephasOps Project



This project is maintained by the Cephas group and may be used by internal and external collaborators.



We expect all contributors to:



\- Treat others with respect and courtesy

\- Assume good intent, but give clear feedback

\- Avoid harassment, discrimination and personal attacks

\- Focus on technical issues, not individuals



Unacceptable behaviour includes (not exhaustive):



\- Insults, threats or intimidation

\- Discriminatory remarks based on race, religion, gender, etc.

\- Sharing sensitive or private customer data in public channels

\- Deliberate sabotage, vandalism or malicious code



Violations can be reported to the project maintainer or team lead.  

The maintainers may take appropriate action, including warning, revoking access to repositories, or escalating within the company.



This code of conduct applies in:



\- GitHub issues \& PRs

\- Chat tools (Slack/Teams/etc.) when discussing this project

\- Any communication related to CephasOps.

