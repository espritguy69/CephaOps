# API_BLUEPRINT
CephasOps – Phase 1 ISP API Overview

This blueprint lists the main HTTP endpoints for CephasOps Phase 1 (ISP vertical).

- Base URL (local): `http://localhost:5000`
- All secured endpoints require:
  - `Authorization: Bearer <JWT>`
  - `X-Company-Id: <Guid>` header for multi-company scoping

> **Note:** This is a high-level blueprint. Actual DTO shapes live in the code (`CephasOps.Api/DTOs`) and Swagger.

---

## 0. Auth & Health

### 0.1 Health Check

- **GET** `/health`  
  - Returns basic health information (used for probes, CI checks).

### 0.2 Login (JWT)

- **POST** `/auth/login`
  - Request body:
    - `email`: string  
    - `password`: string
  - Response:
    - `accessToken` (JWT)
    - `expiresIn`
    - `user` (id, name, roles, companies)

### 0.3 Current User Profile

- **GET** `/auth/me`
  - Headers: `Authorization: Bearer <JWT>`
  - Returns current user info and list of accessible companies.

---

## 1. Orders & Assurance

Orders represent activation, modification and assurance jobs.

### 1.1 List Orders

- **GET** `/api/orders`
  - Query params:
    - `status` (optional, multiple: `Pending,Assigned,OnTheWay,MetCustomer,OrderCompleted,...`)
    - `partnerId` (optional)
    - `installerId` (optional)
    - `from` (optional, ISO date – appointment from)
    - `to` (optional, ISO date – appointment to)
  - Headers:
    - `Authorization`
    - `X-Company-Id`
  - Returns paged list of orders.

### 1.2 Get Order by Id

- **GET** `/api/orders/{orderId}`

### 1.3 Create Order (Manual)

- **POST** `/api/orders`
  - Used for manual order creation (rare – parser usually creates orders).
  - Body:
    - Basic order fields (serviceId, customer, appointment, building, partner, type).

### 1.4 Assign Service Installer

- **POST** `/api/orders/{orderId}/assign-si`
  - Body:
    - `serviceInstallerId` (Guid)
    - `scheduledAt` (DateTime)
  - Transitions order to `Assigned` if valid and logs status change.

### 1.5 Update Order Status

- **POST** `/api/orders/{orderId}/status`
  - Body:
    - `newStatus` (enum: `OnTheWay,MetCustomer,Blocked,OrderCompleted,Rescheduled,...`)
    - `remarks` (optional)

### 1.6 Complete Order (SI or Admin)

- **POST** `/api/orders/{orderId}/complete`
  - Body:
    - `completedAt`: DateTime
    - `materialsUsed`: list of `{ materialId, quantity, serialNumber?, quantityMeters? }`
    - `notes`: optional
  - Side effects:
    - Changes status to `OrderCompleted`
    - Triggers inventory movements
    - Allows docket and invoice flows.

### 1.7 Link Splitter Port

- **POST** `/api/orders/{orderId}/splitter-port`
  - Body:
    - `splitterId`
    - `portNumber`
    - `approvalAttachmentId` (optional, required if port 32 on 1:32 splitter)

### 1.8 Link Invoice

- **POST** `/api/orders/{orderId}/link-invoice`
  - Body:
    - `invoiceId`

---

## 2. Scheduler

The Scheduler exposes SI schedule views and drag & drop rescheduling.

### 2.1 Get Schedule (Day/Week)

- **GET** `/api/scheduler`
  - Query:
    - `date` (ISO date – start of view)
    - `mode` (`day` | `week`)
    - `partnerId` (optional)
    - `region` (optional)

### 2.2 Reschedule Order (Drag & Drop)

- **POST** `/api/scheduler/orders/{orderId}/reschedule`
  - Body:
    - `serviceInstallerId`
    - `newStart` (DateTime)
    - `newEnd` (DateTime)

---

## 3. Buildings & Splitters

### 3.1 List Buildings

- **GET** `/api/buildings`
  - Query:
    - `type` (optional: `Prelaid,NonPrelaid,SDU,RDFPole`)
    - `search` (optional, name or short name)

### 3.2 Get Building with Splitters

- **GET** `/api/buildings/{buildingId}`

### 3.3 Create / Update Building

- **POST** `/api/buildings`
- **PUT** `/api/buildings/{buildingId}`

### 3.4 List Splitters for Building

- **GET** `/api/buildings/{buildingId}/splitters`

### 3.5 Create / Update Splitter

- **POST** `/api/splitters`
- **PUT** `/api/splitters/{splitterId}`

### 3.6 Mark Port Used / Standby Override

- **POST** `/api/splitters/{splitterId}/ports/{portNumber}/use`
  - Body:
    - `orderId`
    - `approvalAttachmentId` (required if `RequiresApproval = true`)

---

## 4. Inventory & RMA

### 4.1 Stock Overview

- **GET** `/api/inventory/stock`
  - Query:
    - `category` (optional)
    - `locationType` (optional: `Warehouse,Installer,Customer,RMA`)
    - `partnerId` (optional)

### 4.2 Movements

- **GET** `/api/inventory/movements`
  - Query:
    - `from` / `to` (optional date range)
    - `materialId` (optional)
    - `installerId` (optional)

### 4.3 RMA Requests

- **GET** `/api/inventory/rma`
- **GET** `/api/inventory/rma/{rmaRequestId}`

### 4.4 Create RMA Request

- **POST** `/api/inventory/rma`
  - Body:
    - `materialId`
    - `serialNumber`
    - `orderId`
    - `partnerId`
    - `reason`

### 4.5 Update RMA Status

- **POST** `/api/inventory/rma/{rmaRequestId}/status`
  - Body:
    - `status` (`Requested,InTransit,Approved,Rejected,Completed`)
    - `remarks` (optional)

---

## 5. Dockets

### 5.1 Mark Docket Received

- **POST** `/api/orders/{orderId}/dockets/received`
  - Body:
    - `receivedAt` (DateTime)

### 5.2 Upload Docket File

- **POST** `/api/orders/{orderId}/dockets/upload`
  - Multipart/form-data:
    - `file` (PDF or image)
  - Sets status `DocketsUploaded` and records timestamp.

---

## 6. Billing & Invoices

### 6.1 List Invoices

- **GET** `/api/invoices`
  - Query:
    - `status` (`Draft,ReadyForInvoice,Invoiced,Paid,Rejected`)
    - `partnerId` (optional)
    - `from` / `to` (invoice date range)

### 6.2 Get Invoice

- **GET** `/api/invoices/{invoiceId}`

### 6.3 Generate Invoice from Orders

- **POST** `/api/invoices/generate`
  - Body:
    - `partnerId`
    - `orderIds`: list of Guid
    - `invoiceDate`

### 6.4 Submit Invoice to Portal

- **POST** `/api/invoices/{invoiceId}/submit`
  - Body:
    - `portalSubmissionId`
    - `submissionDate`

### 6.5 Mark Invoice Paid

- **POST** `/api/invoices/{invoiceId}/mark-paid`
  - Body:
    - `paidDate`

### 6.6 Mark Invoice Rejected

- **POST** `/api/invoices/{invoiceId}/reject`
  - Body:
    - `reason`

---

## 7. Partners & Installers

### 7.1 Partners

- **GET** `/api/partners`
- **GET** `/api/partners/{partnerId}`
- **POST** `/api/partners`
- **PUT** `/api/partners/{partnerId}`

### 7.2 Service Installers

- **GET** `/api/installers`
  - Query:
    - `type` (InHouse/Subcon)
    - `region` (optional)
- **GET** `/api/installers/{installerId}`
- **POST** `/api/installers`
- **PUT** `/api/installers/{installerId}`

---

## 8. Settings & Parser Rules

### 8.1 Parser Rules

- **GET** `/api/settings/parser-rules`
- **GET** `/api/settings/parser-rules/{parserRuleId}`
- **POST** `/api/settings/parser-rules`
- **PUT** `/api/settings/parser-rules/{parserRuleId}`

### 8.2 Notifications

- **GET** `/api/settings/notifications`
- **POST** `/api/settings/notifications`

### 8.3 SI Rates & Payroll Settings

- **GET** `/api/settings/si-rates`
- **POST** `/api/settings/si-rates`

### 8.4 Cost Centres

- **GET** `/api/settings/cost-centres`
- **POST** `/api/settings/cost-centres`

---

## 9. Reporting & PNL

### 9.1 Operational Reports

- **GET** `/api/reports/orders`
  - Query:
    - `from` / `to`
    - `partnerId` (optional)
    - `installerId` (optional)

### 9.2 Company PNL

- **GET** `/api/pnl`
  - Query:
    - `year`
    - `month`

### 9.3 Group PNL (Director Only)

- **GET** `/api/pnl/group`
  - Requires `Director` role.
  - Uses cross-company view to summarise PNL across all companies.

---

## 10. SI PWA Endpoints

These are the main endpoints used by the Service Installer PWA.

### 10.1 SI Jobs

- **GET** `/api/si/jobs`
  - Query:
    - `scope` (`today,upcoming,completed`)

### 10.2 SI Job Detail

- **GET** `/api/si/jobs/{orderId}`

### 10.3 SI Status Updates

- **POST** `/api/si/jobs/{orderId}/on-the-way`
- **POST** `/api/si/jobs/{orderId}/met-customer`
- **POST** `/api/si/jobs/{orderId}/complete`
  - Body similar to `/api/orders/{orderId}/complete` but from SI context.

### 10.4 SI Materials In Hand

- **GET** `/api/si/materials`

### 10.5 SI Profile & KPI

- **GET** `/api/si/profile`

---

This blueprint is intended to stay in sync with:

- `STORYBOOK.md`
- `PAGES.md`
- `WIREFRAMES.md`
- `PHASE1_DOMAIN_MODELS.md`

Update this file whenever you add or change major endpoints.
