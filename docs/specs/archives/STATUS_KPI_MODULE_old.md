📘 STATUS_KPI_SPLITTER_MODULE.md
(Combined: Setup + Technical Specification + Storybook)

This module controls:

All status transitions

All KPI calculations

All Splitter + Port assignment rules

All Blocker conditions

All Portal timestamp rules

Standby port (e.g., port 32) partner approval logic

Ensuring audit logs for every single change

This is the “brains” of workflow control.

# STATUS, KPI & SPLITTER RULES MODULE  
### (Setup + Technical Specification + Storybook)

---

# 1. SETUP OVERVIEW

This module ensures:

### 1. Correct workflow sequencing  
Statuses must follow the correct order and require mandatory data.

### 2. KPI calculation  
- Job KPI (based on Prelaid / Non-prelaid / FTTR / SDU etc.)  
- Docket KPI (30 minutes target)

### 3. Splitter + Port validation  
At Docket stage, system must collect:

- Splitter used
- Port used
- Whether port is standby (e.g., Port 32 on 1:32 splitter)
- If standby is used → must attach partner approval

### 4. Blockers  
Before finishing a job, admin should record:

- Customer Blocker
- Building Blocker
- Network Blocker

Each blocker must have:

- Reason
- Remark
- Attachments

### 5. Audit logging  
Every status change writes to:

- OrderStatusHistory  
- KPIHistory  
- SplitterUsageHistory  

---

# 2. TECHNICAL SPECIFICATION

---

# 2.1 Status Types (Full List)



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
Rescheduled (special)
Cancelled (special)


---

# 2.2 Status Transition Matrix

## Allowed Transitions

| From | To | Conditions |
|------|-----|-------------|
| Pending | Assigned | Installer + schedule required |
| Assigned | OnTheWay | PortalTimeOnTheWay required |
| OnTheWay | MetCustomer | PortalTimeMetCustomer required |
| MetCustomer | OrderCompleted | PortalTimeOrderCompleted required |
| OrderCompleted | DocketsReceived | Splitter + Port required |
| DocketsReceived | DocketsUploaded | Docket file uploaded |
| DocketsUploaded | ReadyForInvoice | Admin verified |
| ReadyForInvoice | Invoiced | Invoice PDF generated |
| Invoiced | Completed | Payment verified |
| ANY | Rescheduled | New appointment required |
| ANY | Cancelled | Partner instruction |

No skipping allowed.

---

# 2.3 Status Change Requirements (VERY IMPORTANT)

## 🟧 1. Assigned  
When changing to **Assigned**:
- InstallerId is required  
- ScheduledStart is required  
- StatusHistory entry created  

---

## 🟨 2. OnTheWay  
Required:
- PortalTimeOnTheWay (datetime, from SI)

Validation:
- Must be AFTER ScheduledStart - 2 hours  
- Must NOT be more than next day  
- SI must be active  

---

## 🟩 3. MetCustomer  
Required:
- PortalTimeMetCustomer  

Validation:
- Must be AFTER OnTheWay time  
- Cannot exceed same-day window  

---

## 🟦 4. OrderCompleted  
Required:
- PortalTimeOrderCompleted  
- Must be AFTER MetCustomer  

Triggers:
- Calculate **Job KPI**  
- Create KPIHistory record  

---

## 🟪 5. DocketsReceived  
Required:
- SplitterId  
- SplitterPort  
- DocketReceivedAt  
- DocketFile (optional at this stage but timestamp required)  

Triggers:
- Calculate **Docket KPI (target 30 minutes)**  
- Validate standby port rule (for port 32 on 1:32 splitters)

---

## 🟥 6. DocketsUploaded  
Required:
- DocketFileUploaded  
- AdminVerified = true  

---

## 🟫 7. ReadyForInvoice  
Required:
- No blockers  
- Correct materials logged  
- Docket validated  

---

## ⬛ 8. Invoiced  
Required:
- InvoiceRecord created  
- PDF generated  

---

## ⬜ 9. Completed  
Required:
- PaymentReceivedDate  
- VerifiedByAccounts = true  

Triggers:
- Closes order  
- Marks final KPI status  

---

# 2.4 KPI Calculation Rules

## Job KPI Target

| Order type | KPI Target |
|-------------|------------|
| Prelaid | 60 minutes |
| Non-Prelaid | 120 minutes |
| FTTR / FTTC / SDU / RDF POLE | 180 minutes |

### Calculation:


JobKpiActual = PortalTimeOrderCompleted – PortalTimeOnTheWay
JobKpiVariance = JobKpiActual – JobKpiTarget
JobKpiMet = JobKpiActual <= JobKpiTarget


---

## Docket KPI Target

Manual docket must arrive **within 30 minutes** after job completion.

### Calculation:


DocketKpiActual = DocketReceivedAt – PortalTimeOrderCompleted
DocketKpiMet = DocketKpiActual <= 30 minutes


---

# 2.5 Splitter + Port Rules

## Splitter Types  
- 1:8  
- 1:12  
- 1:32  

## Standby Port Logic

**For 1:32 splitters:**
- Port 32 is standby
- Must have PartnerApproval attachment
- Must trigger an approval workflow

### Validation:


If SplitterType = 1:32 AND SplitterPort = 32
Require PartnerApproval = true
Require PartnerApprovalFile != null


## Port Availability Rules
When selecting port:
- Port must NOT be already consumed by another order  
- Port status must reflect:
  - Free
  - Used
  - Reserved  
  - Standby  

---

# 2.6 Blockers

Before OrderCompleted:

### Blocker Types:
1. Customer  
2. Building  
3. Network  

Each blocker must contain:

- BlockerType  
- Description  
- CreatedAt  
- SolvedAt  
- Attachment (optional)  
- AdminRemarks  

Blockers **pause** KPI calculation.

---

# 2.7 Audit Logging

Every status change creates a log record with:

- PreviousStatus  
- NewStatus  
- Timestamp  
- Actor (SI/Admin)  
- PortalTime (if applicable)  
- Remarks  
- IP Address  
- Source (Mobile/Web/API)

---

# 3. STORYBOOK (Narrative Understanding)

---

## 3.1 Story: Installer Arrives Late (KPI Fail)

Job Type: Non-Prelaid (120 minutes target)

- OnTheWay → 10:00  
- MetCustomer → 10:20  
- Completed → 14:00  

Actual duration = **240 minutes**

System:

- Calculates KPI  
- Marks KPI as FAIL  
- Stores variance = +120 minutes  
- Adds entry to KPI History  
- Shows red badge in reports  

---

## 3.2 Story: Docket Arrives Late

Manual docket:

- OrderCompleted → 14:00  
- DocketReceivedAt → 14:45  

Docket KPI = **45 minutes** (FAIL)

---

## 3.3 Story: Splitter Standby Port Used

Installer uses port 32.

System:

1. Detect splitter type = 1:32  
2. Port selected = 32  
3. Requires PartnerApproval  
4. Blocks status change unless approval file uploaded  
5. Logs SplitterUsageHistory  
6. Updates splitter port map  

---

## 3.4 Story: Network Blocker

On-site:

> Building’s MDF locked, BM not available.

Admin adds:

- BlockerType = Building  
- Description = MDF room locked  
- SolvedAt = Next day when BM opens  

KPI calculation is paused until blocker resolved.

---

## 3.5 Story: Order Completed → DocketsReceived

Installer completes job.

Admin:

- Selects SplitterId
- Selects Port 12
- Enters DocketReceivedAt

System:

- Validates port usage  
- Marks port as occupied  
- Calculates Docket KPI  
- Moves order forward  

---

## 3.6 Story: Final Status – Completed

After invoicing:

- Invoice uploaded  
- Partner pays  
- Accounts marks payment received  

Order is now:

→ **Completed**  
→ Performance KPIs closed  
→ Splitter port finally locked  

---

# END OF STATUS, KPI & SPLITTER RULES MODULE
