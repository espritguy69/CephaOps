# SYSTEM\_DESIGN\_DIAGRAMS.md

# \# SYSTEM\_DESIGN\_DIAGRAMS

# CephasOps – High-Level System Design

# 

# This document contains ASCII and Mermaid diagrams that describe the main flows in CephasOps.

# 

# ---

# 

# \## 1. High-Level ASCII Flow

# 

# ```text

# \[Partner Email]

# &nbsp;     |

# &nbsp;     v

# \[Email Parser Worker] --> \[Parsed Orders API]

# &nbsp;     |                          |

# &nbsp;     |                          v

# &nbsp;     |                   \[Orders Service] <-> \[Scheduler]

# &nbsp;     |                          |                  |

# &nbsp;     |                          v                  v

# &nbsp;     |                    \[Inventory]        \[SI PWA (Jobs)]

# &nbsp;     |                          |                  |

# &nbsp;     |                          v                  |

# &nbsp;     |                     \[Dockets]               |

# &nbsp;     |                          |                  |

# &nbsp;     |                          v                  |

# &nbsp;     |                      \[Billing] <------------

# &nbsp;     |                          |

# &nbsp;     v                          v

# &nbsp;\[File Storage]             \[PNL \& Reports]

# 

# 2\. Mermaid – Logical Component Flow

# flowchart LR

# &nbsp; E\[Email Inbox] --> P\[Email Parser Worker]

# &nbsp; P --> API\[Parsed Orders API]

# &nbsp; API --> O\[Orders Service]

# 

# &nbsp; O --> S\[Scheduler Service]

# &nbsp; S --> SI\[Service Installer PWA]

# &nbsp; SI --> O

# 

# &nbsp; O --> INV\[Inventory \& RMA Service]

# &nbsp; INV --> DB\[(PostgreSQL)]

# 

# &nbsp; O --> D\[Dockets \& Files]

# &nbsp; D --> FS\[(File Storage)]

# 

# &nbsp; O --> B\[Billing \& Invoicing]

# &nbsp; B --> F\[Finance \& PNL Service]

# 

# &nbsp; F --> DIR\[Director Dashboards]

# 

# 3\. Multi-Company Context Diagram

# flowchart TB

# &nbsp; subgraph CompanyA\[Company: Cephas Trading]

# &nbsp;   A\_Orders\[Orders A]:::mod

# &nbsp;   A\_Inventory\[Inventory A]:::mod

# &nbsp;   A\_Invoices\[Invoices A]:::mod

# &nbsp; end

# 

# &nbsp; subgraph CompanyB\[Company: Cephas Sdn. Bhd.]

# &nbsp;   B\_Orders\[Orders B]:::mod

# &nbsp;   B\_Inventory\[Inventory B]:::mod

# &nbsp;   B\_Invoices\[Invoices B]:::mod

# &nbsp; end

# 

# &nbsp; DIR\[Director User] --> DIR\_API\[Group PNL API]

# 

# &nbsp; DIR\_API --> A\_Orders

# &nbsp; DIR\_API --> B\_Orders

# 

# &nbsp; classDef mod fill=#e7f5ff,stroke=#228be6;

# 

# 

# Note: all companies share the same DB schema; rows are separated by CompanyId and enforced via query filters and middleware.

# 

# 4\. Sequence – TIME Activation → Completed → Paid

# sequenceDiagram

# &nbsp; participant TIME as TIME Portal

# &nbsp; participant Mailbox as Cephas Mailbox

# &nbsp; participant Parser as Parser Worker

# &nbsp; participant API as CephasOps API

# &nbsp; participant Admin as Admin User

# &nbsp; participant Scheduler as Scheduler User

# &nbsp; participant SI as Service Installer

# 

# &nbsp; TIME->>Mailbox: Send Activation Email + Excel

# &nbsp; Parser->>Mailbox: Poll mailbox (POP3/IMAP)

# &nbsp; Parser->>Parser: Classify as Activation

# &nbsp; Parser->>API: POST /internal/parsed-orders

# 

# &nbsp; API->>API: Create Order (Pending)

# &nbsp; Admin->>API: GET /api/orders

# &nbsp; Scheduler->>API: POST /api/orders/{id}/assign-si

# 

# &nbsp; SI->>API: POST /api/si/jobs/{id}/on-the-way

# &nbsp; SI->>API: POST /api/si/jobs/{id}/met-customer

# &nbsp; SI->>API: POST /api/si/jobs/{id}/complete

# 

# &nbsp; Admin->>API: POST /api/orders/{id}/dockets/received

# &nbsp; Admin->>API: POST /api/orders/{id}/dockets/upload

# &nbsp; Admin->>API: POST /api/invoices/generate

# &nbsp; Admin->>TIME: Upload invoice, docket, photos

# &nbsp; Admin->>API: POST /api/invoices/{id}/submit (SubmissionId)

# 

# 

# Exported PNG versions should be placed into architecture/diagrams/.

# 

# 

# ---

# 

# \### `MULTI\_COMPANY\_IMPLEMENTATION.md`

# 

# ```markdown

# \# MULTI\_COMPANY\_IMPLEMENTATION

# CephasOps – Multi-Company Implementation Guide

# 

# CephasOps is designed as a \*\*single shared platform\*\* for multiple legal entities (Cephas Trading, Cephas Sdn. Bhd., Kingsman, Menorah, etc.).

# 

# This document explains how multi-company support is implemented across:

# 

# \- Domain models

# \- Database schema

# \- API layer

# \- Repositories

# \- Director-level analytics

# 

# ---

# 

# \## 1. Core Principles

# 

# 1\. \*\*Single DB, many companies\*\*

# &nbsp;  - One PostgreSQL database, one schema.

# &nbsp;  - Each row associated to exactly one `CompanyId`.

# 

# 2\. \*\*Tenant separation by `CompanyId`\*\*

# &nbsp;  - All company-scoped entities extend a common base:

# &nbsp;    - `CompanyId`, `CreatedAt`, `UpdatedAt`, `CreatedBy`, `UpdatedBy`.

# 

# 3\. \*\*No “leaks” across companies\*\*

# &nbsp;  - Global query filters restrict queries to the current `CompanyId`.

# &nbsp;  - Repositories always apply those filters.

# &nbsp;  - APIs require `X-Company-Id` and validate access.

# 

# 4\. \*\*Director-only cross-company views\*\*

# &nbsp;  - Special endpoints use `IgnoreQueryFilters()` and role checks.

# &nbsp;  - Only Director role may aggregate across companies.

# 

# ---

# 

# \## 2. Base Entity

# 

# ```csharp

# public abstract class CompanyEntityBase

# {

# &nbsp;   public Guid CompanyId { get; set; }

# 

# &nbsp;   public DateTime CreatedAt { get; set; }

# &nbsp;   public DateTime UpdatedAt { get; set; }

# 

# &nbsp;   public Guid CreatedBy { get; set; }

# &nbsp;   public Guid UpdatedBy { get; set; }

# }

# 

# 

# Entities such as Order, Invoice, Material, Building, RmaRequest, etc. derive from CompanyEntityBase.

# 

# 3\. Current Company Context

# public interface ICurrentCompanyContext

# {

# &nbsp;   Guid CompanyId { get; }

# &nbsp;   Guid UserId { get; }

# &nbsp;   bool IsDirector { get; }

# }

# 

# 

# Implementation:

# 

# Reads JWT to get UserId \& roles.

# 

# Reads X-Company-Id header to get selected company.

# 

# Validates that:

# 

# User is allowed to access that company.

# 

# Company is active.

# 

# 4\. Company Scope Middleware

# 

# Pseudo pipeline:

# 

# Authenticate JWT.

# 

# Extract X-Company-Id.

# 

# Validate:

# 

# Header present \& valid Guid.

# 

# User has membership in that company.

# 

# Set ICurrentCompanyContext for the request.

# 

# Downstream layers rely on this context.

# 

# If validation fails → 403 Forbidden or 400 Bad Request.

# 

# 5\. EF Core Global Query Filters

# 

# Example for the DbContext:

# 

# protected override void OnModelCreating(ModelBuilder modelBuilder)

# {

# &nbsp;   base.OnModelCreating(modelBuilder);

# 

# &nbsp;   modelBuilder.Entity<Order>()

# &nbsp;       .HasQueryFilter(x => x.CompanyId == \_companyContext.CompanyId);

# 

# &nbsp;   modelBuilder.Entity<Invoice>()

# &nbsp;       .HasQueryFilter(x => x.CompanyId == \_companyContext.CompanyId);

# 

# &nbsp;   modelBuilder.Entity<Building>()

# &nbsp;       .HasQueryFilter(x => x.CompanyId == \_companyContext.CompanyId);

# 

# &nbsp;   // Apply filter to all company entities

# }

# 

# 

# Director Queries

# 

# For PNL group view:

# 

# if (\_companyContext.IsDirector)

# {

# &nbsp;   var invoices = \_context.Invoices

# &nbsp;       .IgnoreQueryFilters()

# &nbsp;       .Where(i => i.InvoiceDate >= from \&\& i.InvoiceDate <= to);

# &nbsp;   // aggregate per company

# }

# 

# 6\. Tenant-Aware Repository

# public class EfRepository<T> : IRepository<T> where T : CompanyEntityBase

# {

# &nbsp;   private readonly CephasOpsDbContext \_db;

# &nbsp;   private readonly ICurrentCompanyContext \_current;

# 

# &nbsp;   public EfRepository(CephasOpsDbContext db, ICurrentCompanyContext current)

# &nbsp;   {

# &nbsp;       \_db = db;

# &nbsp;       \_current = current;

# &nbsp;   }

# 

# &nbsp;   public IQueryable<T> Query => \_db.Set<T>(); // already filtered by CompanyId

# 

# &nbsp;   public async Task AddAsync(T entity)

# &nbsp;   {

# &nbsp;       entity.CompanyId = \_current.CompanyId;

# &nbsp;       entity.CreatedAt = DateTime.UtcNow;

# &nbsp;       entity.UpdatedAt = DateTime.UtcNow;

# &nbsp;       entity.CreatedBy = \_current.UserId;

# &nbsp;       entity.UpdatedBy = \_current.UserId;

# 

# &nbsp;       \_db.Set<T>().Add(entity);

# &nbsp;       await \_db.SaveChangesAsync();

# &nbsp;   }

# 

# &nbsp;   public async Task UpdateAsync(T entity)

# &nbsp;   {

# &nbsp;       entity.UpdatedAt = DateTime.UtcNow;

# &nbsp;       entity.UpdatedBy = \_current.UserId;

# &nbsp;       await \_db.SaveChangesAsync();

# &nbsp;   }

# }

# 

# 7\. API Contracts

# 

# All company-scoped endpoints require:

# 

# Authorization: Bearer <JWT>

# 

# X-Company-Id: <Guid>

# 

# This rule is documented explicitly in spec/api/API\_BLUEPRINT.md.

# 

# 8\. Director Multi-Company Dashboards

# 

# Director endpoints (e.g. /api/pnl/group):

# 

# Require Director role in JWT claims.

# 

# Use IgnoreQueryFilters() to read across companies.

# 

# Return data grouped by company:

# 

# revenue, cost of labour/materials, gross \& net margin.

# 

# 9\. Testing Multi-Company

# 

# Seed test data for Company A and Company B.

# 

# Ensure:

# 

# A non-director user for Company A can never see B’s rows.

# 

# Director user can see aggregated views but not change another company’s operational data without explicit UI/UX.

# 

# 

# ---

# 

# \### `TECH\_STACK\_DECISION.md`

# 

# ```markdown

# \# TECH\_STACK\_DECISION

# CephasOps – Technology Stack \& Rationale

# 

# This document captures the main technology choices for CephasOps Phase 1.

# 

# ---

# 

# \## Backend

# 

# \### ASP.NET Core 8 Web API

# 

# \- High performance, mature framework for REST APIs.

# \- First-class support for:

# &nbsp; - Middleware (auth, company scoping, logging).

# &nbsp; - Dependency injection.

# &nbsp; - OpenAPI/Swagger.

# 

# \### Entity Framework Core + PostgreSQL

# 

# \- EF Core:

# &nbsp; - Simplifies data access and migrations.

# &nbsp; - LINQ for expressive queries.

# &nbsp; - Global query filters for multi-company scoping.

# \- PostgreSQL:

# &nbsp; - Battle-tested open-source database.

# &nbsp; - Strong JSON support and indexing.

# &nbsp; - Easy to host on multiple cloud providers.

# 

# \### Authentication \& Authorization

# 

# \- JWT-based authentication:

# &nbsp; - Tokens used by Admin web app and SI PWA.

# \- Role-based authorization:

# &nbsp; - Roles per company.

# &nbsp; - Director role for multi-company PNL.

# 

# \### Logging

# 

# \- Serilog:

# &nbsp; - Structured logs (`companyId`, `orderId`, `userId`).

# &nbsp; - Sinks: console, file, optional external log storage.

# &nbsp; - Helps debug field issues, e.g. docket delays.

# 

# \### Background Jobs

# 

# \- Hangfire (preferred) or Quartz:

# &nbsp; - Run scheduled KPI jobs.

# &nbsp; - Periodic email/WhatsApp reminders.

# &nbsp; - Reprocessing for failed parsing or invoice retries.

# 

# ---

# 

# \## Frontend – Admin / Operations

# 

# \### React (+ TypeScript)

# 

# \- Modern SPA framework.

# \- Strong typing for DTOs and hooks.

# \- Large ecosystem of UI libs/tools.

# 

# \### Vite

# 

# \- Fast dev server and build pipeline.

# \- Optimal DX for React + TS.

# 

# \### Tailwind CSS

# 

# \- Utility-first CSS for:

# &nbsp; - Dashboards

# &nbsp; - Tables

# &nbsp; - KPI cards

# \- Helps keep design consistent without heavy custom CSS.

# 

# \### TanStack Query (React Query)

# 

# \- Manages API calls, caching, background re-fetch.

# \- Automatic loading/error states on lists like Orders, Invoices, Inventory.

# 

# \### React Router

# 

# \- SPA routing mapped to:

# &nbsp; - `/dashboard`

# &nbsp; - `/orders`

# &nbsp; - `/scheduler`

# &nbsp; - `/inventory`

# &nbsp; - `/invoices`

# &nbsp; - `/pnl`

# &nbsp; - `/settings`

# 

# ---

# 

# \## Frontend – Service Installer PWA

# 

# \### React PWA

# 

# \- Same stack as Admin, optimised for mobile.

# \- Installable on Android devices (Add to Home Screen).

# 

# \### Camera \& GPS APIs

# 

# \- Camera:

# &nbsp; - Capture site photos, router/ONU images.

# \- GPS:

# &nbsp; - Record OnTheWay/MetCustomer locations for KPI.

# 

# \### Local Storage / IndexedDB

# 

# \- Basic offline support:

# &nbsp; - Cache today’s jobs.

# &nbsp; - Queue status updates during short disconnections.

# 

# ---

# 

# \## Tooling \& DevOps

# 

# \### Docker \& Docker Compose

# 

# \- Local environment:

# &nbsp; - API container

# &nbsp; - PostgreSQL

# &nbsp; - Background worker

# &nbsp; - Optional mailhog for email testing.

# 

# \### GitHub Actions

# 

# \- CI for:

# &nbsp; - Backend: build, tests, migrations check.

# &nbsp; - Frontend: lint, type-check, build.

# \- CD (later):

# &nbsp; - Deploy to staging \& production environments.

# 

# ---

# 

# \## Out of Scope (Phase 1)

# 

# \- Full multi-tenant (white-label) hosting.

# \- Real-time WebSocket updates (Plan for Phase 2).

# \- Fine-grained OpenID Connect integration.

# 

# These can be added later without changing the core architecture.



