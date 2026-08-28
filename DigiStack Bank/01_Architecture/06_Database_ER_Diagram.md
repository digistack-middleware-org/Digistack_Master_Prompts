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
migration script runs. Full table roadmap (redraw trigger at each):

P01 versions:
  app_config       (v1)  — PostgreSQL connectivity test
  users            (v2)  — username, password_hash, role
  accounts         (v3)  — account_id, balance
  accounts.is_frozen (v6) — freeze/unfreeze flag added to accounts
  (JNDI pool replaces direct JDBC at v7 — no schema change)

P02 versions (still PostgreSQL, digistack_bank schema):
  accounts.customer_id, users.customer_id, users.name (v15) — multi-account support
  beneficiary      (v15) — register transfer targets
  fund_transfer    (v15) — async transfer ledger
  (v16 SOAP/REST — no schema change)
  users.otp_*, users.lockout_* (v17) — MFA/lockout fields
  (v18–v21 — no schema changes)

P02 v22.5 — Migration to Oracle 21c XE on dsb-oracle:
  All above tables migrated to DIGISTACK_CBS PDB (Oracle dialect).
  DDL dialect changes documented in P02 v22.5.

P03 versions (Oracle 21c XE, digistack_cbs — CBS only):
  customer         (v24) — standalone CIF table (backfills from v15 columns)
  accounts.branch_name, accounts.ifsc_code (v23) — display fields
  card             (v28) — card lifecycle
  bod_eod_log      (v29) — BOD/EOD batch records
  loan, emi_schedule (v30) — Loan Origination + Servicing

This is the one diagram meant to be genuinely incremental — redraw
after every schema-changing version, not just at milestones.

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
actually re-run and verified.

Database lifecycle note: dsb-db (PostgreSQL) is the target from P01 v1
through P02 v22. At P02 v22.5, all data migrates to Oracle 21c XE on
the dedicated dsb-oracle VM. At P03 v23 Sprint 4, dsb-db is permanently
decommissioned (final pg_dump archived, VM shut down, snapshotted once,
deleted). From P03 v23 onward, only dsb-oracle/DIGISTACK_CBS is drawn
in this diagram.

Next expected change: v2 adds `users`.