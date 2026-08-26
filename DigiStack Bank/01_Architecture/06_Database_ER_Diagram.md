ID: ARCHVIZ06
Version: 0.1 (scaffold)
Status: Not Populated

Title: Database ER Diagram

Imports:
STD (§Database, §Primary/Foreign Key conventions)

Populate At: Starts P01 v1 (app_config), grows every version that adds
a table

---

## What this will show
Entity-relationship diagram, growing table by table as each version's
migration script runs: app_config (v1) → users (v2) → accounts (v3) →
accounts.is_frozen (v6) → ... This is the one diagram in this folder
meant to be genuinely incremental, redrawn after every schema-changing
version rather than once at a single milestone.

## ER Diagram (as of P01 v1)

```
┌───────────────────────────┐
│ app_config                │
│───────────────────────────│
│ (columns per                │
│  V1__create_app_config.sql, │
│  not itemized in P01 v1's   │
│  Sprint text beyond "one    │
│  table, read on page load") │
└───────────────────────────┘
```

Single table, no relationships — this is v1's one PostgreSQL
connectivity test (a read on Home page load). `users` (v2) is the next
table, unrelated to this one (login, not connectivity).

Migration file this diagram reflects: `V1__create_app_config.sql`
(per STD's `V<N>__description.sql` naming).

## Status
**Reset — 2026-08-25 (full project reset #2).** The schema shown above
is still the correct v1 target, but the physical database (dsb-db)
was lost the same day this was drawn — `app_config` does not currently
exist anywhere. Treat as a rebuild target until v1's migration is
actually re-run and verified. This is the one diagram meant to be
genuinely incremental — redraw after every schema-changing version.
Next expected change: v2 adds `users`.