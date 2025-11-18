📄 README.md — Cephas Service Installer Operations System (CSIOS)
---
# CephasOps – Multi-Company ISP Operations System

CephasOps is an enterprise operations platform built for the Cephas Group, supporting:

- ISP operations for TIME, CelcomDigi, U Mobile (Activation, Assurance, RMA)
- Multi-company workflows (Cephas Sdn. Bhd, Cephas Trading, Kingsman, Menorah Travel)
- Scheduler & SI mobile PWA
- Inventory, buildings/splitters, RMA
- Dockets → Invoicing → Submission ID → PNL
- End-to-end automation with email parsing, KPI tracking & notifications

Built as a clean-architecture .NET backend + TypeScript React frontend with a mobile-first PWA for SIs.

---
![Status](https://img.shields.io/badge/Status-Active-brightgreen)
![.NET](https://img.shields.io/badge/.NET-8.0-blueviolet)
![React](https://img.shields.io/badge/React-Typescript-61dafb)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15-blue)
![Clean Architecture](https://img.shields.io/badge/Architecture-Clean-brightgreen)
![Docker](https://img.shields.io/badge/Containerized-Docker-0db7ed)
![CI/CD](https://img.shields.io/badge/GitHub-Actions-blue)
![License](https://img.shields.io/badge/License-Proprietary-orange)
![Coverage](https://img.shields.io/badge/Tests-InProgress-yellow)


# 🔥 Features

### ISP Operations
- Activation, Assurance, Modification orders
- Email-based parsing using mapping rules
- Automatic material planning based on building type
- Splitter & standby port management
- Scheduler view with drag-and-drop assignment
- SI App with GPS, camera, serial scanning

### Finance & Billing
- Docket capture + review workflow
- Automated invoice line generation using partner rate sheets
- Submission ID & aging tracker
- RMA & MRA handling

### Inventory
- Warehouse + SI stock tracking
- Serial & non-serial materials
- Material movements (Warehouse → SI → Customer → RMA)

### Multi-company scope
- Separate company data
- RBAC per company
- Director view for cross-company PNL

---

# 🧱 Tech Stack

**Backend**
- .NET 10 Web API  
- EF Core + PostgreSQL  
- Serilog  
- Hangfire (jobs)  
- JWT auth  

**Frontend (Admin App)**
- React + TypeScript  
- Vite  
- TanStack Query  
- TailwindCSS  

**Service Installer App (PWA)**
- React mobile PWA  
- Camera + GPS  
- Offline support (IndexedDB)  

**Tooling**
- Docker + Docker Compose  
- GitHub Actions CI/CD  

---

# 📚 Documentation

Full documentation is in `/docs`.

Start reading here:  
👉 `/docs/README.md`

---

# 🏗 Repository Structure

/backend → ASP.NET Core 8 backend
/frontend → React (Admin App)
/frontend-si → Service Installer PWA
/docs → Full architecture + specs + APIs + diagrams
/docs/spec → Module specifications
/docs/spec/api → Endpoint specifications
/docs/spec/database → ERD + schema + migrations
/docs/storybook → UI/UX definitions
/docs/archive → Legacy / old versions

yaml
Copy code

---

# 🚀 Getting Started

## Backend
cd backend
dotnet restore
dotnet ef database update
dotnet run

shell
Copy code

## Frontend
cd frontend
npm install
npm run dev

shell
Copy code

## SI App (PWA)
cd frontend-si
npm install
npm run dev

yaml
Copy code

---

# 🧪 Tests

cd backend/CephasOps.Tests
dotnet test

yaml
Copy code

---

# 🤝 Contributing

Please read:

- `/docs/governance/CODE_OF_CONDUCT.md`
- `/docs/governance/CONTRIBUTING.md`
- `/docs/governance/REVIEW_CHECKLIST.md`

---

# 📄 License

This project is proprietary to **Cephas Group**.  
All rights reserved.