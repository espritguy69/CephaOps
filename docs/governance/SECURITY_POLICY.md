# SECURITY\_POLICY.md

\# SECURITY\_POLICY

CephasOps Security Guidelines



---



\## 1. Supported Versions



\- Only the `main` and `develop` branches are considered supported.

\- Security fixes should be backported to `main` as soon as possible.



---



\## 2. Reporting Vulnerabilities



If you discover a security issue:



\- Do \*\*not\*\* open a public issue.

\- Contact the maintainer / internal security contact directly.

\- Include:

&nbsp; - Steps to reproduce

&nbsp; - Impact

&nbsp; - Any logs or screenshots (without secrets)



---



\## 3. Secrets Management



\- Never commit:

&nbsp; - Connection strings

&nbsp; - API keys

&nbsp; - Passwords

\- Use:

&nbsp; - Environment variables for local development.

&nbsp; - Secure secret stores (e.g., Azure Key Vault, AWS Secrets Manager) for production.



---



\## 4. Data Protection



\- All customer-related data is considered sensitive.

\- Logs should not contain:

&nbsp; - Full addresses

&nbsp; - IDs that can be abused

\- Dockets, photos and MRA PDFs are stored in secure file storage with access control.



---



\## 5. Authentication \& Authorization



\- JWTs must be signed with strong keys.

\- Tokens should have a reasonable expiry.

\- Role and company access must be validated on each request.



---



\## 6. Dependencies



\- Regularly run dependency checks (e.g., `dotnet list package --vulnerable`, `npm audit`).

\- Upgrade critical security fixes promptly.



