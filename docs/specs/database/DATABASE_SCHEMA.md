# DATABASE\_SCHEMA.md

\# DATABASE\_SCHEMA

CephasOps – Database Schema (PostgreSQL)



This document summarises the core tables for Phase 1.



Key points:



\- Single schema, shared across companies.

\- All business tables include `CompanyId`.

\- Naming: singular table names (e.g., `Order`, `Invoice`).



---



\## Core Tables



\- `Company`

\- `User`

\- `Role`

\- `UserRole`

\- `Order`

\- `OrderStatusLog`

\- `Building`

\- `Splitter`

\- `SplitterPort`

\- `Material`

\- `MaterialStock`

\- `MaterialMovement`

\- `RmaRequest`

\- `Invoice`

\- `InvoiceLine`

\- `ServiceInstaller`

\- `Partner`

\- `ParserRule`

\- `NotificationSetting`

\- `CostCentre`



Column details are aligned with `PHASE1\_DOMAIN\_MODELS.md`.



---



\## Indexing Strategy (Examples)



\- `Order`

&nbsp; - `IX\_Order\_CompanyId\_ServiceId`

&nbsp; - `IX\_Order\_CompanyId\_AppointmentDateTime`

\- `OrderStatusLog`

&nbsp; - `IX\_OrderStatusLog\_OrderId\_Timestamp`

\- `MaterialStock`

&nbsp; - `IX\_MaterialStock\_CompanyId\_MaterialId\_LocationType`

\- `Invoice`

&nbsp; - `IX\_Invoice\_CompanyId\_InvoiceDate`

&nbsp; - `IX\_Invoice\_CompanyId\_PartnerId`



