# PHASE1_DOMAIN_MODELS  
CephasOps – Domain Model Scope (Phase 1: ISP Vertical)

Phase 1 delivers the **ISP operations core**, covering:

- TIME activation & assurance orders  
- Scheduler & Service Installer (SI) workflows  
- Building & splitter management  
- Inventory & materials  
- Dockets → Invoices → Submission ID  
- RMA (faulty routers/ONUs)  
- Multi-company & RBAC  
- Notifications  
- Parser rules  
- PNL basics (cost centres)

This document defines the **domain entities, aggregates, value objects, and required audit fields** for Phase 1.

> **Convention:**  
> Unless explicitly noted, all “business” entities are **company-scoped** and carry:
> - `CompanyId`
> - `CreatedAt`
> - `UpdatedAt`
> - `CreatedBy`
> - `UpdatedBy`

---

## 1. Core Multi-Company & RBAC

These models define how multiple companies are represented, and how users + roles are scoped per company.

### 1.1 Company

Represents an operating entity (e.g. Cephas Trading, Cephas Sdn. Bhd., Kingsman Classic Services, Menorah Travel & Tours).

**Fields**

- `CompanyId` (GUID)
- `Name`
- `Vertical` (enum: `ISP`, `Barbershop`, `Travel` – Phase 1: mostly `ISP`)
- `IsActive` (bool)
- `CreatedAt`, `UpdatedAt`
- `CreatedBy`, `UpdatedBy`

---

### 1.2 User

Represents a real person with login access.

Typical roles:

- Director  
- Admin / Operations  
- Scheduler  
- Warehouse  
- Finance  
- ServiceInstaller (SI)  
- Viewer / Read-only  

A User is global, but **permissions are per company** via `UserCompanyRole`.

**Fields**

- `UserId` (GUID)
- `Name`
- `Email`
- `Phone`
- `PasswordHash`
- `IsActive` (bool)
- `CreatedAt`, `UpdatedAt`
- `CreatedBy`, `UpdatedBy`

**Relations**

- `UserCompanyRoles` (collection of `UserCompanyRole` records)

---

### 1.3 Role

Role definition at company level.

Examples:

- `Director`  
- `Admin`  
- `Scheduler`  
- `Warehouse`  
- `Finance`  
- `ServiceInstaller`  
- `Viewer`  

**Fields**

- `RoleId` (GUID)
- `CompanyId`
- `Name` (e.g. `"Scheduler"`)
- `Description`
- `IsSystemDefault` (bool – distinguish built-in vs custom)
- `CreatedAt`, `UpdatedAt`
- `CreatedBy`, `UpdatedBy`

---

### 1.4 Permission

Atomic permission used to compose roles.

Examples:

- `Orders.View`, `Orders.Edit`, `Orders.Assign`
- `Scheduler.View`, `Scheduler.Edit`
- `Inventory.View`, `Inventory.Adjust`
- `Invoices.View`, `Invoices.Approve`, `Invoices.MarkPaid`
- `Settings.ManageParserRules`, etc.

**Fields**

- `PermissionId` (GUID)
- `Key` (string, unique, e.g. `"Orders.View"`)
- `Description`

> Note: `Permission` can be treated as global (no CompanyId) or company-scoped if needed. For simplicity you may omit `CompanyId` here and keep it system-wide.

---

### 1.5 RolePermission

Join entity mapping `Role` → `Permission`.

**Fields**

- `RolePermissionId` (GUID)
- `RoleId`
- `PermissionId`
- `CreatedAt`, `UpdatedAt`
- `CreatedBy`, `UpdatedBy`

---

### 1.6 UserCompanyRole

Maps a `User` to a `Role` for a specific `Company`.

**Fields**

- `UserCompanyRoleId` (GUID)
- `UserId`
- `CompanyId`
- `RoleId`
- `CreatedAt`, `UpdatedAt`
- `CreatedBy`, `UpdatedBy`

---

## 2. Service Installers & Partners

These models support assignment, scheduling, and KPI tracking for field technicians and partner telcos.

### 2.1 ServiceInstaller (SI)

Represents a technician doing on-site jobs (in-house staff or subcontractor).  
Can optionally be linked to a `User` for PWA login.

**Fields**

- `ServiceInstallerId` (GUID)
- `CompanyId`
- `DisplayName`
- `InstallerType` (enum: `InHouse`, `Subcon`)
- `Phone`
- `Region` (string – e.g. “PJ”, “KL City”, “Seremban”)
- `SkillTags` (simple string list: `["FTTH","FTTR","SDU"]`)
- `IsActive` (bool)
- `KpiProfileId` (optional, for future KPI configuration)
- `UserId` (optional link to `User`)
- `CreatedAt`, `UpdatedAt`
- `CreatedBy`, `UpdatedBy`

---

### 2.2 Partner

Represents upstream telco or service provider (Phase 1: mainly TIME).

**Fields**

- `PartnerId` (GUID)
- `CompanyId`
- `Name` (e.g. `"TIME dotCom"`)
- `Code` (e.g. `"TIME"`)
- `EmailPatterns` (list of patterns for Parser, e.g. `["*@time.com", "noreply@time.com"]`)
- `OrderTemplateMappings` (JSON or structured config for Excel/email parsing; keyed by rule type)
- `IsActive` (bool)
- `CreatedAt`, `UpdatedAt`
- `CreatedBy`, `UpdatedBy`

---

## 3. Orders, Status & Blockers

These models drive the main ISP operations: activation and assurance flows.

### 3.1 Order (Aggregate Root)

Represents any service order from a partner:

- Activation (FTTH/FTTO/FTTR/SDU/RDF POLE)  
- Modification  
- Assurance / Troubleshooting (TTKT)

**Fields**

- `OrderId` (GUID)
- `CompanyId`
- `OrderType` (enum: `Activation`, `Modification`, `Assurance`)
- `ServiceId` (string from partner, e.g. `TBBNB062587G`)
- `PartnerId`
- `CustomerName`
- `CustomerPhone`
- `CustomerEmail` (optional)
- `Address` (value object: `Line1`, `Line2`, `Postcode`, `City`, `State`)
- `BuildingId` (nullable – some orders may be address-only)
- `AppointmentDateTime` (UTC or with timezone)
- `AssignedInstallerId` (nullable `ServiceInstallerId`)
- `CurrentStatus` (enum: `Pending`, `Assigned`, `OnTheWay`, `MetCustomer`, `OrderCompleted`, `DocketsReceived`, `DocketsUploaded`, `Cancelled`, etc.)

**Assurance-specific fields (optional)**

- `TtktId` (TTKT code from partner)
- `AwoId` (AWO ID)
- `IssueDescription` (e.g. “Link Down, fibre issue”)
- `WarrantyStatus` (string or enum)
- `AssignUrl` (partner portal link to assign SI)
- `DocketUrl` (partner portal link to upload docket)

**Materials & splitter**

- `MaterialsPlanned` (collection of `OrderMaterialPlan` value objects)
- `MaterialsUsed` (collection of `OrderMaterialUsage` value objects)
- `SplitterId` (nullable – building’s splitter)
- `SplitterPortNumber` (nullable)

**Dockets & invoicing**

- `DocketsReceivedAt` (nullable DateTime)
- `DocketsUploadedAt` (nullable DateTime)
- `InvoiceId` (nullable – link to `Invoice` once billed)

**Audit**

- `CreatedAt`, `UpdatedAt`
- `CreatedBy`, `UpdatedBy`

**Typical behaviours**

- Assign/reassign SI  
- Change status (pending→assigned→on the way→…)  
- Reschedule appointment  
- Add/remove blockers  
- Set splitter & port (respecting standby rules)  
- Record docket received/uploaded timestamps  
- Link/unlink invoice  

---

### 3.2 OrderStatusLog

Immutable record of every status change (basis for KPIs like on-time arrival).

**Fields**

- `OrderStatusLogId` (GUID)
- `OrderId`
- `CompanyId`
- `PreviousStatus`
- `NewStatus`
- `ChangedAt`
- `ChangedBy` (UserId or pseudo-user for SI)
- `Remarks`
- `CreatedAt` (often same as `ChangedAt`)
- `CreatedBy` (UserId)

---

### 3.3 OrderBlocker (optional but recommended)

Tracks blockers and their durations.

**Fields**

- `OrderBlockerId` (GUID)
- `OrderId`
- `CompanyId`
- `BlockerType` (enum: `Customer`, `Building`, `Network`, `Partner`, `Internal`)
- `OpenedAt`
- `OpenedBy` (UserId)
- `ClosedAt` (nullable)
- `ClosedBy` (nullable)
- `Remarks`
- `CreatedAt`, `UpdatedAt`
- `CreatedBy`, `UpdatedBy`

---

### 3.4 Order Note (optional/simple)

Simple note log for internal comments.

**Fields**

- `OrderNoteId` (GUID)
- `OrderId`
- `CompanyId`
- `Note`
- `CreatedAt`
- `CreatedBy`
- `UpdatedAt`, `UpdatedBy` (if editable)

---

## 4. Buildings, Splitters & Ports

These models describe where orders happen and how splitter capacity is managed.

### 4.1 Building

Represents a physical site:

- Condo / apartment  
- Shoplot / commercial building  
- Landed SDU  
- RDF Pole deployment  

**Fields**

- `BuildingId` (GUID)
- `CompanyId`
- `Name` (e.g. `"Royce Residence"`)
- `ShortName` (e.g. `"ROYCE_RES"`)
- `Address` (value object)
- `BuildingType` (enum: `Prelaid`, `NonPrelaid`, `Sdu`, `RdfPole`)
- `Notes` (optional)
- `CreatedAt`, `UpdatedAt`
- `CreatedBy`, `UpdatedBy`

---

### 4.2 Splitter

Represents a fibre splitter installed within a building.

**Fields**

- `SplitterId` (GUID)
- `CompanyId`
- `BuildingId`
- `SplitterCode` (label used by field teams, e.g. `"S01-MDF-R1"`)
- `SplitterType` (enum: `OneTo8`, `OneTo12`, `OneTo32` etc.)
- `LocationDescription` (e.g. `"MDF Room Rack 01"`, `"Level 10 riser"`)
- `IsActive` (bool)
- `CreatedAt`, `UpdatedAt`
- `CreatedBy`, `UpdatedBy`

---

### 4.3 SplitterPort

Represents a single port on a splitter.

**Fields**

- `SplitterPortId` (GUID)
- `CompanyId`
- `SplitterId`
- `PortNumber` (int: 1…8, 1…12, 1…32)
- `PortState` (enum: `Available`, `Used`, `Reserved`, `Standby`)
- `OrderId` (nullable – order using this port)
- `IsStandbyPort` (bool, e.g. `true` for port 32 on 1:32 splitters)
- `RequiresApproval` (bool)
- `ApprovalAttachmentUrl` (nullable – link to partner approval)
- `CreatedAt`, `UpdatedAt`
- `CreatedBy`, `UpdatedBy`

---

## 5. Inventory & Materials

These models track static material definitions, their quantities, and movements.

### 5.1 Material

Static definition of a material such as router, ONU, fibre cable, connectors.

**Fields**

- `MaterialId` (GUID)
- `CompanyId`
- `Name`
- `Sku` (optional code)
- `Category` (enum/string: `Router`, `Onu`, `Cable`, `Connector`, `Tools`, `Other`)
- `IsSerialised` (bool)
- `UnitOfMeasure` (e.g. `pcs`, `meter`, `box`)
- `DefaultPartnerId` (optional, if material is partner-owned)
- `MinStockThreshold` (optional)
- `MaxStockThreshold` (optional)
- `CreatedAt`, `UpdatedAt`
- `CreatedBy`, `UpdatedBy`

---

### 5.2 MaterialStock

Represents current stock for one material at a specific location.

**Location types**

- `Warehouse`  
- `ServiceInstaller` (installer’s bag / van)  
- `Customer` (installed at customer premise)  
- `Rma` (faulty stock in RMA flow)  

**Fields**

- `MaterialStockId` (GUID)
- `CompanyId`
- `MaterialId`
- `LocationType` (enum: `Warehouse`, `ServiceInstaller`, `Customer`, `Rma`)
- `LocationRefId`  
  - e.g. `WarehouseId` (if you later model multiple warehouses)  
  - `ServiceInstallerId`  
  - `OrderId` (Customer location)  
- `Quantity`
- `SerializedItems` (optional JSON/list of serial numbers if small)
- `UpdatedAt`
- `UpdatedBy`

---

### 5.3 MaterialMovement

Immutable record of materials moving between locations.

**Examples**

- Warehouse → Installer (stock issue)  
- Installer → Customer (installation)  
- Installer → RMA (faulty return)  
- RMA → Warehouse (replacements received)  

**Fields**

- `MaterialMovementId` (GUID)
- `CompanyId`
- `MaterialId`
- `FromLocationType` (nullable for initial stock)
- `FromLocationRefId` (nullable)
- `ToLocationType`
- `ToLocationRefId`
- `Quantity`
- `SerialNumbers` (optional list)
- `OrderId` (optional – if movement is order-related)
- `MovedAt`
- `MovedBy` (UserId)
- `CreatedAt` (usually same as `MovedAt`)

---

### 5.4 RmaRequest

Tracks defective device RMA/MRA flow with partner.

**Fields**

- `RmaRequestId` (GUID)
- `CompanyId`
- `MaterialId`
- `SerialNumber`
- `PartnerId`
- `OrderId` (optional – if tied to a specific job)
- `Status` (enum: `Requested`, `InTransit`, `Approved`, `Rejected`, `Completed`)
- `MraDocumentUrl` (nullable – link to MRA PDF/scan)
- `Notes`
- `CreatedAt`, `UpdatedAt`
- `CreatedBy`, `UpdatedBy`

---

## 6. Billing, Invoices & Finance

These models support billing to partners and basic PNL inputs.

### 6.1 Invoice (Aggregate Root)

Represents a billing document to a partner, usually grouping one or more orders.

**Fields**

- `InvoiceId` (GUID)
- `CompanyId`
- `PartnerId`
- `InvoiceNumber` (string – CephasOps internal or partner-facing number)
- `Status` (enum: `Draft`, `ReadyForInvoice`, `Invoiced`, `Paid`, `Rejected`)
- `InvoiceDate`
- `SubmissionDate` (nullable – when uploaded to partner portal)
- `PortalSubmissionId` (e.g. `SUBM-20251122-987654`)
- `Currency`
- `TotalAmount`
- `Notes`
- `CreatedAt`, `UpdatedAt`
- `CreatedBy`, `UpdatedBy`

**Relations**

- `Lines` (collection of `InvoiceLine`)

---

### 6.2 InvoiceLine

Represents individual billed components, usually 1:1 with orders, but may include extra charges.

**Fields**

- `InvoiceLineId` (GUID)
- `CompanyId`
- `InvoiceId`
- `OrderId` (nullable – for generic or non-order charges)
- `Description`
- `Quantity`
- `Rate`
- `Amount`
- `CreatedAt`
- `CreatedBy`

---

## 7. Parser Rules & Notifications

These models drive the Email Parser Worker and system notifications.

### 7.1 ParserRule

Defines how incoming emails (and their attachments) are mapped to orders or RMA requests.

**Fields**

- `ParserRuleId` (GUID)
- `CompanyId`
- `PartnerId`
- `RuleType` (enum: `Activation`, `Modification`, `Assurance`, `Mra`)
- `IsActive`
- `FromPatterns` (list of from-address patterns)
- `SubjectPatterns` (list of subject patterns/regex)
- `ExcelTemplateVersion` (optional string)
- `ColumnMappings` (JSON – mapping Excel columns to fields: ServiceId, address, appointment time, etc.)
- `CreatedAt`, `UpdatedAt`
- `CreatedBy`, `UpdatedBy`

---

### 7.2 NotificationSetting

Controls which notifications are sent for which events and via which channels.

**Fields**

- `NotificationSettingId` (GUID)
- `CompanyId`
- `EventType` (enum: `NewOrder`, `Rescheduled`, `LateDocket`, `KpiBreached`, `InvoiceDueSoon`, `InvoiceOverdue`, etc.)
- `Channels` (list: `Email`, `WhatsApp`, `Sms`)
- `RecipientUserIds` (list of UserIds)
- `IsActive`
- `CreatedAt`, `UpdatedAt`
- `CreatedBy`, `UpdatedBy`

---

## 8. Cost Centres & PNL

These models support PNL grouping and reporting.

### 8.1 CostCentre

Used to group revenue and costs for PNL.

**Fields**

- `CostCentreId` (GUID)
- `CompanyId`
- `Name` (e.g. `"ISP Retail"`, `"ISP Corporate"`)
- `Code` (short code for finance systems)
- `IsActive`
- `CreatedAt`, `UpdatedAt`
- `CreatedBy`, `UpdatedBy`

> Later, orders, invoices, and materials can be tagged with `CostCentreId` for reporting.

---

## 9. Required Audit Fields & Conventions

### 9.1 Audit Fields (Standard)

For **all company-scoped entities**, include:

- `CompanyId`  
- `CreatedAt` (DateTime)  
- `UpdatedAt` (DateTime)  
- `CreatedBy` (UserId, string/GUID)  
- `UpdatedBy` (UserId, string/GUID)  

Some “global” entities (like `Permission`) may omit `CompanyId` but still carry `CreatedAt` / `UpdatedAt` as needed.

These are used for:

- Multi-company scoping  
- RBAC enforcement  
- Full traceability (who changed what, when)  
- Audit logs  
- Director-level reporting across multiple companies  

### 9.2 Value Objects

Common value objects used across entities:

- `Address`
  - `Line1`
  - `Line2`
  - `Postcode`
  - `City`
  - `State`
- `Money` (optional for future)
  - `Amount`
  - `Currency`
- `AppointmentWindow` (optional if needed)
  - `Start`
  - `End`

---

## 10. Phase 1 Domain Summary Table

| Category                  | Entities                                                                 |
|---------------------------|--------------------------------------------------------------------------|
| Multi-company & RBAC      | Company, User, Role, Permission, RolePermission, UserCompanyRole        |
| SI & Partners             | ServiceInstaller, Partner                                               |
| Orders & Status           | Order, OrderStatusLog, OrderBlocker, OrderNote                         |
| Buildings & Splitters     | Building, Splitter, SplitterPort                                       |
| Materials & Inventory     | Material, MaterialStock, MaterialMovement                               |
| RMA                       | RmaRequest                                                              |
| Billing & Invoices        | Invoice, InvoiceLine                                                    |
| Parser & Notifications    | ParserRule, NotificationSetting                                         |
| PNL / Finance Structure   | CostCentre                                                              |

---

This `PHASE1_DOMAIN_MODELS.md` is the **single merged, full version** and should be treated as the **source of truth** for:

- Domain layer (`CephasOps.Domain`) C# entities  
- EF Core mappings in Infrastructure  
- API contracts design (DTOs mapped from these entities)  
- Storybook flows & UI pages alignment

Any future changes to the Phase 1 domain should update **this document first**, then be reflected in code and API.
