✅ 2. FOLDER STRUCTURE & FILENAMES FOR CURSOR AI
Production-ready repository layout (for .NET + React)
/csios/
│
├── backend/
│   ├── Csios.Api/
│   │   ├── Controllers/
│   │   │   ├── OrdersController.cs
│   │   │   ├── SchedulersController.cs
│   │   │   ├── InventoryController.cs
│   │   │   ├── DocketsController.cs
│   │   │   ├── InvoicesController.cs
│   │   │   ├── SettingsController.cs
│   │   │   ├── BuildingsController.cs
│   │   │   ├── InstallersController.cs
│   │   │   ├── PartnersController.cs
│   │   │   ├── MaterialsController.cs
│   │   │   ├── ReportsController.cs
│   │   │   └── SplittersController.cs
│   │   ├── DTOs/
│   │   ├── Models/
│   │   ├── Services/
│   │   ├── EmailParser/
│   │   ├── StatusEngine/
│   │   ├── KpiEngine/
│   │   ├── InventoryEngine/
│   │   ├── InvoicingEngine/
│   │   ├── ReportingEngine/
│   │   ├── SettingsEngine/
│   │   └── Program.cs
│   │
│   ├── Csios.Domain/
│   ├── Csios.Infrastructure/
│   ├── Csios.Tests/
│   └── README.md
│
├── frontend/
│   ├── src/
│   │   ├── pages/
│   │   │   ├── dashboard/
│   │   │   ├── orders/
│   │   │   ├── scheduler/
│   │   │   ├── inventory/
│   │   │   ├── invoices/
│   │   │   ├── reporting/
│   │   │   └── settings/
│   │   ├── components/
│   │   ├── hooks/
│   │   └── utils/
│   └── package.json
│
├── docs/
│   ├── SYSTEM_OVERVIEW.md
│   ├── ORDERS_MODULE.md
│   ├── EMAIL_PARSER_MODULE.md
│   ├── STATUS_KPI_SPLITTER_MODULE.md
│   ├── SCHEDULER_MODULE.md
│   ├── INVENTORY_MODULE.md
│   ├── INVOICING_MODULE.md
│   ├── BUILDINGS_SPLITTERS_MODULE.md
│   ├── PARTNERS_INSTALLERS_MODULE.md
│   ├── DOCKETS_MODULE.md
│   ├── REPORTING_MODULE.md
│   └── SETTINGS_MODULE.md
│
└── docker-compose.yml
