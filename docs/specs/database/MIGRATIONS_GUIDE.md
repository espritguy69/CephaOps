# MIGRATIONS\_GUIDE.md

\# MIGRATIONS\_GUIDE

CephasOps – Database Migration Guidelines



---



\## Tooling



\- Use EF Core migrations.

\- Migration naming:

&nbsp; - `AddOrdersAndInvoices`

&nbsp; - `AddRmaRequest`

&nbsp; - `AddCostCentreColumnToInvoiceLine`



---



\## Workflow



1\. Make domain/entity changes in `CephasOps.Domain`.

2\. Update `CephasOps.Infrastructure.Persistence.Configurations`.

3\. Run:

&nbsp;  - `dotnet ef migrations add <Name> -p CephasOps.Infrastructure -s CephasOps.Api`

4\. Review the generated migration.

5\. Run migrations locally:

&nbsp;  - `dotnet ef database update`



---



\## Multi-Company Considerations



\- All new tables must include:

&nbsp; - `CompanyId` (Guid)

&nbsp; - `CreatedAt`, `UpdatedAt`

&nbsp; - `CreatedBy`, `UpdatedBy`

\- If adding a new table without `CompanyId`, document in this file why it is global (e.g., static lookup table).



---



\## Deploying Migrations



\- Avoid auto-migrating in production on API startup.

\- Use a controlled migration process (pipeline or manual) in:

&nbsp; - staging

&nbsp; - production.



