# SETTINGS\_MODULE.md

\# SETTINGS\_MODULE

CephasOps – Settings Module



The Settings module centralises configuration for workflows, rates, notifications and integrations.



---



\## Sections



1\. \*\*Workflow \& KPIs\*\*

&nbsp;  - Per partner \& order type:

&nbsp;    - KPI thresholds (OnTheWay, MetCustomer, DocketsReceived)

&nbsp;    - Allowed status transitions.



2\. \*\*Parser Rules\*\*

&nbsp;  - See `PARSER\_RULES\_MODULE.md`.



3\. \*\*Notifications\*\*

&nbsp;  - Event types: NewOrder, Rescheduled, LateDocket, KpiWarning, InvoiceDue.

&nbsp;  - Channels: Email, WhatsApp, SMS.

&nbsp;  - Recipients: users or groups.



4\. \*\*SI Rates \& Payroll\*\*

&nbsp;  - Rates per:

&nbsp;    - Order type (Prelaid/NonPrelaid/FTTR/FTTC/SDU/RDF)

&nbsp;    - Company

&nbsp;    - Installer type (InHouse/Subcon).



5\. \*\*Cost Centres \& Branches\*\*

&nbsp;  - PNL allocation structure.



6\. \*\*Vertical \& Feature Toggles\*\*

&nbsp;  - Enable/disable ISP, barbershop or travel specific modules for a company.



7\. \*\*External Portals \& Links\*\*

&nbsp;  - TTKT URLs, docket upload URLs, RMA portals per partner.



---



\## API



See:



\- `spec/api/settings.api.md`

\- `spec/api/parser.api.md`

\- `spec/api/notifications.api.md`.



