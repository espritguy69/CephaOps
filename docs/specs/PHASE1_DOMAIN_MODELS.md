# PHASE1\_DOMAIN\_MODELS.md

\# PHASE1\_DOMAIN\_MODELS

CephasOps – Domain Model Scope (Phase 1: ISP Vertical)



Phase 1 delivers the ISP operations core:



\- TIME activation \& assurance orders

\- Scheduler \& SI workflows

\- Buildings \& splitters

\- Inventory \& RMA

\- Dockets → Invoices → Submission ID

\- Multi-company \& RBAC

\- Notifications \& parser rules

\- PNL basics via cost centres



All domain entities derive from `CompanyEntityBase` (see `MULTI\_COMPANY\_IMPLEMENTATION.md`).



---



\## 1. Multi-Company \& RBAC



\### Company



\- `CompanyId` (Guid)

\- `Name`

\- `Vertical` (ISP | Barbershop | Travel)

\- `IsActive`

\- Audit fields.



\### User



\- `UserId`

\- `Name`

\- `Email`

\- `Phone`

\- `PasswordHash`

\- (Relationships to roles \& companies)

\- Audit fields.



\### Role, Permission



\- `RoleId`, `CompanyId`, `Name`

\- `Permissions` (collection)

\- Audit fields.



Permissions are conceptual (e.g., `Orders.Read`, `Inventory.Write`), implemented as enum or string.



---



\## 2. Service Installers \& Partners



\### ServiceInstaller



\- `InstallerId`

\- `CompanyId`

\- `Name`

\- `Type` (InHouse | Subcon)

\- `Phone`

\- `Region`

\- `Skills` (comma-separated or child table)

\- `KpiProfileId` (optional)

\- Audit fields.



\### Partner



\- `PartnerId`

\- `Name` (e.g., TIME)

\- `EmailPatterns`

\- `OrderTemplateMappings` (activation, assurance, MRA)

\- Audit fields.



---



\## 3. Orders \& Status



\### Order (Aggregate Root)



\- `OrderId`

\- `CompanyId`

\- `OrderType` (Activation | Modification | Assurance)

\- `ServiceId`

\- `TTKT` (optional)

\- `AWO` (optional)

\- `CustomerName`

\- `CustomerPhone`

\- `Address` (value object)

\- `BuildingId`

\- `AppointmentDateTime`

\- `PartnerId`

\- `AssignedInstallerId` (nullable)

\- `CurrentStatus`

\- `PortalAssignUrl`

\- `PortalDocketUrl`

\- `MaterialsPlanned` (child table)

\- `MaterialsUsed` (child table)

\- Audit fields.



Behaviour (methods):



\- AssignInstaller()

\- ChangeStatus()

\- Reschedule()

\- AttachSplitterPort()

\- AttachDocket()

\- MarkCompleted()

\- LinkInvoiceLine()



\### OrderStatusLog



\- `OrderStatusLogId`

\- `OrderId`

\- `PreviousStatus`

\- `NewStatus`

\- `Timestamp`

\- `ActorUserId` or `ActorInstallerId`

\- `Remarks`.



---



\## 4. Buildings \& Splitters



\### Building



\- `BuildingId`

\- `CompanyId`

\- `Name`

\- `ShortName`

\- `Address`

\- `Type` (Prelaid | NonPrelaid | SDU | RDFPole)

\- Audit fields.



\### Splitter



\- `SplitterId`

\- `CompanyId`

\- `BuildingId`

\- `SplitterType` (1x8 | 1x12 | 1x32)

\- `LocationDescription`

\- Audit fields.



\### SplitterPort



\- Composite key: (`SplitterId`, `PortNumber`)

\- `PortNumber` (int)

\- `State` (Available | Used | Reserved | Standby)

\- `UsedByOrderId` (nullable)

\- `RequiresApproval` (bool)

\- `ApprovalAttachmentUrl` (nullable)

\- Audit fields.



---



\## 5. Inventory \& RMA



\### Material



\- `MaterialId`

\- `CompanyId`

\- `Name`

\- `Category` (Router | ONU | Cable | Connector | Tools)

\- `IsSerialised` (bool)

\- `PartnerId` (nullable)

\- `Unit` (pcs | meter | box)

\- Audit fields.



\### MaterialStock



\- `MaterialStockId`

\- `CompanyId`

\- `MaterialId`

\- `LocationType` (Warehouse | Installer | Customer | RMA)

\- `LocationRefId` (InstallerId or OrderId, etc.)

\- `Quantity`

\- `SerialNumbers` (JSON or separate table)

\- Audit fields.



\### MaterialMovement



\- `MaterialMovementId`

\- `CompanyId`

\- `MaterialId`

\- `FromLocationType`, `FromLocationRefId`

\- `ToLocationType`, `ToLocationRefId`

\- `Quantity`

\- `SerialNumbers`

\- `OrderId` (nullable)

\- `Timestamp`

\- `ActorUserId`.



\### RmaRequest



\- `RmaRequestId`

\- `CompanyId`

\- `MaterialId`

\- `SerialNumber`

\- `OrderId`

\- `PartnerId`

\- `Status` (Requested | InTransit | Approved | Rejected | Completed)

\- `MraPdfUrl`

\- Audit fields.



---



\## 6. Billing \& Invoices



\### Invoice



\- `InvoiceId`

\- `CompanyId`

\- `PartnerId`

\- `Status` (Draft | ReadyForInvoice | Invoiced | Paid | Rejected)

\- `InvoiceDate`

\- `SubmissionDate` (nullable)

\- `PortalSubmissionId`

\- `TotalAmount`

\- Audit fields.



\### InvoiceLine



\- `InvoiceLineId`

\- `InvoiceId`

\- `OrderId`

\- `Description`

\- `Quantity`

\- `Rate`

\- `Amount`

\- Audit fields (Created).



---



\## 7. Parser Rules \& Notifications



\### ParserRule



\- `ParserRuleId`

\- `CompanyId`

\- `PartnerId`

\- `RuleType` (Activation | Modification | Assurance | MRA)

\- `FromPatterns` (JSON)

\- `SubjectPatterns` (JSON)

\- `ColumnMappings` (JSON)

\- `IsActive`

\- Audit fields.



\### NotificationSetting



\- `NotificationSettingId`

\- `CompanyId`

\- `EventType` (NewOrder | Rescheduled | LateDocket | KpiWarning | InvoiceDue)

\- `Channels` (Email | WhatsApp | SMS)

\- `Recipients` (list of UserIds or group)

\- Audit fields.



---



\## 8. Cost Centres



\### CostCentre



\- `CostCentreId`

\- `CompanyId`

\- `Name`

\- `Code`

\- `IsActive`

\- Audit fields.



Used for PNL allocations and reporting.



---



\## 9. Audit Requirements



All entities listed here:



\- MUST include:

&nbsp; - `CompanyId`

&nbsp; - `CreatedAt`, `UpdatedAt`

&nbsp; - `CreatedBy`, `UpdatedBy`

\- MUST respect global query filters for company scoping.



