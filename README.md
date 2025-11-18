📄 README.md — Cephas Service Installer Operations System (CSIOS)
# Cephas Service Installer Operations System (CSIOS)
A complete operations management platform for ISP installation, assurance support, splitter tracking, material inventory, docket validation, invoicing, and KPI reporting for Cephas Sdn Bhd.

![Status](https://img.shields.io/badge/Status-Active-brightgreen)
![Tech](https://img.shields.io/badge/Backend-.NET%208-blue)
![Frontend](https://img.shields.io/badge/Frontend-React%20%2F%20Next.js-61DAFB)
![Database](https://img.shields.io/badge/Database-SQL%20Server-blueviolet)
![License](https://img.shields.io/badge/License-Proprietary-orange)
![Coverage](https://img.shields.io/badge/Tests-InProgress-yellow)

---

## 🚀 Overview
CSIOS automates and streamlines all operational workflows for Cephas as a subcontractor handling TIME, Celcom, Digi, and U Mobile installation/assurance jobs.

The system manages:

- Automatic email/Excel parsing  
- Order creation  
- Installer scheduling  
- Splitter port tracking  
- Serialized materials & inventory  
- Job status flow & KPI  
- Docket validation  
- Invoice generation & payment cycle  
- Full reporting & analytics  

---

## ✨ Key Features

### 📨 **Automated Email & Excel Parsing**
- Supports TIME activation, modification, assurance  
- Parses Service ID, address, appointment, router, ONU, CPE  
- Normalizes different formats and date/time layouts  
- Automatically creates orders  

---

### 📋 **Order Management**
- Activation / Modification / Assurance  
- Partner prefix validation (TBBN, TTKT, AWO, CELCOM, DIGI)  
- Auto-assign building & default materials  
- Serial number tracking  

---

### 📅 **Smart Scheduler**
- Drag-and-drop calendar  
- Filter by SI skills, availability, partner, building  
- Auto-check SI skillset (FTTH, FTTR, SDU, RDF, FTTC)  
- Color-coded status  

---

### 🏢 **Buildings & Splitters**
- Splitter mapping (1:8 / 1:12 / 1:32)  
- Tracks port usage  
- Standby port approval enforcement  
- History of which SI used which port  

---

### 📦 **Inventory & Materials**
- Warehouse → SI → Customer flow  
- Serialized tracking for ONU, router, booster  
- Return-to-warehouse flow for failed jobs  
- Assurance job support (old device return)  

---

### 📝 **Docket Management**
- Manual docket upload & review  
- OCR-ready  
- Validation: Serial match, signature, work type  
- Correction & partner rejection workflows  
- 30-minute KPI enforcement  

---

### 💰 **Invoicing & Payment**
- Auto-generate invoice PDF  
- Auto-calculates SI rates  
- Upload to partner portal  
- Tracks 45-day payment cycle  
- Financial dashboard  

---

### 📊 **Reporting & Analytics**
- Installer KPI (job duration & docket timing)  
- Partner performance  
- Splitter capacity  
- Material usage  
- Invoice aging  
- Order throughput  

---

### ⚙️ **Settings Module**
- Materials  
- SI profiles & pay rates  
- Partners  
- Buildings  
- KPI rules  
- Default material templates  
- Blockers  
- Status flow (fully configurable)  

---

## 🏛️ System Architecture



Frontend (React / Next.js)
↓
Backend API (ASP.NET Core 8)
↓
Database (SQL Server)
↓
File Storage (Azure/AWS)
↓
Integrations (Email Inbox, Partner Portal Uploads)


---

## 📂 Folder Structure



/csios/
│
├── backend/
│ ├── Csios.Api/
│ ├── Csios.Domain/
│ ├── Csios.Infrastructure/
│ └── Csios.Tests/
│
├── frontend/
│ └── src/
│
└── docs/
├── SYSTEM_OVERVIEW.md
├── ORDERS_MODULE.md
├── EMAIL_PARSER_MODULE.md
├── STATUS_KPI_SPLITTER_MODULE.md
├── SCHEDULER_MODULE.md
├── INVENTORY_MODULE.md
├── INVOICING_MODULE.md
├── BUILDINGS_SPLITTERS_MODULE.md
├── PARTNERS_INSTALLERS_MODULE.md
├── DOCKETS_MODULE.md
├── REPORTING_MODULE.md
└── SETTINGS_MODULE.md


---

## 🛠️ Installation

### 1️⃣ **Clone repository**
```bash
git clone https://github.com/espritguy69/CephaOps.git
cd CephaOps

2️⃣ Backend Setup (.NET 8)

Install dependencies:

cd backend/Csios.Api
dotnet restore


Run API:

dotnet run

3️⃣ Frontend Setup (React/Next.js)

Install dependencies:

cd frontend
npm install


Run dev mode:

npm run dev

4️⃣ Database Setup (SQL Server)

Run migrations:

dotnet ef database update


Or restore .bak file in SQL Server Management Studio.

5️⃣ Environment Variables

Backend .env sample:

DB_CONNECTION_STRING=Server=localhost;Database=CSIOS;Trusted_Connection=True;
EMAIL_HOST=imap.gmail.com
EMAIL_USER=admin@cephas.com.my
EMAIL_PASS=xxxx
STORAGE_PATH=/storage


Frontend .env sample:

NEXT_PUBLIC_API_URL=http://localhost:5000

🧪 Testing

Backend:

dotnet test


Frontend:

npm test

📦 Docker Deployment

Use:

docker-compose up --build -d

👥 Contributing

Fork the repo

Create a feature branch

Commit changes using conventional commits

Submit PR

🛡 License

This system is proprietary and owned by Cephas Sdn Bhd.
Unauthorized distribution is prohibited.

📧 Contact

For inquiries or support:
support@cephas.com.my

END OF README.md

# Cephas Service Installer Operations System (CSIOS)

## Features
- Automated Email Parsing
- Order Management
- Scheduler
- Inventory & Serialized Material Tracking
- Splitter Port Management
- KPI & Status Engine
- Dockets & Invoicing
- Reporting Dashboards
- Full Settings Module (CRUD)

## Tech
- ASP.NET Core 8 (API)
- React (Frontend)
- SQL Server (DB)
- Azure/AWS (Storage)
