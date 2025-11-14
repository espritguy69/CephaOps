Here’s a clean, Cursor-friendly API blueprint you can save as API_SPEC.md.
It’s documentation only (no code), covering all core modules/endpoints.

# CSIOS API SPECIFICATION  
Cephas Service Installer Operations System  
(High-Level REST API Blueprint – Documentation Only)

Base URL (example):
- Production: `https://api.csios.cephas.com.my`
- Staging: `https://staging-api.csios.cephas.com.my`
- Local: `http://localhost:5000`

---

## 0. AUTHENTICATION

> NOTE: Exact auth mechanism to be decided (e.g., JWT, API Keys, Azure AD).  
> Below assumes Bearer JWT tokens.

### Request Header

```http
Authorization: Bearer <token>
Content-Type: application/json

1. ORDERS MODULE

Represents all jobs: Activation, Modification, Assurance, SDU, RDF POLE, etc.

1.1 List Orders

GET /api/orders

Query Params (optional):

serviceId – filter by Service ID (e.g., TBBN123456G)

partnerId – filter by Partner

status – filter by current status

orderType – Activation / Modification / Assurance / SDU / RDFPole

fromDate / toDate – appointment date range

installerId – assigned SI

page / pageSize – pagination

Response (200):

{
  "items": [
    {
      "orderId": 1001,
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

1.2 Get Order By ID

GET /api/orders/{orderId}

Returns full details + history.

1.3 Create Order (Manual)

POST /api/orders

Used for manual entry or non-email orders.

Body (Example):

{
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


Validation:

TIME Activation/Modification Service ID must match pattern: TBBNXXXXXXXXX

TIME Assurance must include ticketId (TTKTxxxx) and awoId (AWOxxxxx) in separate fields if provided.

1.4 Update Order (Non-status fields)

PUT /api/orders/{orderId}

Fields that can be updated: customer, building, appointment, remarks, etc.
(Status changes use a specialized endpoint – see Status Module.)

1.5 Delete Order

DELETE /api/orders/{orderId}

Soft-delete (flag only). Order history retained.

1.6 Attach Files to Order (Optional)

POST /api/orders/{orderId}/attachments

For storing related documents (e.g., email PDF, Excel original).

2. EMAIL PARSER MODULE

Backend service that ingests emails & attachments and converts them to Orders.

2.1 Trigger Manual Parse of Email

POST /api/email/parse

For debugging / manual ingestion. Main production flow may be background job.

Body:

{
  "rawEmailSource": "MIME or text content here",
  "overridePartnerId": 1
}


Response:

{
  "orderId": 1001,
  "created": true,
  "warnings": ["Old address field was empty"]
}

2.2 Upload Excel for Parsing

POST /api/email/parse-excel

Multipart form (file upload).

Result → creates order(s) for Activation / Modification.

3. STATUS & KPI MODULE

Manages status transitions, KPIs, portal timestamps, and splitter enforcement link.

3.1 Change Order Status

POST /api/orders/{orderId}/status

Body Example:

{
  "newStatus": "OnTheWay",
  "timestampPortal": "2025-11-14T09:55:00Z",
  "actorType": "Installer",
  "actorId": 5,
  "remarks": "Leaving for site"
}


Allowed status values:

Pending

Assigned

OnTheWay

MetCustomer

OrderCompleted

DocketsReceived

DocketsUploaded

ReadyForInvoice

Invoiced

Completed

Rescheduled

Cancelled

Validation:

Enforce allowed transitions (no skipping).

For OnTheWay, MetCustomer, OrderCompleted → timestampPortal required.

For DocketsReceived → Splitter + Port must be set.

For Completed → payment data required (through Invoice flow).

Response (Example):

{
  "orderId": 1001,
  "oldStatus": "Assigned",
  "newStatus": "OnTheWay",
  "kpiUpdated": false
}

3.2 Get Status History

GET /api/orders/{orderId}/status-history

Returns chronological list of status transitions, timestamps, actors, remarks.

4. SCHEDULER MODULE

Calendar-based assignment and visualization.

4.1 Get Day Schedule

GET /api/scheduler

Query:

date (required: YYYY-MM-DD)

partnerId (optional)

installerId (optional)

Response Example:

{
  "date": "2025-11-14",
  "unassignedOrders": [
    {
      "orderId": 1001,
      "serviceId": "TBBNB062587G",
      "customerName": "COBNB SDN BHD",
      "appointmentDateTime": "2025-11-14T10:00:00Z",
      "buildingShortName": "ROYCE",
      "partnerName": "TIME"
    }
  ],
  "installers": [
    {
      "installerId": 5,
      "installerName": "Klavin",
      "assignments": [
        {
          "orderId": 1002,
          "serviceId": "TBBNA261593G",
          "timeStart": "2025-11-14T13:00:00Z",
          "timeEnd": "2025-11-14T16:00:00Z",
          "status": "Assigned",
          "buildingShortName": "WES1",
          "partnerName": "TIME"
        }
      ]
    }
  ]
}

4.2 Assign Order to Installer

POST /api/scheduler/assign

Body:

{
  "orderId": 1001,
  "installerId": 5,
  "scheduledStart": "2025-11-14T10:00:00Z",
  "scheduledEnd": "2025-11-14T11:00:00Z",
  "remarks": "Prelaid, expected quick job"
}


Also sets status → Assigned.

4.3 Reschedule Order

POST /api/scheduler/reschedule

{
  "orderId": 1001,
  "newStart": "2025-11-15T11:00:00Z",
  "newEnd": "2025-11-15T12:00:00Z",
  "reason": "Customer requested new time"
}


Optionally sets status → Rescheduled.

5. INVENTORY MODULE

Tracks materials, serialized CPE, and SI inventories.

5.1 Get Warehouse Stock

GET /api/inventory/warehouse

Optional filters: materialId, partnerId.

5.2 Get Installer Inventory

GET /api/inventory/installers/{installerId}

5.3 Record Material Transaction

POST /api/inventory/transactions

For STOCK_IN, STOCK_OUT, RETURN_IN, CONSUME, ASSURANCE_SWAP, etc.

{
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

5.4 Get Material Movement History

GET /api/inventory/transactions

Query params: materialId, installerId, orderId, date range.

6. BUILDINGS & SPLITTERS MODULE
6.1 List Buildings

GET /api/buildings

Optional filters: name, type, partnerId.

6.2 Get Building Details

GET /api/buildings/{buildingId}

Includes:

Contact

Type

Default materials

Splitter list

6.3 List Splitters for Building

GET /api/buildings/{buildingId}/splitters

6.4 Get Splitter Ports

GET /api/splitters/{splitterId}/ports

Response Example:

{
  "splitterId": 20,
  "type": "1:32",
  "ports": [
    { "portNumber": 1, "status": "Used", "orderId": 1002 },
    { "portNumber": 2, "status": "Free", "orderId": null },
    { "portNumber": 32, "status": "Standby", "orderId": null }
  ]
}

6.5 Assign Port to Order

POST /api/orders/{orderId}/splitter

{
  "splitterId": 20,
  "portNumber": 8,
  "isStandbyUsed": false,
  "partnerApprovalFileId": null
}


Validation:

Port must be Free.

If port is standby (e.g., 32 on 1:32) → partnerApprovalFileId required.

7. PARTNERS & SERVICE INSTALLERS MODULE
7.1 List Partners

GET /api/partners

7.2 Get Partner

GET /api/partners/{partnerId}

Includes payment terms, prefixes, special rules.

7.3 List Installers

GET /api/installers

Filters: active, skill, region.

7.4 Get Installer Details

GET /api/installers/{installerId}

Includes skills, KPI overrides, rates.

7.5 Create/Update Installer

POST /api/installers
PUT /api/installers/{installerId}

8. DOCKETS MODULE
8.1 Upload Docket for Order

POST /api/orders/{orderId}/dockets

Multipart form with file (jpg/pdf/png) + metadata.

8.2 Get Docket Info

GET /api/orders/{orderId}/dockets

Returns docket metadata, KPI for docket, validation flags.

8.3 Mark Docket Reviewed

POST /api/orders/{orderId}/dockets/review

{
  "isValid": true,
  "requiresCorrection": false,
  "remarks": "All good, serial matches inventory"
}

8.4 Mark Docket Uploaded to Partner

POST /api/orders/{orderId}/dockets/mark-uploaded

Sets DocketUploadedAt and moves status → DocketsUploaded.

8.5 Mark Docket Rejected by Partner

POST /api/orders/{orderId}/dockets/mark-rejected

{
  "rejectionReason": "Serial mismatch",
  "remarks": "TIME portal screenshot saved under attachments"
}

9. INVOICING MODULE
9.1 Generate Invoice for Order

POST /api/invoices/generate-for-order

{
  "orderId": 1001
}


Returns invoiceId, amount.

9.2 List Invoices

GET /api/invoices

Filters: partnerId, status, fromDate, toDate.

9.3 Get Invoice

GET /api/invoices/{invoiceId}

9.4 Mark Invoice Uploaded to Partner

POST /api/invoices/{invoiceId}/mark-uploaded

{
  "uploadedAt": "2025-11-20T09:30:00Z"
}


Sets DueDate = uploadedAt + partnerPaymentTermsDays (e.g., +45).

9.5 Mark Invoice Paid

POST /api/invoices/{invoiceId}/mark-paid

{
  "paymentDate": "2026-01-05T00:00:00Z",
  "paymentRef": "BANK-FT-12345",
  "notes": "Matched to bank statement"
}


Order status → Completed.

9.6 Mark Invoice Rejected

POST /api/invoices/{invoiceId}/mark-rejected

{
  "rejectionReason": "Incorrect rate",
  "remarks": "Need to regenerate with corrected amount"
}

10. REPORTING MODULE
10.1 Daily Dashboard Summary

GET /api/reports/dashboard

Optional: date

Returns counts of:

Orders by status

Dockets pending

Invoices pending

KPI summary

10.2 SI KPI Report

GET /api/reports/installers-kpi

Query: fromDate, toDate, installerId?

10.3 Partner Performance Report

GET /api/reports/partners

10.4 Splitter Usage Report

GET /api/reports/splitters

10.5 Inventory Consumption Report

GET /api/reports/inventory

10.6 Invoice Aging Report

GET /api/reports/invoices-aging

11. SETTINGS MODULE

(Used for CRUD of master data; some can be merged with modules above.)

11.1 KPI Settings

GET /api/settings/kpi

PUT /api/settings/kpi

Body example:

{
  "prelaidMinutes": 60,
  "nonPrelaidMinutes": 120,
  "fttrMinutes": 180,
  "sduMinutes": 180,
  "docketKpiMinutes": 30
}

11.2 Status Flow Configuration (Optional / Advanced)

GET /api/settings/status-flow
PUT /api/settings/status-flow

11.3 Default Material Templates

GET /api/settings/material-templates
POST /api/settings/material-templates
PUT /api/settings/material-templates/{id}

END OF API SPEC
