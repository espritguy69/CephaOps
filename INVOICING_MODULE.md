📘 INVOICING_MODULE.md
(Combined: Setup + Technical Specification + Storybook)

This module handles:

Invoice creation

PDF generation

Mapping partner invoice formats

Payment tracking

Rejection tracking

45-day credit terms

Per-job revenue vs KPI penalties (future)

The invoicing workflow integrates tightly with:

Orders Module

Dockets Module

Inventory

Scheduler

Partner-specific business rules

# INVOICING MODULE  
### (Setup + Technical Specification + Storybook)

---

# 1. SETUP OVERVIEW

The Invoicing Module ensures:

1. **Every completed job becomes a billable item**  
2. **Invoices follow partner billing formats**  
3. **Admin can generate PDF and upload to TIME Portal**  
4. **Payment terms (45 days) are tracked precisely**  
5. **Rejection handling**  
6. **Revenue reporting with performance penalties (future)**

All billing must be consistent, auditable, and linked directly to the Order.

---

# 2. TECHNICAL SPECIFICATION

---

# 2.1 Invoice Lifecycle

The lifecycle of an invoice:



DocketsUploaded
↓
ReadyForInvoice
↓
InvoiceCreated
↓
PDFGenerated
↓
UploadedToPartner
↓
AwaitingPayment (45 days SLA)
↓
Paid
↓
Closed


---

# 2.2 Invoice Entity Structure

Each invoice contains:

### Identity
- **InvoiceId** (internal)
- **InvoiceNumber** (generated per partner rule)
- **InvoiceDate**
- **Partner** (TIME / Celcom / Digi / UMobile)
- **BillingPeriod** (optional)

### Relationship to Orders
- **LinkedOrders** (list of OrderIds)
- **TotalAmount**
- **AmountPerOrder**

### PDF Metadata
- **PdfFilePath**
- **GeneratedAt**
- **GeneratedBy**

### Status


Draft
Generated
Uploaded
AwaitingPayment
Rejected
Paid
Closed


### Payment Tracking
- PaymentReceivedDate
- PaymentRefNumber
- BankSlipAttachment

### Rejection Tracking
- RejectionReason
- RejectionTimestamp
- RejectionRemarks

---

# 2.3 Invoice Line Structure (Per Job)

Each job contributes one “invoice line”.

| Field | Meaning |
|--------|----------|
| OrderId | Job reference |
| ServiceId | For partner portal |
| TicketId / AWO | For Assurance jobs |
| WorkType | Activation/Modification/Assurance |
| Description | Generated text |
| Rate | Based on SI rate (settings module) |
| Quantity | Usually 1 |
| Amount | Rate × Quantity |
| KPIImpact | (Future) penalty/bonus |
| MaterialsCharge | (Optional) |
| TotalLineAmount | Amount + MaterialsCharge |

---

# 2.4 Rate Calculation Rules

Each partner + job type has fixed rates:

| Partner | Activation | Modification | Assurance | SDU | RDF Pole |
|---------|------------|--------------|-----------|-----|-----------|
| TIME | X | Y | Z | A | B |
| Celcom | X1 | Y1 | Z1 | A1 | B1 |
| Digi | X2 | Y2 | Z2 | A2 | B2 |

Rates stored in Settings → Installer Rates.

Installer rates do NOT affect invoice amount but used for SI payment later.

---

# 2.5 Automatic Invoice Generation Rules

When an order transitions to **ReadyForInvoice**, system performs:

### 1. Check all prerequisites:
- Docket validated  
- SplitterPort assigned  
- Materials consumed logged  
- No blockers  
- OrderCompleted timestamp is present  

### 2. Determine partner billing rules:
- Single-job invoice (common)
- Batch invoice (future)

### 3. Generate InvoiceLine for that order:
- Job type  
- Rate  
- Amount  
- Description  

### 4. Accumulate into Invoice entity:
- If batching → multiple lines  
- If one job per invoice → generate one invoice per order

### 5. Set status: **Draft**

---

# 2.6 PDF Generation Specification

PDF must contain:

### Header:
- Cephas Sdn Bhd logo  
- Partner name  
- Invoice Number  
- Invoice Date  
- Address  

### Body Table:
Columns:
- Service ID  
- Ticket/AWO (if any)  
- Work Type  
- Building  
- Order Completed Date  
- Amount  

### Footer:
- Payment instructions  
- Bank details  
- Terms  
- Prepared by / Approved by  

PDF generation MUST be deterministic.

---

# 2.7 Partner Portal Upload Tracking

Once admin uploads PDF to TIME Portal:

Admin presses:
**“Mark as Uploaded”**

System sets:

- InvoiceStatus → Uploaded  
- UploadedAt (timestamp)
- UploadedBy (admin user)

Now system automatically tracks:

**Due Date = UploadedAt + 45 days**

---

# 2.8 Rejection Workflow

If partner rejects invoice:

Admin selects:


Mark as Rejected


System requires:

- RejectionReason  
- Screenshot / file attachment  
- Remarks  

Order is moved back to:
**ReadyForInvoice**

InvoiceRecord remains but closed as:
**Rejected**

---

# 2.9 Payment Tracking Workflow

When bank transfers payment:

Admin enters:

- PaymentReceivedDate  
- PaymentRefNo  
- BankSlip attachment  

System updates:

- InvoiceStatus → Paid  
- Linked orders → Completed  
- Close invoice as **Closed**

---

# 2.10 KPI and Financial Link (Future Module)

Future functionality:

- Penalty for late job completion  
- Bonus for fast assurance repair  
- Job scoring  
- Installer payout adjustment  

For now, KPI does not affect invoice amount.

---

# 3. STORYBOOK (Narrative Understanding)

---

## 3.1 Story: A job becomes billable

Order transitions:



DocketsUploaded → ReadyForInvoice


System checks:

- No blockers  
- Materials consumed  
- Splitter assigned  
- All timestamps OK  

System automatically creates:

- InvoiceLine  
- Invoice entity (single-job invoice)  

---

## 3.2 Story: PDF generated

Admin clicks:

“Generate Invoice”

System:

- Creates PDF  
- Saves to folder  
- Sets InvoiceStatus = Generated  

---

## 3.3 Story: Upload to TIME Portal

Admin uploads PDF manually in TIME Portal.

Then clicks:
**"Mark As Uploaded"**

System sets:

- UploadedAt = now  
- DueDate = UploadedAt + 45 days  

Invoice now in **AwaitingPayment**.

---

## 3.4 Story: Payment Arrives

Bank pays RM 75 for 1 activation.

Admin:

- Clicks “Mark as Paid”  
- Uploads bank slip  
- Adds PaymentRef  

System:

- InvoiceStatus = Paid  
- OrderStatus = Completed  
- Revenue recognized  

---

## 3.5 Story: Rejection from Partner

TIME says:

> Wrong serial number  
> Please resubmit  

Admin:

- Picks invoice  
- Clicks “Reject Invoice”  
- Attaches TIME screenshot  
- Adds remarks  

System:

- InvoiceStatus = Rejected  
- OrderStatus = ReadyForInvoice  
- New invoice must be generated  

---

## 3.6 Story: Finance Dashboard

Daily snapshot includes:

- Outstanding invoices  
- Due soon invoices  
- Late invoices (>45 days)  
- Rejected invoices  
- Revenue per partner  
- Monthly invoice totals  

---

# END OF INVOICING MODULE
