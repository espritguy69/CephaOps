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
