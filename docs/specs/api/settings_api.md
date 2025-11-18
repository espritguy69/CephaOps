# Settings API (Config-Level)

> Full architecture is in `SETTINGS_MODULE.md`.  
> This file describes only the API endpoints used to **view and modify** key settings.

Settings are:

- Scoped by **System / Company / Site**
- Used by:
  - Status & KPI Module
  - Orders Module
  - Inventory
  - Scheduler
  - Dockets
  - Invoicing

---

## 1. KPI Settings

**GET** `/api/settings/kpi`  
**PUT** `/api/settings/kpi`

### Query (GET)

- `companyId` – Optional; if omitted, returns system-level or context-based.
- `siteId` – Optional; returns site overrides if available.

### Body Example (PUT)

```json
{
  "companyId": "CEPHAS",
  "siteId": null,
  "prelaidMinutes": 60,
  "nonPrelaidMinutes": 120,
  "fttrMinutes": 180,
  "sduMinutes": 180,
  "docketKpiMinutes": 30
}
```

Behaviour:

- If `companyId` and `siteId` are provided:
  - Writes a site-level override.
- If only `companyId` is provided:
  - Writes a company-level override.
- If neither is provided:
  - Writes global/system defaults (admin-only).

---

## 2. Status Flow Configuration (Advanced)

**GET** `/api/settings/status-flow`  
**PUT** `/api/settings/status-flow`

Defines:

- Allowed transitions
- Rules for requiring remarks
- Rules for requiring approvals
- Which transitions affect KPI markers

Most tenants will keep defaults; advanced tenants may override per company.

---

## 3. Default Material Templates

**GET** `/api/settings/material-templates`  
**POST** `/api/settings/material-templates`  
**PUT** `/api/settings/material-templates/{id}`

Used to define **default material bundles** per:

- Company
- Partner
- Job type
- Building type (optional)

Example use:


