# ERD.md

\# ERD

CephasOps – Entity Relationship Diagram (Text Overview)



A graphical ERD should be generated in a DB tool and exported as PNG/SVG.



Below is a textual relationship summary.



---



\## Relationships



\- Company 1–\* User

\- Company 1–\* Role

\- User \*–\* Role (via UserRole)

\- Company 1–\* Order

\- Order 1–\* OrderStatusLog

\- Order \*–1 Building

\- Building 1–\* Splitter

\- Splitter 1–\* SplitterPort

\- Order \*–1 ServiceInstaller

\- Order \*–1 Partner

\- Order 1–\* InvoiceLine

\- Invoice 1–\* InvoiceLine

\- Company 1–\* Invoice

\- Company 1–\* Material

\- Material 1–\* MaterialStock

\- Material 1–\* MaterialMovement

\- Material 1–\* RmaRequest

\- Company 1–\* ParserRule

\- Company 1–\* NotificationSetting

\- Company 1–\* CostCentre



