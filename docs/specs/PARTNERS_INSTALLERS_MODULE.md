# PARTNERS\_INSTALLERS\_MODULE.md

\# PARTNERS\_INSTALLERS\_MODULE

CephasOps – Partners \& Installers Module



This module defines the master data for partners (telcos) and service installers (technicians).



---



\## Partners



\### Responsibilities



\- Store partner metadata:

&nbsp; - Name, email patterns, logos

&nbsp; - Order template mappings (activation, assurance, MRA)

&nbsp; - Portal URLs (TTKT, docket upload, invoice upload)



\### Fields (Partner)



See `PHASE1\_DOMAIN\_MODELS.md`.



---



\## Service Installers (SI)



\### Responsibilities



\- Maintain installer profiles:

&nbsp; - Name, contact, type (in-house/subcon), region

&nbsp; - Skills and KPI profiles

\- Link installers to orders and scheduler views.

\- Provide basis for SI performance dashboards \& payroll.



\### Fields (ServiceInstaller)



See `PHASE1\_DOMAIN\_MODELS.md`.



---



\## API



Documented in:



\- `spec/api/partners.api.md`

\- `spec/api/installers.api.md`.



UI layout:



\- `/settings` → Partners \& Installers sections.



