# INVENTORY\_MODULE.md

\# INVENTORY\_MODULE

CephasOps – Inventory Module Specification



The Inventory module manages all materials used for ISP operations.



---



\## Responsibilities



\- Maintain materials catalogue (`Material`)

\- Track stock in:

&nbsp; - Warehouse

&nbsp; - Installer on-hand

&nbsp; - At customer site

&nbsp; - RMA/faulty

\- Record material movements

\- Support RMA process

\- Feed financial and PNL reporting



---



\## Primary Entities



\- `Material`

\- `MaterialStock`

\- `MaterialMovement`

\- `RmaRequest`



See `PHASE1\_DOMAIN\_MODELS.md` for field-level definitions.



---



\## Flows



\### 1. Material Issue (Warehouse → Installer)



\- Warehouse issues router/ONU/cable to SI.

\- System:

&nbsp; - Creates `MaterialMovement` (Warehouse → Installer).

&nbsp; - Adjusts `MaterialStock` for Warehouse and Installer.



\### 2. Job Completion (Installer → Customer)



\- On job completion, SI records materials used.

\- System:

&nbsp; - Creates `MaterialMovement` (Installer → Customer).

&nbsp; - Updates `MaterialStock` accordingly.



\### 3. Returns \& Faulty



\- Unused materials can be returned:

&nbsp; - `Installer → Warehouse`.

\- Faulty items:

&nbsp; - `Installer → RMA`.

\- `RmaRequest` may be created for serialised items.



---



\## API \& UI



\- Endpoint overview in `spec/api/inventory.api.md`.

\- Admin UI page:

&nbsp; - `/inventory` with:

&nbsp;   - Stock view by material \& location

&nbsp;   - Movements history

&nbsp;   - RMA list \& details.



---



\## KPI \& Reporting



\- Stock alerts when below min threshold (future).

\- Movement data feeds:

&nbsp; - Cost of materials per job.

&nbsp; - Partner-wise and company-wise PNL.



