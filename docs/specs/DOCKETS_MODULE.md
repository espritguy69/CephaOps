# DOCKETS\_MODULE.md

\# DOCKETS\_MODULE

CephasOps – Dockets Module Specification



The Dockets module links physical or digital job dockets to Orders and enforces docket-related KPIs.



---



\## Responsibilities



\- Record when dockets are received from SI

\- Compute time difference between job completion and docket receipt

\- Store scanned docket files (PDF/Image)

\- Block invoicing until docket is validated



---



\## Flows



\### 1. Docket Received



\- Admin marks docket as received after SI submits.

\- System:

&nbsp; - Records `DocketsReceivedAt`.

&nbsp; - Computes time since `OrderCompletedAt`.

&nbsp; - Flags KPI success/failure (e.g. <= 30 minutes).



\### 2. Docket Upload



\- Admin uploads scanned docket to the order.

\- System:

&nbsp; - Stores file in file storage.

&nbsp; - Sets `DocketsUploadedAt`.

&nbsp; - Update order status to `DocketsUploaded`.



\### 3. Invoice Eligibility



\- Only orders with:

&nbsp; - `OrderCompleted`

&nbsp; - `DocketsUploaded`

&nbsp; - No unresolved blockers

&nbsp; - are eligible for `ReadyForInvoice`.



---



\## Entities



Dockets do not need a separate entity in Phase 1; they can be represented as:



\- Fields on `Order`:

&nbsp; - `DocketsReceivedAt`

&nbsp; - `DocketsUploadedAt`

\- Attachments table (if needed) referencing `OrderId`.



---



\## API



\- `POST /api/orders/{orderId}/dockets/received`

\- `POST /api/orders/{orderId}/dockets/upload` (multipart)



See `spec/api/orders.api.md` and `spec/api/invoices.api.md`.



