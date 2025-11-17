✅ 1. COMPRESSED VERSION (1–2 Page Executive Summary)
CSIOS — Cephas Service Installer Operations System (Executive Summary)

Goal: Centralize and automate ALL operational workflows for Cephas Sdn Bhd for ISP partners (TIME, Celcom, Digi, U Mobile).

SYSTEM PURPOSE

CSIOS automates the entire installation/assurance lifecycle:

Incoming orders from email → parsed automatically

Scheduler assigns installers

Material consumption tracked

Splitter port usage enforced

Job status updates tracked end-to-end

Dockets validated & uploaded

Invoices generated & payment tracked

KPIs calculated

Reporting dashboards updated

This eliminates manual processing, delays, missing dockets, and inaccuracies.

TOP 11 MODULES

Orders Module – Central place for all job records (activation, modification, assurance).

Email Parser Module – Extracts data from Excel/Email body into structured orders.

Status & KPI Module – Tracks 10-step job stages + KPI rules (job duration, docket timing).

Scheduler Module – Drag-and-drop SI calendar with skills & availability.

Inventory Module – Tracks warehouse → SI → customer flow, returns, serialized items.

Invoicing Module – Generates PDFs, tracks 45-day payment cycle, handles rejection cases.

Buildings & Splitters Module – Tracks building details, splitter capacity, port usage rules.

Partners & SI Module – Partner prefixes, SI skills, payment rates, emergency contacts.

Dockets Module – Manual docket uploads, validations, corrections, partner rejections.

Reporting Module – KPI dashboard, SI performance, splitter usage, materials, finance.

Settings Module – Master data: materials, templates, KPI rules, status flow, SI rates.

STATUS PIPELINE (10 STEPS)
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


Blockers (Customer / Building / Network) can pause the flow.

KEY AUTOMATION
Email → Order creation

Excel activations

Excel modifications

Email body assurance

Auto-material assignment

Based on building type:
Prelaid, Non-Prelaid, SDU, RDF, FTTR, FTTC.

Splitter management

Tracks port availability

Standby port 32 requires partner approval

Blocks status change until approval uploaded

KPI calculations

Job duration KPI:

Prelaid: 1 hr

Non-prelaid: 2 hr

SDU/FTTR/FTTC/RDF: 3 hr

Docket KPI:

Must submit within 30 minutes of job completion

INVENTORY & SERIAL LOGIC

Serialized items tracked:

ONU

Router

Mesh WiFi

Boosters

VoIP Units

STB (if used)

Assurance jobs require old-device return.

INVOICING & PAYMENT

Invoice auto-generated when docket uploaded

45-day payment countdown

Partner rejection handling

Payment verification

REPORTING

Dashboards cover:

Installer KPI

Docket KPI

Partner breakdown

Splitter usage

Material consumption

Finance (invoices, payments)

Rejections & blockers

TECHNICAL STACK (Recommended)

Backend: ASP.NET Core 8+

Frontend: React / Next.js

Database: SQL Server

Storage: Azure Blob / AWS S3

Integrations: IMAP/POP3 Email, Partner Portal manual upload

Deployment: Docker + CI/CD

END OF EXECUTIVE SUMMARY
