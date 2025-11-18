# \# ARCHITECTURE\_OVERVIEW

# CephasOps – Clean Architecture \& Layers

# 

# CephasOps uses a layered / clean architecture to keep domain logic stable while allowing infrastructure to change over time.

# 

# ---

# 

# \## Layer Overview

# 

# 1\. \*\*API Layer (CephasOps.Api)\*\*

# &nbsp;  - ASP.NET Core 8 Web API

# &nbsp;  - Controllers, DTOs, model binding, filters

# &nbsp;  - JWT auth, company scoping middleware, Swagger

# 

# 2\. \*\*Application Layer (CephasOps.Application)\*\*

# &nbsp;  - Use cases / handlers grouped by module

# &nbsp;  - Business workflows, validation, orchestration

# &nbsp;  - Interfaces for repositories and external services

# 

# 3\. \*\*Domain Layer (CephasOps.Domain)\*\*

# &nbsp;  - Entities, value objects, enums, domain events

# &nbsp;  - Business rules that should not depend on infrastructure

# &nbsp;  - Multi-company base class (`CompanyEntityBase`)

# 

# 4\. \*\*Infrastructure Layer (CephasOps.Infrastructure)\*\*

# &nbsp;  - EF Core DbContext + configurations

# &nbsp;  - PostgreSQL persistence

# &nbsp;  - Repositories

# &nbsp;  - Email client, file storage, background jobs

# 

# ---

# 

# \## API Layer

# 

# Responsibilities:

# 

# \- Expose REST endpoints listed in `spec/api/API\_BLUEPRINT.md`

# \- Handle:

# &nbsp; - Authentication (`/auth/login`)

# &nbsp; - Company scoping via `X-Company-Id`

# &nbsp; - Request validation \& mapping to application commands

# \- Documentation:

# &nbsp; - auto-generated Swagger

# &nbsp; - written API docs in `/docs/spec/api/\*.md`

# 

# Key components:

# 

# \- `Controllers/OrdersController.cs`

# \- `Controllers/SchedulerController.cs`

# \- `Controllers/InventoryController.cs`

# \- `Controllers/InvoicesController.cs`

# \- `Controllers/BuildingsController.cs`

# \- `Controllers/SplittersController.cs`

# \- `Controllers/PartnersController.cs`

# \- `Controllers/InstallersController.cs`

# \- `Controllers/DocketsController.cs`

# \- `Controllers/SettingsController.cs`

# \- `Controllers/ReportsController.cs`

# \- `Controllers/AuthController.cs`

# \- `Middleware/CompanyScopeMiddleware.cs`

# 

# ---

# 

# \## Application Layer

# 

# Responsibilities:

# 

# \- Implement use cases defined in Storybook (e.g. “TIME Activation → Completed → Paid”)

# \- Orchestrate domain entities, repositories and infrastructure services

# \- Own cross-module workflows, e.g.:

# &nbsp; - Order completion → inventory deduction → docket KPI → invoice eligibility

# 

# Structure:

# 

# \- `Orders/`

# \- `Scheduler/`

# \- `Inventory/`

# \- `Invoices/`

# \- `Buildings/`

# \- `Splitters/`

# \- `Installers/`

# \- `Partners/`

# \- `Settings/`

# \- `Reporting/`

# \- `Auth/`

# \- `Common/` (ICurrentCompanyContext, Result<T>, etc.)

# 

# ---

# 

# \## Domain Layer

# 

# Responsibilities:

# 

# \- Represent business reality for ISP operations:

# &nbsp; - `Order`, `OrderStatusLog`

# &nbsp; - `Building`, `Splitter`, `SplitterPort`

# &nbsp; - `Material`, `MaterialStock`, `MaterialMovement`, `RmaRequest`

# &nbsp; - `Invoice`, `InvoiceLine`

# &nbsp; - `ServiceInstaller`, `Partner`

# &nbsp; - `ParserRule`, `NotificationSetting`, `CostCentre`

# \- Enforce invariants such as:

# &nbsp; - Only one active splitter port per service

# &nbsp; - Standby port requires approval

# &nbsp; - Orders cannot be completed twice

# \- Provide clear boundaries for phase 2 (barbershop, travel)

# 

# All domain entities derive from `CompanyEntityBase` to ensure multi-company scoping and auditing.

# 

# ---

# 

# \## Infrastructure Layer

# 

# Responsibilities:

# 

# \- EF Core mappings \& migrations

# \- Database schema for PostgreSQL (see `spec/database/DATABASE\_SCHEMA.md`)

# \- Implementation of:

# &nbsp; - `IOrderRepository`, `IInvoiceRepository`, etc.

# &nbsp; - Email reader (POP3/IMAP) for parser worker

# &nbsp; - File storage for dockets, photos, MRA PDFs

# &nbsp; - Background job scheduler (Hangfire/Quartz)

# 

# ---

# 

# \## Cross-Cutting Concerns

# 

# \- \*\*Multi-company\*\*: `CompanyId` + global query filters

# \- \*\*RBAC\*\*: Role \& permissions per company

# \- \*\*Auditing\*\*: `CreatedAt`, `UpdatedAt`, `CreatedBy`, `UpdatedBy`

# \- \*\*Logging\*\*: Serilog structured logging

# \- \*\*Background Jobs\*\*: nightly KPI jobs, reminders, rollups

# 

# Architectural decisions are documented in `architecture/TECH\_STACK\_DECISION.md` and `spec/database/MIGRATIONS\_GUIDE.md`.



