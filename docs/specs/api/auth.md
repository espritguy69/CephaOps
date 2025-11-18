# Authentication & Context

> NOTE: Exact auth mechanism is still to be finalized (e.g. JWT, API Keys, Azure AD).  
> This spec assumes **Bearer JWT tokens + optional multi-company headers**.

---

## 1. Headers

All authenticated calls should include:

```http
Authorization: Bearer <token>
Content-Type: application/json
X-Company-Id: <companyId>   # Optional, for multi-company users
X-Site-Id: <siteId>         # Optional, for site/outlet context
```

### Behaviour

- If `X-Company-Id` is **not provided**:
  - The API uses the **default company** embedded in the token.
- If `X-Company-Id` **is provided**:
  - The user must have permission to access that company.
- `X-Site-Id` is used for:
  - Branch/outlet-specific operations (e.g. warehouse, outlet, building).

---

## 2. Multi-Company Considerations

- Every operational entity (order, stock transaction, invoice, etc.) belongs to **one `companyId`**.
- Group users (e.g. Group Ops / Group Finance) may:
  - Switch company via `X-Company-Id`.
  - Run cross-company reports (where explicitly allowed).

---

## 3. Error Patterns (High-Level)

- `401 Unauthorized` – Missing/invalid token
- `403 Forbidden` – Token valid but no access to:
  - Company
  - Site
  - Resource or operation
- `404 Not Found` – Resource not found or not visible in that company
- `422 Unprocessable Entity` – Validation issues (e.g. illegal status transition)
- `500 Internal Server Error` – Unexpected errors
