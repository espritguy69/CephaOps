# ORDERS\_MODULE.md

\# ORDERS\_MODULE

CephasOps – Orders Module Specification



The Orders module is the heart of CephasOps, managing activation \& assurance jobs from creation to completion.



---



\## Responsibilities



\- Create orders from parsed emails (activation \& assurance)

\- Allow admins/schedulers to view \& filter orders

\- Assign installers and manage rescheduling

\- Track status timeline \& KPIs

\- Link to Buildings, Splitters, Inventory, Dockets, Invoices



---



\## Entities



\- `Order`

\- `OrderStatusLog`



See `PHASE1\_DOMAIN\_MODELS.md` for field details.



---



\## Order Lifecycle



1\. \*\*Pending\*\*

&nbsp;  - Created by email parser or manually.

2\. \*\*Assigned\*\*

&nbsp;  - Installer assigned via scheduler.

3\. \*\*OnTheWay\*\*

&nbsp;  - SI starts travelling.

4\. \*\*MetCustomer\*\*

&nbsp;  - SI meets customer on site.

5\. \*\*OrderCompleted\*\*

&nbsp;  - Work done, materials recorded.

6\. \*\*DocketsReceived\*\*

&nbsp;  - Docket submitted to office.

7\. \*\*DocketsUploaded\*\*

&nbsp;  - Docket scanned \& uploaded into system.

8\. \*\*ReadyForInvoice\*\*

&nbsp;  - Order passed validation, ready for billing.



Transitions logged in `OrderStatusLog`.



---



\## Integrations



\- \*\*Email Parser\*\*

&nbsp; - Calls `POST /internal/parsed-orders` to create orders.

\- \*\*Scheduler\*\*

&nbsp; - Uses assignment \& reschedule endpoints.

\- \*\*SI PWA\*\*

&nbsp; - Calls SI-specific status endpoints (OnTheWay, MetCustomer, Complete).

\- \*\*Inventory\*\*

&nbsp; - Triggered when orders complete to deduct stock.

\- \*\*Billing\*\*

&nbsp; - Uses completed \& docket-validated orders to generate invoices.



---



\## API \& UI



\- Endpoints:

&nbsp; - documented in `spec/api/orders.api.md`.

\- UI:

&nbsp; - `PAGES.md`: `/orders` list and `/orders/:id` detail layout.

&nbsp; - `WIREFRAMES.md`: structural wireframes for Orders screens.



Orders is the main link between field ops and revenue.



