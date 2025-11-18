# PNL\_COSTCENTRE\_MODULE.md

\# PNL\_COSTCENTRE\_MODULE

CephasOps – PNL \& Cost Centre Module



This module links operational data to financial reporting.



---



\## Responsibilities



\- Define cost centres for each company.

\- Allocate revenue \& costs to cost centres.

\- Support PNL views at:

&nbsp; - Company level

&nbsp; - Cost centre level

&nbsp; - Group level (multi-company)



---



\## Entity: CostCentre



See `PHASE1\_DOMAIN\_MODELS.md`.



---



\## Allocation Logic (Phase 1)



\- Revenue:

&nbsp; - Each `InvoiceLine` is assigned a `CostCentreId`.

\- Costs:

&nbsp; - SI labour cost per order can be mapped to cost centre (via building or branch).

&nbsp; - Material costs can be aggregated by cost centre based on order mappings.



Details of allocation rules can evolve, but the CostCentre entity and link fields are designed now to support future PNL refinement.



