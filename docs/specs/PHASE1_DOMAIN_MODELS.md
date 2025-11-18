# \# PHASE1\_DOMAIN\_MODELS  

# CephasOps – Domain Model Scope (Phase 1: ISP Vertical)

# 

# Phase 1 delivers the \*\*ISP operations core\*\*, covering:

# 

# \- TIME activation orders  

# \- Scheduler \& SI workflows  

# \- Building \& splitter management  

# \- Inventory \& materials  

# \- Dockets → Invoices → Submission ID  

# \- RMA  

# \- Multi-company \& RBAC  

# \- Notifications  

# \- Parser rules  

# \- PNL basics (cost centres)

# 

# This section defines all \*\*domain entities, aggregates, value objects, and required audit fields\*\*.

# 

# ---

# 

# \## 1. Core Multi-Company \& RBAC

# 

# \### \*\*Company\*\*

# Represents an operating entity (Cephas Trading, Cephas Sdn. Bhd., etc.).

# 

# Fields:

# \- `CompanyId` (GUID)

# \- `Name`

# \- `Vertical` (ISP / Barbershop / Travel – Phase 1: ISP)

# \- `IsActive`

# \- `createdAt`, `updatedAt`

# 

# ---

# 

# \### \*\*User\*\*

# Account for Admin, Scheduler, Warehouse, Finance, Director, SI (optional for PWA).

# 

# Fields:

# \- `UserId`

# \- `Name`

# \- `Email`

# \- `Phone`

# \- `PasswordHash`

# \- `Roles` (per company)

# \- `Companies` (list of allowed companyIds)

# \- `createdAt`, `updatedAt`

# 

# ---

# 

# \### \*\*Role \& Permission\*\*

# Role-based access per company.

# 

# Examples:

# \- Director

# \- Admin / Operations

# \- Scheduler

# \- Warehouse

# \- Finance

# \- SI (Service Installer)

# \- Viewer

# 

# Fields:

# \- `RoleId`

# \- `CompanyId`

# \- `Name`

# \- `Permissions` (list)

# \- `createdAt`, `updatedAt`

# 

# ---

# 

# \## 2. Service Installers \& Partner

# 

# \### \*\*ServiceInstaller (SI)\*\*

# Represents a technician doing on-site jobs (in-house or subcon).

# 

# Fields:

# \- `InstallerId`

# \- `CompanyId`

# \- `Name`

# \- `Type` (InHouse / Subcon)

# \- `Phone`

# \- `Region`

# \- `Skills` (fibre, FTTH, FTTR, SDU, etc.)

# \- `KpiProfile` (links to KPI settings)

# \- `createdAt`, `updatedAt`, `createdBy`, `updatedBy`

# 

# ---

# 

# \### \*\*Partner\*\*

# The telco partner issuing orders (Phase 1 = TIME).

# 

# Fields:

# \- `PartnerId`

# \- `Name` (TIME)

# \- `EmailPatterns`

# \- `OrderTemplateMappings` (activation, assurance, MRA)

# \- `createdAt`, `updatedAt`

# 

# ---

# 

# \## 3. Orders \& Status Workflow

# 

# \### \*\*Order\*\* (Aggregate Root)

# Represents any service order: Activation, Modification, Assurance.

# 

# Common fields:

# \- `OrderId`

# \- `CompanyId`

# \- `OrderType` (Activation / Modification / Assurance)

# \- `ServiceId`

# \- `TTKT` (optional for Assurance)

# \- `AWO` (optional for Assurance)

# \- `CustomerName`

# \- `CustomerPhone`

# \- `Address` (value object)

# \- `BuildingId`

# \- `AppointmentDateTime`

# \- `PartnerId`

# \- `CurrentStatus` (enum)

# \- `MaterialsPlanned` (list)

# \- `MaterialsUsed` (list)

# \- `PortalLinks` (Assign URL, Docket URL – Assurance)

# \- `createdAt`, `updatedAt`, `createdBy`, `updatedBy`

# 

# Domain behaviour:

# \- Assign SI

# \- Update status

# \- Reschedule

# \- Add blocker

# \- Add splitter port assignment

# \- Attach docket/photos

# \- Mark completed

# \- Link invoice

# 

# ---

# 

# \### \*\*OrderStatusLog\*\*

# Tracks transitions (for auditing \& KPIs).

# 

# Fields:

# \- `LogId`

# \- `OrderId`

# \- `PreviousStatus`

# \- `NewStatus`

# \- `Timestamp`

# \- `Actor` (userId or SI)

# \- `Remarks`

# 

# ---

# 

# \## 4. Buildings, Splitters \& Ports

# 

# \### \*\*Building\*\*

# Represents a condo, residence, shoplot, landed SDU site, or RDF POLE location.

# 

# Fields:

# \- `BuildingId`

# \- `CompanyId`

# \- `Name`

# \- `ShortName`

# \- `Address`

# \- `Type` (Prelaid / NonPrelaid / SDU / RDFPole)

# \- `Splitters` (list)

# \- `createdAt`, `updatedAt`, `createdBy`, `updatedBy`

# 

# ---

# 

# \### \*\*Splitter\*\*

# A fibre splitter device (1:8, 1:12, 1:32).

# 

# Fields:

# \- `SplitterId`

# \- `BuildingId`

# \- `CompanyId`

# \- `Type` (1:8 / 1:12 / 1:32)

# \- `LocationDescription` (MDF, riser, floor)

# \- `Ports` (list of SplitterPort)

# \- `createdAt`, `updatedAt`, `createdBy`, `updatedBy`

# 

# ---

# 

# \### \*\*SplitterPort\*\*

# One port on a splitter.

# 

# Fields:

# \- `PortNumber`

# \- `State` (Available / Used / Reserved / Standby)

# \- `UsedByOrderId` (optional)

# \- `RequiresApproval` (true if port 32 on 1:32)

# \- `ApprovalAttachmentUrl`

# \- `updatedAt`

# 

# ---

# 

# \## 5. Inventory \& Materials

# 

# \### \*\*Material\*\*

# Static item definition (router, ONU, cable, connector).

# 

# Fields:

# \- `MaterialId`

# \- `CompanyId`

# \- `Name`

# \- `Category` (Router / ONU / Cable / Connector / Tools)

# \- `IsSerialised` (true/false)

# \- `PartnerId` (optional)

# \- `Unit` (pcs/meter)

# \- `createdAt`, `updatedAt`

# 

# ---

# 

# \### \*\*MaterialStock\*\*

# Tracks quantities at locations.

# 

# Locations:

# \- Warehouse

# \- SI (installer stock)

# \- Customer (delivered)

# \- RMA (faulty)

# 

# Fields:

# \- `MaterialStockId`

# \- `MaterialId`

# \- `CompanyId`

# \- `LocationType` (Warehouse / SI / Customer / RMA)

# \- `LocationRefId` (SIId or OrderId)

# \- `Quantity`

# \- `SerialNumbers` (optional)

# \- `updatedAt`, `updatedBy`

# 

# ---

# 

# \### \*\*MaterialMovement\*\*

# Represents any flow:

# 

# Warehouse → SI  

# SI → Customer  

# SI → RMA  

# Customer → RMA  

# RMA → Warehouse, etc.

# 

# Fields:

# \- `MovementId`

# \- `CompanyId`

# \- `MaterialId`

# \- `FromLocationType`

# \- `FromLocationRef`

# \- `ToLocationType`

# \- `ToLocationRef`

# \- `Quantity`

# \- `SerialNumbers`

# \- `OrderId` (optional)

# \- `Timestamp`

# \- `ActorUserId`

# 

# ---

# 

# \### \*\*RmaRequest\*\*

# Tracks router/ONU replacements and RMA/MRA flow.

# 

# Fields:

# \- `RmaRequestId`

# \- `CompanyId`

# \- `MaterialId`

# \- `SerialNumber`

# \- `OrderId`

# \- `PartnerId`

# \- `Status` (Requested / InTransit / Approved / Rejected / Completed)

# \- `MraPdfUrl` (if partner returns MRA)

# \- `createdAt`, `updatedAt`, `updatedBy`

# 

# ---

# 

# \## 6. Billing, Invoices \& Finance

# 

# \### \*\*Invoice\*\* (Aggregate Root)

# Generated when an order is ready (after docket validation).

# 

# Fields:

# \- `InvoiceId`

# \- `CompanyId`

# \- `PartnerId`

# \- `Status` (Draft / ReadyForInvoice / Invoiced / Paid / Rejected)

# \- `InvoiceDate`

# \- `SubmissionDate`

# \- `PortalSubmissionId`

# \- `TotalAmount`

# \- `Lines` (list of InvoiceLine)

# \- `createdAt`, `updatedAt`, `createdBy`, `updatedBy`

# 

# ---

# 

# \### \*\*InvoiceLine\*\*

# Represents amounts billed per order.

# 

# Fields:

# \- `InvoiceLineId`

# \- `InvoiceId`

# \- `OrderId`

# \- `Description`

# \- `Quantity`

# \- `Rate`

# \- `Amount`

# \- `createdAt`

# 

# ---

# 

# \## 7. Parser Rules \& Notifications

# 

# \### \*\*ParserRule\*\*

# Rules for email/Excel parsing per partner \& template version.

# 

# Fields:

# \- `ParserRuleId`

# \- `CompanyId`

# \- `PartnerId`

# \- `RuleType` (Activation / Modification / Assurance / MRA)

# \- `FromPatterns`

# \- `SubjectPatterns`

# \- `ColumnMappings` (JSON)

# \- `IsActive`

# \- `createdAt`, `updatedAt`

# 

# ---

# 

# \### \*\*NotificationSetting\*\*

# Controls notifications for KPI breaches, new orders, reschedules.

# 

# Fields:

# \- `NotificationSettingId`

# \- `CompanyId`

# \- `EventType` (NewOrder / Rescheduled / LateDocket / KPIWarning / InvoiceDue)

# \- `Channels` (Email / WhatsApp / SMS)

# \- `Recipients` (userIds)

# \- `createdAt`, `updatedAt`, `updatedBy`

# 

# ---

# 

# \## 8. Cost Centres (PNL)

# 

# \### \*\*CostCentre\*\*

# Used for PNL grouping.

# 

# Fields:

# \- `CostCentreId`

# \- `CompanyId`

# \- `Name`

# \- `Code`

# \- `IsActive`

# \- `createdAt`, `updatedAt`

# 

# ---

# 

# \## 9. Required Audit Fields (for ALL domain models)

# 

# Every entity must include:

# 

# \- `companyId`

# \- `createdAt`

# \- `updatedAt`

# \- `createdBy` (userId)

# \- `updatedBy` (userId)

# 

# These fields enforce:

# 

# \- Multi-company scoping  

# \- RBAC  

# \- Full traceability  

# \- Clean audit logs  

# \- Director-only visibility for cross-company data

# 

# ---

# 

# \## Phase 1 Domain Summary Table

# 

# | Category                 | Entities |

# |-------------------------|----------|

# | Multi-company \& RBAC   | Company, User, Role, Permission |

# | SI \& Partners          | ServiceInstaller, Partner |

# | Orders                 | Order, OrderStatusLog |

# | Buildings \& Splitters | Building, Splitter, SplitterPort |

# | Materials \& Inventory | Material, MaterialStock, MaterialMovement |

# | RMA                   | RmaRequest |

# | Billing               | Invoice, InvoiceLine |

# | Parser \& Notifications| ParserRule, NotificationSetting |

# | Finance               | CostCentre |

# 

# ---

# 

# This is now \*\*fully aligned\*\* with all updated architecture, Storybook flows, and page designs.

# 

