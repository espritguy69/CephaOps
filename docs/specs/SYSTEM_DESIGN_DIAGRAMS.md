# SYSTEM_DESIGN_DIAGRAMS  
CephasOps – System Design Views (Phase 1: ISP Vertical)

This file captures **high-level** and **logical** diagrams of the CephasOps platform:

- How emails become orders
- How Admin / Scheduler / SI apps interact
- How backend services, DB, and storage fit together

---

## 1. High-Level ASCII Flow

End-to-end “business flow” from email → money:

```text
[Partner Email Inbox]
        |
        v
  [Email Parser Worker]
        |
        v
 [CephasOps API Backend]
        |
        +--> [Orders + Assurance]
        |        |
        |        +--> [Inventory & RMA]
        |        |
        |        +--> [Dockets & Docs Storage]
        |
        +--> [Scheduler Calendar]
        |        |
        |        +--> [Admin Web App]
        |        |
        |        +--> [Assign SI]
        |
        +--> [Service Installer PWA]
        |        |
        |        +--> [Order Status Updates]
        |        +--> [Photos + GPS + Materials Used]
        |
        +--> [Billing & Invoicing]
        |        |
        |        +--> [Portal Submission ID]
        |
        +--> [Finance & PNL Views]
                 |
                 +--> [Director Multi-Company Dashboards]
```

Simplified “operations-only” view:

```text
[Email] -> [Email Parser] -> [Orders] -> [Scheduler] -> [SI App]
[SI App] -> [Order Status + Materials] -> [Billing] -> [PNL]
```

---

## 2. Mermaid (Logical – Core Flow)

```mermaid
flowchart LR
  subgraph Partner Side
    E[Email Inbox (TIME)]
  end

  subgraph Workers
    P[Email Parser Worker<br/>POP3/IMAP + Excel Parser]
  end

  subgraph Backend["CephasOps Backend (API + App + Domain + Infra)"]
    O[Orders & Assurance Service]
    SCH[Scheduler Service]
    INV[Inventory & RMA Service]
    B[Billing & Invoicing Service]
    NTF[Notifications Service]
    CFG[Settings & Parser Rules]
  end

  subgraph Frontend["Frontends"]
    ADMIN[Admin / Ops Web App]
    SI[Service Installer PWA]
  end

  subgraph Data["Data Layer"]
    DB[(PostgreSQL<br/>EF Core)]
    FS[(File Storage<br/>Dockets, Photos, MRA PDFs)]
  end

  subgraph Jobs["Background Jobs"]
    J[Hangfire / Quartz<br/>KPI + PNL + Reminders]
  end

  %% Email ingestion
  E --> P
  P --> CFG
  P --> O

  %% Core backend flows
  ADMIN <-->|REST/JSON| O
  ADMIN <-->|REST/JSON| SCH
  ADMIN <-->|REST/JSON| INV
  ADMIN <-->|REST/JSON| B
  ADMIN <-->|REST/JSON| CFG

  SCH --> O
  O --> SCH

  SI <-->|REST/JSON| O
  SI --> INV

  %% Billing -> Finance
  O --> B
  B --> ADMIN
  B --> J

  %% Inventory and RMA
  O --> INV
  INV --> FS

  %% Notifications & Jobs
  O --> NTF
  B --> NTF
  NTF --> ADMIN
  NTF --> SI

  J --> O
  J --> B
  J --> INV

  %% Persistence
  O --> DB
  SCH --> DB
  INV --> DB
  B --> DB
  CFG --> DB
  NTF --> DB
  P --> FS
  O --> FS
```

---

## 3. Mermaid (Clean Architecture View)

```mermaid
flowchart TB
  subgraph Frontend
    A1[Admin Web App<br/>React + TS + Vite]
    A2[Service Installer PWA<br/>React PWA + Camera + GPS]
  end

  subgraph API["CephasOps.Api (HTTP Layer)"]
    C1[Controllers<br/>Orders, Scheduler, Inventory, Invoices, Settings]
    MW[Middleware<br/>Auth + Company Scope + Logging]
  end

  subgraph Application["CephasOps.Application (Use Cases)"]
    UO[Orders Use Cases]
    US[Scheduler Use Cases]
    UI[Inventory Use Cases]
    UB[Billing Use Cases]
    UR[RMA Use Cases]
    UN[Notifications Use Cases]
    UC[Settings & ParserRules Use Cases]
  end

  subgraph Domain["CephasOps.Domain (Core Model)"]
    D1[Entities<br/>Order, Building, Splitter, Invoice, etc.]
    D2[Value Objects<br/>Address, Money, Appointment]
    D3[Enums & Domain Events]
  end

  subgraph Infra["CephasOps.Infrastructure"]
    DB[(PostgreSQL / EF Core)]
    REP[Repositories]
    MAIL[Email Sender]
    STORE[File Storage Adapter]
    SCHED[Hangfire / Quartz]
  end

  subgraph Worker["CephasOps.Workers.EmailParser"]
    W1[POP3/IMAP Client]
    W2[Excel/Email Parser]
  end

  %% Flows
  A1 -->|HTTP| C1
  A2 -->|HTTP| C1

  C1 --> MW
  MW --> UO
  MW --> US
  MW --> UI
  MW --> UB
  MW --> UC
  MW --> UR
  MW --> UN

  UO --> D1
  US --> D1
  UI --> D1
  UB --> D1
  UR --> D1
  UC --> D1
  UN --> D1

  UO --> REP
  US --> REP
  UI --> REP
  UB --> REP
  UR --> REP
  UC --> REP
  UN --> REP

  REP --> DB
  UO --> STORE
  UI --> STORE
  UR --> STORE

  UN --> MAIL
  SCHED --> UO
  SCHED --> UB
  SCHED --> UN

  W1 --> W2
  W2 --> UC
  W2 --> UO
  W2 --> STORE
```

---

## 4. Mermaid (Order Lifecycle Sequence – Optional)

```mermaid
sequenceDiagram
  autonumber
  participant TIME as TIME Email
  participant Worker as Email Parser Worker
  participant API as CephasOps API
  participant App as Admin Web App
  participant SI as SI PWA

  TIME->>Worker: Activation Email + Excel
  Worker->>Worker: Parse email + Excel via ParserRule
  Worker->>API: POST /internal/parsed-orders
  API->>API: Create Order (Status = Pending)

  App->>API: GET /orders?status=Pending
  API-->>App: Orders list
  App->>API: POST /orders/{id}/assign-si
  API-->>App: Order Assigned

  SI->>API: GET /si/jobs (today)
  API-->>SI: Jobs list
  SI->>API: POST /orders/{id}/status OnTheWay
  SI->>API: POST /orders/{id}/status MetCustomer
  SI->>API: POST /orders/{id}/complete (materials + photos)
  API-->>SI: OrderCompleted

  App->>API: POST /orders/{id}/dockets/received
  App->>API: POST /invoices (generate invoice)
  API-->>App: Invoice Draft

  App->>TIME: Upload Invoice + Dockets (portal)
  App->>API: POST /invoices/{id}/submit (with SubmissionId)
  API-->>App: Invoice Invoiced
```

---

This file is fully aligned with CephasOps architecture and Storybook.
