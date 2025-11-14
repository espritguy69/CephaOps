# EMAIL PARSER MODULE  
### (Setup + Technical Specification + Storybook)

---

# 1. SETUP OVERVIEW

The Email Parser Module ingests all incoming:

- TIME Activation / Modification Excel files  
- TIME Assurance emails (TTKT + AWO)
- Celcom / Digi / UMobile job orders  
- Manual orders inside email body  

The parser must:

1. Download all emails from POP3/IMAP inbox
2. Detect job type
3. Parse Excel attachments OR parse body text
4. Extract fields
5. Normalise all values
6. Create or update an Order in the Orders Module
7. Log parsing history for audits

This module **never schedules** or **changes workflow** — it only creates metadata.

---

# 2. TECHNICAL SPECIFICATION

## 2.1 Supported Email Origins

| Origin | Format | Parsing Method |
|--------|--------|----------------|
| TIME Activation | Excel attachment | Structured rows/columns |
| TIME Modification | Excel attachment | Structured rows/columns |
| TIME Cancellation | Excel attachment | Structured rows/columns |
| TIME Assurance | Email body text | Regex-based extraction |
| Celcom | Email body + excel | Partner-specific |
| Digi | Email body + excel | Partner-specific |
| UMobile | Email body | Plain text |
| Manual Orders | Email body (free text) | Classifier + extraction |

---

## 2.2 Pipeline

### Step 1 — Email Retrieval

- Connect to inbox via POP3 or IMAP.
- Only fetch unread OR flagged emails.
- Save:
  - MessageId
  - Sender
  - Subject
  - DateReceived
  - Attachments (Excel, PDF, Images)

### Step 2 — Email Classification

Classify email as:

1. **Activation**
2. **Modification**
3. **Cancellation**
4. **Assurance**
5. **Partner Order (Celcom / Digi / UMobile)**
6. **Manual Order**
7. **Non-order / Irrelevant** (ignored)

Classifier uses:

- Subject keyword patterns  
- Sender domain  
- Attachment type  
- Excel template detection  
- Presence of TTKT / AWO / TBBN patterns  

### Step 3 — Data Extraction

### For Excel-based orders:

The parser must extract the following structured elements:

#### 1. Customer Details  
- CustomerName  
- ServiceAddress  
- ContactPerson  
- ContactNo  

#### 2. Activation/Modification Details  
- ServiceId (TBBN…)  
- Package  
- Bandwidth  
- LoginId  
- ONU Password  
- SerialNumber  
- RouterType / ONUModel  
- VoIP details  
- Additional Equipment choices  
- Remarks  
- Splitter details  

#### 3. Appointment  
- AppointmentStart datetime  
- AppointmentEnd (auto-fill if not provided)

#### 4. Installer  
- InstallerName (“CEPHAS TRADING & SERVICES”) always present in TIME files

### For Assurance (body-based)

Parser extracts:

- ServiceId (TBBNxxxx)
- TTKTxxxx
- AWOxxxx
- CustomerName
- ServiceAddress
- ContactNo
- Appointment datetime
- Problem description (“Link Down”, etc.)
- ONU Model
- Warranty details
- Splitter Info  
- URL

### For manual orders

The parser uses:

- Pattern recognition  
- Keyword extraction  
- Address detection  
- NLP-based datetime detection  

---

## 2.3 Field Normalisation

This module ensures all data passed to the Orders module is:

- Clean  
- Correct type  
- ISO-format  
- Lowercase/uppercase standardised  
- Validated  

### Normalisations:

| Field | Process |
|-------|----------|
| Phone Numbers | `0123456789` only |
| Datetime | `YYYY-MM-DD HH:mm:ss` |
| Address | Trim → Remove duplicates → Convert with rules |
| Booleans | TRUE/FALSE |
| Partner | Enum: TIME / CELCOM / DIGI / UMOBILE |
| OrderType | Activation / Modification / Assurance / Cancellation / SDU / RDF |

### Special Rules

- TIME assurance: **Must** contain ServiceId + TTKT + AWO.
- Activation: Must contain Activation details section.
- Modification: Must contain old address + new address sections.
- Excel missing fields → must be empty but preserved in comments.

---

## 2.4 Output to Orders Module

### Final JSON sent to Orders module:

```json
{
  "serviceId": "TBBNB062587G",
  "partner": "TIME",
  "orderType": "Activation",
  "taskType": "Activation",
  "customerName": "COBNB SDN BHD",
  "serviceAddress": "Level 17, Unit 7, ROYCE RESIDENCE...",
  "contactPerson": "WONG VOON HWA",
  "contactNo": "122164657",
  "appointmentStart": "2025-11-14T10:00:00",
  "appointmentEnd": "2025-11-14T11:00:00",
  "buildingShortName": "ROYCE RES",
  "onuModel": "Huawei HG8145X6",
  "package": "TIME Fibre 200Mbps Home Broadband",
  "remarks": "FTTH BB Bundle With Voice",
  "ticketId": null,
  "awoId": null,
  "rawEmailId": "MSG124556789"
}

2.5 Validation Before Order Creation
Required fields:

ServiceId

Partner

AppointmentStart

CustomerName

ServiceAddress

Extra for Assurance:

TicketId

AwoId

If missing:

Parser logs error

Creates RejectedEmail record

Sends alert to admin

3. STORYBOOK (Narrative Understanding)
3.1 Story: The Email Arrives

TIME sends:

APPMT - CEPHAS TRADING & SERVICES
TBBNA261593G
TTKT202511138603863
AWO437884
Appointment: 14 Nov 2025, 1pm

The parser reads this email.

What the parser sees:

Subject contains TTKT and AWO → Assurance

Body contains structured customer info

Appointment is present

ServiceId is present

The parser extracts all fields → creates Assurance Order.

3.2 Story: Excel Activation Email

TIME sends an Excel attachment:

CustomerName = COBNB SDN BHD

ServiceId = TBBNB062587G

AppointmentStart = 14 Nov 2025, 10am

Package = TIME Fibre 200Mbps

SplLiter details empty

Parser:

Opens Excel

Reads known coordinates

Extracts fields

Converts address to clean string

Identifies OrderType = Activation

Order is created automatically.

3.3 Story: Modification Email

Excel contains:

Old Address

New Address

SerialNumber

ONU Password

CustomerName

Relocation remark

Parser identifies:

Modification

TaskType = Modification

Creates the order.

3.4 Story: Manual Order Inside Email Body

Someone sends:

Please assign SI to install for
Customer: John Tan
ServiceID: DIGI12345
Date: Tomorrow morning 10am
Address: 12, Jalan Putra Prima, Puchong

Parser:

Detects text patterns

Extracts fields

Converts datetime (“tomorrow morning 10am”) → actual timestamp

Creates order under DIGI partner

3.5 Story: Assurance Troubleshooting

Key content:

Problem: Link Down
ONU Status: Offline
Router Model: HG8145V5
Appointment: 13:00
TTKT + AWO provided

Parser recognises:

Assurance type

Mandatory fields → OK

Extracts all relevant diagnostic info

Order is created and routed to scheduler.

3.6 Story: Data Quality Guarantees

Parser enforces:

No garbage datetime

No invalid phone numbers

No inconsistent addresses

No missing service IDs

If something is unclear:

Parser creates EmailPendingReview

Admin dashboard will show

Admin can manually create the order

Nothing is ever lost.

END OF EMAIL PARSER MODULE
