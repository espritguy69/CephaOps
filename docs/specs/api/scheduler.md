# Scheduler API

Calendar-based view and assignment of orders to Service Installers (SIs).

---

## 1. Get Day Schedule

**GET** `/api/scheduler`

### Query Params

- `date` – Required (`YYYY-MM-DD`).
- `companyId` – Optional; for group users to specify company.
- `siteId` – Optional; filter schedule by site/region.
- `partnerId` – Optional.
- `installerId` – Optional; see schedule for a specific SI.

### Response Example

```json
{
  "date": "2025-11-14",
  "unassignedOrders": [
    {
      "orderId": 1001,
      "companyId": "CEPHAS",
      "siteId": "ROYCE-BUILDING",
      "serviceId": "TBBNB062587G",
      "customerName": "COBNB SDN BHD",
      "appointmentDateTime": "2025-11-14T10:00:00Z",
      "buildingShortName": "ROYCE",
      "partnerName": "TIME"
    }
  ],
  "installers": [
    {
      "installerId": 5,
      "installerName": "Klavin",
      "assignments": [
        {
          "orderId": 1002,
          "companyId": "CEPHAS",
          "siteId": "WES1",
          "serviceId": "TBBNA261593G",
          "timeStart": "2025-11-14T13:00:00Z",
          "timeEnd": "2025-11-14T16:00:00Z",
          "status": "Assigned",
          "buildingShortName": "WES1",
          "partnerName": "TIME"
        }
      ]
    }
  ]
}
```

---

## 2. Assign Order to Installer

**POST** `/api/scheduler/assign`

### Body Example

```json
{
  "orderId": 1001,
  "companyId": "CEPHAS",
  "siteId": "ROYCE-BUILDING",
  "installerId": 5,
  "scheduledStart": "2025-11-14T10:00:00Z",
  "scheduledEnd": "2025-11-14T11:00:00Z",
  "remarks": "Prelaid, expected quick job"
}
```

- Also sets status → `Assigned` (through Status Module).

---

## 3. Reschedule Order

**POST** `/api/scheduler/reschedule`

### Body Example

```json
{
  "orderId": 1001,
  "companyId": "CEPHAS",
  "siteId": "ROYCE-BUILDING",
  "newStart": "2025-11-15T11:00:00Z",
  "newEnd": "2025-11-15T12:00:00Z",
  "reason": "Customer requested new time"
}
```

- Optionally sets status → `Rescheduled`.

---


