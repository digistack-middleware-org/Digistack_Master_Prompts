ID: SESSION01
Version: 1.9
Status: Active

Title: Session State — Global Pointer

---

## Currently Active

Folder:   02_Application_Development/
Part:     P01 — Foundation
Version:     v12 — WAS SSL Configuration (End-to-End)
Sprint:   Sprint 1
Status:   NOT YET STARTED
VM Status: dsb-dmgr ON | dsb-node02 ON | dsb-ihs ON | dsb-db ON

---

## Load Instructions (every session)

Project Files (always loaded automatically):
- CONTEXT_PACK.md
- SESSION_STATE.md

Upload manually each session:
- CURRENT_SPRINT.md (contains active version's 8 sprints only)

Upload only at version sign-off:
- Progress_Log.md  (never store passwords/credentials in it — see Progress_Log Environment Notes)

Do Not Load:
- P01_Sprint_Plan.md (content already in CURRENT_SPRINT.md — EXCEPT when
  CURRENT_SPRINT.md has not been prepared for the active version; then
  upload P01_Sprint_Plan.md directly instead, per Progress_Log.md note)
- P02/P03/P04 files (not started)
- ARCH01/ARCH02/IDX/RACI01 (only if architectural question arises)
- 01_Architecture diagram files (only when updating diagrams)

---

## Standing Code Discipline (added 2026-09-11, post-v8)

All JSPs (Home, Login, Dashboard, Account, Deposit, Withdraw, Freeze,
Unfreeze) have ALL CSS extracted to css/common/common.css and
css/pages/*.css. JSPs contain ONLY logic and structure — no <style>
blocks, no inline style="" attributes.

Two intentional exceptions that stay inline (documented, not gaps):
1. style="display:none" on balance toggle elements — JavaScript
   controls this at runtime via element.style.display.
2. dbConnStatus colour in Home.jsp — JSP EL expression
   (${dbConnStatus == 'Connected' ? ... : ...}), cannot be static CSS.

Every NEW JSP or JSP edit from v8.5 onward must follow the same rule:
logic/structure in the JSP, styling in the matching css/pages/<name>.css
file, shared styles in css/common/common.css. No new inline styles or
<style> blocks are to be introduced.

---

## Version Pins (WAS ND / PostgreSQL / IHS CONFIRMED; Oracle / ojdbc8 / MQ still target)

WAS ND:       9.0.5.28   (CONFIRMED at P01 v5 sign-off, 2026-09-01)
PostgreSQL:   16          (CONFIRMED at P01 v5 sign-off, 2026-09-01;
                           decommissioned at P03 v23)
Oracle DB:    21c XE      (confirms at v22.5 sign-off)
ojdbc8.jar:   latest      (confirms at v22.5 sign-off)
IHS:          9.0.5.28   (CONFIRMED running at P01 v4.5)
IBM MQ:       9.3.x/9.4.x (confirms at P02 v19)
OS:           RHEL 8.x   (CONFIRMED on dsb-dmgr, P01 v1 Sprint 2)

---

## VM Status

dsb-dmgr:   ON — built and running
            WAS ND 9.0.5.28 | DMgr profile: devdsbindmgr01
            Also hosts devdsbinnode01 (cluster member 1)
            Install path: /apps/IBM/WebSphere/AppServer/
dsb-db:     ON — built and running
            Target spec: 2 vCPU / 2 GB RAM (never resized — Oracle
            runs on dsb-oracle, not here)
            PostgreSQL 16 only (P01 v1 through P02 v22)
            Decommissioned at P03 v23 Sprint 4 (final pg_dump,
            snapshot, VM deleted — PostgreSQL ceases to exist)
dsb-oracle: OFF — powers on at P02 v22.5
            Target spec: 2 vCPU / 4 GB RAM / 60 GB disk
            Oracle 21c XE / DIGISTACK_CBS PDB (v22.5 onward)
            NEVER shares host with dsb-db
dsb-node02: ON — powered on at P01 v5
            WAS ND 9.0.5.28 running
            (install path corrected: /apps/IBM/WebSphere/AppServer/)
dsb-ihs:    ON — powered on at P01 v4.5, confirmed running IHS 9.0.5.28
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
| 2026-09-11 | Roadmap updated — v8.5 and v9.5 inserted into P01_Foundation.md / P01_Sprint_Plan.md | P01 v8.5 Sprint 1 — not started |
| 2026-09-30 | Roadmap updated — v9.5 relocated to v14.5 (manual-first); v9 signed off | P01 v10 Sprint 1 — not started |
| 2026-09-30 | Gap-fill pass — pins marked CONFIRMED, v10 pre-flight added (see below), credentials removed from Progress_Log | P01 v10 Sprint 1 — not started |
| 2026-10-07 | v11 signed off — SSL (HTTPS at the Web Tier) complete | P01 v12 Sprint 1 — not started |

---

## v10 Pre-Flight (added 2026-09-30)

Before v10 Sprint 1 — enabling Administrative Security is disruptive:
1. Take backupConfig on dsb-dmgr (and record the archive name in SetupDoc-v10.md).
2. Enabling admin security forces restart of DMgr, both node agents and
   both cluster members + full resync. Plan the outage.
3. After it is on, EVERY wsadmin call (incl. v6 freezeAccount.py) needs
   credentials (-user/-password or soap.client.props). Never hardcode.
4. Decide app-login mechanism before Sprint 3 (see P01_Foundation.md
   v10 "Application Authentication Note").

---

## How to Update This File

At each version sign-off, change:
- Version field to next version
- Sprint field to Sprint 1
- Status to NOT YET STARTED
- VM Status if a new VM powered on this version

Last Updated: 2026-10-07