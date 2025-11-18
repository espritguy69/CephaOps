# Orders API

Represents all jobs: Activation, Modification, Assurance, SDU, RDF/POLE, etc.

Orders can be created from:

- Manual entry (UI / API)
- Email Parser (Excel WO → Parse Session → Approved / AutoApproved)
- Future integrations (e.g. partner APIs)

Every Order:

- Belongs to a single `companyId` (and optional `siteId`)
- Has a `createdAt` timestamp
- May contain a `source` block describing where it came from

---

## 1. Order Data Model (High-Level)

### 1.1 Identity

- `orderId` – internal numeric or GUID
- `companyId` – e.g. `CEPHAS`, `KINGSMAN`
- `siteId` – optional site/warehouse/region
- `orderType` – `Activation`, `Modification`, `Assurance`, `SDU`, `RDFPole`, etc.
- `partnerId` – ISP partner (TIME, CelcomDigi, etc.)
- `status` – current workflow status (see Status & KPI API)
- `createdAt` – when the order record was first created
- `updatedAt` – last update timestamp

### 1.2 Customer Info

- `customerName`
- `customerContact` – phone number (normalized)
- `customerEmail` – optional

### 1.3 Address / Building

- `fullAddress` – normalized full address
- `unitNumber` – unit/tower if parsed
- `buildingName` – human-readable name
- `buildingId` – foreign key to Buildings table (if matched)

### 1.4 Appointment / Scheduling

- `appointmentDate` – date
- `appointmentTime` – time (if separate)
- `appointmentDateTime` – combined datetime
- `preferredInstallerId` – optional

### 1.5 Technical (Activation/Modification)

- `serviceId` – e.g. `TBBNB062587G`
- `floorNumber`
- `routerModel`
- `packageName`
- `speedMbps`
- `networkType` – `FTTR`, `FTTH`, `Copper`, etc.
- `isPreLaid` – boolean
- `remarks` – free-text remarks from WO or operator

### 1.6 Assurance / Trouble Ticket

- `ticketId` – e.g. `TTKT123456`
- `awoId` – e.g. `AWO123456`
- `issueCategory`
- `faultDescription`

### 1.7 Source Metadata (Important for Email Parser)

```json
"source": {
  "type": "EmailParser",           // or "Manual", "APIIntegration"
  "parseSessionId": "PS-2025-000123",
  "parsedAt": "2025-11-14T08:06:10Z",
  "ordersCreatedAt": "2025-11-14T08:20:00Z",
  "parserProfile": "TIME_ACTIVATION_V1"
}
```

This block is mainly filled when orders are created via Email Parser.

---

## 2. List Orders

**GET** `/api/orders`

### Query Params (optional)

- `companyId` – filter by company (else default from context)
- `siteId` – filter by site/region
- `serviceId` – search by Service ID
- `partnerId`
- `status`
- `orderType`
- `installerId`
- `fromDate` / `toDate` – filter by appointment date range
- `createdFrom` / `createdTo` – filter by **order created date** (ISO)
- `sourceType` – e.g. `EmailParser`, `Manual`
- `page` / `pageSize` – pagination

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
      "installerId": 5,
      "createdAt": "2025-11-14T08:20:00Z",
      "source": {
        "type": "EmailParser",
        "parseSessionId": "PS-2025-000123",
        "parsedAt": "2025-11-14T08:06:10Z",
        "ordersCreatedAt": "2025-11-14T08:20:00Z",
        "parserProfile": "TIME_ACTIVATION_V1"
      }
    }
  ],
  "page": 1,
  "pageSize": 20,
  "totalCount": 123
}
```

---

## 3. Get Order by ID

**GET** `/api/orders/{orderId}`

Returns full order details, including source metadata and links to:

- Status history
- Dockets
- Attachments
- Invoices

(Exact fields depend on implementation; core fields listed above.)

---

## 4. Create Order (Manual)

**POST** `/api/orders`

Used for:

- Manual entry in admin UI
- Orders not coming from Email Parser

### Body Example

```json
{
  "companyId": "CEPHAS",
  "siteId": "ROYCE-BUILDING",
  "orderType": "Activation",
  "partnerId": 1,

  "serviceId": "TBBNB062587G",
  "customerName": "COBNB SDN BHD",
  "customerContact": "0123456789",
  "customerEmail": "admin@cobnb.com",

  "fullAddress": "Level 17, Unit 7, ROYCE RESIDENCE, ...",
  "buildingId": 10,
  "unitNumber": "17-7",
  "appointmentDateTime": "2025-11-14T10:00:00Z",

  "routerModel": "TP-Link Archer X1",
  "packageName": "TIME 500Mbps",
  "speedMbps": 500,
  "networkType": "FTTH",
  "isPreLaid": false,

  "remarks": "Manual activation created by admin",

  "source": {
    "type": "Manual"
  }
}
```

---

## 5. Update Order (Non-status Fields)

**PUT** `/api/orders/{orderId}`

Updates non-status fields, e.g.:

- Customer details
- Address (if corrected)
- Appointment Date/Time
- Remarks

> Status changes should go through the **Status & KPI API**.

---

## 6. Delete Order (Soft Delete)

**DELETE** `/api/orders/{orderId}`

- Soft delete (flag only).
- History retained.
- May be restricted to admin roles.

---

## 7. Attach Files to Order

**POST** `/api/orders/{orderId}/attachments`

Multipart/form-data upload.

Use cases:

- WO snapshot (from Email Parser)
- Custom supporting documents
- Partner screenshots

When created via Email Parser:

- Snapshot is initially `ParseSessionSnapshot`
- On approval or auto-approval, snapshot is linked to the Order as a permanent attachment.
