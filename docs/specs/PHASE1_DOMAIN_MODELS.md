# TECH_STACK_DECISION  
CephasOps – Technology Stack (Phase 1)

This document summarizes the **official technology choices** for CephasOps Phase 1 (ISP vertical).  
All components are selected for reliability, long-term maintainability, and strong developer tooling support (Cursor, GitHub Copilot, etc.).

---

## 1. Backend (API + Workers)

### **Runtime & Framework**
- **ASP.NET Core 8 Web API**  
  Modern, fast, cross-platform framework suited for clean architecture.

### **Architecture**
- Clean Architecture:  
  - `CephasOps.Api` (HTTP boundary)  
  - `CephasOps.Application` (use cases, handlers, validation)  
  - `CephasOps.Domain` (entities, VOs, enums, domain rules)  
  - `CephasOps.Infrastructure` (EF Core, email worker, file storage, integrations)  
  - `CephasOps.Workers.EmailParser` (POP3/IMAP ingestion)

### **Data Layer**
- **Entity Framework Core 8** (code-first)
- **PostgreSQL**  
  - Reliable, open-source, strong JSONB support  
  - Ideal for structured + semi-structured data (parser mappings)

### **Authentication & Authorization**
- **JWT Authentication**
- **Role-based Authorization (RBAC)**  
  - Multi-company scope enforced at middleware & application layer  
  - Users can have different roles per company

### **Logging & Monitoring**
- **Serilog**
  - Structured logging to console, file, Seq, or cloud log sinks
  - Request/response logging
  - Integration logs (email parser, TIME portal, storage, RMA)

### **Background Jobs & Scheduling**
- **Hangfire** or **Quartz.NET**  
  Used for:
  - KPI recalculation  
  - Late docket notifications  
  - Invoice aging jobs  
  - Parser polling (if not using dedicated worker)  
  - PNL snapshot jobs

### **File Storage**
- File system (local dev) → S3-compatible storage (production)
  - Stores docket scans, Excel attachments, job photos, MRA PDFs

---

## 2. Frontend – Admin / Ops Web App

### **Framework**
- **React + TypeScript**  
  - Maintainable, scalable for enterprise operations UI.

### **Build Tool**
- **Vite**
  - Fast dev server + optimized builds

### **Styling**
- **TailwindCSS**
  - Utility-first CSS for fast UI building  
  - Consistent design system for operations screens

### **Data Fetching**
- **TanStack Query (React Query)**  
  - Server state management for:
    - Orders list  
    - Scheduler data  
    - Inventory stock  
    - Invoices  
    - Buildings & splitters

### **Routing**
- **React Router**
  - `/dashboard`, `/orders`, `/scheduler`, `/inventory`, `/invoices`, `/pnl`, `/settings`, etc.

### **State/Context**
- Light usage of React Context for:
  - Auth tokens  
  - Active company  
  - User preferences  
- Everything else handled by React Query.

---

## 3. Frontend – Service Installer PWA (Mobile App)

### **Framework**
- **React PWA (Progressive Web App)**  
  Optimized for field technician usage.

### **Mobile Capabilities**
- **Camera API**  
  - Upload router installation photos  
  - Scan serial numbers via PWA camera

- **GPS API**  
  - Capture coordinates on:
    - On The Way  
    - Met Customer  
    - Completed

### **Offline Support**
- **IndexedDB or Local Storage**
  - Store queued events (photos, status updates) if network unstable  
  - Automatic sync when online

### **UI**
- Mobile-first TailwindCSS  
- Minimal pages:
  - `/login`
  - `/jobs`
  - `/jobs/:id`
  - `/materials`
  - `/profile`

---

## 4. Email Parser Worker

### **Worker Runtime**
- `.NET 8 Console Application`

### **Responsibilities**
- Poll POP3/IMAP  
- Download TIME activation/assurance emails  
- Parse Excel attachments (EPPlus / ClosedXML)  
- Apply Parser Rules  
- Post parsed payload → `CephasOps.Api` internal endpoint  
- Store raw files in storage bucket

### **Why separate worker?**
- Prevents email parsing from blocking API  
- Can scale independently  
- Cleaner architecture boundary (IO heavy tasks separate)

---

## 5. General Infrastructure

### **Containerization**
- **Docker**  
  - Backend API  
  - Email Parser Worker  
  - Frontend Admin  
  - SI PWA  
  - PostgreSQL (local)

- **Docker Compose**  
  - Local dev environment
  - `docker-compose up` spins up:
    - API
    - DB
    - Email Parser Worker
    - Frontend
    - Local storage bucket (e.g. MinIO)

### **CI/CD**
- **GitHub Actions**
  - Frontend build & deploy  
  - Backend build, test, publish  
  - Docker image build & push  
  - Automated lint/test on PRs  
  - Secrets stored in GitHub Secrets (never in repo)

### **Environments**
- `local`  
- `staging`  
- `production`

---

## 6. Reasoning Summary (Why This Stack?)

- .NET 8 provides **performance, structure, and long-term support**.
- PostgreSQL is robust and ideal for multi-company operations.
- Clean architecture aligns with CephasOps modular design.
- React + Tailwind enables **fast, consistent UI**.
- React Query drastically simplifies server-state management.
- The SI PWA avoids store deployment and supports:
  - Camera  
  - GPS  
  - Offline caching  
- Docker enables uniform development and predictable deployments.
- GitHub Actions gives reliable CI/CD with no vendor lock-in.

---

## 7. Phase-2 Ready
This stack can easily expand to:

- Barbershop CRM (Kingsman)  
- Travel booking modules (Menorah Travel)  
- Multi-vertical analytics  
- External partner integrations (Celcom, Digi, U Mobile, TM, etc.)

---

This revised version is clean, consistent with your architecture files, and ready to add into your official documentation set.

