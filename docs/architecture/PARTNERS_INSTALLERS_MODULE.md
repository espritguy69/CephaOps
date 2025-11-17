📘 PARTNERS_INSTALLERS_MODULE.md
(Combined: Setup + Technical Specification + Storybook)

This module covers:

Partner master data (TIME, Celcom, Digi, UMobile)

Their billing rules, email patterns, and SLAs

Service Installer (SI) master data

SI skills, rates, contact info, KPI targets

Partners + Installers are the two main actors around each job.

# PARTNERS & SERVICE INSTALLERS MODULE  
### (Setup + Technical Specification + Storybook)

---

# 1. SETUP OVERVIEW

This module manages two major master datasets:

1. **Partners**  
   (TIME, Celcom, Digi, UMobile, etc.)  
   - How they send jobs  
   - How we bill them  
   - Their payment terms  
   - Special rules (e.g. standby port approvals)

2. **Service Installers (SIs)**  
   - Who actually does the job  
   - Their skills & zones  
   - Their contact details  
   - Their job-type rates (for SI payment)  
   - Their KPI tracking

These are used across:

- Email Parser  
- Orders  
- Scheduler  
- Inventory  
- Invoicing  
- KPI / Reporting  

---

# 2. TECHNICAL SPECIFICATION

---

# 2.1 Partner Entity Structure

Each partner record must contain:

### Identity
- **PartnerId**  
- **Name** (e.g. TIME dotCom, Celcom, Digi, UMobile)  
- **ShortCode** (TIME, CELCOM, DIGI, UMB)  

### Contact & Billing Details
- **BillingAddress**  
- **SupportEmail**  
- **PortalUrl** (e.g. TIME portal link)  
- **DefaultPaymentTermsDays** (e.g. 45)  

### Order Behaviours
- **SupportedOrderTypes**  
  - Activation / Modification / Assurance / SDU / RDF Pole / Cancellation  
- **EmailPatterns**  
  - Typical “From” addresses  
  - Subject templates  
- **AttachmentTemplates**  
  - Excel layout IDs  
- **RequiresTTKT** (for Assurance)  
- **RequiresAWO** (for Assurance)  

### Invoicing Rules
- **InvoiceFormat** (Single-Job / Batch)  
- **InvoicePrefix** (e.g. `TIME-YYYYMM-`)  
- **AllowMultipleJobsPerInvoice** (true/false)  

### Rate Rules (for Cephas revenue)
Stored in a nested table:

| OrderType | TaskType | Rate |
|----------|----------|------|
| Activation | Activation | X |
| Modification | Modification | Y |
| Assurance | Assurance | Z |
| SDU | Activation | A |
| RDFPole | Activation | B |

*(Exact numbers are configured later.)*

### Technical Rules
- **RequiresStandbyPortApproval** (true for TIME)  
- **RequiresSplitterInfoAtDocket** (true for TIME)  

---

# 2.2 Service Installer (SI) Entity Structure

Each SI is an individual or team doing on-site work.

### Identity
- **InstallerId**  
- **FullName**  
- **Nickname** (for scheduler label, e.g. “Klavin”)  
- **Active** (true/false)  

### Contact Details
- **Phone**  
- **WhatsApp**  
- **Email**  
- **EmergencyContactName**  
- **EmergencyContactPhone**  

### Operational Attributes
- **Region/Zone**  
  - E.g. Klang Valley, Bahau, Subang, PJ  
- **VehicleType**  
  - Bike, Car, Van, Lorry (optional)  
- **MaxJobsPerDay**  
- **PreferredShifts**  
  - Morning / Afternoon / Evening  
- **CanDoNightJobs**  

### Skills Matrix
Boolean flags:

- **CanDoFTTH**  
- **CanDoFTTO**  
- **CanDoFTTR**  
- **CanDoFTTC**  
- **CanDoSDU**  
- **CanDoRDFPole**  
- **CanDoAssurance**  
- **CanHandleHighRise**  
- **CanHandleIndustrial**  

### KPI Targets (SI-level overrides)
Optional overrides for global KPI values:

- **JobKpiPrelaidMinutesOverride**  
- **JobKpiNonPrelaidMinutesOverride**  
- **JobKpiFTTRMinutesOverride**  

If null → use system defaults (60 / 120 / 180).

### SI Rate Table (for paying SI)

Not for partner billing, but for Cephas’ internal payment:

| OrderType | TaskType | RateAmount |
|-----------|-----------|-----------|
| Activation | Activation | RMxx |
| Modification | Modification | RMxx |
| Assurance | Assurance | RMxx |
| SDU | Activation | RMxx |
| RDF Pole | Activation | RMxx |

This rate table is used later in **SI Payment / Payroll** module (future).

---

# 2.3 Partner–Order Integration

For each order:

- **PartnerId** references a partner  
- The system uses partner config to:
  - Decide email parsing rules  
  - Validate required fields (TTKT/AWO)  
  - Set payment terms (45 days for TIME)  
  - Apply rate rules for invoice amount  
  - Enforce special rules (splitter approval, etc.)

---

# 2.4 SI–Order Integration

For each order:

- **InstallerId** references a Service Installer  
- Scheduler uses:
  - Skills  
  - Region  
  - Availability  

KPI runs per SI based on:

- OnTheWay / MetCustomer / Completed timestamps  
- Job KPI rules (Prelaid, Non-Prelaid, FTTR, etc.)  

Reports show:

- Jobs per SI  
- KPI pass/fail rate  
- Docket KPI compliance  
- Revenue handled per SI  

---

# 2.5 SI Availability & Status

Installer can have:

- **Active/Inactive** flag  
- **OnLeaveFrom / OnLeaveTo**  
- **DailyCapacity** (max jobs/day)  

Scheduler must:

- Exclude inactive SIs  
- Warn if SI is near capacity  

---

# 2.6 Partner SLA & Reporting

Partner config supports:

- **Payment SLA days** (e.g. 45 days for TIME)  
- **Response SLA** for Assurance  
- **Preferred Docket upload timing**  

Reporting can later show:

- SLA breaches per partner  
- Payment delays per partner  

---

# 2.7 Data Validation Rules

### For Partners:
- Name required  
- ShortCode required  
- DefaultPaymentTermsDays > 0  
- At least 1 SupportedOrderType  

### For SIs:
- FullName required  
- Phone required  
- At least 1 skill must be TRUE  
- Must belong to at least 1 region/zone  

---

# 3. STORYBOOK (Narrative Understanding)

---

## 3.1 Story: TIME as a Partner

TIME dotCom is configured as:

- Name: TIME dotCom  
- ShortCode: TIME  
- PaymentTerms: 45 days  
- Supported Orders: Activation, Modification, Assurance, Cancellation, SDU, RDF Pole  
- Special rules:
  - Requires TTKT + AWO for Assurance  
  - Requires splitter + port at DocketsReceived  
  - Standby 1:32 port requires approval  

So when the parser sees a TIME email:

- It knows to enforce TTKT/AWO  
- It knows invoice due = UploadDate + 45 days  
- It expects Excel templates for Activation/Modification  

---

## 3.2 Story: Celcom / Digi / UMobile

Each partner has:

- Their sender domains  
- Their subject patterns  
- Their own rates  
- Their own payment terms  

The system isolates logic to **Partner config**, not hardcoded anywhere else.

---

## 3.3 Story: Creating a new SI

New installer joins:

> Name: Arun  
> Phone: 012-xxxxxxx  
> Region: Klang Valley  
> Skills: FTTH, FTTR, SDU, Assurance  

Admin creates SI record. Arun now appears in:

- **Scheduler** as a column  
- **SI selector** in orders  
- **Inventory** as a material location  

---

## 3.4 Story: Scheduler filters SIs by skill

Order:

- Type: FTTR  
- Building: High-rise home interior job  

Scheduler must only allow:

- SIs with CanDoFTTR = TRUE  
- Optionally with CanHandleHighRise = TRUE  

Admin cannot assign this order to a basic FTTH-only SI.

---

## 3.5 Story: SI KPI Comparison

Reporting uses:

- SI’s job data  
- Job KPIs (Met/NotMet)  
- Docket KPIs  

Admin can see:

- Top 5 SIs (green)  
- Average (orange)  
- Underperforming (red)  

This later can plug into SI payment / bonus logic.

---

## 3.6 Story: Partner Finance View

Finance can:

- Filter invoices by Partner  
- See:

  - Total billed  
  - Total paid  
  - Outstanding  
  - Overdue  

- Drill down to orders, dockets, SIs involved.

---

# 4. SETTINGS LINKS

This module relies on and feeds into:

- **Settings Module** (for rate tables, SLA)  
- **Orders Module** (partner & SI references)  
- **Scheduler Module** (SI assignment)  
- **Invoicing Module** (partner rate rules)  
- **Status/KPI Module** (SI performance metrics)  

---

# END OF PARTNERS & SERVICE INSTALLERS MODULE
