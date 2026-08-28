ID: SESSION01
Version: 1.5
Status: Active

Title: Session State — Global Pointer

---

## Currently Active

Folder:   02_Application_Development/
Part:     P01 — Foundation
Version:  v1 — Project Setup & Enterprise Architecture
Sprint:   Sprint 1 — Provision VM + Validate WAS Install
Status:   NOT YET STARTED

---

## Load Instructions (every session)

Project Files (always loaded automatically):
- CONTEXT_PACK.md
- SESSION_STATE.md

Upload manually each session:
- CURRENT_SPRINT.md (contains active version's 8 sprints only)

Upload only at version sign-off:
- Progress_Log.md

Do Not Load:
- P01_Sprint_Plan.md (content already in CURRENT_SPRINT.md)
- P02/P03/P04 files (not started)
- ARCH01/ARCH02/IDX/RACI01 (only if architectural question arises)
- 01_Architecture diagram files (only when updating diagrams)

---

## Version Pins (target only — not confirmed)

WAS ND:       9.0.5.28   (confirms at P01 v1 sign-off)
PostgreSQL:   16          (confirms at P01 v1 sign-off;
                           decommissioned at P03 v23)
Oracle DB:    21c XE      (confirms at v22.5 sign-off)
ojdbc8.jar:   latest      (confirms at v22.5 sign-off)
IHS:          9.0.5.28   (confirms at P01 v8)
IBM MQ:       9.3.x/9.4.x (confirms at P02 v19)
OS:           RHEL 8.x 

---

## VM Status

dsb-dmgr:   OFF — not built (reset #2, 2026-08-25)
dsb-db:     OFF — not built (reset #2, 2026-08-25)
            Target spec: 2 vCPU / 2 GB RAM (never resized — Oracle
            runs on dsb-oracle, not here)
            PostgreSQL 16 only (P01 v1 through P02 v22)
            Decommissioned at P03 v23 Sprint 4 (final pg_dump,
            snapshot, VM deleted — PostgreSQL ceases to exist)
dsb-oracle: OFF — powers on at P02 v22.5
            Target spec: 2 vCPU / 4 GB RAM / 60 GB disk
            Oracle 21c XE / DIGISTACK_CBS PDB (v22.5 onward)
            NEVER shares host with dsb-db
dsb-node02: OFF — powers on at P01 v5
dsb-ihs:    OFF — powers on at P01 v4.5
dsb-mq:     OFF — powers on at P02 v19
dsb-monitor:OFF — powers on at P04 v31
dsb-elk:    OFF — powers on at P04 v32
dsb-tracing:OFF — powers on at P04 v33
dsb-tomcat: OFF — powers on at P03 v26

---

## Pause/Resume Log

| Date       | Event                        | Resume Point                    |
|------------|------------------------------|---------------------------------|
| 2026-08-11 | Reset #1 — VM + chat lost    | P01 v1 Sprint 1 — not started   |
| 2026-08-25 | Reset #2 — VM + chat lost    | P01 v1 Sprint 1 — not started   |

---

## How to Update This File

At each version sign-off, change:
- Version field to next version
- Sprint field to Sprint 1
- Status to NOT YET STARTED
- VM Status if a new VM powered on this version

Last Updated: 2026-08-25