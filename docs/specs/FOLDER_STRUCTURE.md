/cephasops/

│

├── backend/

│   ├── CephasOps.Api/                     # API layer (HTTP boundary)

│   │   ├── Controllers/

│   │   │   ├── OrdersController.cs

│   │   │   ├── SchedulerController.cs

│   │   │   ├── InventoryController.cs

│   │   │   ├── DocketsController.cs

│   │   │   ├── InvoicesController.cs

│   │   │   ├── SettingsController.cs

│   │   │   ├── BuildingsController.cs

│   │   │   ├── InstallersController.cs

│   │   │   ├── PartnersController.cs

│   │   │   ├── SplittersController.cs

│   │   │   ├── PnlController.cs

│   │   │   └── AuthController.cs

│   │   ├── DTOs/                         # Request/response contracts

│   │   ├── Filters/                      # Exception filters, etc.

│   │   ├── Middleware/                   # Auth, logging, multi-company scope

│   │   ├── Swagger/                      # OpenAPI setup

│   │   └── Program.cs

│   │

│   ├── CephasOps.Application/            # Use cases, business workflows

│   │   ├── Orders/                       # Commands/queries/handlers

│   │   ├── Scheduler/

│   │   ├── Inventory/

│   │   ├── Dockets/

│   │   ├── Invoices/

│   │   ├── Buildings/

│   │   ├── Settings/

│   │   ├── Reporting/                    # PNL, dashboards, KPIs

│   │   ├── Installers/

│   │   ├── Partners/

│   │   ├── Assurance/                    # TTKT, AWO, repair flows

│   │   ├── Interfaces/                   # IRepository, IEmailSender, etc.

│   │   ├── Common/                       # Validation, mapping helpers

│   │   └── DependencyInjection.cs

│   │

│   ├── CephasOps.Domain/                 # Entities, value objects, enums

│   │   ├── Orders/

│   │   ├── Invoices/

│   │   ├── Inventory/

│   │   ├── Buildings/

│   │   ├── Splitters/

│   │   ├── Materials/

│   │   ├── Companies/

│   │   ├── Users/

│   │   ├── ServiceInstallers/

│   │   ├── Rma/

│   │   ├── ValueObjects/

│   │   ├── Enums/

│   │   └── DomainEvents/

│   │

│   ├── CephasOps.Infrastructure/         # EF Core, integrations, IO

│   │   ├── Persistence/

│   │   │   ├── CephasOpsDbContext.cs

│   │   │   ├── Configurations/           # EF entity configs

│   │   │   └── Migrations/

│   │   ├── Email/

│   │   ├── FileStorage/

│   │   ├── BillingIntegrations/          # TIME, others (if any)

│   │   ├── BackgroundJobs/               # Hangfire/Quartz, etc.

│   │   ├── Parsers/                      # Excel/email parsing helpers

│   │   ├── Repositories/                 # IOrderRepository impl, etc.

│   │   └── DependencyInjection.cs

│   │

│   ├── CephasOps.Workers.EmailParser/    # Separate worker for POP3/IMAP parsing

│   │   ├── EmailParserWorker.cs

│   │   ├── ParserMappings/               # Activation/Assurance mapping rules

│   │   └── Program.cs

│   │

│   ├── CephasOps.Tests/                  # Test root (optional split)

│   │   ├── CephasOps.Api.Tests/

│   │   ├── CephasOps.Application.Tests/

│   │   ├── CephasOps.Domain.Tests/

│   │   └── CephasOps.Infrastructure.Tests/

│   │

│   └── README.md

│

├── frontend/

│   ├── app/                              # Admin web app (operations/finance)

│   │   ├── src/

│   │   │   ├── pages/

│   │   │   │   ├── dashboard/

│   │   │   │   ├── orders/

│   │   │   │   ├── scheduler/

│   │   │   │   ├── inventory/

│   │   │   │   ├── invoices/

│   │   │   │   ├── pnl/

│   │   │   │   ├── settings/

│   │   │   │   └── buildings/

│   │   │   ├── components/

│   │   │   ├── hooks/

│   │   │   ├── lib/                      # API clients, helpers

│   │   │   └── styles/

│   │   ├── public/

│   │   └── package.json

│   │

│   ├── si-app/                           # Service Installer PWA

│   │   ├── src/

│   │   │   ├── pages/

│   │   │   │   ├── login/

│   │   │   │   ├── jobs/                 # /jobs, /jobs/:id

│   │   │   │   ├── materials/            # Materials in hand

│   │   │   │   └── profile/              # KPI summary

│   │   │   ├── components/

│   │   │   ├── hooks/

│   │   │   ├── lib/

│   │   │   └── styles/

│   │   ├── public/

│   │   └── package.json

│   │

│   └── README.md

│

├── docs/

│   ├── STORYBOOK.md                      # Real-life flows (what you pasted)

│   ├── PAGES.md                          # Routes \& screens spec

│   ├── WIREFRAMES.md                     # Text wireframes (aligned with PAGES)

│   ├── ARCHITECTURE\_OVERVIEW.md          # Api/Application/Domain/Infra

│   ├── MULTI\_COMPANY\_MODULE.md           # CompanyId, RBAC, scoping

│   ├── API\_CONVENTIONS.md                # Naming, versioning, error shapes

│   └── README.md                         # How to read the docs

│

├── infra/                                # Infra-as-code \& runtime scaffolding

│   ├── docker/

│   │   ├── backend.Dockerfile

│   │   ├── app.Dockerfile

│   │   └── si-app.Dockerfile

│   ├── k8s/

│   │   ├── api-deployment.yaml

│   │   ├── app-deployment.yaml

│   │   └── si-app-deployment.yaml

│   ├── terraform/

│   └── README.md

│

├── .editorconfig

├── .gitignore

├── README.md

└── docker-compose.yml



