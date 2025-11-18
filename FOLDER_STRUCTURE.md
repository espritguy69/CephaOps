Repository / Folder Structure

cephasops/

├── README.md

├── .gitignore

├── docker-compose.yml

├── backend/

│   ├── CephasOps.Api/

│   │   ├── Controllers/

│   │   │   ├── OrdersController.cs

│   │   │   ├── SchedulerController.cs

│   │   │   ├── InventoryController.cs

│   │   │   ├── InvoicesController.cs

│   │   │   ├── BuildingsController.cs

│   │   │   ├── SplittersController.cs

│   │   │   ├── PartnersController.cs

│   │   │   ├── InstallersController.cs

│   │   │   ├── DocketsController.cs

│   │   │   ├── SettingsController.cs

│   │   │   ├── ReportsController.cs

│   │   │   └── AuthController.cs

│   │   ├── DTOs/

│   │   ├── Middleware/

│   │   │   └── CompanyScopeMiddleware.cs

│   │   ├── Filters/

│   │   ├── Mapping/

│   │   └── Program.cs

│   │

│   ├── CephasOps.Application/

│   │   ├── Common/

│   │   │   ├── ICurrentCompanyContext.cs

│   │   │   ├── IRepository.cs

│   │   │   └── Result.cs

│   │   ├── Orders/

│   │   ├── Scheduler/

│   │   ├── Inventory/

│   │   ├── Invoices/

│   │   ├── Buildings/

│   │   ├── Splitters/

│   │   ├── Partners/

│   │   ├── Installers/

│   │   ├── Dockets/

│   │   ├── Settings/

│   │   ├── Reporting/

│   │   └── Auth/

│   │

│   ├── CephasOps.Domain/

│   │   ├── Common/

│   │   │   └── CompanyEntityBase.cs

│   │   ├── Entities/

│   │   │   ├── Company.cs

│   │   │   ├── User.cs

│   │   │   ├── Role.cs

│   │   │   ├── Order.cs

│   │   │   ├── OrderStatusLog.cs

│   │   │   ├── Building.cs

│   │   │   ├── Splitter.cs

│   │   │   ├── SplitterPort.cs

│   │   │   ├── Material.cs

│   │   │   ├── MaterialStock.cs

│   │   │   ├── MaterialMovement.cs

│   │   │   ├── RmaRequest.cs

│   │   │   ├── Invoice.cs

│   │   │   ├── InvoiceLine.cs

│   │   │   ├── ServiceInstaller.cs

│   │   │   ├── Partner.cs

│   │   │   ├── ParserRule.cs

│   │   │   ├── NotificationSetting.cs

│   │   │   └── CostCentre.cs

│   │   ├── Enums/

│   │   └── ValueObjects/

│   │

│   ├── CephasOps.Infrastructure/

│   │   ├── Persistence/

│   │   │   ├── CephasOpsDbContext.cs

│   │   │   └── Configurations/

│   │   ├── Repositories/

│   │   │   └── EfRepository.cs

│   │   ├── Email/

│   │   ├── FileStorage/

│   │   ├── Jobs/

│   │   └── Identity/

│   │

│   └── CephasOps.Tests/

│       ├── ApiTests/

│       ├── ApplicationTests/

│       └── DomainTests/

│

├── frontend/

│   ├── app/          # Admin / Ops Web App

│   │   ├── src/

│   │   │   ├── pages/

│   │   │   │   ├── dashboard/

│   │   │   │   ├── orders/

│   │   │   │   ├── scheduler/

│   │   │   │   ├── inventory/

│   │   │   │   ├── invoices/

│   │   │   │   ├── pnl/

│   │   │   │   └── settings/

│   │   │   ├── components/

│   │   │   ├── hooks/

│   │   │   ├── api/

│   │   │   ├── types/

│   │   │   └── utils/

│   │   ├── index.html

│   │   ├── vite.config.ts

│   │   └── package.json

│   │

│   └── si-app/       # Service Installer PWA

│       ├── src/

│       │   ├── pages/

│       │   │   ├── jobs/

│       │   │   ├── materials/

│       │   │   └── profile/

│       │   ├── components/

│       │   ├── hooks/

│       │   ├── api/

│       │   └── utils/

│       ├── public/

│       ├── vite.config.ts

│       └── package.json

│

├── docs/

│   ├── SYSTEM\_OVERVIEW.md

│   ├── ARCHITECTURE\_OVERVIEW.md

│   ├── TECH\_STACK\_DECISION.md

│   ├── FOLDER\_STRUCTURE.md

│   ├── PHASE1\_DOMAIN\_MODELS.md

│   ├── SYSTEM\_DESIGN\_DIAGRAMS.md

│   ├── MULTI\_COMPANY\_IMPLEMENTATION.md

│   ├── STORYBOOK.md

│   ├── PAGES.md

│   ├── WIREFRAMES.md

│   ├── ORDERS\_MODULE.md

│   ├── SCHEDULER\_MODULE.md

│   ├── INVENTORY\_MODULE.md

│   ├── INVOICING\_MODULE.md

│   ├── BUILDINGS\_SPLITTERS\_MODULE.md

│   ├── PARTNERS\_INSTALLERS\_MODULE.md

│   ├── DOCKETS\_MODULE.md

│   ├── EMAIL\_PARSER\_MODULE.md

│   ├── REPORTING\_MODULE.md

│   ├── SETTINGS\_MODULE.md

│   └── API\_BLUEPRINT.md   ← \*\*NEW FILE\*\*

│

└── .github/

&nbsp;   └── workflows/

&nbsp;       ├── backend-ci.yml

&nbsp;       └── frontend-ci.yml



