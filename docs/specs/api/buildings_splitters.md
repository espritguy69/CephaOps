# Buildings & Splitters API

Manages:

- Building master data
- ODF / splitter structures
- Ports and their usage

---

## 1. List Buildings

**GET** `/api/buildings`

### Optional Filters

- `companyId`
- `partnerId`
- `name`
- `type` – e.g., High-rise, Landed, Commercial

---

## 2. Get Building Details

**GET** `/api/buildings/{buildingId}`

Includes:

- Basic info (name, type, address)
- Contact person
- Default materials (if any)
- Splitters present

---

## 3. List Splitters for a Building

**GET** `/api/buildings/{buildingId}/splitters`

Returns:

- Splitter IDs
- Types (1:8, 1:16, 1:32, etc.)
- Basic capacity/usage info

---

## 4. Get Splitter Ports

**GET** `/api/splitters/{splitterId}/ports`

### Response Example

```json
{
  "splitterId": 20,
  "type": "1:32",
  "ports": [
    { "portNumber": 1, "status": "Used", "orderId": 1002 },
    { "portNumber": 2, "status": "Free", "orderId": null },
    { "portNumber": 32, "status": "Standby", "orderId": null }
  ]
}
```

---

## 5. Assign Port to Order

**POST** `/api/orders/{orderId}/splitter`

### Body Example

```json
{
  "companyId": "CEPHAS",
  "siteId": "ROYCE-BUILDING",
  "splitterId": 20,
  "portNumber": 8,
  "isStandbyUsed": false,
  "partnerApprovalFileId": null
}
```

### Validation

- Port must be `Free` before assignment.
- If port is a designated **standby port** (e.g., port `32` on a 1:32 splitter):
  - `partnerApprovalFileId` is required.
- Status flow may enforce:
  - No `DocketsReceived` until port is assigned.

---

