# Inventory API

Tracks:

- Warehouse stock
- Installer-held inventory
- Serialized CPE (routers, ONUs, etc.)
- Material movements and returns

---

## 1. Get Warehouse Stock

**GET** `/api/inventory/warehouse`

### Optional Filters

- `companyId`
- `siteId` – Warehouse or storage location
- `materialId`
- `partnerId`

---

## 2. Get Installer Inventory

**GET** `/api/inventory/installers/{installerId}`

### Optional

- `companyId` – For group users or installers shared across companies.

Returns the list of materials and quantities currently assigned to the installer.

---

## 3. Record Material Transaction

**POST** `/api/inventory/transactions`

Used for:

- `STOCK_IN`
- `STOCK_OUT`
- `RETURN_IN`
- `CONSUME`
- `ASSURANCE_SWAP`
- Other inventory movements

### Body Example

```json
{
  "companyId": "CEPHAS",
  "siteId": "SUBANG-WH",
  "transactionType": "STOCK_OUT",
  "materialId": 101,
  "fromLocation": "Warehouse",
  "toLocation": "Installer",
  "installerId": 5,
  "quantity": 3,
  "serials": ["HWE123", "HWE124", "HWE125"],
  "referenceOrderId": null,
  "referenceNote": "Issued to Klavin"
}
```

---

## 4. Get Material Movement History

**GET** `/api/inventory/transactions`

### Query Params

- `companyId`
- `siteId`
- `materialId`
- `installerId`
- `orderId`
- Date range: `fromDate`, `toDate`

Used for audit trails, discrepancy checks, and reporting.

---


