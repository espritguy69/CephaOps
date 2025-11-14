Perfect — here are clean, professional text-based wireframe UI mockups for:

Orders Page

Scheduler (Calendar) Page

Dockets Review Page

Splitters & Ports Page

Settings (Master Data) Page

These are ASCII wireframes designed for Cursor AI and developers to instantly understand the layout and component placement.

🎨 1. ORDERS PAGE — Wireframe
+-------------------------------------------------------------+
|                        ORDERS PAGE                          |
+-------------------------------------------------------------+

[ Search Box: Service ID / Customer / Building / SI ] [Filter]

[ Partner ▼ ] [ Order Type ▼ ] [ Status ▼ ] [ Date Range ▼ ]
----------------------------------------------------------------

| Service ID | Customer Name      | Building      | Partner |
|------------|---------------------|---------------|---------|
| TBBN1234G  | COBNB SDN BHD       | Royce Res     | TIME    |
|------------|---------------------|---------------|---------|
| Status: Pending | Appointment: 2025-11-14 10:00 |
| SI: Not Assigned | Splitter: N/A                 |
| [ View ] [ Assign SI ] [ Edit ] [ Change Status ]          |
----------------------------------------------------------------

| Service ID | Customer Name      | Building      | Partner |
|------------|---------------------|---------------|---------|
| CELCOM567  | Adam Putra          | Radia Resid   | Celcom  |
|------------|---------------------|---------------|---------|
| Status: On The Way | Appt: 2025-11-14 14:00     |
| SI: Klavin           | Splitter: 1:32 / Port 08 |
| [ View ] [ Change Status ] [ Open Docket ]                 |
----------------------------------------------------------------

[ + Create Manual Order ]

🎨 2. SCHEDULER (CALENDAR) PAGE — Wireframe
+-------------------------------------------------------------+
|                        SCHEDULER PAGE                       |
+-------------------------------------------------------------+

[ Date Navigation: <  Nov 2025  > ]  [ View: Day | Week | Month ]
[ Filter: SI ▼ ] [ Partner ▼ ] [ Building ▼ ] [ Skills ▼ ]

----------------------------------------------------------------
|   MON  |   TUE  |   WED  |   THU  |   FRI  |   SAT  |   SUN  |
----------------------------------------------------------------
|        |        |        | 09:00 |        |        |        |
|        |        |        | ------- Order Card ---------------|
|        |        |        | Service ID: TBBN1234G             |
|        |        |        | Customer: COBNB                   |
|        |        |        | Time: 10:00-11:00                 |
|        |        |        | SI: Alex                          |
|        |        |        | Status: Assigned                  |
|        |        |        | [ Change Status ▼ ]               |
----------------------------------------------------------------
| Drag SI List (Left Pane):                                     |
|  - Klavin (FTTH, Assurance)                                   |
|  - Alex (FTTH, SDU)                                           |
|  - Danial (FTTR, FTTC, RDF)                                   |
|  - Marcus (All Skills)                                        |
----------------------------------------------------------------

(Drag SI → drop onto a time slot to assign.)

🎨 3. DOCKET REVIEW PAGE — Wireframe
+-------------------------------------------------------------+
|                       DOCKET REVIEW PAGE                     |
+-------------------------------------------------------------+

[ Back to Order ]

Service ID: TBBN1234G
Customer: COBNB SDN BHD
Building: Royce Residence
Completion Time: 2025-11-14 11:00 AM
Docket KPI: 30 min required

----------------------------------------------------------------
|                        DOCKET UPLOAD                         |
----------------------------------------------------------------
[ Upload JPG/PDF ] [ Re-upload ]  [ View Fullscreen ]

Uploaded File:
[ Thumbnail Preview Here ]

Checkboxes:
[✓] Customer Signature Present
[✓] Installer Signature Present
[ ] Serial Number Match (Mismatch!)
[✓] Work Details Correct
[✓] Date Correct

----------------------------------------------------------------
|                     SPLITTER & PORT DETAILS                  |
----------------------------------------------------------------
Splitter: 1:32
Port: 08
Standby Port Used? No

----------------------------------------------------------------
|                  ADMIN REVIEW ACTIONS                        |
----------------------------------------------------------------
[ Approve Docket ]     [ Reject / Needs Correction ]  
Reason (if rejecting):
[ Textbox ]

If Standby Port = 32:
→ “Upload Partner Approval (Required): [ Browse File ]”

🎨 4. SPLITTERS & PORTS PAGE — Wireframe
+-------------------------------------------------------------+
|                  BUILDING SPLITTERS PAGE                     |
+-------------------------------------------------------------+

Select Building: [ Royce Residence ▼ ]

----------------------------------------------------------------
Splitter List
----------------------------------------------------------------
| Splitter Code | Type | Location | Ports Used | Ports Free | View |
|---------------|------|----------|------------|-------------|------|
| SP-B1         | 1:32 | MDF Room |    25      |     7       | View |
| SP-B2         | 1:8  | G Floor  |     7      |     1       | View |
----------------------------------------------------------------

Click "View":

Splitter: SP-B1 (1:32)
Location: MDF Room

----------------------------------------------------------------
| PORT | STATUS        | ORDER ID     | SI        | STANDBY? |
----------------------------------------------------------------
| 01   | Used          | TBBN12345    | Klavin    | No       |
| 02   | Used          | TBBN12333    | Alex      | No       |
| 03   | Free          |              |           | No       |
| …    | …             | …            | …         | …        |
| 32   | Standby (Rsv) | N/A          | N/A       | YES      |
----------------------------------------------------------------

[ Reserve Port ] [ Release Port ] [ View History ]

🎨 5. SETTINGS (MASTER DATA) PAGE — Wireframe
+-------------------------------------------------------------+
|                        SETTINGS PAGE                         |
+-------------------------------------------------------------+

Left Sidebar:
- Materials
- Service Installers
- Partners
- Buildings
- Splitters
- Default Material Templates
- Blockers
- Status Flow
- KPI Rules
--------------------------------------------------------------

### MATERIALS
[ + Add Material ]
----------------------------------------------------------------
| Name          | Type        | Serialized | Min Stock | Active |
|---------------|-------------|------------|-----------|--------|
| ONU HG8145X6  | ONU         | Yes        | 10        | Yes    |
| Fiber 80m     | Cable       | No         | 50        | Yes    |
----------------------------------------------------------------

### SERVICE INSTALLERS
[ + Add Installer ]
----------------------------------------------------------------
| Name   | Skills                   | Contact  | Active |
|--------|---------------------------|----------|--------|
| Klavin | FTTH, Assurance          | 013...   | Yes    |
| Alex   | FTTH, SDU                | 017...   | Yes    |
----------------------------------------------------------------

### KPI RULES
----------------------------------------------------------------
Prelaid = 1 hour
Non-Prelaid = 2 hours
SDU/FTTR/FTTC/RDF = 3 hours
Docket KPI = 30 minutes
[ Edit ]
----------------------------------------------------------------

### STATUS FLOW (Editable)
----------------------------------------------------------------
Pending → Assigned → OnTheWay → MetCustomer → Completed → DocketsReceived → …
[ Edit Flow ]
----------------------------------------------------------------

🎉 All Wireframes Completed
