Understood — I will now generate the FINAL MASTER DOCUMENT:

✅ SYSTEM_OVERVIEW.md

This will be the single high-level architecture file that ties together:

All 10 modules

All workflows

All entities

All pages

All rules

All status flows

All integrations

All validations

All KPIs

All technical foundations

This is the “top-level blueprint” you will give Cursor AI before any coding begins.

📘 SYSTEM_OVERVIEW.md
Master Architecture Document for Cephas Service Installer Operations System
# SYSTEM OVERVIEW  
### Cephas Service Installer Operations System (CSIOS v1.0)

This document provides a top-level architecture for the full Cephas ecosystem:

- Order Management  
- Email Parsing  
- Scheduling  
- Materials & Inventory  
- Buildings, Splitters & Port Tracking  
- Service Installers  
- Status Flow & KPI Engine  
- Dockets  
- Invoicing  
- Reporting  
- Settings (Master Data)

This overview links all modules and shows how the system works as one unified engine.

---

# 1. SYSTEM PURPOSE

The goal of CSIOS is to automate and standardize all operational flows for:

**TIME, Celcom, Digi, U Mobile & other ISP partner installation and assurance jobs.**

The system:

- auto-imports orders from emails & Excel  
- parses customer details, materials, appointments  
- books and schedules service installers  
- tracks port usage & splitters  
- logs status updates for each step  
- enforces KPI (job duration + docket submission)  
- validates and uploads dockets  
- manages invoicing & payment tracking  
- handles materials, serialized devices & stock flow  
- generates analytics dashboards  
- provides CRUD-based setup for all master data

---

# 2. CORE MODULES (Linked)

1. **Orders Module**  
2. **Email Parser Module**  
3. **Status & KPI + Splitter Enforcement Module**  
4. **Scheduler Module**  
5. **Inventory & Materials Module**  
6. **Invoicing Module**  
7. **Buildings & Splitters Module**  
8. **Partners & Service Installers Module**  
9. **Dockets Module**  
10. **Reporting Module**  
11. **Settings Module**

Each module has its own standalone documentation file.

---

# 3. HIGH-LEVEL SYSTEM FLOW



Incoming Email / Manual Entry
↓
Order Created
↓
Scheduler Assigns SI
↓
SI Job Execution
↓
Status Tracking (10 steps)
↓
Splitter Port Selection
↓
Order Completed
↓
Docket Submission (must be within 30 min KPI)
↓
Admin Review
↓
Docket Uploaded to Partner
↓
Invoice Generated
↓
Payment Cycle Tracking (45 Days)
↓
Order Fully Closed
↓
Reporting & KPI Analytics


---

# 4. ENTITY RELATIONSHIP SUMMARY

### Key Entities

| Entity | Purpose |
|--------|---------|
| **Order** | Master record containing service ID, type, customer info |
| **Service Installer** | Technician performing job |
| **Building** | Location of installation |
| **Splitter** | 1:8 / 1:12 / 1:32 port units storing capacity info |
| **InventoryItem** | Cables, ONU, Router, Connectors, Patchcords |
| **InventoryTransaction** | Movement of each item |
| **Docket** | Proof of work completed |
| **Invoice** | Billing & payment tracking |
| **Partner** | ISP brand (TIME, Celcom, Digi, U Mobile) |

---

# 5. DATA FLOW BETWEEN MODULES

### 5.1 Order Creation ← Email Parser  
- Excel → Parse → Create Order  
- Email Body → Parse → Create Assurance Order  
- Assign unique ID per partner rules  
- Pull building & partner settings  
- Auto-load default materials for building type  
- Track materials required  

### 5.2 Scheduler  
- Reads SI skills  
- Reads SI availability  
- Drag-and-drop calendar  
- Tracks booking history  
- Updates status = Assigned  

### 5.3 Installer Workflow  
Statuses updated:



Pending → Assigned → OnTheWay → MetCustomer → OrderCompleted


System logs:

- Timestamp  
- SI location (future)  
- KPI duration  
- Splitter port used  

### 5.4 Docket Submission  
After job completed:

- Docket must be submitted in **30 minutes KPI**  
- Admin uploads manual docket  
- System validates: serial, signature, port  
- If mismatch → RequiresCorrection  

### 5.5 Partner Upload  
Admin uploads docket to TIME / Celcom portal.

System updates:



Status = DocketsUploaded → ReadyForInvoice


### 5.6 Invoicing  
System generates:

- Invoice number  
- PDF  
- Uploads to partner portal  
- Sets payment due date (45 days)  

### 5.7 Reporting  
All data feeds into:

- Installer KPI  
- Docket KPI  
- Material tracking  
- Splitter usage analytics  
- Partner performance  

---

# 6. STATUS FLOW ENGINE

This is the heart of operational tracking.



Pending

Assigned

OnTheWay

MetCustomer

OrderCompleted

DocketsReceived

DocketsUploaded

ReadyForInvoice

Invoiced

Completed (Paid)


**Blockers** may be inserted between:

- MetCustomer → OrderCompleted  
- OrderCompleted → DocketReceived  

Blocker types:

- Customer  
- Building  
- Network  

---

# 7. KPI ENGINE

### Job Duration KPI  
| Job Type | KPI Duration |
|---------|--------------|
| Prelaid | 1 hour |
| Non-Prelaid | 2 hours |
| SDU / FTTR / FTTC / RDF Pole | 3 hours |

System compares:



Actual job duration = CompletedAt – MetCustomerAt


### Docket KPI  


Docket must be submitted within 30 minutes of job completion.


KPI applies per SI.

---

# 8. SPLITTER PORT MANAGEMENT

### Splitter types:
- 1:8  
- 1:12  
- 1:32 (has standby port 32)

### Rules:
- Every activation must consume a port  
- Port cannot be reused  
- Standby port (32) requires partner approval  
- Approval file required  
- System blocks order if approval missing  

### Tracking:
- Port used  
- Order ID  
- SI who used it  
- Date/time  

---

# 9. INVENTORY FLOW

### 9.1 For each job:



Warehouse → Service Installer → Customer


### 9.2 If job fails:



Installer → Warehouse


### 9.3 Assurance:
- Old device must return  
- New device issued  
- Old serial logged  

### Serialized items tracked:

- ONU  
- Router  
- Deco/Mesh WiFi  
- Boosters  
- VoIP equipment  

---

# 10. INVOICING LIFECYCLE

1. OrderCompleted  
2. DocketReceived  
3. DocketUploaded  
4. InvoiceCreated  
5. Invoice PDF uploaded to partner portal  
6. Payment countdown starts (45 days)  
7. Status = Invoiced  
8. When payment validated → Completed (Paid)

### If rejected:

- Reason required  
- Correction cycle triggered  

---

# 11. REPORTING LAYERS

The reporting module covers:

- Daily operations  
- Installer KPI  
- Docket KPI  
- Partner breakdown  
- Building-level performance  
- Material consumption  
- Splitter capacity  
- Financial reports  
- Aging payments  
- Revenue forecast  
- Missing materials  
- Rejections (partner & internal)

All reports support:

- Filters  
- Date ranges  
- CSV export  
- PDF export  

---

# 12. SETTINGS MODULE (MASTER DATA CONTROL)

Settings allow CRUD for:

- Materials  
- Buildings  
- Splitters  
- Service Installers  
- Partners  
- Status flow  
- Blockers  
- KPI rules  
- SI payment rates  
- Default material templates  

Any change applies system-wide instantly.

---

# 13. SYSTEM ARCHITECTURE LAYERING



Frontend (React/Next/Web)
↓
Backend (ASP.NET Core)
↓
Database (SQL Server)
↓
File Storage (Local / Cloud)
↓
Integrations (Email, Partner Portals)


---

# 14. INTEGRATION POINTS

### 14.1 Email  
- IMAP/POP3 inbox  
- Excel attachment  
- Plain text assurance email  
- Parser populates Orders  

### 14.2 Partner Portals  
- Manual upload for:  
  - Dockets  
  - Invoices  
- System tracks upload time  

### 14.3 PDF Generation  
- For invoices  
- For reporting  
- For archiving  

---

# 15. PAGE STRUCTURE (Frontend Navigation)

### Top Navigation
- Dashboard  
- Orders  
- Scheduler  
- Inventory  
- Invoices  
- Reporting  
- Settings  
- Buildings  

### Settings Subpages
- Materials  
- Installers  
- Partners  
- Blockers  
- Status Flow  
- KPI Rules  
- Default Material Templates  

---

# 16. FINAL SYSTEM OBJECTIVE

Provide a **fully automated** and **fully tracked** operational workflow that ensures:

- Zero missed orders  
- Zero misplaced dockets  
- Zero untracked materials  
- Zero delayed invoices  
- Installer accountability  
- Accuracy in splitter usage  
- Real-time KPI monitoring  
- Faster payment cycles  
- Full compliance with partner SOP  
- Scalable, future-proof system  

---

# END OF SYSTEM OVERVIEW
