# Status & KPI API

Manages:

- Order status transitions
- KPI timestamps
- SLA enforcement
- Linkage with splitter usage (for some statuses)

---

## 1. Change Order Status

**POST** `/api/orders/{orderId}/status`

### Body Example

```json
{
  "newStatus": "OnTheWay",
  "timestampPortal": "2025-11-14T09:55:00Z",
  "actorType": "Installer",  // e.g. Installer, Admin, System
  "actorId": 5,
  "remarks": "Leaving for site",
  "companyId": "CEPHAS"      // Optional; context validation
}
```

### Allowed Status Values (examples)

- `Pending`
- `Assigned`
- `OnTheWay`
- `MetCustomer`
- `OrderCompleted`
- `DocketsReceived`
- `DocketsUploaded`
- `ReadyForInvoice`
- `Invoiced`
- `Completed`
- `Rescheduled`
- `Cancelled`

Status flow definitions are driven by the **Settings Module**.

### Validation Rules (Examples)

- Enforce allowed transitions (no illegal jumps).
- For `OnTheWay`, `MetCustomer`, `OrderCompleted`:
  - `timestampPortal` is required.
- For `DocketsReceived`:
  - Splitter + Port must be set (see `buildings_splitters.md`).
- For `Completed`:
  - Payment-related fields must be populated (via Invoicing module).

### Response Example

```json
{
  "orderId": 1001,
  "oldStatus": "Assigned",
  "newStatus": "OnTheWay",
  "kpiUpdated": false
}
```

---

## 2. Get Status History

**GET** `/api/orders/{orderId}/status-history`

Returns a chronological list of:

- Previous statuses
- Timestamp(s)
- Actor type and ID
- Remarks
- KPI markers (e.g. first `OnTheWay`, first `OrderCompleted`)

### Response Example

```json
[
  {
    "oldStatus": "Pending",
    "newStatus": "Assigned",
    "changedAt": "2025-11-14T08:30:00Z",
    "actorType": "Admin",
    "actorId": 1,
    "remarks": "Assigned to Klavin"
  },
  {
    "oldStatus": "Assigned",
    "newStatus": "OnTheWay",
    "changedAt": "2025-11-14T09:55:00Z",
    "actorType": "Installer",
    "actorId": 5,
    "remarks": "Leaving for site"
  }
]
```

