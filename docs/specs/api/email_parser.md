# Email Parser API

The Email Parser service ingests **emails or Excel Work Orders (WO)** and converts them into **Orders**.

By default:

- A **Parse Session** is created first.
- A **human review** is required before Orders are created.
- A small **snapshot** (one-page PDF/image) of the WO is generated.
- The original large Excel/email file may be deleted safely after parsing.
- Snapshot files are auto-deleted after **7 days** unless attached to a created Order.

Once a parser is confirmed stable, specific profiles may be configured to:
- **AutoApprove Valid Rows** (skip human approval).

All parsing logic is driven by the **Settings Module**:
- Parser profiles  
- Expected Excel templates  
- Column mappings  
- Partner validation rules  
- Parser mode (`ManualReview` or `AutoApproveValidRows`)  

---

## 1. Core Concept – Parse Session

Every upload creates a **Parse Session** that records:

- `parseSessionId`
- `companyId`, `siteId`, `partnerId`
- `parserProfile`
- `status`:
  - `PendingReview`
  - `Approved`
  - `Rejected`
  - `AutoApproved`
- **Timestamps**:
  - `createdAt` – when file/email arrived  
  - `parsedAt` – when parsing completed  
  - `ordersCreatedAt` – when orders were created  

- Row-by-row preview results  
- Snapshot file(s)  
- Validation errors & warnings  

Orders are **NEVER** created until:
- Human approves a session **OR**  
- Mode = `AutoApproveValidRows` in Settings  

---

## 2. Excel Upload → Create Parse Session

### POST `/api/email/parse-excel`

Creates a parse session from a WO file (Excel).

### Request (multipart/form-data)

| Field          | Required | Description                             |
|----------------|----------|-----------------------------------------|
| `file`         | ✔        | Excel WO file                           |
| `companyId`    | optional | Defaults from token/header              |
| `siteId`       | optional | Warehouse/region context                |
| `partnerId`    | optional | Auto-detected if not provided           |
| `parserProfile`| optional | Explicit parser profile key             |

### Behaviour

1. Store uploaded file **temporarily** (not permanently).  
2. Generate a **small snapshot**:  
   - **ONLY the first worksheet’s first page**  
   - Saved as PDF (preferred) or PNG fallback  
3. Determine parser profile from:
   - Explicit field  
   - Company + Partner mapping  
   - Content heuristics  
4. Parse Excel using Settings:
   - Validate required columns  
   - Perform pattern checks (TBBN, TTKT, AWO)  
   - Map Excel columns → Order Data Map  
5. Create `ParseSession`:
   - `status = PendingReview` (Manual mode)  
   - OR `status = AutoApproved` (Auto mode)  
6. If mode = **AutoApproveValidRows**:
   - Create orders immediately  
   - Attach snapshot to each order  
   - Set `ordersCreatedAt`  
   - Delete original file  
   - Snapshot becomes permanent  

### Response (ManualReview Mode)

```json
{
  "parseSessionId": "PS-2025-000123",
  "companyId": "CEPHAS",
  "siteId": "SUBANG-WH",
  "partnerId": 1,
  "parserProfile": "TIME_ACTIVATION_V1",
  "status": "PendingReview",

  "createdAt": "2025-11-14T08:05:00Z",
  "parsedAt": "2025-11-14T08:06:10Z",
  "ordersCreatedAt": null,

  "snapshotFiles": [
    {
      "fileId": "SNAP-001",
      "fileName": "WO-2025-11-14-page1.pdf",
      "contentType": "application/pdf"
    }
  ],

  "summary": {
    "totalRows": 10,
    "validRows": 8,
    "rowsWithErrors": 2
  },

  "rows": [
    {
      "rowIndex": 2,
      "status": "Valid",
      "orderPreview": { "...": "..." },
      "messages": []
    },
    {
      "rowIndex": 3,
      "status": "Error",
      "orderPreview": { "serviceId": "TBBNINVALID" },
      "messages": [
        "ServiceId does not match TBBN pattern",
        "Appointment date missing"
      ]
    }
  ]
}
```

### Response (Auto-Approve Profiles)

```json
{
  "parseSessionId": "PS-2025-000456",
  "companyId": "CEPHAS",
  "siteId": "SUBANG-WH",
  "partnerId": 1,
  "parserProfile": "TIME_ACTIVATION_V1",
  "status": "AutoApproved",

  "createdAt": "2025-11-20T07:50:00Z",
  "parsedAt": "2025-11-20T07:50:30Z",
  "ordersCreatedAt": "2025-11-20T07:50:31Z",

  "snapshotFiles": [
    {
      "fileId": "SNAP-010",
      "fileName": "WO-2025-11-20-page1.pdf"
    }
  ],

  "summary": {
    "totalRows": 10,
    "validRows": 10,
    "rowsWithErrors": 0
  },

  "createdOrders": [
    { "rowIndex": 2, "orderId": 2001, "serviceId": "TBBNB999001G" },
    { "rowIndex": 3, "orderId": 2002, "serviceId": "TBBNB999002G" }
  ]
}
```

---

## 3. Retrieve / List Parse Sessions

### GET `/api/email/parse-sessions/{parseSessionId}`

Returns full parse session details for review.

### GET `/api/email/parse-sessions`

Query filters:

- `companyId`
- `status`
- `fromCreatedAt` / `toCreatedAt`
- `fromParsedAt` / `toParsedAt`

---

## 4. Approve Parse Session (Manual Mode)

### POST `/api/email/parse-sessions/{parseSessionId}/approve`

#### Body

```json
{
  "companyId": "CEPHAS",
  "createFrom": "ValidRowsOnly",
  "attachSnapshotAs": "OrderAttachment",
  "remarks": "Checked by admin"
}
```

#### Behaviour

1. Validate session is `PendingReview`.  
2. Create orders from selected rows.  
3. Attach snapshot(s) to each created order.  
4. Mark:
   - `status = Approved`
   - `ordersCreatedAt = now()`
5. Delete original large file (optional).  
6. Snapshot becomes **permanent** (not auto-deleted).  

---

## 5. Reject Parse Session

### POST `/api/email/parse-sessions/{parseSessionId}/reject`

#### Body

```json
{
  "companyId": "CEPHAS",
  "reason": "Wrong file format",
  "remarks": "Customer attached billing summary"
}
```

- Sets `status = Rejected`  
- No orders created  
- Snapshots kept for **7 days**, then auto-deleted by `SnapshotCleanupJob`  

---

## 6. Snapshot Generation & Auto-Deletion

### Snapshot Creation Rules

- Snapshot = **first worksheet’s first page only**
- Format:
  - Preferred: PDF  
  - Fallback: PNG  
- Kept small (<200KB ideally)

### Retention / Deletion Rules

| Snapshot Type            | Kept For                | Auto-Delete |
|--------------------------|-------------------------|------------|
| ParseSessionSnapshot     | 7 days from `createdAt` | Yes        |
| OrderAttachmentSnapshot  | Permanent               | No         |

A background job `SnapshotCleanupJob` (see `system/operations.md`) runs daily to delete old `ParseSessionSnapshot` files that are not attached to any order.

---

## 7. Order Data Map (Extracted Fields)

The parser extracts the following fields from Excel/email:

### A. Identification

- `serviceId`
- `orderType`
- `partnerId`
- `parserProfile`

### B. Customer

- `customerName`
- `customerContact`
- `customerEmail` (optional)

### C. Address / Building

- `fullAddress`
- `unitNumber`
- `buildingName`
- `buildingId`

### D. Appointment

- `appointmentDate`
- `appointmentTime`
- `appointmentDateTime`

### E. Technical (Activation/Modification)

- `floorNumber`
- `routerModel`
- `packageName`
- `speedMbps`
- `networkType`
- `isPreLaid`
- `remarks`

### F. Assurance

- `ticketId`
- `awoId`
- `issueCategory`
- `faultDescription`

### G. Multi-Company

- `companyId`
- `siteId`

### H. Parser Metadata

- `parseSessionId`
- `parsedAt`
- `ordersCreatedAt`
- `snapshotFileIds`
- `sourceType = EmailParser`

### I. Validation

- `isValidRow`
- `validationMessages`

---

## 8. Manual Email Parse (Raw MIME)

### POST `/api/email/parse`

Same concept as `parse-excel`, but input is raw email content.  
Still uses Parse Sessions, snapshots, and the same approval/auto-approval logic.

---

## 9. Error Handling

- `400` – Missing file or invalid format  
- `404` – Parse session not found  
- `409` – Approve/reject on non-pending session  
- `422` – Template mismatch, no valid rows  
- `500` – Unexpected errors  

---

## 10. Integration with Orders

Orders created by parser should include:

```json
"source": {
  "type": "EmailParser",
  "parseSessionId": "PS-2025-000123",
  "parsedAt": "2025-11-14T08:06:10Z",
  "ordersCreatedAt": "2025-11-14T08:20:00Z",
  "parserProfile": "TIME_ACTIVATION_V1"
}
```
