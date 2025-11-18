ORDERS_MODULE.md

(Combined: Setup + Technical Spec + Storybook)

This is the foundation module that all other modules depend on — parsing, scheduling, inventory, KPI, dockets, invoicing, and reporting all orbit around Orders.

# ORDERS MODULE  
### (Setup + Technical Specification + Storybook)

---

# 1. SETUP OVERVIEW

The **Orders Module** is the heart of the Cephas TIME Ops system.  
Every single job, regardless of origin (email, portal, manual), is represented as an **Order**.

An Order represents:

- Activations (FTTH / FTTO)
- Modifications (Relocation / Upgrade / Cancellation)
- Assurance cases (TTKT + AWO)
- SDU / RDF POLE jobs
- Any partner job: TIME, CELCOM, DIGI, UMobile

The entire business workflow is built on top of Orders:

1. **Email Parser → Order created**
2. **Scheduler → Order assigned to Service Installer**
3. **Status transitions → OnTheWay, MetCustomer, Completed**
4. **KPI calculation**
5. **Building & Splitter mapping**
6. **Docket stage**
7. **Invoicing**
8. **Payment tracking**

Orders are **immutable in history** – they store timestamps, logs, and KPI values.

---

# 2. TECHNICAL SPECIFICATION

## 2.1 Order Entity Structure

### Core Identifiers  
Required for all partners:

- **OrderId** (internal, auto-generated)  
- **ServiceId** (UNIQUE business key — e.g., TBBNxxxx, CELCOMxxx, DIGIxxxxx)  
- **Partner** (TIME / Celcom / Digi / UMOBILE)  
- **OrderType**
  - Activation
  - Modification
  - Assurance
  - Cancellation
  - SDU / RDF POLE (special)
- **TaskType**
  - Activation / Modification / Cancellation  
  *(Assurance orders use OrderType = Assurance)*

### Customer Information

- CustomerName  
- ServiceAddress (full raw)
- NormalisedAddress (cleaned & standardised)
- ContactPerson  
- ContactNo  

### Appointment Information

- AppointmentStart (ISO datetime)
- AppointmentEnd (ISO datetime)
  - If missing → auto-calc 1-hour default

### Location Information

- BuildingId (link to Buildings module)
- BuildingShortName  
- SplitterId  
- SplitterPort  
- IsStandbyPortOverride (true if using standby port like 32 in 1:32 splitter)

### Workflow Status
Pending
Assigned
OnTheWay
MetCustomer
OrderCompleted
DocketsReceived
DocketsUploaded
ReadyForInvoice
Invoiced
Completed

### Assurance-Specific Fields

Only for TIME assurance emails:

- TicketId (TTKTXXXX)
- AwoId (AWOXXXXX)
- ReportedIssueDescription
- ONUModel
- WarrantyStatus

### Assignment

- InstallerId (nullable)
- ScheduledStart
- ScheduledEnd

### KPI Fields

**Job KPI:**
- JobKpiTargetMinutes
- JobKpiActualMinutes
- JobKpiVarianceMinutes
- JobKpiMet (true/false)

**Docket KPI:**
- IsManualDocket
- DocketReceivedAt
- DocketKpiTargetMinutes (always 30 for manual)
- DocketKpiActualMinutes
- DocketKpiVarianceMinutes
- DocketKpiMet

### Portal Timestamp Fields

Captured during status updates:

- PortalTimeOnTheWay
- PortalTimeMetCustomer
- PortalTimeOrderCompleted

### Audit Fields

- CreatedFromEmailId  
- UpdatedFromEmailId  
- StatusChangeHistory (append-only)  
- AdminRemarks  
- BlockerReason (Customer / Building / Network)  

---

## 2.2 Order Status Transition Rules

All status transitions must go through a **central StatusChangeAsync** operation.

### Rules:

| New Status | Required Fields | Notes |
|-----------|------------------|-------|
| Assigned | InstallerId, ScheduledStart | Created via scheduler |
| OnTheWay | PortalTimeOnTheWay | Must follow Assigned |
| MetCustomer | PortalTimeMetCustomer | Must follow OnTheWay |
| OrderCompleted | PortalTimeOrderCompleted | Must follow MetCustomer |
| DocketsReceived | DocketReceivedAt, SplitterId, SplitterPort | Triggers KPI calc |
| DocketsUploaded | FileUploaded / PortalConfirm | |
| ReadyForInvoice | All docket checks OK | |
| Invoiced | InvoiceLine created | |
| Completed | Payment verified | |

All transitions log timestamps and compute KPIs where relevant.

---

## 2.3 Integration Points

### Email Parser → Orders
- Creates or updates Orders.
- Ensures field normalisation (datetime / address / phone).
- Uses ServiceId to detect duplicates.

### Scheduler → Orders
- Assigns InstallerId.
- Sets ScheduledStart and ScheduledEnd.
- Moves status to Assigned.

### Inventory → Orders
- Provides OrderMaterialPlan per building type.
- Logs OrderMaterialUsage.

### Buildings / Splitters → Orders
- Assign Splitter + Port at DocketsReceived.

### Dockets → Orders
- Marks DocketReceivedAt.
- Uploads docket file.
- Moves order into ReadyForInvoice.

### Invoicing → Orders
- Marks orders as Invoiced.
- Later marks as Completed when paid.

---

## 2.4 Order Validations

### Required for ALL Orders:
- ServiceId must be present.
- Partner cannot be empty.
- CustomerName required.
- AppointmentStart required.
- ServiceAddress required.

### For Assurance Orders:
- Must contain TicketId AND AwoId.

### For DocketsReceived:
- SplitterId required.
- SplitterPort required.
- If SplitterType = 1:32 AND Port = 32 → must have PartnerApproval.

### For ReadyForInvoice:
- All dockets verified.
- Materials usage completed.
- No unresolved blockers.

---

# 3. STORYBOOK (Narrative Understanding)

This is the human-level explanation used to align Cursor AI and the development team.

---

## 3.1 Story: The Order Appears

An email arrives from TIME:

> NOTIFICATION OF INSTALLATION – FTTH  
> Customer: COBNB SDN BHD  
> Service ID: TBBNB062587G  
> Appointment: 14 Nov 2025, 10:00 AM  

The Email Parser reads it, extracts data, normalises it, and creates:

- OrderType = Activation
- TaskType = Activation
- Partner = TIME
- ServiceId = TBBNB062587G
- Customer = COBNB SDN BHD
- Address = Cleaned + normalised
- AppointmentStart = 2025-11-14 10:00:00

This is now an **Order** in the system.

---

## 3.2 Story: Admin Schedules the Job

On the Scheduler page:

- Order appears in “Unassigned” column.
- Admin drags it to “Klavin” at 10:00 AM.
- System:
  - Creates assignment → InstallerId = Klavin
  - Sets ScheduledStart/End
  - Status becomes Assigned

Now Klavin sees this job in his daily schedule.

---

## 3.3 Story: Job Execution

On the day of work:

1. Klavin updates to **On The Way** at 9:45 AM.  
   → PortalTimeOnTheWay set.

2. At site, he updates to **Met Customer**.  
   → PortalTimeMetCustomer set.

3. After finishing the job, he updates **Order Completed**.  
   → PortalTimeOrderCompleted set.  
   → Job KPI is calculated (e.g., Prelaid = 60 minutes target).

Order now moves to OrderCompleted.

---

## 3.4 Story: Dockets Arrive

If manual:

- SI brings physical docket within 30 minutes.
- Admin marks **DocketsReceived**.
- System:
  - Validates docket.
  - Calculates Docket KPI (max 30 min).
  - Requires SplitterPort selection.
  - Records SplitterPortUsage.

Then admin uploads docket to partner portal → **DocketsUploaded**.

---

## 3.5 Story: Ready for Invoice → Payment

Admin marks order **ReadyForInvoice**.

Invoicing module:

- Creates invoice
- Adds invoice line
- Generates PDF

Order → **Invoiced**.

When partner pays:

- PaymentReceivedDate set.
- Order → **Completed**.

---

## 3.6 Story: Full Traceability

For any ServiceId, system can show:

1. Email it came from  
2. Customer details  
3. Building & Splitter port  
4. Assigned SI  
5. Job KPI timeline  
6. Docket timestamps & files  
7. Materials installed  
8. Invoice & payment status  

Every single part of the business is linked through the Order.

---

# END OF ORDERS MODULE
