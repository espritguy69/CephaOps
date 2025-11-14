Perfect — proceeding with the FINAL MODULE before we assemble everything into the big combined system documentation set.

This is a major module because it controls all configuration logic that powers the rest of the system.

📘 SETTINGS_MODULE.md
(Combined: Setup + Technical Specification + Storybook)

This module maintains all master data and configuration logic for the Cephas Service Installer Operations System.

Every CRUD here directly affects the workflows of:
Orders → Schedule → Splitters → Materials → Inventory → Dockets → Invoicing.

# SETTINGS MODULE  
### (Setup + Technical Specification + Storybook)

---

# 1. SETUP OVERVIEW

The **Settings Module** contains the “Master Data” and “Reference Tables” for the entire system.

It includes:

### ✔ Materials  
✔ Buildings  
✔ Splitters  
✔ Partners  
✔ Service Installers  
✔ Default Material Templates  
✔ Blockers & Status Flow  
✔ KPI Settings (job duration, docket KPI)  
✔ System Rules  
✔ SI Payment Rate Matrix  

The purpose:

- Centralize all configurable data  
- Future-proof system  
- Allow Admin to modify workflow without developer involvement  
- Auto-populate forms across whole system  

---

# 2. TECHNICAL SPECIFICATION

---

# 2.1 Material Settings

Materials include:

- Fiber Cable (2-core, RDF, 80m, etc.)
- Patchcord 6m
- Patchcord 10m
- UPC Connector
- APC Connector
- ONU
- Router
- Mesh WiFi
- Booster
- CPE
- Etc.

### Material Fields:


MaterialId
MaterialName
MaterialCode
MaterialType (Fiber / ONU / Router / Connector)
IsSerialized (true/false)
PartnerProvided (true/false)
UnitOfMeasure
MinimumStock
Active (bool)


### Inventory Logic Powered By Materials
- Material issued automatically follows template by building type.
- Material deducted from warehouse via service installer job completion.
- Serialized material tracked individually.

---

# 2.2 Building Settings

Buildings include:

- High-rise condo  
- Office tower  
- SDU houses  
- RDF pole deployments  
- Prelaid buildings  
- Non-prelaid buildings  

### Building Fields:


BuildingId
BuildingName
BuildingShortName
Address
BuildingType (Prelaid / Non-Prelaid / SDU / RDF Pole)
ContactPersonName
ContactPersonMobile
ContactPersonEmail
Partner (optional)
DefaultSplitterId
DefaultMaterialTemplateId
Notes
Active (bool)


---

# 2.3 Splitter Settings

Splitters belong to buildings.

Splitter types:

- 1:8
- 1:12
- 1:32

Each splitter has ports:

- Ports must be tracked from 1 → N
- Port 32 is STANDBY port for 1:32 splitters

### Splitter Fields:


SplitterId
BuildingId
SplitterType
SplitterCode
LocationDescription
TotalPorts
PortsUsed
StandbyPort = 32 (if applicable)
ReservedPorts (list)
Active (bool)


### Port Usage Tracking:
Each port must know:

- Which order used it
- Which SI used it
- Whether standby port used
- Approval file if standby used

---

# 2.4 Partner Settings

Partners include:

- TIME
- Celcom
- Digi
- U Mobile

### Partner Fields:


PartnerId
PartnerName
PrefixRules (e.g., TBBNXXXXX)
AllowAssurance (bool)
RequiresApprovalForStandbySplitterUse (bool)
RateCardReference
PortalURL
ContactEmail
Active (bool)


### Partner ID validation:
TIME:
- Unique ID = TBBNXXXXXXXX

TIME Assurance:
- Ticket ID: TTKTXXXXXXXX
- AWO: AWOXXXXXXXXX

CELCOM:
- CELCOMXXX

DIGI:
- DIGIXXXX

All validated here.

---

# 2.5 Service Installer (SI) Settings

Each SI stores:



InstallerId
FullName
MobileNumber
Email
EmergencyContactName
EmergencyContactPhone
SkillSet (FTTH / FTTR / SDU / RDF Pole / Assurance)
PaymentRates (per job type)
Active (bool)


### SI Payment Rate Matrix
Admin can set for each order type:

- FTTH Activation Rate  
- FTTH Modification Rate  
- FTTR Rate  
- SDU Rate  
- RDF Pole Rate  
- Assurance Rate  

This powers:

- SI earnings reports  
- Invoicing validation  
- Performance dashboards  

---

# 2.6 Default Material Templates

Each building type must have default materials.

Examples:

### Prelaid
- Patchcord 6m x1  
- Patchcord 10m x1  

### Non-Prelaid
- 80m fiber  
- UPC connector  
- APC connector  

### SDU
- 80m RDF cable  
- UPC connector  
- APC connector  

### Fields:


MaterialTemplateId
TemplateName
BuildingType
MaterialList (list of MaterialId + quantity)


System uses this template when creating an order.

---

# 2.7 Blockers & Status Flow Management

Admin can define:

### Blocker Types:
- Customer  
- Building  
- Network  

Each blocker stores:



BlockerId
BlockerCategory
BlockerDescription
FollowUpRequired (bool)
Resolved (bool)
ResolvedAt
ResolvedBy


### Status Flow Settings

Status flow sequence fully configurable:



Pending
Assigned
OnTheWay
MetCustomer
Completed
DocketReceived
DocketUploaded
ReadyForInvoice
Invoiced
Completed (Paid)


Admin can modify allowed transitions.

---

# 2.8 System Rules

Admin can configure:

### KPI Rules


Prelaid = 1 hour
NonPrelaid = 2 hours
FTTR = 3 hours
FTTC = 3 hours
SDU = 3 hours
RDF Pole = 3 hours


### Docket KPI


Manual docket submission: 30 minutes after completion


### Standby Splitter Rule
If SI uses port 32:

- Partner approval required  
- Approval file upload mandatory  

System enforces this.

---

# 2.9 Audit Logging on Settings

Every setting change must be logged:

- Old value  
- New value  
- Changed by  
- Timestamp  
- Module  

---

# 3. STORYBOOK (Narrative Use Cases)

---

## 3.1 Story: Admin adds a new material

Admin opens:

**Settings → Materials → Add New**

Input:

- ONU Huawei HG8145X6  
- Type: ONU  
- Serialized: Yes  
- MinimumStock: 10  

System:

- adds new item to materials  
- makes it available in inventory  
- enables it to appear in SI templates  

---

## 3.2 Story: Create new building

Admin fills:

- Royce Residence  
- Type: Prelaid  
- BM Contact: Bryan  
- Splitter default: Splitter B-1  

System:

- auto applies Prelaid default material template  
- creates building record  
- links splitters  
- Orders page now shows building in dropdown  

---

## 3.3 Story: Admin updates SI skill sets

Installer “Klavin” gains new certification:

- FTTR certified

System updates:

- FTTR jobs now assignable to Klavin  
- Scheduler filters reflect new skill  
- KPI report includes FTTR metrics  

---

## 3.4 Story: Change KPI rules

Management modifies:

- SDU KPI: from 3 hours → 2 hours

System immediately updates:

- All future orders  
- KPI evaluation rules  
- SI performance calculations  
- Reporting dashboards  

No code changes required.

---

## 3.5 Story: Standby splitter approval rule

SI uses port 32 at a 1:32 splitter.

System triggers:

**“Partner approval required”**

Admin must upload PDF or screenshot.

Without approval:

- cannot move order to DocketReceived stage  
- cannot complete order  

---

## 3.6 Story: Add new partner

Admin adds:

**“YES 5G Broadband”**

Fields:

- Prefix = Y5G000XX  
- AllowAssurance = true  

System now supports completely new partner with zero coding.

---

## 3.7 Story: Blockers

Order stuck at MetCustomer.

Admin marks:

Blocker Category: **Network**  
Description: **OLT port faulty**  

System:

- pauses order  
- flags Supervisor  
- logs blocker  
- prevents status change until resolved  

---

# END OF SETTINGS MODULE

🎉 ALL 10 MODULES ARE COMPLETED.

Next step (Final Step):
