# CSIOS / CephasOps API – Split Spec (All Files in One)

Below are the contents you can split into individual files under `docs/specs/api/`:

---

## File: docs/specs/api/_index.md

# CSIOS / CephasOps API – Index

This folder contains the **high-level REST API specification** for:

- **CSIOS** – Cephas Service Installer Operations System (ISP vertical)
- Future verticals (Kingsman Retail, Menorah Travel) that will later extend the same patterns

The API is:

- **Multi-company aware** (Cephas, Kingsman, Menorah, etc.)
- **Site-aware** (warehouses, branches, outlets)
- Designed to work with the **Settings Module** (for workflow, KPI, parser, and rates configuration)

---

## Files

- `auth.md` – Authentication & headers (multi-company context)
- `orders.md` – Orders / Jobs (Activation, Modification, Assurance, etc.)
- `email_parser.md` – Email / Excel ingestion into orders
- `status_kpi.md` – Status transitions and KPI tracking
- `scheduler.md` – Calendar scheduling and assignments
- `inventory.md` – Materials, serialized CPE, installer inventory
- `buildings_splitters.md` – Buildings, ODF, splitters, ports
- `partners_installers.md` – ISP partners and service installers
- `dockets.md` – Docket upload, review, and partner feedback
- `invoicing.md` – Invoice lifecycle and payment
- `reporting.md` – Dashboards and reports (KPI, partners, stock, aging)
- `settings_api.md` – API-level access to configuration (KPI, status flow, material templates)

---

## Base URLs (Examples)

- Production: `https://api.csios.cephas.com.my`
- Staging: `https://staging-api.csios.cephas.com.my`
- Local: `http://localhost:5000`

---

## Multi-Company Model (Short)

The **Multi-Company Module** & **Settings Module** define behaviour:

- `companyId` – Legal entity / brand (e.g. `CEPHAS`, `KINGSMAN`, `MENORAH`)
- `siteId` – Warehouse, outlet, or office

APIs use:

- Headers: `X-Company-Id`, `X-Site-Id`
- Or explicit `companyId`, `siteId` in query/body, where needed

For full architecture, see:

- `docs/architecture/MULTI_COMPANY_MODULE.md`
- `docs/architecture/SETTINGS_MODULE.md`

---

