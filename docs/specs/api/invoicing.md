# Invoicing API

Handles:

- Invoice generation from orders
- Upload tracking to partner portals
- Payment and rejection flows

---

## 1. Generate Invoice for Order

**POST** `/api/invoices/generate-for-order`

### Body Example

```json
{
  "orderId": 1001,
  "companyId": "CEPHAS"
}
```

Returns:

- `invoiceId`
- Computed amount (based on rates, materials, etc.)

---

## 2. List Invoices

**GET** `/api/invoices`

### Filters

- `companyId`
- `partnerId`
- `status`
- `fromDate`
- `toDate`

---

## 3. Get Invoice

**GET** `/api/invoices/{invoiceId}`

Returns:

- Header
- Line items
- Status
- Related orders

---

## 4. Mark Invoice Uploaded to Partner

**POST** `/api/invoices/{invoiceId}/mark-uploaded`

### Body Example

```json
{
  "uploadedAt": "2025-11-20T09:30:00Z"
}
```

Behaviour:

- Sets `UploadedAt`.
- Sets `DueDate` = `uploadedAt + partnerPaymentTermsDays` (e.g. +45 days).

---

## 5. Mark Invoice Paid

**POST** `/api/invoices/{invoiceId}/mark-paid`

### Body Example

```json
{
  "paymentDate": "2026-01-05T00:00:00Z",
  "paymentRef": "BANK-FT-12345",
  "notes": "Matched to bank statement"
}
```

May also:

- Mark linked order as `Completed`.
- Trigger SI payment processing.

---

## 6. Mark Invoice Rejected

**POST** `/api/invoices/{invoiceId}/mark-rejected`

### Body Example

```json
{
  "rejectionReason": "Incorrect rate",
  "remarks": "Need to regenerate with corrected amount"
}
```

Typically:

- Returns invoice to draft/fix state.
- Flags linked orders for re-validation.

---

