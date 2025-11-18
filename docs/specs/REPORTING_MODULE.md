# REPORTING\_MODULE.md

\# REPORTING\_MODULE

CephasOps – Reporting \& PNL Module



The Reporting module provides both operational and financial insights.



---



\## Responsibilities



\- Operational KPIs:

&nbsp; - Orders per partner

&nbsp; - On-time arrival

&nbsp; - Docket submission speed

&nbsp; - Assurance volumes

\- Financial PNL:

&nbsp; - Revenue by partner \& period

&nbsp; - Cost of labour \& materials

&nbsp; - PNL per company and per cost centre

&nbsp; - Group PNL across companies (Director only)



---



\## Data Sources



\- `Order`, `OrderStatusLog`

\- `Invoice`, `InvoiceLine`

\- `MaterialMovement`, `RmaRequest`

\- `CostCentre`



---



\## API



\- `GET /api/reports/orders`

\- `GET /api/pnl`

\- `GET /api/pnl/group`



See `spec/api/reporting.api.md`.



---



\## UI



\- `/pnl` – company PNL dashboard.

\- `/pnl/group` – director cross-company view.

\- Additional reports accessible from `/dashboard` shortcuts.



