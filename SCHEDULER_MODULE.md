📘 SCHEDULER_MODULE.md
(Combined: Setup + Technical Specification + Storybook)

This module controls the visual scheduling board, calendar view, drag-and-drop assignment, installer availability, and the “Order card” features.

This is the module your admin will interact with the most.

# SCHEDULER MODULE  
### (Setup + Technical Specification + Storybook)

---

# 1. SETUP OVERVIEW

The Scheduler Module enables:

- Viewing all orders for a given day/week
- Viewing all service installers and their timeline
- Drag-and-drop assignment of orders
- Changing status directly from the order card
- Managing SI availability, skills, and shifts
- Viewing unassigned orders
- Viewing blockers or location/job-type restrictions
- Handling rescheduled appointments
- Updating portal timestamps from an SI mobile app (future)

The scheduler must be **fast**, **real-time**, and **high visibility**.

---

# 2. TECHNICAL SPECIFICATION

---

# 2.1 Calendar View Types

The system must provide:

### 1. **Daily View** (default)
- Columns = Service Installers
- Rows = Time slots (10–60 min granularity)
- Left panel = unassigned orders

### 2. **Weekly View**
- Overview of daily totals per installer
- Color-coded status counts

### 3. **Installer View**
- Shows workload for a specific installer
- KPI performance indicators

---

# 2.2 Scheduler Data Structure

### Objects fetched by UI:



{
"date": "2025-11-14",
"unassignedOrders": [ ... ],
"installers": [
{
"installerId": 1,
"installerName": "Klavin",
"assignments": [
{
"orderId": 1001,
"serviceId": "TBBNB062587G",
"customerName": "COBNB SDN BHD",
"timeStart": "2025-11-14T10:00:00",
"timeEnd": "2025-11-14T11:00:00",
"status": "Assigned",
"buildingShort": "ROYCE",
"partner": "TIME"
}
]
}
]
}


---

# 2.3 Order Card Design (VERY IMPORTANT)

Each card MUST show:

### Header (Bold text)
- **Service ID + WO ID (if Assurance: TTKT + AWO)**  
  Example:  
  TBBNA261593G / TTKT202511138603863

### Main info:
- Customer name  
- Appointment time  
- Building short name  
- Partner (TIME/CELCOM/DIGI)  
- Status color  
- Installer name (if assigned)

### On click:
- Dropdown status selector  
- Edit appointment  
- Add remarks  
- Reschedule  
- View customer details  
- View building + splitter info  
- View materials (auto-populated by building type)  

---

# 2.4 Drag & Drop Rules

The Scheduler must support:

### 🔹 Drag from “Unassigned Orders” → Installer column
This creates:

- Installer assignment  
- ScheduledStart + ScheduledEnd  
- Status changes to **Assigned**  

### 🔹 Drag within same installer column → adjust time
Updates:

- ScheduledStart/End  
- Logs reschedule if > 10 minutes shift  

### 🔹 Drag from one installer → another installer
Updates:

- InstallerId  
- Status stays Assigned  
- Logs “ReassignHistory”  

---

# 2.5 Scheduling Validation Rules

### 1. Installer availability
- Cannot assign during **off days**
- Cannot assign during **blocked hours**
- Cannot assign overlapping appointments unless manually overridden

### 2. Installer skills
Some job types require specific skill:

| Order Type | Skill Required |
|------------|----------------|
| DSL/FTTC | RJ45 knowledge |
| FTTR | Home Mesh experience |
| SDU | RDF pole experience |
| Assurance | Troubleshooting |

Scheduler must block assignment if skill mismatch.

### 3. Building restrictions
Some buildings ONLY allow specific installers.

### 4. Prelaid vs Non-prelaid duration
Default durations:

| Type | Duration |
|------|-----------|
| Prelaid | 1 hour |
| Non-prelaid | 2 hours |
| FTTR/FTTC/SDU/RDF Pole | 3 hours |

Admin may override.

---

# 2.6 Rescheduling Logic

Moving to Rescheduled status requires:

- New appointment date/time
- Reason for reschedule
- Remarks
- Updated appointment window
- Notification to SI (future)

Rescheduled orders appear with a **Yellow badge** in the scheduler.

---

# 2.7 Status Update From Scheduler

Clicking the card opens a dropdown:



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


When selecting a status:

- UI prompts for required fields (PortalTime, SplitterPort, etc.)
- StatusChange module validates the transition

Example:

If user tries:

Assigned → OrderCompleted  
System blocks it.

---

# 2.8 Installer List Rules

The Installer column list on the left side of the calendar shows:

| Field | Requirement |
|--------|--------------|
| Name | Required |
| Phone | Required |
| Active / Inactive | Must show |
| Skills | Shown as tags |
| Region | Shown |
| KPI Ranking | Optional display badge |

Inactive SI → greyed out.

Only **Active SIs** shown in the scheduler.

---

# 2.9 Scheduler API (Document Only, No Coding)

### GET /scheduler?date={date}
Returns daily view.

### POST /scheduler/assign
Assign an order.

### POST /scheduler/reschedule
Reschedule an order.

### GET /scheduler/installers
List all installers.

### GET /scheduler/unassigned
List of orders without installer or slot.

### POST /scheduler/update-status
Trigger status update from the card.

---

# 3. STORYBOOK (Narrative Understanding)

---

## 3.1 Story: Assigning a Job

Email parser created:

- Order: Activation  
- Service ID: TBBNB062587G  
- Appointment: 10am  

Scheduler screen:

- Order appears in “Unassigned” section.

Admin drags this order into:

- Column: *Klavin*  
- Slot: 10:00–11:00

System:

- Creates assignment  
- Sets InstallerId  
- Status → Assigned  
- Logs history  

Now the order sits under Klavin's schedule.

---

## 3.2 Story: SI is busy → Reassign

Klavin has an emergency leave.

Admin drags order to:

- Column: *Pravin*

System:

- Updates InstallerId  
- Logs reassignment  
- Status does **not** reset  

---

## 3.3 Story: On-site updates

SI updates:

- OnTheWay → 9:55  
- MetCustomer → 10:05  
- Completed → 11:00  

Scheduler displays a blue → green → purple progress color.

Right after OrderCompleted, scheduler:

- Shows badge “KPI processing…”  
- After calculation: KPI Met/Failed displayed  

---

## 3.4 Story: Rescheduling Required

Customer unreachable.

Admin:

- Opens order card  
- Clicks “Reschedule”  
- Puts new time: Next day, 11:00  
- Status changes to “Rescheduled”  

Scheduler:

- Moves order to new date  
- Marks with yellow badge  

---

## 3.5 Story: Morning Dashboard Snapshot

Each morning:

- Completed jobs yesterday  
- KPIs met/failed  
- Dockets pending  
- Orders without assignments  
- High-priority SIs (best KPI rate)  

Scheduler helps admin plan the day.

---

# 4. UNIQUE FEATURES FOR CEPHAS

Your scheduling board MUST support:

### ⭐ Installer KPI colour ranking  
Green = strong  
Orange = average  
Red = poor

### ⭐ Order color by status  
Pending = grey  
Assigned = blue  
OnTheWay = teal  
MetCustomer = gold  
OrderCompleted = purple  
Docket stages = brown  
ReadyForInvoice = aqua  
Invoiced = silver  
Completed = green  

### ⭐ Building restrictions  
Some buildings are high-risk and require senior SI.

### ⭐ Long-press to duplicate task  
Used for mass SDU bookings.

---

# END OF SCHEDULER MODULE
