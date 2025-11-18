# Partners & Service Installers API

Manages:

- ISP partners
- Service installer (SI) master data

---

## 1. List Partners

**GET** `/api/partners`

Optional filters:

- `companyId`
- `isActive`

---

## 2. Get Partner Details

**GET** `/api/partners/{partnerId}`

Includes:

- Partner name & codes
- Payment terms
- Work order ID formats
- SLA and special rules

---

## 3. List Installers

**GET** `/api/installers`

Optional filters:

- `companyId`
- `active`
- `skill`
- `region`

---

## 4. Get Installer Details

**GET** `/api/installers/{installerId}`

Includes:

- Basic contact details
- Skills
- Regions/zones
- KPI overrides
- Rate profiles (linked to Settings Module)
- Hiring type (subcon, in-house, etc.)

---

## 5. Create Installer

**POST** `/api/installers`

Creates a new installer record under a company.

---

## 6. Update Installer

**PUT** `/api/installers/{installerId}`

Updates installer info (contact, skills, active flag, etc.).

---


