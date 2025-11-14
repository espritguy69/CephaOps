📘 DOCKETS_MODULE.md
(Combined: Setup + Technical Specification + Storybook)

This module controls:

Manual docket flow

Digital docket flow (future)

Docket validation

Docket upload

Admin review workflow

Docket KPI (must be submitted within 30 minutes after job completion)

Rejections and corrections

Linkage to invoicing

This is the “financial accuracy” module — every mistake in dockets = delayed payment.

# DOCKETS MODULE  
### (Setup + Technical Specification + Storybook)

---

# 1. SETUP OVERVIEW

The Dockets Module ensures:

### ✔ Every completed job must have a docket  
- Manual paper docket  
- Photo upload  
- Partner-specific format

### ✔ Admin must review the docket before invoicing  
Ensures details match:

- Service ID  
- Customer Name  
- Building  
- Serial numbers  
- Splitter & Port  
- Work type  
- Installer name  
- Task completed  
- Signature  

### ✔ Docket KPI enforced  
Manual docket must be received **within 30 minutes** after job completion.

### ✔ Upload to Partner Portal  
Admins upload docket file to TIME portal (or other partners).

### ✔ Supports corrections & reuploads  
If partner rejects docket, admin can fix and re-upload.

---

# 2. TECHNICAL SPECIFICATION

---

# 2.1 Docket Entity Structure

### Identity
- **DocketId**  
- **OrderId**  
- **DocketNumber** (if provided)  

### File Info
- **DocketFilePath**  
- **FileType** (jpg/pdf/png)  
- **FileSize**  
- **UploadedByAdmin**  
- **UploadedAt**  

### Timing
- **DocketReceivedAt**  
- **DocketReviewedAt**  
- **DocketUploadedAt** (to partner)  

### Validation Flags
- **IsValid**  
- **RequiresCorrection**  
- **CorrectionReason**  
- **CorrectionRemarks**  
- **PartnerRejected** (bool)  
- **PartnerRejectionReason** (string)  

### KPI Fields
- **DocketKpiTargetMinutes = 30**  
- **DocketKpiActualMinutes**  
- **DocketKpiMet**  

### Splitter / Port
- (Pulled from Status module)  
- SplitterId  
- PortNumber  
- IsStandbyUsage  
- PartnerApprovalAttachment  

---

# 2.2 Validation Requirements

A docket is considered valid if:

### 1. Required fields present:
- Service ID  
- Installer signature  
- Job type (Activation/Assurance/etc.)  
- Date of work  
- Serial number (if ONU replaced)  
- Work completed section checked  
- Customer sign-off (except Assurance where not required)  

### 2. Must match the Order:
- ServiceId must match  
- Building must match  
- Installer must match  
- OrderCompleted time must match date on docket  
- Work type must match order task type  
- Splitter port must match what SI input  

### 3. Serial Numbers (critical)
If job included ONU or Router:

- Serial in docket MUST match the serial consumed by SI  
- Assurance: Old serial must be recorded too  

### 4. If mismatch occurs:
Docket flagged:


RequiresCorrection = TRUE
CorrectionReason = "Serial mismatch"


---

# 2.3 Manual Docket Entry Flow

For manual paper dockets:

SI submits physical docket → admin uploads a photo.

Admin steps:

1. Click **Upload Docket**  
2. Select file (PDF, JPG, PNG)  
3. System extracts text (OCR future)  
4. Admin reviews  
5. System validates fields  
6. Approval → moves to **DocketsUploaded** stage  

---

# 2.4 Docket KPI Enforcement

**KPI target: 30 minutes** after job completion.

### Calculation:


DocketKpiActualMinutes = DocketReceivedAt - PortalTimeOrderCompleted
DocketKpiMet = DocketKpiActualMinutes <= 30


If SI submits late:

- KPI fails  
- Marked in SI KPI history  
- Admin can still accept docket (but KPI marked red)  

If severely late (>3 hours):

- System flags in dashboard  
- Admin must input reason  

---

# 2.5 Workflow States



AwaitingDocket
↓
DocketReceived
↓
DocketReviewed
↓
DocketUploaded
↓
ReadyForInvoice


Special states:



DocketCorrection
PartnerRejected
CorrectionResubmitted


---

# 2.6 Partner Portal Upload

Once admin uploads docket to partner portal:

Admin clicks:

**“Mark Docket Uploaded to Partner”**

System updates:

- DocketUploadedAt = now  
- Order.Status = DocketsUploaded  

Now order is ready to proceed into invoicing workflow.

---

# 2.7 Handling Partner Rejection

If TIME or Celcom rejects docket:

Admin selects:

**“Mark as Rejected”**

System requires:

- RejectionReason (mandatory)  
- Screenshot or file (evidence)  
- Admin comments  

Now docket enters:



PartnerRejected


Then admin can correct docket:

- Fix serial  
- Fix date  
- Upload new file  

Then presses:

**“Submit Correction”**

System updates:



Status = DocketsUploaded
IsValid = TRUE
PartnerRejected = FALSE


---

# 2.8 Docket Dashboard

Important for daily admin work:

### Shows:
- Jobs pending docket  
- Dockets pending review  
- Dockets pending upload  
- Dockets rejected  
- Dockets with serial mismatch  
- KPI results (green/red)  

### Filters:
- Date range  
- Installer  
- Partner  
- Building  
- KPI Met/Not  

---

# 3. STORYBOOK (Narrative Understanding)

---

## 3.1 Story: Installer completes job

Status transitions:



MetCustomer → OrderCompleted


System expects docket within 30 minutes.

---

## 3.2 Story: SI submits docket late (35 minutes)

PortalTimeOrderCompleted = 1:00 PM  
Admin uploads docket at 1:35 PM

System:

- DocketKpiActualMinutes = 35  
- KPI FAIL  
- Marks docket but allows processing  

---

## 3.3 Story: Serial mismatch

Docket shows ONU serial:  
**HWE12345678**

Installer selected serial:  
**HWE12345679**

System flags:

- RequiresCorrection = TRUE  
- CorrectionReason = Serial Mismatch  

Admin must correct before invoicing.

---

## 3.4 Story: Standby port usage shown in docket

Installer used port 32 on 1:32 splitter.

System checks:

- Has partner approval?  
- Approval file present?  

If missing:



BLOCK DocketReceived
Show: “Standby port requires approval”


Admin cannot proceed.

---

## 3.5 Story: Partner rejection

TIME portal rejects:

> Wrong service address. Please correct.

Admin performs:

- Opens docket  
- Marks rejection  
- Uploads screenshot  
- Edits docket PDF  
- Uploads corrected version  
- Marks correction resubmitted  

System:

- Status → DocketsUploaded  
- Ready for Invoice restored  

---

## 3.6 Story: Error-free flow

Ideal case:

- SI completes job  
- SI submits docket quickly  
- Admin reviews  
- All validations pass  
- Admin uploads docket to TIME portal  
- Order transitions to ReadyForInvoice  

This triggers invoice generation automatically.

---

# END OF DOCKETS MODULE
