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

## Status
Not yet populated. app_config exists per P01 v1's V1__create_app_config.sql
— diagram not yet drawn.