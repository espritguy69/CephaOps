# INVOICING\_MODULE.md

\# INVOICING\_MODULE

CephasOps – Invoicing Module Specification



The Invoicing module manages creation, tracking and portal submission of invoices.



---



\## Responsibilities



\- Generate invoices from completed orders

\- Apply partner-specific rate sheets

\- Track status: Draft → ReadyForInvoice → Invoiced → Paid/Rejected

\- Store portal Submission ID and dates

\- Provide data to PNL reports



---



\## Entities



\- `Invoice`

\- `InvoiceLine`



---



\## Flows



\### 1. Generate Invoice



Input:



\- Company \& Partner

\- One or more eligible OrderIds



System:



\- Loads each order.

\- Applies rate sheet:

&nbsp; - Standard activation fee

&nbsp; - Additional charges (extra fibre length, boosters, etc.)

\- Creates `Invoice` and `InvoiceLine` rows.

\- Sets status = `Draft` or `ReadyForInvoice`.



\### 2. Submit to Partner Portal



\- Admin logs into TIME portal and uploads invoice PDF, docket, photos.

\- Portal returns Submission ID.

\- Admin records Submission ID via API:

&nbsp; - sets `PortalSubmissionId` and `SubmissionDate`.

&nbsp; - Status → `Invoiced`.



\### 3. Payment \& Rejection



\- When payment received:

&nbsp; - `POST /api/invoices/{id}/mark-paid` with `paidDate`.

\- If rejected:

&nbsp; - `POST /api/invoices/{id}/reject` with `reason`.

&nbsp; - Admin may correct and re-submit keeping same Submission ID.



---



\## API



See `spec/api/invoices.api.md` and `spec/api/API\_BLUEPRINT.md`.



---



\## UI



\- `/invoices` list page:

&nbsp; - Filters by partner, period, status.

\- Invoice detail:

&nbsp; - Lines, attachments, Submission ID, status controls.



