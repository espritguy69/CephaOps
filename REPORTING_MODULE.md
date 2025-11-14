Great — proceeding with Module 10 right now.

Because this is a heavy analytics module, I will make it structured and clean for Cursor AI to process later.

📘 REPORTING_MODULE.md
(Combined: Setup + Technical Specification + Storybook)

This module provides all analytics, KPI tracking, performance dashboards, and operational health metrics for your entire Cephas Installation Workflow System.

It connects directly to:

Orders

Schedules

Installers

Inventory

Splitters

Dockets

Invoicing

This is your management-level visibility layer.

# REPORTING MODULE  
### (Setup + Technical Specification + Storybook)

---

# 1. SETUP OVERVIEW

The Reporting Module provides real-time dashboards for:

### ✔ Installer KPI performance  
✔ Order status throughput  
✔ Building workload & partner workload  
✔ Splitter port usage  
✔ Docket submission KPI  
✔ Material consumption patterns  
✔ Invoicing & payment cycles  
✔ Rejections (partner & internal)  

It supports:

- Daily summary  
- Weekly performance  
- Monthly financial + operational reports  
- Export to PDF  
- Export to CSV  
- Filter by SI, Building, Partner, Date Range, Status  

This module is primarily used by:

- Admin  
- Manager  
- Finance  
- Directors  

---

# 2. TECHNICAL SPECIFICATION

---

# 2.1 Reporting Data Sources



Orders
Schedules
ServiceInstallers
Buildings
Splitters
InventoryTransactions
Dockets
Invoices
Partners


Each report pulls from multiple sources to give accurate analytics.

---

# 2.2 Core Reports

Below are the mandatory reports for Cephas operations:

---

## 2.2.1 Installer KPI Report

Shows if individual SI meets KPI for:

### Job duration KPI
- Prelaid → 1 hour  
- Non-Prelaid → 2 hours  
- FTTR / FTTC / SDU / RDF Pole → 3 hours  

### Docket KPI
- Manual docket must be submitted **within 30 minutes**.

### Fields:
- Installer name  
- Number of jobs  
- KPI met count  
- KPI failed count  
- % KPI success  
- Average job duration  
- Average docket time  
- Breakdown by job type  

### Alerts:
- SI with >20% KPI fail = **flag red**  
- SI with consistent late dockets  
- SI with rescheduled jobs due to lateness  

---

## 2.2.2 Daily Operations Report

Summary of operations:

- Orders received today  
- Completed today  
- Pending assignment  
- Pending docket  
- Docket corrections  
- Partner rejections  
- Material shortages  
- Inventory issues  
- Splitter ports reaching full capacity  

This is the **morning briefing report**.

---

## 2.2.3 Building Load Report

Shows which buildings have:

- Frequent issues  
- Assurance cases  
- High material usage  
- High installation volume  
- Nearly full splitter capacity  
- SDU vs Non-Prelaid vs High-density deployments  

Important for:

- Predicting stocking  
- Predicting manpower  
- Maintenance planning  

---

## 2.2.4 Partner Breakdown Report

Shows each partner:

- TIME  
- Celcom  
- Digi  
- U Mobile  

Metrics:

- Jobs completed per partner  
- Rejected dockets per partner  
- Repeated issues  
- Average completion duration  
- Average approval time  
- Invoicing cycle time  

---

## 2.2.5 Docket Compliance Report

Tracks:

- Total dockets  
- Dockets submitted late  
- Dockets with issues  
- Serial mismatch  
- Missing customer signature  
- Partner rejections  
- Pending reupload  
- Average docket submission time  

---

## 2.2.6 Splitter Usage Report

For each splitter:

- Building  
- Splitter ID (1:8 / 1:12 / 1:32)  
- Ports consumed  
- Ports free  
- Standby port (Port 32) usage  
- Approvals attached  
- Which SI used standby  
- Jobs tied to each port  

This keeps management aware of remaining capacity.

---

## 2.2.7 Financial / Invoicing Report

For each job:

- Invoicing status  
- Invoice submitted date  
- Payment due date  
- Payment received  
- Overdue invoices  
- Partner rejection count  
- Revenue projection  
- Invoice PDF path  
- Payment verification  

Finance uses this daily.

---

## 2.2.8 Material Consumption Report

Tracks:

- Material issued  
- Material returned  
- Unreturned material (SI accountability)  
- Faulty device return  
- CPE swaps  
- Stock forecast  
- Monthly usage trend  

---

# 2.3 Dashboard Widgets

Your main dashboard will contain:

### Top Level KPIs
- Today Orders  
- Pending Assignments  
- SI KPI %  
- Docket KPI %  
- Rejections Today  
- Jobs Completed Today  
- Invoices Submitted  
- Payments Cleared  

### Operational Widgets  
- Orders by Partner  
- Orders by OrderType (FTTF, FTTO...)  
- Orders by Building  
- Splitter capacity  
- Installer availability  

### Financial Widgets  
- Revenue Expected  
- Revenue Collected  
- Outstanding Payments  
- Invoice Success Rate  

---

# 2.4 Filtering System

Every report must support:

- Date range  
- Partner  
- SI  
- Building  
- Order Type  
- KPI Passed / Failed  
- Status  
- Material Type  

This is important for management decision-making.

---

# 2.5 Exporting

All reports must export to:

- **CSV**
- **PDF**
- **Excel (XLSX)** (optional)

---

# 3. STORYBOOK (Narrative Use Cases)

---

## 3.1 Story: Daily Morning Briefing

At 9 AM, admin opens dashboard.

System shows:

- Jobs pending assignment  
- Jobs pending SI confirmation  
- Yesterday’s completed jobs  
- Dockets pending review  
- Splitters nearing capacity  
- Inventory shortage  
- KPI fail rate for yesterday  

This gives clear visibility for the day.

---

## 3.2 Story: SI Performance Review

Manager selects an installer (e.g., “Klavin”).

System shows:

- SI completed 52 jobs this month  
- KPI met: 48  
- KPI failed: 4  
- Docket KPI success 90%  
- Average Prelaid job time: 40 mins  
- Late reschedules: 1  

Manager can identify top performers or underperformers.

---

## 3.3 Story: Building Analysis

Manager reviews Desa ParkCity.

System shows:

- Splitter 1:32 at 93% capacity  
- High assurance cases (12 this month)  
- CPE swap trend increasing  
- Material usage spike (fiber cable)  
- Frequent repeat complaints  

This triggers preventive maintenance planning.

---

## 3.4 Story: Partner Complaint Review

TIME complains about late uploads.

Admin pulls “Partner Rejection Report”.

System shows:

- TIME: 14 docket rejections  
- Celcom: 2  
- Digi: 0  
- U-Mobile: 0  

TIME is the main issue.

Admin finds reasons:

- 10 were serial mismatch  
- 3 signature missing  
- 1 wrong address  

Now admin can address root problem.

---

## 3.5 Story: Finance Team Tracking Payments

Finance checks “Invoicing Dashboard”.

System shows:

- Invoice submitted: 37  
- Pending payment: 28  
- Overdue: 4  
- Cleared today: 5  

Finance can chase accordingly.

---

## 3.6 Story: Material shrinkage issue

Inventory report shows:

- 12 ONUs issued  
- Only 10 returned or accounted for  
- 2 units missing

System flags:

**Missing Material Alert: Installer "Alex"**  

Manager can now investigate.

---

# 4. FUTURE EXPANSION (Optional)

- Heatmap of high-fault zones  
- Inventory reorder automation  
- Auto email alerts for overdue invoices  
- SI ranking leaderboard  
- Predictive modelling for material consumption  

---

# END OF REPORTING MODULE
