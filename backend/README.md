\# Backend – CephasOps (.NET 8 Web API)



This folder contains the backend services for CephasOps, implemented using:



\- .NET 8 Web API

\- Clean architecture

\- Entity Framework Core

\- PostgreSQL

\- JWT authentication

\- Serilog logging

\- Hangfire jobs



---



\# 🧱 Project Structure



/Csios.Api → Controllers, DTOs, Middleware, Mappers

/Csios.Application → Use cases, Validators, Services

/Csios.Domain → Entities, Value Objects, Aggregates

/Csios.Infrastructure → EF Core, Repository, File Storage, Email, Parsers

/Csios.Tests → Unit \& Integration tests



yaml

Copy code



---



\# 🚀 Running Locally



dotnet restore

dotnet ef database update

dotnet run



yaml

Copy code



Environment configuration uses:



appsettings.json

appsettings.Development.json

.env (optional)



yaml

Copy code



---



\# 📄 Documentation



Backend is driven by:



\- `/docs/spec/\*.md` (module specifications)

\- `/docs/spec/api/\*.md` (API endpoints)

\- `/docs/spec/database/\*.md` (schema \& migrations)

