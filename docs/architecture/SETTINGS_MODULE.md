# SETTINGS_MODULE

The Settings Module is the **central configuration brain** of CephasOps.

It provides a single, consistent way to control behaviour across:

- Multiple companies (Cephas, Kingsman, Menorah, etc.)
- Multiple verticals (ISP, Retail/Barbershop, Travel)
- Multiple modules (Orders, Scheduler, Inventory, Billing, KPI, Parser, Notifications, SI Rates)

This document is layered in two parts:

1. **Platform-Level Settings** (CephasOps-wide)  
2. **Vertical-Specific Settings** (detailed CSIOS ISP settings)

---

## 1. Core Purpose (Platform Level)

At platform level, the Settings Module:

- Acts as a **single source of truth** for configuration.
- Enables **System / Company / Site-level overrides**.
- Coordinates behaviour across modules without hard-coding rules.
- Allows CephasOps to support multiple businesses under one platform.

Key goals:

- Centralize “knobs” and “switches”.
- Keep business rules outside code.
- Support multi-company growth without rewrites.

---

## 2. Configuration Scopes

Settings are evaluated using three scopes:

### 🌐 2.1 System-Level (Global)

- Platform defaults that apply to all companies unless overridden.
- Examples:
  - Global parser patterns
  - Global KPI time buckets
  - Reserved status codes
  - Default currency / locale fallback

### 🏢 2.2 Company-Level

- Settings specific to a **legal entity / brand**:
  - Cephas Sdn. Bhd
  - Kingsman Classic Services
  - Menorah Travel & Tours

- Examples:
  - Invoice numbering rules
  - KPI thresholds
  - SI rate cards
  - Branding (theme, logo)
  - Preferred notification channels (WhatsApp / Email / SMS)

### 🏪 2.3 Site / Branch-Level

- Settings specific to a building, outlet, or warehouse:
  - Kingsman PJ Outlet
  - Cephas Warehouse Subang
  - Menorah KL Office

- Examples:
  - Opening hours
  - Local dispatch zones
  - Site-specific inventory rules
  - Local WhatsApp number / sender profile

### ✔ 2.4 Resolution Order

When a setting is requested:

1. Site-level value (if exists)
2. Else company-level value
3. Else global system default (from definition)

---

## 3. Platform Settings Categories

These categories are used **for all companies** (Cephas, Kingsman, Menorah, etc.), but each company can have its own values.

### 3.1 Workflow Configuration

- Order/job lifecycle rules
- Auto-close completed jobs
- Allowed backdating (e.g. max N days)
- Required approvals for:
  - Relocation
  - Rescheduling
  - Standby / pending states
- Cancellation reasons & policies
- Default job durations (per job type)

### 3.2 KPI & Performance Thresholds

- Time-based thresholds:
  - Accept → On-site
  - On-site → Complete
  - Complete → Close
- Job-type specific limits (activation, modification, relocation, assurance)
- KPI scoring formulas (e.g. on-time %, rejection %, redo rate)
- Colour thresholds for dashboards (green / amber / red zones)
- Installer ranking rules (daily / weekly / monthly views)

### 3.3 Parser & Automation Rules

- Email subject patterns and routing rules
- Excel/PDF field positions
- Work order classification:
  - ISP partner
  - Job type
  - Building type
- Address trimming and normalization rules
- Auto-tagging (VIP, critical, repeat, etc.)
- Auto-creation of jobs from approved emails

### 3.4 Notifications

- Channel enable/disable per company/site:
  - WhatsApp
  - SMS
  - Email
- Trigger rules:
  - Job assigned
  - Job accepted
  - Job completed
  - Job rejected / rescheduled
- Templates:
  - Customer notifications
  - Subcon/installer notifications
  - Internal alerts (ops group / manager)

### 3.5 SI Rates & Payroll Settings

- Per-company **rate profiles**:
  - Job-based rates
  - Day rates
  - KPI bonuses
  - Penalties (late, no-show, poor quality)
- Subcon vs in-house installer schemes
- Effective date ranges (rate revision switchovers)
- Site-specific allowances (parking, tolls, outstation)

### 3.6 Cost Centres & Branch Management

- Cost centre codes per company
- Mapping between:
  - Company → Business unit → Site
- Finance grouping:
  - For P&L
  - For invoice export
- Inventory site mapping (warehouse vs outlet)

### 3.7 Localization & Branding

- Company-specific:
  - Logo
  - Primary / secondary colours
  - Currency
  - Date and number format
  - Language preference
- Used across:
  - Web app UI theme
  - Reports
  - PDFs (invoices, job sheets)
  - SI app header

---

## 4. Cross-Module Integration (Platform View)

The Settings Module feeds configuration into:

- **Orders Module**
  - Status flow
  - Auto-close rules
  - Validation behaviour per job type

- **Scheduler Module**
  - Working hours per site
  - Slot duration
  - Max jobs per installer per day
  - Zone-specific dispatch rules

- **Inventory & RMA Module**
  - Require serials or not
  - Allowed negative stock or strict zero
  - Device-to-job linking rules

- **Invoicing & Finance Module**
  - Invoice prefix and numbering strategy
  - Payment terms
  - Tax flags (zero-rated, standard rated)

- **KPI / Status Module**
  - SLA boundaries
  - “Late” definitions by job type
  - Monthly target thresholds

- **SI App**
  - Jobs visible per company/site
  - Features toggled on/off (photo upload, checklist, chat)

---

## 5. Vertical-Specific Settings

The platform supports multiple verticals. Settings can be specialized by vertical while still sharing the same Settings Module infrastructure.

Verticals:

1. **ISP / CSIOS (Cephas Service Installer Operations System)**
2. **Retail / Barbershop (Kingsman)**
3. **Travel & Tours (Menorah)**

Below is the **detailed ISP/CSIOS configuration**, which is the most advanced and fully specified.

---

## 6. ISP / CSIOS Settings (Detailed)

This section describes the **deep, operational settings** used for Cephas ISP operations (CSIOS). It assumes:

- ISP partners (TIME, CelcomDigi, others)
- Fibre installation workflows
- Materials, splitters, ODF, ports, etc.
- Docket & KPI tracking

### 6.1 Master Data Catalogues

These are maintained via Settings / Master Data screens and managed primarily by admin roles. They provide controlled lists used by Orders, Scheduler, Inventory & KPI.

#### 6.1.1 Materials Catalogue

Settings cover:

- Material definitions:
  - `MaterialCode`
  - `MaterialName`
  - Material group (router, ONU, STB, fibre cable, trunking, accessories)
  - `IsSerialized` (serial mandatory or not)
  - Unit of measure
  - IsActive flag
- Default material templates per job type:
  - Activation – standard
  - Activation – high-rise
  - Relocation
  - Assurance / repair
- Per-partner material mappings (if certain partners use different item codes).

Use cases:

- Auto-suggest materials for installers based on job type.
- Ensure correct material list for stock and billing.

#### 6.1.2 Buildings & Splitters

Settings define:

- Building profiles:
  - High-rise vs landed vs commercial
  - MDF/ODF locations
- Splitter hierarchy:
  - Building → ODF → Splitter → Port
- Rules for:
  - Port naming/numbering conventions
  - Reserved ports (e.g. port 32 as standby / reserved)
- Capacity tracking:
  - Max ports per splitter
  - Occupancy thresholds
  - Warnings for near-full capacity

These settings allow Orders & Scheduler to:

- Suggest available ports
- Prevent overbooking a splitter
- Track “standby” and “spare” capacity accurately.

#### 6.1.3 Partners & Products (TIME, CelcomDigi, Others)

Settings include:

- Partner master list:
  - Partner name
  - Partner code
  - Product offerings (FTTH, FTTR, Metro-E, etc.)
- Work order ID formats per partner (e.g. activations, FTTR, ODR, etc.).
- SLA definitions per partner and job type.
- Partner-specific required fields and documents.

This ensures incoming work orders are parsed and categorized correctly.

#### 6.1.4 Service Installers & Subcons

Settings cover:

- SI basic master data:
  - Name, contact, zones covered
  - Service area (district, state)
  - Allowed job types (activation, relocation, assurance only, etc.)
- SI rating criteria:
  - KPI scoring formula
  - Blacklist / suspension flags
- SI groups and partner alignment (e.g. SIs only for TIME jobs in PJ area).

The Scheduler and Orders Module rely heavily on these settings.

---

### 6.2 Operational Rules & Status Flow

#### 6.2.1 Status Flow Definitions

Settings define:

- Standard lifecycle statuses:
  - New
  - Pending scheduling
  - Assigned
  - Accepted
  - On the way
  - On-site
  - Completed
  - Closed
  - Cancelled
  - Rejected
- Partner-specific variations (if required).
- Rules about which transitions are:
  - Allowed
  - Require remarks
  - Require approval (e.g. moving back from Completed to Assigned).

#### 6.2.2 Blockers

Settings define "Blockers" which are reasons why a job cannot proceed:

- Customer not home
- Wrong address
- No access to MDF/ODF
- Site not ready (renovation, no power, etc.)
- Building infra issue (splitter full, no path)

Each blocker can have:

- Required photo / attachment
- Whether it pauses KPI timer or not
- Whether SI still gets some payment
- Whether partner notification is required

---

### 6.3 KPI & SLA Rules

ISP settings include:

- KPI time windows per activity:
  - Time to accept
  - Time to arrive on site
  - Time to complete
- Job-specific SLA:
  - Activation: e.g. must complete within X days of order creation
  - Assurance: must attend within Y hours
- SLA status mapping:
  - On-time
  - Slight delay
  - Late
- KPI scoring:
  - Weighted by job type or partner
  - Exclusions for approved blockers

These settings feed the **Status & KPI Module** dashboards and SI performance reports.

---

### 6.4 Scheduler & Capacity Settings (ISP)

Settings cover:

- Standard working hours per site / zone.
- Break periods (lunch, evening, etc.).
- Max jobs per installer per day.
- Rules for:
  - Group jobs in same building / same area.
  - Avoid long-distance jumps in a single day.
- Special zones (VIP customers, priority buildings).

These allow the Scheduler to generate efficient schedules.

---

### 6.5 Dockets & Documentation Requirements

Settings define:

- Required attachments per job type:
  - Before / after photos
  - Modem/router photos
  - Splitter/ODF photos
  - Customer ID (if allowed)
- Required fields:
  - GPS coordinates
  - Signal levels (if captured)
  - MAC/serial numbers
- Docket check rules:
  - Mandatory fields before “Completed”
  - Conditions where job cannot be closed without attachments

Docket rules feed both QAQC and payment.

---

### 6.6 SI Payment & Rate Matrix

Settings define:

- Base rate for each job type (per company / per partner).
- Adjustments by:
  - Zone
  - Job complexity (e.g. high-rise vs landed)
  - Special tasks (flat roof, extra cable length, etc.)
- KPI-based incentives:
  - Bonus if on-time % above threshold
  - Penalty for redo jobs / customer complaints
- Payment schedule rules:
  - Cut-off periods
  - Payment release conditions (e.g. TBBN approved, docket clean)

This interacts closely with **Invoicing & Finance Module**.

---

### 6.7 Parser & Work Order Rules (ISP)

Settings for the Email Parser & WO importer:

- Subject line patterns for:
  - Activation
  - FTTR
  - Relocation
  - Assurance
- Body/attachment mapping:
  - Which columns map to:
    - Customer name
    - Address
    - Package
    - TBBN/TTKT IDs
- Duplicate order detection rules.
- VIP vs normal tag detection.
- Auto-tagging:
  - “Building not in DB → create new building record?” (Y/N per company)

These rules keep parser behaviour configurable without code changes.

---

### 6.8 Roles & Permissions (ISP/CSIOS view)

Though RBAC is its own module, Settings define:

- Which roles can:
  - Change KPI thresholds
  - Edit partner and product lists
  - Edit materials and splitter data
  - Adjust SI rates
  - Override SLA or close jobs manually

This keeps ISP operations safe and audited.

---

## 7. Retail / Kingsman Settings (High-Level)

For Kingsman, the same Settings Module is used, but tuned to Retail/Barbershop:

- POS / service price lists (per outlet)
- Barber commission rules
- Outlet working hours
- Appointment duration per service
- Product stock thresholds & reorder rules
- Kingsman-specific themes and invoice styles

(Detailed Kingsman Retail module can be specified in a separate architecture file.)

---

## 8. Travel & Menorah Settings (High-Level)

For Menorah Travel & Tours:

- Default package templates (tour types)
- Passenger / group size thresholds
- Payment schedule rules (deposit, balance)
- Trip date rules (cut-off for booking)
- Agent commission schema
- Travel invoice and voucher formatting

(Detailed Menorah Travel module can be further expanded later.)

---

## 9. Future Enhancements

Planned evolutions of the Settings Module:

- Export/import of settings profiles between companies.
- Preset templates per business type:
  - ISP profile
  - Retail profile
  - Travel profile
- Feature-flag system for rolling out new capabilities gradually.
- UI wizard for onboarding a new company with recommended defaults.

---

# End of Document
