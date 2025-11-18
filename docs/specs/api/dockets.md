# Dockets API

Dockets capture proof of work:

- Site photos
- Router/ONU photos
- Splitter/ODF photos
- Required documents for partner submission

---

## 1. Upload Docket for Order

**POST** `/api/orders/{orderId}/dockets`

Multipart/form-data upload including:

- File(s): `jpg`, `png`, `pdf`, etc.
- Metadata (e.g., `type`, `description`, `takenAt`).

---

## 2. Get Docket Info

**GET** `/api/orders/{orderId}/dockets`

Returns:

- List of docket files
- Metadata (type, size, uploadedAt, uploadedBy)
- Validation flags (e.g., missing required photo types)

---

## 3. Mark Docket Reviewed

**POST** `/api/orders/{orderId}/dockets/review`

### Body Example

```json
{
  "isValid": true,
  "requiresCorrection": false,
  "remarks": "All good, serial matches inventory"
}
```

May be used by QAQC / admin roles before partner upload.

---

## 4. Mark Docket Uploaded to Partner

**POST** `/api/orders/{orderId}/dockets/mark-uploaded`

Sets `DocketUploadedAt` and typically moves order status → `DocketsUploaded`.

---

## 5. Mark Docket Rejected by Partner

**POST** `/api/orders/{orderId}/dockets/mark-rejected`

### Body Example

```json
{
  "rejectionReason": "Serial mismatch",
  "remarks": "TIME portal screenshot saved under attachments"
}
```

Triggers:

- Rework flows
- KPI impact
- Possibly reduced payment

---

