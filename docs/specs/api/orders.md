# Orders API

Represents all jobs: Activation, Modification, Assurance, SDU, RDF/POLE, etc.

---

## 1. List Orders

**GET** `/api/orders`

### Query Params (optional)

- `companyId` – Filter by company (for group users).  
  If omitted, uses context from token / `X-Company-Id`.
- `siteId` – Filter by site/outlet (e.g. building cluster, warehouse).
- `serviceId` – Filter by Service ID (e.g. `TBBN123456G`).
- `partnerId` – Filter by partner.
- `status` – Filter by current status.
- `orderType` – `Activation` / `Modification` / `Assurance` / `SDU` / `RDFPole`, etc.
- `fromDate` / `toDate` – Appointment date range.
- `installerId` – Assigned SI.
- `page` / `pageSize` – Pagination.

### Response 200 Example

```json
{
  "items": [
    {
      "orderId": 1001,
      "companyId": "CEPHAS",
      "siteId": "ROYCE-BUILDING",
      "serviceId": "TBBNB062587G",
      "partnerId": 1,
      "orderType": "Activation",
      "status": "Assigned",
      "customerName": "COBNB SDN BHD",
      "buildingId": 10,
      "appointmentDateTime": "2025-11-14T10:00:00Z",
      "installerId": 5
    }
  ],
  "page": 1,
  "pageSize": 20,
  "totalCount": 123
}
```

---

## 2. Get Order by ID

**GET** `/api/orders/{orderId}`

Returns full order details + history.

Order always belongs to a single `companyId`.  
Access is controlled by the user’s company/site permissions.

---

## 3. Create Order (Manual)

**POST** `/api/orders`

Used for manual entry or non-email orders.

### Body Example

```json
{
  "companyId": "CEPHAS",       // Optional if provided via header/token
  "siteId": "ROYCE-BUILDING",  // Optional, building/site context
  "serviceId": "TBBNB062587G",
  "partnerId": 1,
  "orderType": "Activation",
  "customerName": "COBNB SDN BHD",
  "customerContact": "0123456789",
  "buildingId": 10,
  "fullAddress": "Level 17, Unit 7, ROYCE RESIDENCE, ...",
  "appointmentDateTime": "2025-11-14T10:00:00Z",
  "remarks": "Manual activation created by admin"
}
```

### Validation Notes

- `companyId` must:
  - Match `X-Company-Id` if header is present; otherwise
  - Match effective company in token.
- For TIME Activation/Modification:
  - `serviceId` must match pattern: `TBBNXXXXXXXXX`.
- For TIME Assurance:
  - Include `ticketId` (TTKTxxxx) and `awoId` (AWOxxxxx) in dedicated fields when available.

---

## 4. Update Order (Non-status Fields)

**PUT** `/api/orders/{orderId}`

Updates:

- Customer details
- Building
- Appointment date/time
- Remarks
- Non-critical metadata

> Status changes are done via **Status & KPI API** (`status_kpi.md`).

---

## 5. Delete Order (Soft Delete)

**DELETE** `/api/orders/{orderId}`

- Soft delete (flag only).
- History is retained.
- May be restricted to admin roles.

---

## 6. Attach Files to Order

**POST** `/api/orders/{orderId}/attachments`

- Multipart/form-data.
- Used for:
  - Original email PDFs
  - Excel WOs
  - Screenshots (e.g. portal).

Response typically returns a simple file metadata object with:

- File Id
- Filename
- Content type
- Upload timestamp


