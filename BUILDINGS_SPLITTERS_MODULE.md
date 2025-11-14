📘 BUILDINGS_SPLITTERS_MODULE.md
(Combined: Setup + Technical Specification + Storybook)

This module handles:

Building database

Building managers (BM info)

Building types (Prelaid, Non-prelaid, SDU, RDF Pole, etc.)

Default materials for each building type

Splitters (1:8 / 1:12 / 1:32)

Splitter port tracking (usage, free ports, standby ports)

Port reservation and approval logic

This module ensures Cephas has complete visibility of all infrastructure.

# BUILDINGS & SPLITTERS MODULE  
### (Setup + Technical Specification + Storybook)

---

# 1. SETUP OVERVIEW

This module provides:

### ✔ Building directory  
For every job, the system must know:

- Building metadata  
- Building Type  
- BM Contacts  
- Partner rules  
- Splitters inside the building  
- Default materials  
- Restrictions (installer skill, access rules, etc.)

### ✔ Splitter management  
The most important component:

- Splitter type (1:8, 1:12, 1:32)  
- Total ports  
- Available ports  
- Used ports  
- Reserved ports  
- Standby port rules  
- Port history  
- Per-port assignment to job (OrderId)

### ✔ Automatic material suggestion  
BuildingType → DefaultMaterialSet  
Used by:

- Inventory Module  
- Orders module  
- Scheduler module  

---

# 2. TECHNICAL SPECIFICATION

---

# 2.1 Building Entity Structure

Every building entry must contain:

### Identity
- **BuildingId**  
- **BuildingName**  
- **ShortName** (e.g., ROYCE, RADIA, WS1)  
- **Address**  

### Building TYPE (VERY IMPORTANT)
Enum:



Prelaid
Non-Prelaid
SDU
RDF Pole
FTTR
FTTC
Commercial High-Rise
Landed Home
Other


### Contacts
- BuildingManagerName  
- PhoneNumber  
- Email  
- Office Hours  
- Emergency Contact  

### Integration
- DefaultSplitterId (optional)  
- Partner rules (TIME/CELCOM restrictions)  

### Default Materials
Linked table:

- MaterialId  
- DefaultQuantity  
- ApplicableFor (Activation / Assurance / both)  

### Access Rules
- Requires BM appointment (Yes/No)  
- Requires Building Pass (Yes/No)  
- Allows Only Senior SI (Yes/No)

---

# 2.2 Splitter Entity Structure

Each splitter belongs to one building.

### Identity
- **SplitterId**  
- **BuildingId**  
- **SplitterCode** (MDF1_OLT_...)  
- **Type** (1:8 / 1:12 / 1:32)  
- **Location** (MDF Room / Riser / Floor)

### Capacity
- TotalPorts (8 / 12 / 32)  
- StandbyPort =  
  - Port 8 (for 1:8)  
  - Port 12 (for 1:12)  
  - Port 32 (for 1:32)  

### Port Map Table
List of ports:

| PortNumber | Status | OrderId | UsedBy | DateUsed |
|-------------|---------|-----------|---------|------------|
| 1 | Free / Used / Reserved / StandbyUsed | FK | SI Name | Timestamp |

Status options:



Free
Used
Reserved
StandbyUsed
Faulty


---

# 2.3 Standby Port Rule (CRITICAL)

For 1:32 splitters:

- Port 32 = Standby  
- Cannot be used *without partner approval*  
- Approval must include:
  - Attachment (photo/screenshot/email)  
  - Remarks  
  - Admin user who approved  
  - Timestamp  

If standby port used:

- Port status → StandbyUsed  
- Order splitter assignment must store:
  - IsStandbyUsage = TRUE  
  - PartnerApprovalAttachment  

System blocks OrderCompleted → DocketsReceived unless approval present.

---

# 2.4 Port Allocation Logic

When job reaches **DocketsReceived**, the system must:

1. Show building splitters  
2. Show available ports  
3. Allow selecting:



SplitterId
PortNumber


### Validation:
- Port must be Free  
- Standby port must have PartnerApproval  
- Port must not be “Faulty”  
- SI must confirm port used  

### After selecting:
- Mark port as Used  
- Link OrderId → Port  
- Add entry to SplitterPortHistory  

---

# 2.5 Splitter Port History

Log every activity:

- Port allocated  
- Port freed (rare)  
- Standby usage  
- Reassignment (if any)  
- Faulty marking  

Fields:

- SplitterId  
- PortNumber  
- OldStatus  
- NewStatus  
- OrderId  
- SI Name  
- Timestamp  
- Remarks  

---

# 2.6 Building-Type Material Templates

Table: **BuildingTypeMaterialDefaults**

Example entries:

### Prelaid:

- Patchcord 6m ×1  
- Patchcord 10m ×1  
- ONU ×1  

### Non-Prelaid:

- 80m 2-core fibre cable  
- 1× UPC connector  
- 1× APC connector  
- ONU ×1  

### SDU:

- 80m RDF cable  
- 1× UPC connector  
- 1× APC connector  

### RDF Pole:

- RDF pole cable  
- Connectors  
- Outdoor mount components  

These are used by:

- Inventory module  
- Orders module  
- Scheduler module  

---

# 2.7 Building-Level Restrictions

Some buildings require special rules:

| Rule | Example |
|-------|-----------|
| Senior SI only | Desa ParkCity |
| Restricted hours | Office blocks |
| BM approval needed | All Klang Valley high-rise |
| Special access | Keycard required |
| Outdoor relocation allowed/not | Varies by building |

These rules must be stored and visible in Order Card UI.

---

# 2.8 Detection of Building From Address

Email parser normalises address:

- Extract building name  
- Match against building database  
- If multiple matches → ask admin  
- If unknown → mark building as “UNREGISTERED”  

Admin can register building on the Orders page.

---

# 2.9 Integration Points

### Orders Module
- Links building to every order  
- Stores splitter info

### Scheduler
- Shows building type  
- Suggests materials  
- Blocks assignment if restricted SI

### Inventory
- Auto-suggest material plan  
- Validate materials against building type

### Splitter History
- Used for SLA/usage metrics  

---

# 3. STORYBOOK (Narrative Understanding)

---

## 3.1 Story: Email parser identifies building

Email says:

> ROYCE RESIDENCE, Level 17 Unit 7 Jalan Yap Kwan Seng

Parser normalises:



Building = ROYCE RESIDENCE
BuildingShort = ROYCE
BuildingType = Prelaid


Materials auto-suggest:

- Patchcord 6m  
- Patchcord 10m  
- ONU  

---

## 3.2 Story: Splitter selection during DocketsReceived

Admin selects:

- Splitter: MDF1 Riser 11  
- Port 12  

System:

- Checks: PortFree  
- Marks port as Used  
- Links Order → Port  
- Updates SplitterHistory  

---

## 3.3 Story: Standby port usage

SI uses:

- Splitter 1:32  
- Port 32  

System:

- Requires partner approval screenshot  
- Stores approval file  
- Marks port as StandbyUsed  
- Stores IsStandbyUsage = TRUE  

Order cannot move to DocketsReceived without approval.

---

## 3.4 Story: Building restrictions block SI assignment

Scheduler:

- Job at “Desa ParkCity – WS1”  
- This building requires senior SI  

If admin tries to assign junior SI:

→ System blocks  
→ Shows rule  
→ Suggests senior SI list  

---

## 3.5 Story: Unknown building detected

Order arrives from:

> “Menara XYZ Jalan 7”

Not in database.

System:

- Marks building as Unregistered  
- Allows admin to add  
- Automatically creates mapping  
- Next time auto-detects  

---

## 3.6 Story: Building dashboard

Admin sees:

- Splitter map per building  
- Free ports  
- Standby usage count  
- Jobs per building  
- Consumption rate  

---

# END OF BUILDINGS & SPLITTERS MODULE
