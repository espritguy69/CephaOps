1️⃣ Updated INVOICING_MODULE.md (full file)

You can paste this as a full replacement.

# INVOICING MODULE  
### (Setup + Technical Specification + Storybook – UPDATED WITH PORTAL SUBMISSION ID)

---

# 1. PURPOSE

The Invoicing Module is responsible for:

- Turning completed & docket-verified orders into invoices  
- Generating invoice documents (PDF)  
- Recording **partner portal submission details** (Submission ID, URL, status)  
- Tracking payment terms (e.g. 45 days for TIME)  
- Tracking invoice status (Draft → Uploaded → Paid / Rejected)  
- Feeding financial data into Reporting & SI payment calculations  

It sits **after**:

- Orders  
- Status & KPI  
- Dockets  

and **before**:

- Reporting  
- Finance workflows  
- SI payment logic (future module)

---

# 2. TECHNICAL SPECIFICATION

---

## 2.1 Invoice Entity Structure

Each invoice is tied to exactly one order.

### Core Fields

```text
Invoice
- InvoiceId              (internal PK)
- OrderId                (FK → Orders)
- PartnerId              (FK → Partners)
- InvoiceNumber          (string, formatted per partner)
- Amount                 (decimal)
- Currency               (e.g. MYR)
- GeneratedAt            (datetime)
- UploadedAt             (datetime, when uploaded to portal)
- DueDate                (datetime, e.g. UploadedAt + PaymentTermsDays)
- PaidAt                 (datetime, when payment received)
- PaymentRef             (string, bank reference / notes)
- Status                 (Draft / Uploaded / Paid / Rejected / Cancelled)

NEW: Partner Portal Submission Tracking

These fields link your invoice to the partner portal submission:

- PortalSubmissionId       (string, REQUIRED when marking as uploaded)
- PortalSubmissionUrl      (string, optional deep-link to portal)
- PortalSubmissionStatus   (string, optional – e.g. Submitted / Processing / Accepted / Rejected)


Rules:

When Status moves to Uploaded, PortalSubmissionId must be provided.

PortalSubmissionUrl is optional but recommended for quick access.

PortalSubmissionStatus is optional text; can be updated later if you ever build sync with partner portal.

2.2 Invoice Lifecycle
States
Draft          – auto-created when ReadyForInvoice
Uploaded       – after admin uploads invoice PDF + captures PortalSubmissionId
Paid           – after finance confirms payment
Rejected       – if partner rejects invoice
Cancelled      – manually cancelled (internal)


Key transitions:

ReadyForInvoice (Order) → Draft (Invoice)

Draft → Uploaded (requires portal submission info)

Uploaded → Paid (requires payment info)

Uploaded → Rejected (requires rejection reason)

Rejected → Uploaded (after correction & resubmission)

2.3 Generating an Invoice

Triggered when an order reaches:

Order.Status = ReadyForInvoice


System can:

Auto-generate invoice on status change, or

Allow admin to click "Generate Invoice".

Required data from Order:

ServiceId

PartnerId

Customer Name

Building

Job Type (Prelaid / Non-Prelaid / SDU / FTTR / FTTC / RDF POLE / Assurance)

Internal rate rules (from Partner + Services settings)

SI rates (if needed for internal reporting)

Invoice Amount

Pulled from partner rate tables:

Rate determined by:
- Partner
- OrderType (Activation / Modification / Assurance / SDU / RDF Pole)
- TaskType if needed


Invoice amount = Partner Rate x (Quantity 1)
(Future: multi-line invoices if needed.)

2.4 Invoice Document (PDF)

The module generates a PDF document that will be:

Downloaded by admin

Manually uploaded to TIME/Celcom/Digi/U Mobile portal

Minimum contents:

Cephas name & address

Partner name

Invoice number & date

Service ID / Ticket ID / AWO (for TIME Assurance)

Customer & building details

Job description (Activation, Outdoor Relocation, Assurance visit, etc.)

Amount & currency

Payment terms (e.g. 45 days)

File metadata stored in DB:

- InvoiceFilePath
- InvoiceFileName
- FileSize
- MimeType

2.5 Portal Submission Tracking (CRITICAL)

Once admin uploads invoice in partner portal, the portal will generate a Submission ID.

Admin must immediately record it in CSIOS.

UI Flow

On invoice detail view:

Invoice #: TIME-INV-2025-00123
Status  : Draft

[ I have uploaded invoice to partner portal ]

→ Opens form:
    Portal Submission ID: [ SUBM-20251120-123456 ]
    Portal Submission URL: [ https://portal.time.com/... ] (optional)
    Portal Submission Status: [ Submitted ] (optional)
    Uploaded At: [ 2025-11-20T09:30:00 ]

[ Save & Mark as Uploaded ]

System Logic

On save:

Set UploadedAt from form

Set PortalSubmissionId (required)

Set PortalSubmissionUrl if provided

Set PortalSubmissionStatus if provided (e.g. Submitted)

Set Invoice.Status = Uploaded

Calculate DueDate = UploadedAt + Partner.PaymentTermsDays (e.g. 45 days)

Now invoice appears in:

Invoices list as Uploaded

Finance dashboard with correct payment due date

Reporting for outstanding invoices

2.6 Invoice Payment Handling

When payment is received:

UI

Finance opens invoice:

Invoice #: TIME-INV-2025-00123
Status  : Uploaded

[ Mark as Paid ]

Payment Date: [ 2026-01-05 ]
Payment Ref : [ BANK-FT-12345 ]
Notes       : [ Paid & matched to bank statement ]

[ Save ]


System:

Sets PaidAt

Sets PaymentRef

Sets Status = Paid

Updates Order.Status → Completed (if everything else done)

2.7 Invoice Rejection Handling

If partner rejects invoice:

UI

Admin selects:

[ Mark as Rejected ]

Reason: [ Incorrect rate / Missing docket / etc. ]
Remarks: [ TIME rejected due to miscalculated rate, to reissue ]


System:

Sets Status = Rejected

Logs rejection reason

Keeps existing PortalSubmissionId for reference

Later, after correction:

Admin regenerates invoice (maybe new number)

Uploads again in portal

Enters new PortalSubmissionId

Status returns to Uploaded

2.8 Invoice List & Filters

Invoice list page columns:

InvoiceNumber

Partner

ServiceId

Status

Amount

GeneratedAt

UploadedAt

DueDate

PaidAt

PortalSubmissionId (NEW)

Filters:

By status (Draft / Uploaded / Paid / Rejected)

By partner

By date range

By overdue / on-time

2.9 Reporting Integration
Reports using Invoice data:

Invoice Aging Report

Days until due / days overdue

By partner

By SI / region (optional)

Partner Performance Report

How many invoices rejected per partner

Average time from ReadyForInvoice → Uploaded → Paid

Cashflow Forecast

Upcoming due amounts per week (based on DueDate)

All of these now can include and reference:

PortalSubmissionId

PortalSubmissionStatus

for quick cross-check against partner portal.

3. STORYBOOK (NARRATIVE USE CASES)
3.1 Story: Standard TIME Activation

Order moves to ReadyForInvoice.

System auto-generates invoice Draft.

Admin opens invoice, checks amount.

Admin downloads PDF, uploads to TIME portal.

TIME portal shows Submission ID = SUBM-20251120-123456.

Admin returns to CSIOS → enters:

PortalSubmissionId = SUBM-20251120-123456

UploadedAt = actual time

CSIOS:

Sets Status = Uploaded

Sets DueDate = UploadedAt + 45 days

Finance dashboard now shows invoice as Awaiting Payment, with portal reference.

3.2 Story: Invoice Rejected, Resubmitted

TIME rejects invoice (wrong rate).

Admin marks invoice Rejected, with reason.

Admin corrects rate / regenerates invoice PDF.

Admin re-uploads to TIME portal; portal generates new submission ID

e.g. SUBM-20251122-987654

Admin calls “Mark as Uploaded” again for new invoice:

New PortalSubmissionId saved

New invoice now tracks correctly; old rejected entry kept for audit.

3.3 Story: Cross-check with Partner Portal

During audit, manager wants to confirm if a missing payment matches portal.

They filter in CSIOS:

Partner = TIME

Status = Uploaded

PortalSubmissionId = SUBM-20251120-123456

They see:

Invoice amount, due date, portal link.

They click portal link and verify status in TIME portal.

END OF INVOICING MODULE (UPDATED)
