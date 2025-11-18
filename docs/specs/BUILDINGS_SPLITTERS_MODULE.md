# BUILDINGS\_SPLITTERS\_MODULE.md

\# BUILDINGS\_SPLITTERS\_MODULE

CephasOps – Buildings \& Splitters Module Specification



This module describes physical locations (Buildings) and their fibre splitters \& ports.



---



\## Responsibilities



\- Maintain building master data

\- Define splitter devices installed per building

\- Manage splitter port usage

\- Enforce standby port approval rules



---



\## Entities



\- `Building`

\- `Splitter`

\- `SplitterPort`



See `PHASE1\_DOMAIN\_MODELS.md`.



---



\## Flows



\### 1. Create Building



\- Admin creates building with:

&nbsp; - Name, short name, type, address.

\- Used when mapping parsed orders to known sites.



\### 2. Configure Splitters



\- For each building:

&nbsp; - Add splitter(s) with:

&nbsp;   - Type (1x8, 1x12, 1x32)

&nbsp;   - Location description

&nbsp; - Create ports:

&nbsp;   - 1..N based on type.

&nbsp;   - If 1x32, port 32 is standby by default (`RequiresApproval = true`).



\### 3. Assign Port to Order



\- After job completion:

&nbsp; - Admin chooses splitter and port.

&nbsp; - System:

&nbsp;   - Marks port as Used.

&nbsp;   - Links to `OrderId`.

&nbsp;   - For standby port:

&nbsp;     - Requires approval attachment.



---



\## UI



\- Building list \& detail screens:

&nbsp; - With nested splitters \& ports views.

\- Order detail:

&nbsp; - “Materials \& Splitter” tab for selecting splitter \& port.



