
# SETTINGS\_MODULE

* 
* The Settings Module centralizes all configurable parameters across CephasOps to ensure flexibility, per-company customization, and consistent control of operational behaviour.
* 
* ---
* 
* \## 1. Core Purpose
* \- Provide a \*\*single source of truth\*\* for operational settings.
* \- Allow \*\*per-company\*\*, \*\*per-site\*\*, and \*\*system-level\*\* overrides.
* \- Centralize parameters used across Orders, Inventory, Billing, KPI, Scheduling, and Parser workflows.
* \- Enable easy scaling for multi-company operations.
* 
* ---
* 
* \## 2. Configuration Categories
* 
* \### 🔵 Workflow Configuration
* \- Order flow rules  
* \- Auto-close handling  
* \- Backdate permissions  
* \- Required approvals  
* \- Default job durations  
* \- Cancellation \& rescheduling policies  
* 
* \### 🔵 KPI \& Performance Thresholds
* \- Installer KPI scoring rules  
* \- Timeliness thresholds  
* \- Job quality \& defect scoring  
* \- Rejection/redo criteria  
* \- SLA configuration per company  
* 
* \### 🔵 Parser \& Automation Rules
* \- Email parser rules  
* \- Excel/PDF extraction rules  
* \- Order classification rules  
* \- Address trimming \& normalization  
* \- Auto-tagging logic  
* 
* \### 🔵 Notifications
* \- WhatsApp notifications  
* \- SMS rules  
* \- Email templates  
* \- Trigger conditions  
* \- Per-company sender profiles  
* 
* \### 🔵 SI Rates \& Payroll Settings
* \- Day rates / job rates  
* \- KPI bonus settings  
* \- Deductions \& penalty rules  
* \- Overtime calculation  
* \- Subcon/company rate profiles  
* 
* \### 🔵 Cost Centres \& Branch Management
* \- Cost centre codes  
* \- Branch/outlet mapping  
* \- Finance grouping  
* \- Stock tracking location rules  
* \- Billing grouping configuration  
* 
* \### 🔵 Localization \& Branding
* \- Company logos  
* \- Colour themes  
* \- Currency  
* \- Date/number formatting  
* \- Language options  
* 
* ---
* 
* \## 3. Levels of Settings (Scopes)
* 
* \### 🌐 System-Level (Platform-wide)
* \- Parser templates  
* \- Global operational defaults  
* \- Reserved codes (status, categories, etc.)
* 
* \### 🏢 Company-Level
* \- Billing behaviours  
* \- KPI thresholds  
* \- Notification rules  
* \- Branding  
* \- SI rates  
* 
* \### 🏪 Site/Branch-Level
* \- Opening hours  
* \- Technician assignment rules  
* \- Zone restrictions  
* \- Local inventory handling  
* 
* ---
* 
* \## 4. Technical Behaviour
* 
* \### ✔ Priority Resolution
* 1\. Site-level value (highest)
* 2\. Company-level value
* 3\. System default (fallback)
* 
* \### ✔ API Access
* \- `/api/settings/definitions`
* \- `/api/settings/value`
* \- `/api/settings/effective`
* 
* \### ✔ Auditing
* \- All setting changes logged  
* \- Tracks \*\*who\*\*, \*\*when\*\*, \*\*old vs new values\*\*  
* \- Company and site scoping included  
* 
* ---
* 
* \## 5. Integrations With Other Modules
* 
* \### ➤ Orders Module
* \- Validation logic  
* \- Auto-close logic  
* \- Scheduling defaults  
* \- Order numbering rules  
* 
* \### ➤ Inventory \& RMA
* \- Negative stock allowance  
* \- Serial tracking requirements  
* \- Warehouse / outlet rules  
* 
* \### ➤ Billing \& Finance
* \- Invoice prefix \& numbering  
* \- Payment terms  
* \- RSD and penalty handling  
* 
* \### ➤ KPI/Status Module
* \- Thresholds  
* \- Scoring formulas  
* \- Daily/weekly/monthly intervals  
* 
* \### ➤ Scheduler Module
* \- Default working hours  
* \- Slot durations  
* \- Capacity rules  
* 
* ---
* 
* \## 6. Use Cases
* \- Adjust KPI thresholds per company  
* \- Change parser rules without code deployment  
* \- Enable or disable WhatsApp notifications per site  
* \- Set different SI rates for different subcontractors  
* \- Customize invoice numbering per brand  
* \- Localize branding for Kingsman vs Cephas vs Menorah  
* 
* ---
* 
* \## 7. Future Enhancements
* \- Export/import settings profiles  
* \- UI presets per business type (ISP / Barbershop / Travel)  
* \- Feature flagging support  
* \- AB testing controls  
* 
* ---
* 
* \# End of Document
* 
