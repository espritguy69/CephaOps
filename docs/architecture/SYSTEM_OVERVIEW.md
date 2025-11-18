# \# SYSTEM\_OVERVIEW

# CephasOps – ISP Operations Platform Overview

# 

# CephasOps is a multi-company operations platform built to standardise and automate:

# 

# \- ISP field operations (Phase 1 – TIME, later other telcos)

# \- Inventory \& RMA for CPE and fibre materials

# \- Dockets → Invoice → Portal submission flows

# \- KPI tracking (dockets, on-time arrival, reworks)

# \- Multi-company P\&L and cost centre reporting

# 

# Future phases will reuse the same backbone for:

# 

# \- Kingsman Classic (barbershop \& spa operations)

# \- Menorah Travel \& Tours (travel vertical)

# \- Other service verticals under the Cephas group

# 

# ---

# 

# \## Phase 1 Scope (ISP Vertical)

# 

# Phase 1 focuses on TIME and similar ISP operations:

# 

# \- \*\*Email Parsing\*\*

# &nbsp; - Activation (Breakdown Notification)

# &nbsp; - Assurance (TTKT / AWO)

# &nbsp; - MRA / RMA PDF emails

# 

# \- \*\*Orders \& Assurance\*\*

# &nbsp; - Orders created automatically from email parser

# &nbsp; - Types: Activation, Modification, Assurance

# &nbsp; - Full lifecycle: Pending → Assigned → OnTheWay → MetCustomer → Completed → DocketsReceived → Invoiced → Paid

# 

# \- \*\*Scheduler \& SI\*\*

# &nbsp; - Day/week calendar view

# &nbsp; - Drag-and-drop assignment and rescheduling

# &nbsp; - SI PWA for job execution, GPS + photo capture

# 

# \- \*\*Buildings \& Splitters\*\*

# &nbsp; - Building master data (type, address, short name)

# &nbsp; - Splitter devices and individual port usage

# &nbsp; - Standby port (port 32 on 1:32) approval logic

# 

# \- \*\*Inventory \& RMA\*\*

# &nbsp; - Materials master (router, ONU, cable, connectors)

# &nbsp; - Stock at warehouse, with SI, at customer, in RMA

# &nbsp; - RMA requests \& MRA PDF linkage

# 

# \- \*\*Dockets \& Invoices\*\*

# &nbsp; - Docket received/uploaded KPI

# &nbsp; - Invoice generation from completed jobs

# &nbsp; - Portal Submission ID tracking and ageing

# 

# \- \*\*Reporting \& PNL\*\*

# &nbsp; - Operational KPIs (on-time arrival, docket KPI)

# &nbsp; - Financial PNL per company and cost centre

# &nbsp; - Group PNL for directors

# 

# ---

# 

# \## Core Actors

# 

# \- Admin / Operations Coordinator

# \- Scheduler

# \- Warehouse / Inventory Staff

# \- Service Installer (in-house \& subcon)

# \- Finance / Billing Team

# \- Director (multi-company view)

# 

# Each actor’s flows are recorded in `docs/storybook/STORYBOOK\_V1.md` and mapped to UI pages in `docs/storybook/PAGES.md`.

# 

# ---

# 

# \## High-Level Goals

# 

# \- \*\*Single source of truth\*\* for orders, dockets, inventory, invoices and KPIs

# \- \*\*Multi-company by design\*\* – everything is scoped by `CompanyId`

# \- \*\*Reliable billing\*\* – no order is invoiced without validated dockets

# \- \*\*Scalable\*\* – able to onboard new partners and verticals with configuration

# \- \*\*Developer-friendly\*\* – clean architecture, clear docs, strong contracts



