# PARSER\_RULES\_MODULE.md

\# PARSER\_RULES\_MODULE

CephasOps – Parser Rules Module



Parser rules drive how emails \& Excel attachments are converted into orders and RMA records.



---



\## Responsibilities



\- Define patterns to classify emails:

&nbsp; - Activation, Assurance, MRA, etc.

\- Map Excel columns to typed fields.

\- Allow versioning \& toggling of rules.



---



\## Entity: ParserRule



See `PHASE1\_DOMAIN\_MODELS.md`.



---



\## Flows



\### 1. Email Classification



Parser worker:



1\. Downloads email.

2\. Matches `From` and `Subject` against active ParserRules.

3\. If match:

&nbsp;  - Loads corresponding `ColumnMappings`.

&nbsp;  - Parses Excel accordingly.



\### 2. Rule Management



Admin can:



\- Add new rules for new templates.

\- Deactivate old rules.

\- Test rules using sample files (future enhancement).



---



\## API



\- `GET /api/settings/parser-rules`

\- `GET /api/settings/parser-rules/{id}`

\- `POST /api/settings/parser-rules`

\- `PUT /api/settings/parser-rules/{id}`



(See `spec/api/parser.api.md`.)



