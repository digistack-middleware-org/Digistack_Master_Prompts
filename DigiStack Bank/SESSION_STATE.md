ID: SESSION01
Version: 2.0
Status: Active

Title: Session State — Global Pointer

---

## Currently Active

Folder:   02_Application_Development/
Part:     P01 — Foundation
Version:     v12.5 — Customer Onboarding & Registry-Based Login
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

## Editing Existing Code — Standing Rule (added 2026-10-09)

Claude cannot see the project code between chats. Therefore:
1. Before changing ANY existing file (Java, JSP, CSS, web.xml, pom.xml),
   the user pastes the CURRENT file into the chat.
2. Claude returns the COMPLETE updated file (NDS01 Rule 1), changing only
   what the sprint requires. Never rewrite from memory.
3. Claude states exactly which lines changed and why (Rule 6: no regression).
4. TEST01: all 25 unit tests must still pass after every code change.

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

## Environment Facts Built Through v12 (added 2026-10-09)

Credentials: never stored in project files. wasadmin password, keystore
passwords and DB password are kept in the user's password manager and
supplied by the user when a command needs them.

Cell/cluster: cell devdsbincell01; cluster devdsbinappcluster01;
members devdsbinappclustermember01 (node devdsbinnode01 on dsb-dmgr)
and devdsbinappclustermember02 (node devdsbinnode02 on dsb-node02).
App name in WAS: digistack-bank. Current EAR: digistack-bank-v12.ear.

Hosts: dsb-dmgr 192.168.10.10 | dsb-node02 192.168.10.11 |
dsb-ihs 192.168.10.20 | dsb-db 192.168.10.30.

Security (v10): Administrative + Application Security ON. Users:
wasadmin (console admin), customer1 (Customer group), admin1
(Administrator group). Every wsadmin call needs -user/-password.
FORM login. /Freeze and /Unfreeze = Administrator only (403 for customer).

SSL (v11): IHS HTTPS on 443, self-signed cert digistack-ihs-webtier
(CN=www.digistack.cloud), files in /apps/IBM/HTTPServer/ssl/.
HTTP→HTTPS 301 redirect, path preserved. SSLv2/SSLv3 disabled.

SSL (v12): plugin→WAS over HTTPS (plugin keystore
/apps/IBM/HTTPServer/ssl/plugin-key.kdb). SSL Repertoire
DigiStackInternalSSL referenced by NodeDefaultSSLSettings and
CellDefaultSSLSettings. mTLS on WAS→PostgreSQL: private CA digistack-ca,
files in /etc/postgresql/ssl/ (dsb-db) and
<profile>/ssl/mtls/ (WAS hosts). jdbc/BankDS has ssl, sslmode,
sslcert, sslkey, sslrootcert custom properties; client cert is
digistack-mtls-internal-hop.crt. CI01 §5.2 and §5.3 recorded.

Logs to know: IHS /apps/IBM/HTTPServer/logs/error_log; plugin
/apps/IBM/WebSphere/Plugins/logs/webserver1/http_plugin.log; WAS
SystemOut.log under profiles/<profile>/logs/<server>/.

Lessons carried forward: Admin Console green does NOT prove SSL or
dependencies work; configtest does not check file existence; after any
cert/key change check owner and mode of key files on every host.

Latest backups: backup-v12-signoff.zip (dmgr backups folder),
ihs-v12-signoff.tar.gz (dsb-ihs), pg-v12-signoff.tar.gz (dsb-db).

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
| 2026-09-30 | Gap-fill pass — pins marked CONFIRMED, v10 pre-flight added, credentials removed from Progress_Log | P01 v10 Sprint 1 — not started |
| 2026-10-07 | v11 signed off — SSL (HTTPS at the Web Tier) complete | P01 v12 Sprint 1 — not started |
| 2026-10-09 | v12 signed off — WAS SSL Configuration (End-to-End) complete | P01 v13 Sprint 1 — not started |
| 2026-10-09 | Roadmap updated — v12.5 (Customer Onboarding & Registry-Based Login) inserted between v12 and v13; v13 prerequisite changed to v12.5 | P01 v12.5 Sprint 1 — not started |
| 2026-10-09 | Roadmap updated — v12.5 (Customer Onboarding & Registry-Based Login) inserted between v12 and v13; v13 prerequisite changed to v12.5 | P01 v12.5 Sprint 1 — not started |

---

## How to Update This File

At each version sign-off, change:
- Version field to next version
- Sprint field to Sprint 1
- Status to NOT YET STARTED
- VM Status if a new VM powered on this version
- Add a row to the Pause/Resume Log
- Add any new durable environment facts to "Environment Facts"

Last Updated: 2026-10-09