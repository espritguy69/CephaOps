# Email Parser API

Backend service that ingests email content or Excel files and converts them into **Orders**.

Most production flows will be automated (background jobs).  
These endpoints are primarily for debugging, manual ingestion, or re-processing.

---

## 1. Manual Email Parse

**POST** `/api/email/parse`

### Body

```json
{
  "rawEmailSource": "MIME or text content here",
  "overridePartnerId": 1,
  "companyId": "CEPHAS"      // Optional; defaults to company from context
}
```

### Behaviour

- Extracts partner, order type, building, and job details from raw email.
- If `companyId` is not supplied:
  - Uses the token / header company context.
- Uses Settings Module:
  - Parser rules per partner
  - Validation patterns

### Response Example

```json
{
  "orderId": 1001,
  "created": true,
  "warnings": [
    "Old address field was empty"
  ]
}
```

---

## 2. Upload Excel for Parsing

**POST** `/api/email/parse-excel`

Multipart form upload.  
Used for partner-provided WOs in Excel form.

### Request

- `file` – Excel file (required).
- Optional additional fields:
  - `partnerId`
  - `companyId` (if not inferred)

### Behaviour

- Creates one or more orders.
- Each order is associated with a `companyId`, `partnerId`, and appropriate job type.

### Response

- Typically returns a list of created order IDs and warnings/errors encountered.

---
