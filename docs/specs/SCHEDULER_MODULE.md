# SCHEDULER\_MODULE.md

\# SCHEDULER\_MODULE

CephasOps – Scheduler Module Specification



The Scheduler module provides calendar-based job planning and resource allocation.



---



\## Responsibilities



\- Visualise daily/weekly workload per SI

\- Enable drag-and-drop assignment and rescheduling

\- Enforce basic rules:

&nbsp; - No overlapping jobs per SI

&nbsp; - Respect SI working hours



---



\## Data



The Scheduler operates on top of:



\- `Order` (with `AppointmentDateTime` \& `AssignedInstallerId`)

\- `ServiceInstaller` (availability, type, region)



No separate “Schedule” entity is strictly required in Phase 1; we read/write from Orders.



---



\## Features



\- Day and Week views.

\- Filters:

&nbsp; - Company

&nbsp; - Partner

&nbsp; - Region

&nbsp; - Installer type (InHouse/Subcon)

\- Drag \& drop:

&nbsp; - Move an order from one installer to another.

&nbsp; - Move an order to a different time slot.



---



\## API



\- `GET /api/scheduler`

\- `POST /api/scheduler/orders/{orderId}/reschedule`



Detailed in `spec/api/scheduler.api.md`.



---



\## UI



See:



\- `storybook/PAGES.md` – `/scheduler` route.

\- `storybook/WIREFRAMES.md` – day/week grid layout.



