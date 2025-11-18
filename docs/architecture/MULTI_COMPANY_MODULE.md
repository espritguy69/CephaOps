# \# MULTI\_COMPANY\_MODULE

# 

# The Multi-Company Module defines how CephasOps supports \*\*multiple companies, brands, and business verticals\*\* within a single unified platform, while respecting strict data separation, modular settings, and vertical-specific behaviour.

# 

# This module works hand-in-hand with the \*\*Settings Module\*\*, which provides the configuration needed to govern each company’s workflows, KPI rules, billing format, parser rules, SI rates, branding, and more.

# 

# ---

# 

# \# 1. Purpose

# 

# The Multi-Company Module ensures:

# 

# \- Each company operates independently within CephasOps.

# \- Cross-company operations are possible for “Group Ops” or “Group Finance”.

# \- Every module (Orders, Inventory, Billing, Scheduler, KPI, Parser) loads settings \*\*based on the active company\*\*.

# \- Vertical-specific behaviour is triggered automatically based on company type:

# &nbsp; - ISP (CSIOS)

# &nbsp; - Retail / Barbershop (Kingsman)

# &nbsp; - Travel \& Tours (Menorah)

# 

# ---

# 

# \# 2. Multi-Company Structure

# 

# Each company is modeled using three layers:

# 

# \### 2.1 \*\*Company (Legal Entity)\*\*

# Examples:  

# \- Cephas Sdn. Bhd  

# \- Kingsman Classic Services  

# \- Menorah Travel \& Tours  

# 

# Company determines:

# \- Branding

# \- Invoice numbering rules

# \- KPI thresholds

# \- SI rates / payroll rules

# \- Parser rules

# \- Billing \& finance behavior

# \- Feature enable/disable

# 

# \### 2.2 \*\*Business Unit / Vertical\*\*

# Examples:

# \- ISP Installer Operations (CSIOS)

# \- Kingsman Retail Operations

# \- Menorah Travel Management

# 

# Vertical controls:

# \- Which submodules are active for the company

# \- Workflow definitions

# \- Module visibility

# \- Additional vertical-specific settings

# 

# \### 2.3 \*\*Site / Outlet / Branch\*\*

# Examples:

# \- Kingsman PJ Outlet

# \- Cephas Subang Warehouse

# \- Menorah HQ KL Office

# 

# Site determines:

# \- Local working hours

# \- Dispatch zones

# \- Stock availability

# \- Local notification numbers

# \- Per-site overrides in Settings Module

# 

# ---

# 

# \# 3. Company Context Rules

# 

# \### 3.1 Data Isolation

# Every row in operational modules carries a `CompanyId`.

# Users only access data for companies assigned to them.

# 

# \### 3.2 Company Switcher (UI)

# The active company dictates:

# \- Theme \& branding

# \- Module availability

# \- Workflow rules loaded from Settings Module

# \- Navigational visibility

# \- Notification templates

# \- Parser behaviour

# \- KPI boundaries

# 

# \### 3.3 Scoping Integration With Settings Module

# When loading:

# \- Workflow rules  

# \- KPI thresholds  

# \- Parser definitions  

# \- Billing rules  

# \- SI rates  

# 

# CephasOps resolves:

# 

# 1\. Site-level overrides →  

# 2\. Company-level settings →  

# 3\. System-level (global) defaults

# 

# ---

# 

# \# 4. Module Interactions

# 

# \### 4.1 Orders Module

# \- Status flows per company/vertical  

# \- Cancellation rules  

# \- Backdating rules  

# \- Required fields per job type  

# \- ISP-specific splitter/ODF rules via vertical settings  

# 

# \### 4.2 Scheduler

# \- Working hours per site  

# \- Max jobs per SI per day  

# \- ISP job grouping (MDF/ODF)  

# \- Kingsman appointments  

# \- Menorah trip dates  

# 

# \### 4.3 Inventory

# \- ISP materials \& serialized devices (routers, ONU, fibre)

# \- Kingsman products (shampoo, pomade, vouchers)

# \- Travel documents (non-applicable)

# 

# \### 4.4 Billing \& Finance

# \- Company-based invoice prefix  

# \- Payment terms  

# \- SI payroll rules (per vertical)  

# \- Subcon rate cards (ISP)  

# \- Barber commissions (Kingsman)  

# \- Tour deposit/balance schedule (Menorah)  

# 

# \### 4.5 Parser \& Automation

# \- Different patterns for ISP WOs  

# \- Retail input rules (future POS)  

# \- Travel booking import templates  

# 

# ---

# 

# \# 5. Vertical-Specific Company Behaviour

# 

# \### 5.1 ISP / CSIOS (Cephas)

# Loaded settings include:

# \- Materials catalogue  

# \- Buildings \& Splitters  

# \- Partner definitions (TIME, CelcomDigi)  

# \- KPI \& SLA per job type  

# \- Blockers \& required photos  

# \- Parser rules (TBBN, TTKT, FTTR, ODR)  

# \- SI rate matrix  

# \- Docket mandatory fields  

# 

# \### 5.2 Retail / Kingsman

# Loaded settings include:

# \- Service definitions (cut, shave, wash, etc.)

# \- Product inventory  

# \- POS-related notifications  

# \- Barber commission logic  

# \- Branding (gold/black theme)  

# \- Appointment duration per service  

# 

# \### 5.3 Travel / Menorah

# Loaded settings include:

# \- Tour package templates  

# \- Passenger details  

# \- Trip date rules  

# \- Payment schedule  

# \- Agent commission rules  

# \- Travel-specific invoice format  

# 

# ---

# 

# \# 6. Security \& Permissions

# 

# \- Users receive access through \*\*UserCompanyMembership\*\*.

# \- System enforces:

# &nbsp; - Company scoping in queries  

# &nbsp; - Vertical scoping for modules  

# &nbsp; - Role-based access for settings editing  

# 

# Group-level users may access all companies with aggregation views.

# 

# ---

# 

# \# 7. Future Enhancements

# 

# \- Feature flagging per company  

# \- Company onboarding wizard to auto-generate defaults  

# \- Multi-company reporting with combined P\&L and KPI dashboards  

# 

# ---

# 

# \# End of Document



