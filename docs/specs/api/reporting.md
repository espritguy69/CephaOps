# Reporting API

Provides dashboards and reports for:

- Daily operations
- SI performance
- Partner performance
- Splitter usage
- Inventory consumption
- Invoice aging

All endpoints are **multi-company aware** and may support `companyId` / `siteId`.

---

## 1. Daily Dashboard Summary

**GET** `/api/reports/dashboard`

### Query

- `date` (optional)
- `companyId` (optional; default from context)
- `siteId` (optional; branch-level view)

Returns high-level counts:

- Orders by status
- Dockets pending review
- Invoices pending upload/payment
- Headline KPI metrics

---

## 2. Installer KPI Report

**GET** `/api/reports/installers-kpi`

### Query

- `companyId`
- `fromDate`
- `toDate`
- Optional: `installerId`

Shows:

- On-time %
- Late %
- Jobs per installer
- Repeat/redo counts

---

## 3. Partner Performance Report

**GET** `/api/reports/partners`

### Query

- `companyId`
- `fromDate`
- `toDate`

Shows:

- Volume per partner
- Rejection rates
- Average approval time

---

## 4. Splitter Usage Report

**GET** `/api/reports/splitters`

### Query

- `companyId`
- Optional building/splitter filters

Shows:

- Port utilization
- Standby usage
- Near-full capacity alerts

---

## 5. Inventory Consumption Report

**GET** `/api/reports/inventory`

