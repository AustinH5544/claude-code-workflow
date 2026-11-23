# Database design

A checklist for designing or reviewing a schema, a prompt that runs it, and short refreshers on the principles behind it.

---

## The prompt

```
Review this database design. Don't change anything yet.
{{paste the schema, migration, or describe the feature}}

Read the existing schema and the closest existing models first, and
judge the design against how this codebase already does things.
Go through the checklist in database-design.md (paste it if you can't
see it). For each item: applies or not, and what you found. Then give me:
- the 3 most serious problems, with the fix for each
- the queries this design must serve, and the index for each
- how the migration rolls out on a large table, and how to roll it back
Mark anything you didn't verify as an assumption.
```

---

## Checklist

### Shape
- **One fact in one place.** No column copies data that lives elsewhere unless it's a deliberate, documented denormalization (for speed, or a snapshot of history, such as the price at the time of sale).
- **Right table for the relationship.** One-to-many uses a foreign key on the "many" side. Many-to-many uses a join table, with its own columns if the relationship has data (who added it, when, what role).
- **History vs current state.** If you'll ever need "what was it on March 3", store events or versions, not just a value you overwrite.

### Keys and constraints
- **Primary key** on every table. Surrogate keys (UUID or bigint) for entities; natural keys only when they truly never change.
- **Foreign keys** with a deliberate `ON DELETE` (restrict, cascade or set null). Cascades on big parent tables can delete far more than expected.
- **Unique constraints** for real-world uniqueness (one enrollment per student per course). Don't rely on app code alone, because two requests can race past an app-level check.
- **NOT NULL** by default. Null should mean "unknown", not "I didn't decide".
- **Check constraints or enums** for fixed sets of values and valid ranges.

### Types
- **Money:** integer cents or `numeric`, never float.
- **Time:** `timestamptz` (UTC) for moments. A plain `date` for calendar days that aren't moments (a birthday, a due date). Store the time zone when local time matters (a class at 9 am local time).
- **IDs from outside systems:** text, because they're not numbers you do math on.
- **Booleans that will grow into states:** use a status column instead (`draft`, `active`, `archived`).

### Indexes
- Index for the **queries you actually run**: the WHERE, JOIN and ORDER BY columns, most selective first in a composite index.
- Foreign keys usually need an index (Postgres doesn't add one automatically).
- Every index slows writes and uses space. Don't add one without a query that needs it.
- Watch for queries with no LIMIT, and N+1 patterns (one query per row in a loop).

### Multi-tenancy and access
- Every tenant-owned row carries the tenant ID (organization, school, account), and **every query filters on it**, checked on the server.
- Consider including the tenant ID in unique constraints and composite indexes.
- Know who can read and write each table, and whether row-level security is used.

### Audit and deletes
- `created_at`, `updated_at`, and who made the change, where it matters.
- Records you must be able to prove later (training hours, payments, signatures) get an **append-only audit trail**: who, what, when, old value and new value. Corrections are new rows, never silent edits.
- **Soft delete** (`deleted_at`) vs hard delete: soft delete keeps history but every query must exclude deleted rows, and unique constraints must account for them.

### Concurrency and correctness
- **Read-modify-write races:** two requests read the same value, both pass a check, both write. Fix with one atomic statement (`UPDATE ... SET n = n + 1 WHERE id = ? AND n < limit`, then check rows affected), a unique constraint, or a row lock.
- **Transactions** around changes that must succeed or fail together. Keep them short, with no network calls inside.
- **Idempotency:** anything that can be retried (webhooks, offline sync, job queues, payment callbacks) needs a unique key so a retry can't create a duplicate. Upsert on that key.
- **Optimistic concurrency** (a version column) when two users might edit the same record.

### Migrations
- **Backwards compatible first.** Expand, then migrate, then contract: add the new column, deploy code that writes both, backfill, switch reads, and only then drop the old column.
- Adding a NOT NULL column to a big table needs a default or a backfill. Know whether the change takes a lock that blocks writes.
- Backfill large tables in batches, not one giant UPDATE.
- Know how to roll back. Never edit a migration that has already shipped; write a new one.

### Privacy and security
- Personal data (licenses, birthdates, medical info) only where needed. Encrypt sensitive fields, and keep them out of logs.
- Parameterized queries only, never string-built SQL.
- Retention: how long is data kept, and how is it deleted when required?

---

## Refreshers

- **Normalization.** 1NF: one value per cell, no repeating groups. 2NF: every column depends on the whole key. 3NF: no column depends on another non-key column. Normalize first, then denormalize on purpose, with the reason written down.
- **ACID.** Atomic (all or nothing), Consistent (constraints hold), Isolated (concurrent transactions don't see each other's half-done work), Durable (committed means saved).
- **Isolation levels.** Read committed (the Postgres default) still allows two transactions to read the same value and both act on it. Repeatable read and serializable prevent more anomalies but can fail with errors that must be retried.
- **Locking.** Pessimistic: `SELECT ... FOR UPDATE` locks the row until the transaction ends. Optimistic: check a version number at write time and retry on conflict.
- **Index types.** B-tree for equality and ranges (the default). GIN for full-text search, arrays and JSON. Partial indexes for a subset (`WHERE deleted_at IS NULL`). Unique indexes enforce rules.
- **Case-insensitive search.** `ILIKE`, `lower(col)` with a matching index, `citext`, or (in Prisma) `mode: "insensitive"`.
- **Offset vs cursor paging.** Offset (`skip`, `take`) is simple but slows down on deep pages and can skip or repeat rows when data changes. Cursor paging (`WHERE (created_at, id) < (?, ?)`) stays fast and stable.
- **OLTP vs reporting.** Heavy reports run against a replica, a summary table or a materialized view, not the live transactional tables.
