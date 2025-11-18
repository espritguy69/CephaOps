
# 📦 CephasOps (CSIOS) – Cephas Service Installer Operations System
### *Unified Installer Workflow Platform for ISP Partners (TIME, CelcomDigi, etc.)*

CephasOps is an end-to-end workflow automation platform built for managing ISP Service Installer operations.  
It provides one centralized system to control:

- Email → Excel → Order automation  
- Installer scheduling & job routing  
- Building & splitter mapping  
- Docket management  
- Inventory & serial tracking  
- KPI & status enforcement  
- Partner invoicing  
- Multi-company operations  
- Background job automation

CephasOps is optimized for high-volume daily job ingestion (100–1000+/day).

---

# 🚀 Key Features

### 📥 Email Parser
- Parses Excel + raw email orders from partners  
- Creates Parse Sessions  
- Human review or Auto-Approve  
- Snapshots of first worksheet page  
- Automatic cleanup after 7 days

### 🧭 Order Management
- Canonical order data model  
- Multi-company, multi-site  
- Full job lifecycle  
- Status & KPI enforcement  
- Attachments & snapshots

### 📅 Scheduler
- Visual SI daily calendar  
- Drag & assign  
- Unassigned job pool  
- Partner/time filters

### 📦 Inventory Control
- Warehouse + Installer level  
- Serial tracking  
- Stock movements  
- Assurance swaps

### 🏗 Buildings & Splitters
- Port assignment  
- Standby port logic  
- 1:8 / 1:16 / 1:32 rules

### 📄 Dockets
- Upload, validate, review  
- Partner portal alignment  
- Rejections tracking

### 💸 Invoicing
- Invoice generation  
- Partner uploads  
- Payment tracking  
- Invoice aging reports

### 📊 Reporting
- Daily dashboard  
- KPI reports  
- Inventory consumption  
- Splitter usage

### 🧩 Multi-Company Architecture
- Company-level separation  
- Per-site configurations  
- Parser profiles by company/site  
- Multi-tenant Ready

---

# 🏗 System Architecture Overview

```
                +-------------------+
                | Incoming Emails   |
                | Partner Excel WOs |
                +---------+---------+
                          |
                POST /api/email/parse-excel
                          |
                          v
                  +---------------+
                  | Parse Session |
                  +---------------+
                    | PendingReview
                    | AutoApproved
                          |
       +------------------+--------------------+
       | Approve (Manual) | Auto Approve       |
       v                  v
+--------------+     +--------------+
| Orders       |     | Orders       |
| Created      |     | Created      |
+--------------+     +--------------+
       |
       v
+------------------+
| Attach Snapshots |
+------------------+
       |
       v
+-------------------------------+
| Daily SnapshotCleanupJob      |
| (delete >7d temp snapshots)   |
+-------------------------------+
```

Backend is built as a modular API with clean separation:

- orders  
- email_parser  
- scheduler  
- inventory  
- buildings  
- dockets  
- invoices  
- settings  
- system (background jobs)

---

# 📁 Repository Folder Structure

```
CephasOps/
│
├── src/
│   ├── api/
│   ├── core/
│   ├── modules/
│   ├── jobs/
│   ├── config/
│   └── infrastructure/
│
├── docs/
│   ├── specs/
│   │   ├── api/
│   │   ├── system/
│   │   ├── architecture/
│   │   └── modules/
│   └── diagrams/
│
├── docker/
│   ├── Dockerfile
│   └── docker-compose.yml
│
├── .env.example
├── README.md
└── package.json
```

---

# 🛠 Installation & Setup

## 1. Clone Repository
```
git clone https://github.com/your-org/CephasOps.git
cd CephasOps
```

---

# ⚙️ Environment Variables
Copy environment file:
```
cp .env.example .env
```

### Required Variables

```
APP_ENV=local
APP_PORT=5000

POSTGRES_HOST=postgres
POSTGRES_PORT=5432
POSTGRES_DB=cephasops
POSTGRES_USER=cephas
POSTGRES_PASSWORD=cephas123

JWT_SECRET=super-secret-key
FILE_STORAGE_PATH=/var/data/cephasops/files

PARSER_SNAPSHOT_RETENTION_DAYS=7
```

---

# 🐘 Database Setup (PostgreSQL)
```
npm run migrate
```

---

# 🐳 Docker Setup

### Start
```
docker-compose up -d
```

### Stop
```
docker-compose down
```

---

# 🔐 Running in Production

```
docker-compose -f docker/docker-compose.prod.yml up -d
```

---

# 🧪 Developer Guide

```
npm install
npm run dev
npm run format
npm run lint
npm test
```

---

# 📘 Email Parser Flow

1. Upload Excel → Parse Session  
2. Human review  
3. Auto-approve valid rows (optional)  
4. Snapshot cleanup after 7 days  

---

# 📦 Order Creation Logic

Orders can be created from:

- Manual creation (`POST /api/orders`)
- Email Parser → Parse Session → Approve
- Email Parser AutoApprove
- API Integrations (future)

Each order includes a `source` block.

---

# 🧩 Multi-Company Architecture

Each order belongs to:

```
companyId → siteId → order
```

Parser profiles and settings are scoped per company/site.

---

# ⏱ Background Jobs

Documented in:

```
docs/specs/system/operations.md
```

Jobs include:

- SnapshotCleanupJob  
- Invoice notifier (optional)  
- Docket validator (optional)

---

# 🧵 API Documentation

Located in:

```
docs/specs/api/
```

---

# 🚀 Deployment Checklist

✔ Env configured  
✔ Docker running  
✔ DB migrated  
✔ Parser profile JSON loaded  
✔ Companies + Sites created  
✔ Installer accounts ready  
✔ Buildings imported  
✔ Cron job enabled  

---

# 🎯 Summary

CephasOps provides a complete operational stack for ISP installer workflows, with:

- High-volume order ingestion  
- Status + KPI lifecycle  
- Multi-company architecture  
- Full documentation  
- Scalable Docker deployment  
