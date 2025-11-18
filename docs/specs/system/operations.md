# System Operations & Background Jobs

This document describes key background jobs that keep the system healthy and storage usage under control.

Currently defined jobs:

- `SnapshotCleanupJob` – cleans up temporary snapshots created by the Email Parser.

---

## 1. SnapshotCleanupJob

### 1.1 Purpose

The Email Parser generates small **snapshot files** (one-page PDF/image of the first worksheet) for each Parse Session.

These snapshots are:

- **Temporary** when attached only to the Parse Session
- **Permanent** once attached to an Order as an attachment/docket

To avoid growing disk usage, we run a cleanup job to:

- Delete temporary snapshots older than **7 days**
- Keep snapshots that are actually linked to Orders

---

### 1.2 Snapshot Types

We distinguish between two logical types:

- `ParseSessionSnapshot`
  - Snapshot attached only to a `parseSessionId`
  - Used for human review / troubleshooting
  - Safe to delete after 7 days if not attached to an order

- `OrderAttachmentSnapshot`
  - Snapshot attached to a specific `orderId`
  - Part of permanent order record
  - **Never auto-deleted**

---

### 1.3 Retention Rules

| Snapshot Type            | Retention                  | Auto-Delete |
|--------------------------|----------------------------|------------|
| ParseSessionSnapshot     | 7 days from `createdAt`    | Yes        |
| OrderAttachmentSnapshot  | Indefinite (permanent)     | No         |

---

### 1.4 Job Behaviour

**Name:** `SnapshotCleanupJob`  
**Schedule:** Daily (e.g. 03:00 local time)  

Pseudo-logic:

```sql
DELETE FROM SnapshotFiles
WHERE snapshotType = 'ParseSessionSnapshot'
  AND createdAt < NOW() - INTERVAL '7 days'
  AND NOT EXISTS (
    SELECT 1
    FROM OrderAttachments oa
    WHERE oa.fileId = SnapshotFiles.fileId
  );
```

Implementation details (example):

- Storage may be:
  - Database + file system, or
  - Object storage (S3, Azure Blob)
- The job should:
  - Mark file as deleted in DB
  - Trigger physical delete in storage

---

### 1.5 Monitoring & Logging

The job should log:

- `runAt`: when the job started
- `deletedCount`: number of snapshots deleted
- `errors`: any error messages

Optional:

- Dashboard tile: “Storage savings from snapshot cleanup over last 30 days”

---

## 2. Future Jobs (Placeholders)

Other jobs can be documented here in future:

- `OrderKpiRecalculationJob`
- `InvoiceAgingJob`
- `ReminderNotificationJob`
