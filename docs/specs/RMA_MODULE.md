# RMA\_MODULE.md

\# RMA\_MODULE

CephasOps – RMA \& MRA Module



The RMA module tracks faulty equipment returned to partners and associated MRA documents.



---



\## Responsibilities



\- Record faulty routers/ONUs

\- Create RMA requests for serialised items

\- Track status with partners

\- Link MRA PDFs to RMA requests



---



\## Entity: RmaRequest



See `PHASE1\_DOMAIN\_MODELS.md`.



---



\## Flows



\### 1. Mark Item as Faulty



\- SI or Warehouse flags item as faulty.

\- System:

&nbsp; - Creates `MaterialMovement` to RMA location.

&nbsp; - Optionally creates `RmaRequest` if serialised.



\### 2. Partner MRA Email



\- Partner sends MRA PDF by email.

\- Parser:

&nbsp; - Saves PDF.

&nbsp; - Attempts to match to existing `RmaRequest` by serial number or reference.

&nbsp; - Attaches `MraPdfUrl`.



\### 3. Close RMA



\- On resolution:

&nbsp; - Status → Approved / Rejected / Completed.

&nbsp; - Optional material stock adjustments.



---



\## API



See `spec/api/inventory.api.md` for RMA endpoints.



