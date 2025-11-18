# \# ARCHITECTURE\_OVERVIEW.md  

# CephasOps – Architecture Overview (v1)

# 

# CephasOps follows a \*\*layered / clean architecture\*\* with a strong separation of concerns and explicit boundaries between:

# 

# \- `Api` (HTTP boundary)

# \- `Application` (use cases)

# \- `Domain` (business rules)

# \- `Infrastructure` (EF Core, integrations, IO)

# 

# All core behaviour is driven by the \*\*Domain + Application\*\* layers.  

# `Api` and `Infrastructure` are delivery \& implementation details.

# 

# ---

# 

# \## 1. Project / Layer Structure

# 

# A typical .NET solution layout:

# 

# \- `CephasOps.Api`  

# &nbsp; HTTP controllers, authentication, DTOs, Swagger/OpenAPI.

# 

# \- `CephasOps.Application`  

# &nbsp; Use cases, command/query handlers, services, validation, mapping.

# 

# \- `CephasOps.Domain`  

# &nbsp; Entities, value objects, enums, domain events, core business rules.

# 

# \- `CephasOps.Infrastructure`  

# &nbsp; EF Core persistence, email/SMS/WhatsApp gateways, file storage, billing \& partner integrations, background job host.

# 

# Optionally:

# 

# \- `CephasOps.Shared`  

# &nbsp; Shared primitives (Result, paging, base exceptions, etc.) if needed across layers.

# 

# ---

# 

# \## 2. Layer Responsibilities

# 

# \### 2.1 Api Layer (`CephasOps.Api`)

# 

# \*\*Purpose:\*\*  

# Expose CephasOps functionality via HTTP/JSON to:

# 

# \- Admin Web App (`frontend/app`)

# \- SI PWA (`frontend/si-app`)

# \- Internal workers (e.g. Email Parser Worker via internal endpoints)

# 

# \*\*Responsibilities:\*\*

# 

# \- HTTP controllers / minimal APIs:

# &nbsp; - Orders, Scheduler, Inventory, Invoices, PNL, Settings, Auth, etc.

# \- Request/response \*\*DTOs\*\*:

# &nbsp; - Shape API contracts independently from domain entities.

# \- Authentication \& authorization glue:

# &nbsp; - JWT or cookie-based auth

# &nbsp; - Mapping claims → `UserId`, `CompanyId(s)`, roles.

# \- Model binding + validation (API-level):

# &nbsp; - Basic validation before calling Application layer.

# \- Swagger / OpenAPI:

# &nbsp; - API docs for human devs + AI coding assistants (Cursor, etc.)

# 

# \*\*Rules:\*\*

# 

# \- No business logic in controllers.

# \- Controllers call \*\*Application\*\* (e.g. `IMediator` / service interfaces).

# \- No direct EF Core or external integrations.

# 

# ---

# 

# \### 2.2 Application Layer (`CephasOps.Application`)

# 

# \*\*Purpose:\*\*  

# Coordinate \*\*use cases\*\* / workflows defined in `STORYBOOK.md` \& `PAGES.md` (e.g. “TIME Activation Order → Completed → Paid”).

# 

# \*\*Responsibilities:\*\*

# 

# \- Commands \& queries:

# &nbsp; - E.g. `CreateOrderFromParsedEmail`, `AssignOrderToSi`, `MarkOrderCompleted`, `CreateInvoice`, `GeneratePayroll`.

# \- Use case orchestration:

# &nbsp; - Call repositories/interfaces

# &nbsp; - Apply domain rules (via Domain services / entities)

# &nbsp; - Trigger domain events.

# \- Cross-use-case services:

# &nbsp; - Scheduling service (for Availability checks)

# &nbsp; - KPI calculation helpers

# &nbsp; - PNL aggregation services

# \- Validation:

# &nbsp; - Application-level validation (FluentValidation or similar):

# &nbsp;   - Complex rules that span multiple aggregates (e.g. splitter port availability, standby port approval attached, etc.).

# \- Mapping:

# &nbsp; - Map between:

# &nbsp;   - DTOs → Domain models

# &nbsp;   - Domain models → View models / DTOs.

# 

# \*\*Rules:\*\*

# 

# \- Depends only on \*\*Domain\*\* (and maybe `Shared`).

# \- No direct EF Core, SMTP, external HTTP calls.

# &nbsp; - Use \*\*interfaces\*\* that are implemented in Infrastructure:

# &nbsp;   - `IOrderRepository`

# &nbsp;   - `IInventoryService`

# &nbsp;   - `IInvoiceRepository`

# &nbsp;   - `IEmailSender`

# &nbsp;   - etc.

# 

# ---

# 

# \### 2.3 Domain Layer (`CephasOps.Domain`)

# 

# \*\*Purpose:\*\*  

# Capture the \*\*business language and rules\*\* of CephasOps, shared across all companies and verticals (ISP, barbershop, travel).

# 

# \*\*Core concepts:\*\*

# 

# \- Entities:

# &nbsp; - `Order`, `OrderStatusLog`

# &nbsp; - `Building`, `Splitter`, `SplitterPort`

# &nbsp; - `Material`, `MaterialMovement`

# &nbsp; - `Invoice`, `InvoiceLine`

# &nbsp; - `RmaRequest`

# &nbsp; - `Company`, `ServiceInstaller`, `User`

# \- Value objects:

# &nbsp; - `Address`

# &nbsp; - `AppointmentWindow`

# &nbsp; - `Money`

# &nbsp; - `KpiWindow` (e.g. minutes allowed for docket submission)

# \- Enums:

# &nbsp; - `OrderType` (Activation, Modification, Assurance)

# &nbsp; - `OrderStatus` (Pending, Assigned, OnTheWay, MetCustomer, OrderCompleted, etc.)

# &nbsp; - `BuildingType` (Prelaid, NonPrelaid, SDU, RDFPole)

# &nbsp; - `MaterialCategory` (Router, ONU, Cable, Connector, etc.)

# \- Domain services:

# &nbsp; - Splitter \& standby port rules

# &nbsp; - Default materials by building type

# &nbsp; - KPI calculations

# &nbsp; - Payroll calculations (base + KPI multipliers)

# 

# \*\*Rules:\*\*

# 

# \- \*\*No dependency\*\* on Application, Api, or Infrastructure.

# \- Pure business logic; persistence-agnostic.

# \- Every entity with business meaning should have a `CompanyId` where relevant (for multi-company scoping).

# 

# ---

# 

# \### 2.4 Infrastructure Layer (`CephasOps.Infrastructure`)

# 

# \*\*Purpose:\*\*  

# Provide concrete implementations for persistence, messaging, external APIs, and background processing.

# 

# \*\*Components:\*\*

# 

# \- \*\*EF Core\*\*:

# &nbsp; - DbContext

# &nbsp; - Entity configurations (mapping Domain entities to tables)

# &nbsp; - Migrations

# \- \*\*Repositories / adapters\*\*:

# &nbsp; - Implementation of:

# &nbsp;   - `IOrderRepository`

# &nbsp;   - `IInvoiceRepository`

# &nbsp;   - `IInventoryRepository`

# &nbsp;   - `IBuildingRepository`

# &nbsp;   - etc.

# \- \*\*Email / Messaging\*\*:

# &nbsp; - Outbound email (SMTP/API)

# &nbsp; - WhatsApp / SMS integration (future)

# \- \*\*File storage\*\*:

# &nbsp; - Storage for:

# &nbsp;   - Docket scans

# &nbsp;   - TIME Excel files

# &nbsp;   - Photos

# &nbsp;   - MRA PDFs

# \- \*\*Billing / Partner integrations\*\*:

# &nbsp; - Integration clients (TIME portal, others, where applicable)

# &nbsp; - Might be used by background workers / admin tooling.

# \- \*\*Background jobs\*\*:

# &nbsp; - Hosted services or job scheduler (e.g. Hangfire / Quartz):

# &nbsp;   - KPI recalcs

# &nbsp;   - PNL snapshot generation

# &nbsp;   - Reminder notifications (late dockets, upcoming appointments)

# 

# \*\*Rules:\*\*

# 

# \- Depends on \*\*Application\*\* + \*\*Domain\*\*.

# \- Exposes services via interfaces consumed by Application.

# \- Keeps all IO-bound logic away from Application/Domain.

# 

# ---

# 

# \## 3. Cross-Cutting Concerns

# 

# These concerns apply \*\*across all layers\*\*, with implementation details usually in Infrastructure + middleware in Api.

# 

# \### 3.1 Multi-Company Scoping

# 

# \*\*Principle:\*\*  

# Every relevant row belongs to a \*\*company\*\*.

# 

# \- Most Domain entities have:

# &nbsp; - `CompanyId` (FK → `Company` aggregate)

# \- At Api/Application boundaries:

# &nbsp; - Active `CompanyId` is resolved from:

# &nbsp;   - User context (claims)

# &nbsp;   - Or explicit selection (company switcher)

# \- All queries are filtered by `CompanyId`:

# &nbsp; - No cross-company data leakage.

# \- Directors:

# &nbsp; - Can have access to multiple companies

# &nbsp; - Group PNL uses \*\*aggregate queries\*\* across allowed CompanyIds.

# 

# ---

# 

# \### 3.2 RBAC (Role-Based Access Control)

# 

# \*\*Dimensions:\*\*

# 

# \- \*\*Per company\*\*:

# &nbsp; - Same user can have different roles per company:

# &nbsp;   - Example:

# &nbsp;     - `Admin` in Cephas Trading

# &nbsp;     - `Viewer` in Kingsman

# \- \*\*Per role\*\*:

# &nbsp; - Roles like:

# &nbsp;   - Director

# &nbsp;   - Admin / Operations

# &nbsp;   - Scheduler

# &nbsp;   - Warehouse

# &nbsp;   - Finance

# &nbsp;   - SI (Service Installer)

# &nbsp;   - Read-only / Reporting

# 

# \*\*Enforcement:\*\*

# 

# \- Api layer:

# &nbsp; - Authorization attributes / middleware:

# &nbsp;   - `\[Authorize(Roles = "Scheduler")]`, etc.

# &nbsp; - Claims include:

# &nbsp;   - `UserId`

# &nbsp;   - `CompanyId` (or list)

# &nbsp;   - Role memberships

# \- Application layer:

# &nbsp; - Additional checks inside handlers where necessary:

# &nbsp;   - E.g. only finance roles can change invoice status.

# &nbsp;   - Only warehouse roles can adjust stock.

# 

# ---

# 

# \### 3.3 Auditing \& Logging

# 

# \*\*Auditing:\*\*

# 

# \- Track \*\*who did what, when, in which company\*\*.

# \- For key aggregates (Orders, Invoices, Stock, Splitters, etc.):

# &nbsp; - CreatedBy, CreatedAt

# &nbsp; - UpdatedBy, UpdatedAt

# &nbsp; - Optionally:

# &nbsp;   - `OrderStatusLog` acts as a status audit trail

# &nbsp;   - Invoice history as event log

# 

# \*\*Logging:\*\*

# 

# \- Structured logging for:

# &nbsp; - Incoming HTTP requests

# &nbsp; - Application commands/queries

# &nbsp; - Background jobs

# &nbsp; - Integration calls (TIME, email, storage)

# \- Used for:

# &nbsp; - Debugging

# &nbsp; - KPI analysis

# &nbsp; - Compliance / traceability

# 

# ---

# 

# \### 3.4 Background Job Scheduling

# 

# \*\*Usage examples:\*\*

# 

# \- KPI enforcement:

# &nbsp; - Nightly recalculation of:

# &nbsp;   - Docket KPIs

# &nbsp;   - On-time arrival KPIs

# \- PNL snapshots:

# &nbsp; - Monthly/weekly summarization

# \- Notifications:

# &nbsp; - Reminder emails for:

# &nbsp;   - Upcoming appointments

# &nbsp;   - Overdue invoices

# &nbsp;   - Late dockets

# \- Sync / ingestion:

# &nbsp; - Email parser worker polling mailbox (POP3/IMAP)

# &nbsp; - Periodic import from partner portals if needed

# 

# \*\*Implementation options:\*\*

# 

# \- Hosted services in Infrastructure:

# &nbsp; - Using `IHostedService`

# \- Or external scheduler (Hangfire, Quartz, etc.) wired into Infrastructure.

# 

# ---

# 

# \## 4. Architectural Principles

# 

# \- \*\*Domain-first\*\*:

# &nbsp; - Storybook and domain model drive everything.

# \- \*\*Use-case centric\*\*:

# &nbsp; - Each operation (e.g. “SI completes job”) → a dedicated Application command.

# \- \*\*Persistence-agnostic domain\*\*:

# &nbsp; - Domain doesn’t know about EF Core or HTTP.

# \- \*\*Thin controllers\*\*:

# &nbsp; - Api only translates HTTP → Application calls → HTTP responses.

# \- \*\*Multi-company, multi-vertical ready\*\*:

# &nbsp; - Everything scoped by `CompanyId`.

# &nbsp; - Vertical toggles (ISP / Barbershop / Travel) live in Settings + UI, not in Domain.

# 

# ---

# 

# This `ARCHITECTURE\_OVERVIEW.md` should be kept in sync with:

# 

# \- `STORYBOOK.md` (flows)

# \- `PAGES.md` (routes \& modules)

# \- `WIREFRAMES.md` (UI)

# \- `MULTI\_COMPANY\_MODULE.md` (company/role logic, if present)

# 

# Whenever we add new modules (e.g. barbershop bookings or travel tours), we extend the \*\*Domain \& Application\*\* first, then adapt Api \& Infrastructure accordingly.



