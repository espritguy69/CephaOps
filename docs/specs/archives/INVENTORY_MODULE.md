📘 INVENTORY_MODULE.md
(Combined: Setup + Technical Specification + Storybook)

Inventory is a mission-critical module for Cephas because you are:

Receiving stock from TIME, Celcom, Digi, UMobile

Passing stock to installers

Consuming stock at customer site

Returning unused stock

Handling Assurance cases (swap ONU, recover old ONU)

Managing serial & non-serial items

Enforcing building-type default materials

This module ensures complete material accountability.

# INVENTORY MODULE  
### (Setup + Technical Specification + Storybook)

---

# 1. SETUP OVERVIEW

The Inventory Module provides:

### ✔ Material Database  
- Serial-based and non-serial-based materials  
- Categories (ONU, Router, Patchcord, Fibre Cable, RJ45, RDF Cable etc.)  
- Material type configuration per building type  
- Unit of Measure  
- Active/Inactive flag  

### ✔ Warehouse Stock Management  
- Stock IN from Partner  
- Stock OUT to Installer  
- Returning stock from Installer to Warehouse  
- Faulty/DOA flow  
- Assurance recovery flow (old ONU returned, new ONU given)

### ✔ Order Material Flow  
For each job:



Warehouse → Installer → Customer
or
Warehouse → Installer → Return → Warehouse
or
Warehouse → Installer → Replace (Assurance)


### ✔ Auto material suggestion  
- Based on Building Type → Default Materials  
- FTTH, FTTO, FTTR, SDU, RDF Pole  
- Amount of cable default, connectors, patchcord length etc.

### ✔ Material Deduction  
- On OrderCompleted → auto deduct from installer inventory  
- On failed job → return stock to warehouse  

### ✔ Material History  
Every movement is logged permanently.

---

# 2. TECHNICAL SPECIFICATION

---

# 2.1 Material Entity Structure

Each material requires:

- **MaterialId**  
- **Name**  
- **Category** (ONU, Router, Cable, Patchcord, Connector)  
- **Partner** (optional: TIME / Celcom / Digi / UMobile / Generic)  
- **SerialRequired** TRUE/FALSE  
- **UnitOfMeasure**  
- **SKU** / code  
- **Description**  
- **Active** flag  

### Serial-required materials:
- ONU  
- Routers  
- DECT phones  
- WiFi Mesh nodes  

### Non-serial materials:
- Patchcords  
- Cable coils  
- Trunking  
- RJ45 connectors  
- Fibre connectors  

---

# 2.2 Warehouse Stock Table

Tracks total stock at Cephas warehouse:

| Field | Description |
|-------|-------------|
| MaterialId | FK |
| Quantity | Total non-serial stock |
| SerialList | JSON list of serials |
| Location | Rack/shelf |
| PartnerOwnership | TIME/CELCOM/DIGI |

---

# 2.3 Installer Inventory Table

Each installer has a mini-inventory:

| Field | Description |
|-------|-------------|
| InstallerId | FK |
| MaterialId | FK |
| Quantity | Int |
| SerialList | JSON |

This ensures you can see:

- What each installer is holding  
- What they consumed on each job  

---

# 2.4 Material Movement Types

Material transaction entries:



STOCK_IN – partner gives material to warehouse
STOCK_OUT – warehouse gives material to installer
RETURN_IN – installer returns unused items
CONSUME – installer uses item at customer site
FAULTY_RETURN – faulty ONU returned by customer
ASSURANCE_SWAP – assurance case swapping router/ONU
ADJUSTMENT – admin adjustment for recount


Each has:

- Timestamp  
- Actor (Admin/SI)  
- Document reference (OrderId, DeliveryNote, TTKT etc.)  
- Quantity change (+/-)  

---

# 2.5 Building-Type Default Material Mapping

BuildingType (from Buildings module):

| Building Type | Default Material |
|----------------|------------------|
| Prelaid | Patchcord 6m + Patchcord 10m |
| Non-Prelaid | 80m 2-core cable + 1×UPC + 1×APC |
| SDU | 80m RDF cable + 1×UPC + 1×APC |
| RDF Pole | 80m RDF cable + connectors |
| FTTR | Fibre cable + Mesh nodes (if required) |
| FTTC | RJ45 + OFC termination items |

When an order is created:

- System checks BuildingType
- Auto-populates **OrderMaterialPlan**  
- Scheduler shows this in the Order Card

---

# 2.6 Order Material Plan

When a job is scheduled, system creates:

- ExpectedMaterials  
- ExpectedQuantity  
- SerialRequired  
- PulledFromInstaller (future dropdown)  
- ActualMaterialsUsed (populated when SI completes job)

Example:



[
{ "Material": "ONU HG8145X6", "SerialRequired": true },
{ "Material": "Patchcord 10m", "Qty": 1 },
{ "Material": "Patchcord 6m", "Qty": 1 }
]


---

# 2.7 Material Deduction Logic (Automatic)

When status = **OrderCompleted**:

- Installer’s inventory → deduct actual used materials  
- If serial used → must be scanned OR typed  
- If surplus → treated as return process  

### If job failed:

Status = Rescheduled or Cancelled:

- Installer MUST return:
  - ONU  
  - Router  
  - Cable if cut  
  - Patchcord (unused only)  

---

# 2.8 Serial Rules

Serial materials MUST follow strict rules:

### When giving to installer:
- Must record serials  
- Cannot give duplicates  
- Must match brand/model for job type

### When consuming:
- Must scan or type serial  
- Must match material issued to installer  
- For Assurance:  
  - New ONU: new serial  
  - Old ONU: must be returned  

### When returning:
- Must scan serial  
- Must be marked as Returned  

---

# 2.9 Faulty / Warranty Rules

For Assurance jobs:

### If ONU is faulty:
- Old ONU MUST be returned  
- New ONU serial is issued  
- Return entry created with type=ASSURANCE_SWAP  

### If router (HG8145V5) still under warranty:
- Mark as WARRANTY  
- Tag TTKT in movement  
- Revenue impact calculation (future)  

---

# 2.10 Inventory Audit Trail

Every action must create a record.

Audit entry includes:

- TransactionType  
- Installer  
- OrderId  
- FromLocation  
- ToLocation  
- MaterialId  
- Qty  
- Serial (if applicable)  
- Timestamp  
- PerformedBy  
- ReferenceNumber (DN, TTKT, AWO, etc.)

---

# 3. STORYBOOK (Narrative Understanding)

---

## 3.1 Story: TIME delivers material to warehouse

TIME gives:

- 50× ONU HG8145X6  
- 100× Patchcord 10m  
- 50× Patchcord 6m  

Warehouse admin receives:

- Creates **STOCK_IN** entry  
- Updates warehouse quantities  
- Serial list updated  

System now knows full stock levels.

---

## 3.2 Story: Installer takes materials

Installer Klavin takes:

- 3× ONU HG8145X6  
- 10× Patchcords 10m  
- 5× Patchcords 6m  

Admin:

- STICK OUT entry  
- Klavin now has these in his SI inventory  

---

## 3.3 Story: Activation Order

Order created for:

- TBBNB062587G  
- BuildingType = Prelaid

System auto-suggests materials:

- Patchcord 6m  
- Patchcord 10m  
- ONU HG8145X6 (from Installer’s stock)

---

## 3.4 Story: Job Completed

SI uses:

- 1× ONU  
- 1× Patchcord 10m  
- 1× Patchcord 6m  

System:

- Deducts these from Installer inventory  
- Logs **CONSUME** movement  

---

## 3.5 Story: Job Failed → Customer Not Home

If SI didn’t install:

- ONU NOT used → must return  
- Patchcord NOT used → must return  
- Cable NOT used → must return

Status moves to Rescheduled.

System logs **RETURN_IN**.

---

## 3.6 Story: Assurance Case

TTKT received:

- ONU faulty  
- Technician installs new ONU  
- Old ONU returned to warehouse  
- New ONU serial logged in job  
- Assurance swap entry created  

System logs:

- ASSURANCE_SWAP  
- FAULTY_RETURN  

---

## 3.7 Story: Inventory Dashboard Shows Warnings

Dashboard highlights:

- SI inventory low  
- Warehouse low stock  
- Serial duplication detected  
- Over-use or under-return  
- Missing old ONU return (red alert)  

---

# END OF INVENTORY MODULE
