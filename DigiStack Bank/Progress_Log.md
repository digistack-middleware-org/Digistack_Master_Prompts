# DigiStack Bank — Progress Log

**Instructions:** Update this file yourself after each version is approved (or ask Claude to update it at the end of a sprint). Upload this file — along with the MASTER INDEX and the relevant Part file — at the start of every new chat so Claude knows exactly where to resume.

---

## Folder Status Legend

| Status | Meaning |
|---|---|
| 🔒 Frozen | All versions in this folder signed off (per TCS01 §2.7 rubric). No further edits except a documented correction, logged below. |
| 🔓 In Progress | Actively being built. Only one folder should carry this status at a time. |
| ⏳ Not Started | No work begun. Files may exist as roadmap-only (unbuilt) drafts. |
| ⏸️ Paused | Was In Progress, deliberately set aside — note why and the resume point in that folder's own README. |

---

## Folder Tracker

| # | Folder | Status | Last Completed Version | Next Version | Current Focus (AI Resume one-liner) |
|---|---|---|---|---|---|
| 00 | Core | 🔒 Frozen | — | — | — |
| 02 | Application_Development | 🔓 In Progress | v9 (P01) | v10 (P01) | v9 (Session Management) signed off 2026-09-30 — 32 test cases pass (17 Critical + 12 High + 3 Medium), 25 unit tests pass (TEST01). Sticky sessions via CloneID confirmed, sticky-only baseline proven, M2M replication (DigiStackSessionReplication, single replica) — session survives kill -9 and graceful restart, DRSV0003I + DRSV0019I confirmed. Session timeout 30 min, SessionTimeoutListener, Login page expired message. DB-backed persistence evaluated — sessions table + jdbc/SessionDS, ship decision = M2M. Fault drill INC-v9-001: silent replication failure (wrong domain name), MTTD + MTTR recorded. Next: v10 Sprint 1 (v9.5 relocated to v14.5 on 2026-09-30). dsb-dmgr, dsb-node02, dsb-ihs, dsb-db all ON. Mirrors Multi-Part Folder Detail row below. |
| 03 | Interview_Prep | ⏳ Not Started | — | Interview-1 (P03.1) | Not started — depends on P03 completion |
| 04 | Observability | ⏳ Not Started | — | v31 (P04) | Not started — depends on P03 completion |
| 05 | HA_DR | ⏳ Not Started | — | v36 (P05) | Not started — depends on P04 completion |
| 06 | Multi_Region | ⏳ Not Started | — | v39 (P06) | Not started — depends on P05 completion |
| 07 | WAS_Migration | ⏳ Not Started | — | v44 (P07) | Not started — depends on P06 completion |
| 08 | Automation | ⏳ Not Started | — | v49 (P08) | Not started — depends on P07 completion |
| 09 | Cloud_Migration | ⏳ Not Started | — | v54 (P09) | Not started — depends on P08 completion |
| 10 | Containerization | ⏳ Not Started | — | v74 (P10) | Not started — depends on P09 completion |

> The "Current Focus" column is a one-line mirror of that folder's own README's **AI Resume Context → Next Task**. Update both together — if they ever disagree, the folder's own README governs (same precedent as SetupDoc vs. Environment Notes).

---

## Folder Freeze Rule

A folder moves to 🔒 Frozen only when:
1. Every version's `TestCases-v<N>.md` sign-off rubric (TCS01 §2.7) is satisfied.
2. The Part's own Completion Checklist (in its roadmap file) is fully checked.
3. This file's Cross-Part Dependency Chain has a row for every version in the folder.
4. Promotion tag applied (per EPS01 §3.5/3.6/3.7 as applicable) — `part<N>-release`.

Once frozen, a folder is only reopened for a documented correction — never silently edited. Log any post-freeze correction here:

| Date | Folder | Version | What Changed | Why |
|---|---|---|---|---|
| | | | | |

---

## Multi-Part Folder Detail

> The Folder Tracker above shows one row per folder. Folders `02_Application_Development` and `03_Interview_Prep` each contain multiple Parts with independent lifecycles — tracked in full detail inside that folder's own README, summarized here for a project-wide glance.

### 02_Application_Development

| Part | Status | Last Approved Version | Next Version |
|---|---|---|---|
| P01 — Foundation | 🔓 In Progress | v9 | v10 |
| P02 — Middleware | ⏳ Not Started | — | v15 |
| P03 — Banking Systems | ⏳ Not Started | — | v23 |

### 03_Interview_Prep

| Part | Status | Last Approved Section/Version | Next Section/Version |
|---|---|---|---|
| P03.1 — Interview Drills | ⏳ Not Started | — | Interview-1 |
| P03.2 — Interview Book | ⏳ Not Started | — | Version 1 (Ch. 1) |

### 07_WAS_Migration

| Part | Status | Last Approved Version | Next Version |
|---|---|---|---|
| P07 — WebSphere Migration | ⏳ Not Started | — | v44 |
| P07.1 — Interview Book | ⏳ Not Started | — | Version 1 (Ch. 1) |

### 08_Automation

| Part | Status | Last Approved Version | Next Version |
|---|---|---|---|
| P08 — DevOps Automation | ⏳ Not Started | — | v49 |
| P08.1 — Interview Book | ⏳ Not Started | — | Version 1 (Ch. 1) |

### 09_Cloud_Migration

| Part | Status | Last Approved Version | Next Version |
|---|---|---|---|
| P09 — AWS Migration | ⏳ Not Started | — | v54 |
| P09.1 — Interview Book | ⏳ Not Started | — | Version 1 (Ch. 1) |

### 10_Containerization

| Part | Status | Last Approved Version | Next Version |
|---|---|---|---|
| P10 — Containerization | ⏳ Not Started | — | v74 |
| P10.1 — Interview Book | ⏳ Not Started | — | Version 1 (Ch. 1) |

---
> **Note on Part 3.1 (Interview Preparation):** unlike every other Part,
> this one produces no versioned deployables (no EAR/WAR, no SQL
> migration, no infrastructure) — its "Next Version" entry in the Folder
> Tracker and in `02_Interview_Prep`'s own row above refers to its six
> Interview sub-sections (Interview-1 through Interview-6), not a
> numbered version. Track its progress as a simple checklist against its
> own Completion Checklist rather than the Detailed Version Log /
> Cross-Part Dependency Chain tables below, which assume a numbered
> version with concrete build artifacts.
---
## Interview Preparation (Part 3.1) — Section Completion Status

| Section | Deliverable | Completed? | Score (if applicable) | Notes |
|---|---|---|---|---|
| Interview-1 — Project Walkthrough | 5-min unscripted walkthrough | Not started | — | |
| Interview-2 — WebSphere Administration | ≥80% on random 25-Q draw | Not started | — | |
| Interview-3 — Production Support | 1-page runbook, defended | Not started | — | |
| Interview-4 — Troubleshooting | 3 random scenarios, live | Not started | — | |
| Interview-5 — Banking Production Environment | 10 domain Qs answered | Not started | — | |
| Interview-6 — Mock Interview | Full mock run, scored ≥4/5 all sections | Not started | — | |

---
## Standing Reference Documents (non-versioned — tracked here for completeness)

| Doc ID | Title | Version | Status |
|---|---|---|---|
| IDX | Master Index | 1.4 | Active |
| STD | Standing Rules | 1.14 | Active |
| SOE01 | Golden Image Specification | 1.10 | Active |
| ARCH01 | Enterprise Architecture | 1.0 | Active |
| ARCH02 | Solution Architecture | 1.1 | Active |
| STDGAP01 | Consolidated Standing Standards | 1.1 | Active |
| CAP01 | Capacity & Sizing Standard | 1.1 | Active |
| RACI01 | Operational RACI | 1.1 | Active |


Note: these are reference standards, not versioned Parts — they don't
carry a v<N> in the P01-P78 sequence and aren't subject to the Version
Numbering Freeze. Updated in place (with a version bump + change note,
per STD's Metadata Block Standard) when their content changes.

Sprint Plan Files (not reference standards — no ID/version block):
  P01_Sprint_Plan.md — all 8 sprints for v1–v14.5 (P01)
  P02_Sprint_Plan.md — all 8 sprints for v15–v22 (P02)
  P03_Sprint_Plan.md — all 8 sprints for v23–v30 (P03)
SESSION_STATE.md's Load Instructions refer to "CURRENT_SPRINT.md"
as a manually uploaded file each session. In practice, the relevant
Part's Sprint Plan file (above) is the source — copy the active
version's 8 sprints from it into CURRENT_SPRINT.md before each session,
or upload the full Sprint Plan file directly.
---

## Detailed Version Log

> Add one row per version as it's completed. Keep the most recent entry at the bottom (or top — your preference, just stay consistent).

| Date | Part | Version | Feature | Status (Started / Dev Done / Deployed to WAS / Tested / Approved) | Notes / Issues |
|---|---|---|---|---|---|
| ~~2026-07-30~~ | ~~P01~~ | ~~v1~~ | ~~Project Setup & Enterprise Architecture~~ | **RESET 2026-08-04** | Entry reset per project owner request — P01 v1 is not started. Original row (Approved 2026-07-30) struck through rather than deleted, per this project's "never silently edit" discipline. See Open Questions section below for the full reset decision log. |
| 2026-08-07 | P01 | v1 | Project Setup & Enterprise Architecture | Approved | Signed off per TCS01 §2.7 — 13/13 test cases pass. WAS ND 9.0.5.28 confirmed on dsb-dmgr; PostgreSQL 16 confirmed on dsb-db (digistack_bank DB, digistack_app user). SetupDoc-v1.md is the source record. |
| 2026-08-08 | P01 | v2 | Login & Session | Approved | Signed off per TCS01 §2.7 — 14/14 test cases pass. users table created (SHA-256+salt hashing), Login/Logout with HttpSession working, digistack-bank-v2.ear redeployed over v1. SetupDoc-v2.md is the source record. |
| 2026-08-09 | P01 | v3 | Basic Transaction (Deposit & Withdraw) | Approved | Signed off per TCS01 §2.7 — 17/17 test cases pass. accounts table (FK to users), full Controller->Service->DAO->DB layering, overdraft rejection enforced, ClassLoader policy documented. SetupDoc-v3.md is the source record. |
| ~~2026-08-07~~ | ~~P01~~ | ~~v1~~ | ~~Project Setup & Enterprise Architecture~~ | **RESET 2026-08-11** | Full project reset per project owner request — both lab VM and chat context lost. Entry struck through rather than deleted, per this project's "never silently edit" discipline. WAS ND/PostgreSQL version pins in STD reverted to placeholder. |
| ~~2026-08-11~~ | ~~P01~~ | ~~v2~~ | ~~Login & Session~~ | **RESET 2026-08-25** | Entry reset per full project reset #2 — lab VM + chat context lost again, confirmed with project owner 2026-08-25. Struck through rather than deleted, per this project's "never silently edit" discipline. |
| ~~2026-08-11~~ | ~~P01~~ | ~~v3~~ | ~~Basic Transaction (Deposit & Withdraw)~~ | **RESET 2026-08-25** | Entry reset per full project reset #2 — same event as the v2 reset row above. |
| FILL | P01 | v1 | Project Setup & Enterprise Architecture | Approved | ⚠ BACK-FILL from SetupDoc-v1.md / TestCases-v1.md: sign-off date, test-case counts, key features. Row missing since Reset #2 (2026-08-25); v5 (2026-09-01) already assumes v1–v4.5 done. |
| FILL | P01 | v2 | Login & Session | Approved | ⚠ BACK-FILL from SetupDoc-v2.md / TestCases-v2.md: sign-off date, test-case counts, key features. Row missing since Reset #2 (2026-08-25); v5 (2026-09-01) already assumes v1–v4.5 done. |
| FILL | P01 | v3 | Basic Transaction (Deposit & Withdraw) | Approved | ⚠ BACK-FILL from SetupDoc-v3.md / TestCases-v3.md: sign-off date, test-case counts, key features. Row missing since Reset #2 (2026-08-25); v5 (2026-09-01) already assumes v1–v4.5 done. |
| FILL | P01 | v4 | EAR Update, Rollback & Application Lifecycle | Approved | ⚠ BACK-FILL from SetupDoc-v4.md / TestCases-v4.md: sign-off date, test-case counts, key features. Row missing since Reset #2 (2026-08-25); v5 (2026-09-01) already assumes v1–v4.5 done. |
| FILL | P01 | v4.5 | Basic IHS Standalone Era | Approved | ⚠ BACK-FILL from SetupDoc-v4.5.md / TestCases-v4.5.md: sign-off date, test-case counts, key features. Row missing since Reset #2 (2026-08-25); v5 (2026-09-01) already assumes v1–v4.5 done. |
| 2026-09-01 | P01 | v5 | WAS Clustering | Approved | 80/80 test cases pass. 2-member cluster (devdsbinappcluster01), DMgr devdsbindmgr01, both nodes federated/synchronized, memory-to-memory session replication, live failover test passed. digistack-bank-v5.ear deployed to cluster target. Environment corrections applied from this version: WAS path /apps/IBM/WebSphere/AppServer/, DB credential removed from this log (kept only in gitignored config/db-local.properties / JAAS alias BankDS_Alias). SetupDoc-v5.md + FaultDrill-v5.md complete. |
| 2026-09-08 | P01 | v6 | Application Administration | Approved | 59/59 test cases pass (38 Critical + 18 High + 3 Medium), 25/25 unit tests pass — TEST01 gating satisfied. Features: Freeze/Unfreeze (UI + wsadmin), Node Sync vs Full Resync drill, is_frozen DB column (V4__add_frozen_flag.sql), EAR packaging (digistack-bank-v6.ear), pre-v6 code refactor (service split, servlet split, JSP split, CSS extraction). Standing rule TEST01 established — unit tests GATING for all future sign-offs. SetupDoc-v6.md + FaultDrill-v6.md complete. Fault drill: Node Agent stopped on dsb-node02 (INC-v6-001). |
| 2026-09-11 | P01 | v7 | JNDI DataSource Migration | Approved | 47/47 test cases pass (31 Critical + 14 High + 2 Medium), 25/25 unit tests pass — TEST01 gating satisfied. Features: PostgreSQL JDBC Provider at Cell scope (org.postgresql.ds.PGConnectionPoolDataSource), JAAS Auth Alias (BankDS_Alias), jdbc/BankDS DataSource, connection pool min=5/max=20 per member (40 total, 60 headroom against max_connections=100), pre-test validation (SELECT 1), all 7 classes migrated from DriverManager to InitialContext.lookup("jdbc/BankDS"), SeedUsers.java credentials moved to gitignored config/db-local.properties, transaction-boundary traceability note documented, digistack-bank-v7.ear deployed. SetupDoc-v7.md + FaultDrill-v7.md complete. Fault drill: JAAS Auth Alias wrong password (INC-v7-001). |
| 2026-09-11 | P01 | v8 | IHS Plugin Configuration (Cluster Era) | Approved | 66/66 test cases pass (38 Critical + 24 High + 4 Medium), 25/25 unit tests pass — TEST01 gating satisfied. Features: plugin-cfg.xml regenerated with both cluster members (192.168.10.10:9080 and 192.168.10.11:9081), propagated via IHS Administration Server on port 8008, static assets served from IHS document root (/static/css/ihs-brand.css, /static/images/digistack-logo.svg), custom 404/500 error pages via ErrorDocument directives (self-contained HTML, no external deps), IHS brand banner added to Home.jsp and Login.jsp, digistack-bank-v8.ear deployed. SetupDoc-v8.md + FaultDrill-v8.md complete. Fault drill: plugin-cfg.xml port typo — member 2 port changed to 9999 (INC-v8-001). Post-sign-off pre-v9 CSS extraction applied — all inline style="" attributes and <style> blocks removed from all 8 JSPs (Home, Login, Dashboard, Account, Deposit, Withdraw, Freeze, Unfreeze). All styles moved to dedicated CSS files. common.css updated with shared utility classes (icon-gold, icon-navy, icon-green, navbar-user-label, alert-icon). Two intentional inline style exceptions documented: display:none on balance toggle elements (JS-controlled at runtime) and dbConnStatus colour in Home.jsp (JSP EL expression — cannot be static CSS). Project structure confirmed and documented post-extraction. |
| 2026-09-29 | P01 | v8.5 | Transaction Service / XA Recovery | Approved | 32 test cases pass (14 Critical + 15 High + 3 Medium). 25/25 unit tests pass (TEST01). XA DataSources jdbc/DebitDS and jdbc/CreditDS at Cell scope under PostgreSQL XA JDBC Provider (org.postgresql.xa.PGXADataSource). EJB CMT FundsTransferBean proves atomic rollback across two XA resources (DEBIT_DS + CREDIT_DS). NonXaControlBean (NOT_SUPPORTED + autocommit=true) negative control proves orphaned debit without XA. 2PC trace captured in trace.log: enlist ×2 → prepare ×2 → tranlog write → commit ×2. WTRN0006E timeout proven via SleepTransactionServlet (ut.setTransactionTimeout(15), RollbackException caught). Heuristic outcome forced by ROLLBACK PREPARED mid-recovery — resolved via TransactionAdmin.forget(). XA recovery proven: kill -9 mid-2PC, no manual DB intervention, WTRN0017I → WTRN0057I ×2 → WTRN0100I, zero funds lost or duplicated (total funds invariant held). Sprint 8 fault drill (INC-v8.5-001): JMeter 5-thread load on /XATransfer + kill -9, MTTD and MTTR recorded, total funds invariant confirmed post-recovery. SetupDoc-v8.5.md, TestCases-v8.5.md, FaultDrill-v8.5.md, wtrn-error-cheatsheet.md all complete. backupConfig + tranlog backup taken on both nodes. digistack-bank-v8.5.ear deployed to devdsbinappcluster01. |
| 2026-09-30 | P01 | v9 | Session Management | Approved | 32 test cases pass (17 Critical + 12 High + 3 Medium). 25/25 unit tests pass (TEST01). Sticky sessions via CloneID in plugin-cfg.xml confirmed — five consecutive requests routed to same member (http_plugin.log). Sticky-only control case proven (session lost on kill -9 with no replication). M2M replication configured (DigiStackSessionReplication, numberOfReplicas=1) — session survives kill -9 and graceful restart (ADMU0512I confirmed), DRSV0003I + DRSV0019I on both members. Session timeout 30 minutes (web.xml + server-level invalidationTimeout), http-only cookie, COOKIE tracking mode. SessionTimeoutListener logs SESSION CREATED (maxInactiveInterval=1800s) and SESSION DESTROYED. Login page shows amber "session expired" and green "logged out" messages. DB-backed persistence evaluated — V5__create_sessions.sql, jdbc/SessionDS, sessions table row written on login, session retrieved by Member 2 after kill. Three-way comparison recorded: ship decision = M2M (zero DB load, very low latency). Fault drill INC-v9-001: BrokenDomain name injected on Member 1 — silent failure, DRSV0019I absent, session lost on crash. Fix: correct domain name restored, DRSV0019I confirmed, session survived kill after fix. SetupDoc-v9.md, TestCases-v9.md, FaultDrill-v9.md complete. backupConfig taken. digistack-bank-v9.ear deployed to devdsbinappcluster01. |
---

## Cross-Part Dependency Chain

> Per Engineering Standards §8. Add one row per version **as it is actually implemented** — do not pre-fill with guesses at roadmap-design time. `Depends On` = what prior version(s)/artifact(s) this version required to exist first. `Produces` = the concrete artifact(s) this version adds (code module, EAR, table, endpoint, queue, etc.). `Used By` = later version(s) that consume what this version produced (fill in retroactively once that later version is built, or note "known future consumer" if the roadmap already documents the link).

| Version | Depends On | Produces | Used By |
|---|---|---|---|
| ~~V2~~ | ~~V1 (app_config table, EAR skeleton)~~ | ~~users table, Login/Logout servlets, HttpSession creation, Dashboard.jsp~~ | ~~V3, V5, V10~~ |
| ~~V3~~ | ~~V2 (users table, session mechanism)~~ | ~~accounts table (FK to users), AccountDao/Service/Controller layers, Deposit/Withdraw UI, InsufficientFundsException~~ | ~~V4, V5, V6~~ |

Rows above struck through per the 2026-08-25 full reset #2 — these
describe artifacts from the pre-reset build (lost along with the lab
VM), not artifacts currently on disk. Re-add un-struck rows once each
version is actually rebuilt and signed off again.

| Version | Depends On | Produces | Used By |
|---|---|---|---|
| V5 | V4 (digistack-bank-v4.ear), V1 (WAS profile) | DMgr devdsbindmgr01, federated nodes devdsbinnode01/02, cluster devdsbinappcluster01, memory-to-memory session replication, digistack-bank-v5.ear | V6 (DMgr/federation deep-dive, Freeze/Unfreeze via wsadmin), V7 (JNDI DataSource across cluster), V8 (plugin-cfg.xml regenerated for cluster) |
| V6 | V5 (cluster, federation, digistack-bank-v5.ear), V5-DB (accounts table) | is_frozen column (V4__add_frozen_flag.sql), DepositService/WithdrawService/FreezeService, DepositServlet/WithdrawServlet/FreezeServlet/UnfreezeServlet, Deposit.jsp/Withdraw.jsp/Freeze.jsp/Unfreeze.jsp, 9 CSS files, freezeAccount.py wsadmin script, digistack-bank-v6.ear, unit test suite (TEST01 — DepositServiceTest/WithdrawServiceTest/FreezeServiceTest) | V7 (all Services replace direct JDBC with jdbc/BankDS JNDI DataSource — closes v1–v6 JDBC debt) |
| V7 | V6 (all Services, digistack-bank-v6.ear), V6-WAS (cluster devdsbinappcluster01) | PostgreSQL JDBC Provider (Cell scope), JAAS Auth Alias BankDS_Alias, DataSource jdbc/BankDS, connection pool config (min=5/max=20/EntirePool/SELECT 1 validation), 7 migrated classes (AccountService/DepositService/WithdrawService/FreezeService/HomeServlet/LoginServlet/DashboardServlet), config/db-local.properties (gitignored), digistack-bank-v7.ear | V8 (plugin-cfg.xml regenerated against cluster — jdbc/BankDS pool active on both members during all IHS-routed requests) |
| V8 | V7 (digistack-bank-v7.ear, jdbc/BankDS DataSource), V4.5 (webserver1 Web Server Definition, IHS install on dsb-ihs) | cluster-aware plugin-cfg.xml (both members: 192.168.10.10:9080 and 192.168.10.11:9081), ihs-brand.css (IHS htdocs), digistack-logo.svg (IHS htdocs), 404.html/500.html (IHS htdocs/errors/), ErrorDocument directives in httpd.conf, Home.jsp/Login.jsp IHS brand banner, digistack-bank-v8.ear | V8.5 (XA DataSources and EJB CMT FundsTransferBean built on top of the IHS-routed cluster confirmed at v8) |
| V8.5 | V7 (jdbc/BankDS, all Services migrated to JNDI), V8 (digistack-bank-v8.ear, IHS plugin-cfg.xml, cluster devdsbinappcluster01 fully operational) | PostgreSQL XA JDBC Provider at Cell scope (org.postgresql.xa.PGXADataSource), jdbc/DebitDS XA DataSource, jdbc/CreditDS XA DataSource, FundsTransferBean (@Stateless EJB, @TransactionAttribute REQUIRED), NonXaControlBean (@Stateless EJB, @TransactionAttribute NOT_SUPPORTED), FundsTransferServlet (/XATransfer), NonXaControlServlet (/XAControl), SleepTransactionServlet (/XASleep), XATransfer.jsp, XASleep.jsp, alert-warning class in common.css, wtrn-error-cheatsheet.md, digistack-bank-v8.5.ear, enableLoggingForHeuristicReporting=true on both cluster members | V9 (Session Management — cluster-aware sessions built on top of the fully operational IHS-routed cluster and XA-capable DataSources confirmed at v8.5) |
| V9 | V8.5 (digistack-bank-v8.5.ear, XA DataSources), V5 (cluster devdsbinappcluster01, M2M base) | Replication domain DigiStackSessionReplication (numberOfReplicas=1), CloneID sticky routing verified in http_plugin.log, SessionTimeoutListener, 30-min timeout (web.xml + server invalidationTimeout), http-only COOKIE tracking, V5__create_sessions.sql, jdbc/SessionDS + sessions table (evaluated, not shipped), Login expired/logged-out messages, digistack-bank-v9.ear | V10 (next — identity/roles must survive failover; fill retroactively once built) |

---

## Migration Cutover Status (populate only once Part-7 v47 is reached)

> Per doc 04's "Migration Part Promotion" section: while Part-7's cutover is in progress, the **Folder Tracker**'s `06_WAS_Migration` row (and its status field in **Multi-Part Folder Detail**) must show **`Cutover in-flight`** — not "In progress" or left blank — and this detailed per-region table must show explicit per-region state underneath it. The Part cannot be marked `part7-release` until every region reads "Cut over — decommissioned" here **and** the Folder Tracker/Multi-Part Folder Detail entries for `06_WAS_Migration` are updated to `part7-release` in the same edit.

| Region | Platform State | Canary % | Post-Cutover Observation Window Closed? | Old Platform Decommissioned? |
|---|---|---|---|---|
| India | Not started | — | — | — |
| Singapore | Not started | — | — | — |
| Dubai | Not started | — | — | — |

---

## AWS Migration Cutover Status (populate only once Part-9 v60 is reached)

> Added per the 2026-07-19 cross-file audit (Finding F4). Per doc 04's "Phased Cloud Migration Promotion" section: Part-9's progressive cutover is at least as complex as Part-7's — it spans four phase capstones (v58, v63, v68, v73) and region-by-region decommissioning that can happen well before the Part-level UAT/Prod promotion at the very end. Track each region's AWS migration state independently here. Part-9 is not `part9-release` until every region reads "Cut over — on-prem decommissioned" below **and** the **Folder Tracker**'s `08_Cloud_Migration` row (and its status field in **Multi-Part Folder Detail**) is updated to `part9-release` in the same edit — mirroring the Migration Cutover Status table's own rule for Part-7.
>
> **Reminder:** unlike the promotion event itself (which happens once, at the end, per doc 04's Phased Cloud Migration Promotion section), the columns below can and should be updated incrementally as each phase/region reaches that milestone — don't wait until v73 to start filling this in.

| Region | On-Prem/AWS Split (v58) | Lift-and-Shift Complete? (v63) | Platform Modernized? (v68) | Final Cutover % (v73) | Old On-Prem Decommissioned? |
|---|---|---|---|---|---|
| India | Not started | — | — | — | — |
| Singapore | Not started | — | — | — | — |
| Dubai | Not started | — | — | — | — |

---
## Environment Notes

- **WAS ND version installed:** 9.0.5.28 — confirmed at P01 v5 sign-off (2026-09-01). Install path: /apps/IBM/WebSphere/AppServer/ (corrected this version).
- **Profile(s) created so far:** devdsbindmgr01 (DMgr, dsb-dmgr, P01 v5); nodes devdsbinnode01 (dsb-dmgr) and devdsbinnode02 (dsb-node02); cluster devdsbinappcluster01. Standalone profile devdsbinappserver01 (v1–v4.5 era) — FILL: retained or removed at v5?
- **Database (PostgreSQL):** 16 — confirmed at P01 v5 sign-off. Runs on dsb-db (192.168.10.30), digistack_app password: NOT recorded here — see gitignored config/db-local.properties. V4__add_frozen_flag.sql applied at v6 (is_frozen column added to accounts). Active P01 v1 through P02 v22 only; decommissioned at P03 v23 Sprint 4.
- **Database (Oracle):** Not yet provisioned — dsb-oracle VM not built. Oracle 21c XE target/placeholder pin (v22.5 sign-off confirms). IP: 192.168.10.32. Powers on at P02 v22.5. NEVER co-hosted with PostgreSQL on dsb-db.
- **IBM HTTP Server installed:** Yes — IHS 9.0.5.28 on dsb-ihs (192.168.10.20). plugin-cfg.xml regenerated at v8 with both cluster members (port 9080 and 9081). Static assets under /apps/IBM/HTTPServer/htdocs/static/. Custom error pages under /apps/IBM/HTTPServer/htdocs/errors/. ErrorDocument 404/500/503 configured in httpd.conf.
- **Any deviations from the roadmap so far:** OS confirmed as RHEL 8.x on dsb-dmgr (P01 v1 Sprint 2) — 
SOE01/CONTEXT_PACK referenced Rocky Linux 8.x as the expected OS; 
RHEL 8.x is fully compatible, no technical change, 
documentation corrected to reflect actual installed OS.
---

## Open Questions / Decisions Pending

**Resolved — v9.5 relocated to v14.5, 2026-09-30.**

Reason: manual-first learning — v10–v14 are configured by hand before any automation toolkit is built. v9.5 (wsadmin Jython Toolkit & Troubleshooting) is now v14.5, after v14. v10 follows v9 directly; v10 prerequisite changed to "P01 v9 signed off"; v14.5 prerequisite is "P01 v14 signed off"; P01 consolidation now triggers after v14.5 sign-off. No other version numbers changed. Files updated: P01_Foundation.md, P01_Sprint_Plan.md, SESSION_STATE.md, this file.

**Resolved — v8.5 and v9.5 suffix-slot versions added, 2026-09-11.** (v9.5 later relocated to v14.5 — see entry above.)

Two new suffix-slot versions inserted into P01_Foundation.md and
P01_Sprint_Plan.md, following the same convention already established
by v4.5 (a version that doesn't renumber the versions after it):

- **v8.5 — Transaction Service / XA Recovery** — inserted between v8
  and v9. Covers JTA transaction service, XA vs non-XA DataSources,
  2PC, transaction/recovery logs, timeouts, heuristic outcomes,
  WTRN/WSVR error codes. Prerequisite: P01 v7 signed off (DataSource/
  pooling already in place). Produces a synthetic "FundsTransfer"
  test (Servlet → EJB CMT → two XA DataSources) — infrastructure
  proof only, not the customer-facing Fund Transfer feature (that
  remains P02 v15).
- **v9.5 — wsadmin Jython Toolkit & Troubleshooting** — inserted
  between v9 and v10. Covers AdminControl/AdminConfig/AdminApp/
  AdminTask fluency, thread/heap dump analysis, JVM tuning. Produces
  `wasOps.py`, a properties-driven automation toolkit. Prerequisite:
  P01 v8.5 signed off (reuses its transaction timeout settings as one
  of the toolkit's scripted actions).

No existing version numbers changed — same suffix-slot discipline
already used for v4.5. P01's version count is now 17 total (was 15).
SESSION_STATE.md pointer updated: next version is v8.5 Sprint 1, not
v9. Files updated: P01_Foundation.md, P01_Sprint_Plan.md,
SESSION_STATE.md, this file (Folder Tracker + Multi-Part Folder
Detail Next Version columns).

Resolved — Database engine change: PostgreSQL → Oracle 21c XE
from v22.5 onward, 2026-08-28.

digistack_bank (PostgreSQL 16) remains the database engine for
P01 through v22. Version 22.5 (new, inserted between v22 and v23)
migrates all existing data to Oracle 21c XE DIGISTACK_CBS PDB via
a JDBC-based Java migration utility deployed inside WAS. From P03
v23 onward, Oracle 21c XE is the sole database engine. PostgreSQL
is decommissioned at P03 v23 Sprint 4 (final pg_dump archived, VM
shut down, snapshotted once, deleted). Oracle 21c XE runs on a NEW
dedicated VM dsb-oracle (2 vCPU / 4 GB RAM / 60 GB disk, Oracle
Linux 8) — it is NEVER installed on dsb-db. dsb-db retains its
original 2 GB RAM sizing throughout its life. No existing version
numbers changed — v22.5 follows the established suffix-slot
convention.

Files updated: CONTEXT_PACK.md, ARCH01, ARCH02, P02_Middleware.md,
P02_Sprint_Plan.md, 01_Network_Diagram.md, 02_VM_Layout.md,
02_VM_Layout.md (dsb-oracle row added), SESSION_STATE.md,
P03_Banking_Systems.md.

Resolved — NDS01 Rule 7 formalised, 2026-08-27.
Standing requirement that every WebSphere configuration task must
include both Admin Console (GUI) steps AND wsadmin (Jython) steps
was previously implicit in SetupDoc §4.1/§4.2 structure. Promoted
to an explicit named rule (NDS01 Rule 7) at project owner's request
during P01 v1 Sprint 4. Effective from Sprint 5 onward — all
remaining sprints in P01–P10 must deliver both paths.

**Resolved — STD v1.9 PIS01/FIS01 Sprint-Count Retroactivity Correction, 2026-08-27.**

STD's v1.9 change note (PIS01/FIS01 addendum) stated the Sprint 7/8
structural change was "effective P02 onward (v15-v78) — NOT retroactive
to P01 (v1-v14), which remains signed off under its original 6-sprint
structure per version." This directly contradicted `P01_Sprint_Plan.md`,
which was already authored with the full 8-sprint structure (Sprint 7
Sign-off, Sprint 8 Fault Injection + Incident) across all 14 versions.

Corrected: STD bumped to v1.14 with a new change note stating the
8-sprint structure applies project-wide, P01 through the final Part
(v1-v78), no exceptions — matching what `P01_Sprint_Plan.md` already
reflects and what STD's own "Applies to" line already claimed elsewhere
in the same section. Since P01 v1-v14 have not actually been built (full
reset #2, confirmed 2026-08-25), no retroactive rework is required —
this is a documentation-consistency fix only, same category as the
2026-07-28 STD/SOE01 port-table correction and the 2026-08-25 STD/SOE01
version-pin revert. No architectural or technical change. Standing
Reference Documents table above updated to reflect STD v1.14.

**Open — Three Unscoped UI Elements (Business toggle, Open an Account,
Forgot Password), logged 2026-08-24, no target version assigned.**

Project owner's public landing page / login page mockup (2026-08-24)
included three elements with no corresponding backend anywhere in
P01-P10:

| Element | Why it's a gap | Decision needed |
|---|---|---|
| "Personal \| Business" toggle | Business/corporate banking not scoped anywhere in ARCH01 or any Part (retail-only project) | Scope a Business Banking module (new Part?) or drop permanently — undecided |
| "Open an Account" (self-service) | Accounts are currently only created via SQL seed scripts, not a customer-facing flow. Related to the Admin/Teller account-opening gap already logged (2026-08-24, tied to P03 v29 Branch Portal) | Decide: self-service (customer-facing) vs. admin-assisted (Teller, P03 v29) vs. both — undecided |
| "Forgot Password?" | No password-reset flow anywhere in P01 v2 or v10 | Decide which version builds it (candidate: P01 v10, Administrative Security) — undecided |

Decision (2026-08-24): render all three as disabled/"Coming soon" in the
UI rather than removing them or silently wiring them to nothing — visible
so the roadmap gap stays honest, non-functional so no false capability is
implied. Revisit and formally scope each once a decision is made — not
before. Per NDS01/Roadmap Discipline, none of this is built ahead of
schedule; P01 v5 (WAS Clustering) continues uninterrupted.

**Open — Dashboard Full Layout, logged 2026-08-24, updated 2026-08-24 (ATM tile removed).**

| Section | Behavior | Target Version |
|---|---|---|
| 1. View Balance (toggle, hidden by default) | In-page reveal, no redirect | **P01 v3 Sprint 4** — `Dashboard.jsp` retrofit |
| 2. Payments & Transfers tile | In-app Fund Transfer | **P02 v15** (Fund Transfer live) |
| 3. Last 10 Transactions list | In-page list | **P02 v16** (SOAP Account Statement/Transaction History) |
| 4. Cards summary tile | Redirects to card.digistack.cloud | **P03 v28** |
| 5. Loans section | In-page summary, omitted if no active loan | **P03 v30** |
| 6. Last login timestamp | Persistent header, not just post-login flash | **P01 v2/v3 retrofit** |
| 7. Frozen-account banner | Shown when `is_frozen` = true | **P01 v6 retrofit** |
| 8. Notification bell | Withdraw email (v13) → extended to Fund Transfer (v15) | **P01 v13 / P02 v15** |
| 9. Multi-account switcher | "Your Accounts" becomes a list once 2+ accounts possible | **P02 v15** |
| 10. Download Statement link | Next to Recent Transactions, reuses v16's SOAP service | **P02 v16** |

No new backend logic introduced by any of these — all reuse data/events
already produced by the version they're attached to. Per NDS01/Roadmap
Discipline, none of this is built ahead of schedule; P01 v5 (WAS
Clustering) continues uninterrupted.

Decision: do NOT build ahead of schedule (NDS01/Roadmap Discipline). P01 v5
continues uninterrupted. ATM tile (previously logged 2026-08-24) removed
the same day — project owner agreed ATM is a physical channel, not
something a customer launches from online banking; ATM Simulator (P03
v27) remains a standalone kiosk app with no Dashboard entry point.

> Anything you were mid-discussion on when a chat ended, so it isn't lost.
**Resolved — Full Project Reset #2, 2026-08-25.**

Project owner's lab VM (dsb-dmgr, dsb-db, etc.) and prior chat session
were both lost a second time. Confirmed explicitly with project owner
(2026-08-25) that this is a genuine restart, mirroring the 2026-08-11
event. Reset executed:
- SESSION_STATE.md pointer reverted to P01 v1, Sprint 1 (not started);
  header version bumped 1.4 → 1.5, correcting a duplicate-"v1.4"
  change-note labeling bug found in the same pass
- This file's Folder Tracker row for `02_Application_Development`
  reverted to ⏳ Not Started (was stuck showing "v3 signed off,
  In Progress" — itself stale against both this reset and the prior
  2026-08-11 one)
- Detailed Version Log: the 2026-08-11 v2 and v3 "Approved" rows
  struck through (not deleted), per this project's "never silently
  edit" discipline
- Cross-Part Dependency Chain: V2/V3 rows struck through for the same
  reason
- Environment Notes updated to reflect the second loss
- STD's WAS ND / PostgreSQL version pins reverted from CONFIRMED back
  to target/placeholder (STD v1.13), SOE01's mirrored pins likewise
  (SOE01 v1.10) — this also closes a real drift found during this
  pass: the 2026-08-11 reset entry below already claimed this same
  revert had been done, but STD/SOE01's actual pins were never edited
  at that time and still read CONFIRMED (dated 2026-08-07) until now



Physical rebuild (VM provisioning, WAS ND install, PostgreSQL install)
begins fresh at P01 v1 Sprint 1.

**Resolved — Full Project Reset, 2026-08-11.**

Project owner's lab VM (dsb-dmgr, dsb-db, etc.) and prior chat session
were both lost. Confirmed explicitly with project owner (2026-08-11) that
this is a genuine restart, not a documentation drift correction like the
2026-07-28/2026-08-04 events. Reset executed:
- SESSION_STATE.md pointer reverted to P01 v1, Sprint 1 (not started)
- Progress_Log.md's Folder Tracker, Multi-Part Folder Detail, Detailed
  Version Log (old row struck through, not deleted), and Environment
  Notes all reset to reflect zero completed work
- 02_Application_Development_README.md's P01 section reset to 0/14
  versions, AI Resume Context reset to Sprint 1 start
- STD's WAS ND / PostgreSQL version pins reverted from CONFIRMED back to
  target/placeholder, unconfirmed (SOE01's mirrored pins likewise)
Physical rebuild (VM provisioning, WAS ND install, PostgreSQL install)
begins fresh at P01 v1 Sprint 1.

**Resolved — 2026-07-29 Standing Reference Documents sync check.**

Progress_Log.md's Standing Reference Documents table had drifted out of
date against two files' actual metadata blocks: STD showed 1.5 (actual
file: 1.7, already carrying the NDS01 and Build Tool change notes) and
SOE01 showed 1.4 (actual file: 1.5, already carrying the 9.0.5.21
target-pin change note). Both corrected in the table above. Separately,
ARCH02's own file had undocumented content drift — §2a (Maven Project
Structure) and the Maven CI/CD row existed in the file but the metadata
block was never bumped past 1.0 and no change note recorded the
addition; ARCH02 corrected to 1.1 with a change note in the same pass.

**Resolved — 2026-07-28 cross-file audit (premature WAS ND version-pin
correction + stale sync drift).**

A 2026-07-27 edit to STD had “corrected” the WebSphere ND pin from a 9.0.3
placeholder to 9.0.5.21, citing P01 v1's SetupDoc-v1.md as an actual
install record. That document does not exist — this project's own
Folder Tracker and 01_Application_Development's README both confirm
P01 is still “Not Started” (0/14 versions, no SetupDocs, no Pause/Resume
or Deviation entries logged). Confirmed with the project owner
(2026-07-28) that nothing has actually been built yet. Corrected:
- STD's WAS ND/Java SDK pins reverted to the 9.0.3 placeholder (STD v1.5)
- SOE01 §9's mirrored pin reverted to match (SOE01 v1.4)
- This file's own Environment Notes line (previously asserted “9.0.3
  recorded in SetupDoc-v1.md §4.1” — itself premature, since that file also
  doesn't exist yet) reworded to reflect “not yet installed, placeholder
  target only”
- SESSION_STATE.md's Sprint pointer corrected from “Sprint 2 (next)” to
  “Sprint 1 (next — not yet started)”, matching this file's Folder Tracker
- This file's own Standing Reference Documents table was also out of
  sync on STD (showed 1.3, actual file was already 1.4 pre-revert) and
  SOE01 (showed 1.2, actual file was already 1.3 pre-revert) — both
  corrected to the current post-revert versions (1.5 / 1.4)
- Two Ports table gaps between STD and SOE01 also fixed in the same
  pass: STD was missing port 22 (SSH), which SOE01's firewall table
  already opened; SOE01's firewall table was missing port 8080
  (Tomcat), which STD already defines

Re-validate the WAS ND pin against SetupDoc-v1.md §4.1 once P01 v1 is
actually built and signed off — only then promote it back to “confirmed.”

**Resolved — RACI01 / P04 v35 / P05 v38 cross-reference gap (flagged
2026-07-22, resolved 2026-07-23).**

RACI01 (Operational RACI, §4) introduced a named **Incident Commander**
role specifically to resolve an ambiguity in two already-frozen files:

- **P04 v35** (Production Operations, Capacity Planning & Reporting)
  defined the Incident Management Scope and Support Process without
  naming who holds cross-team authority when an incident spans more than
  one team.
- **P05 v38** (Business Continuity & Application Resilience) referenced
  "Management Approval (simulated)" in its DR Runbook and "Incident
  Response" in its Business Continuity section (§7), again without
  naming who actually approves or coordinates.

**Resolution executed:** both P04 v35 and P05 v38 now carry an explicit
one-line cross-reference to RACI01 §4 ("Management Approval → see RACI01
§4, Incident Commander"), added as a documented in-place note — the same
style used for this project's other post-hoc corrections (e.g., STD's
Dependency Matrix note citing the 2026-07-22 audit). This is a
documentation cross-reference only; no technical scope in either P04 or
P05 changed, consistent with the Version Numbering Freeze discipline
(Engineering Standards §7).

This item is now closed — both P04 and P05 explicitly cite RACI01 §4
inline, per the closure condition originally set out when this gap was
first logged.



*(Resolved items below.)*

**Resolved:**
- **Fixed Deposits & Recurring Deposits — Resolved (superseded), 2026-07-22.** Previously tracked as an open question with a default assumption to defer scoping to Part-10 "once roadmapped." Part-10 (Containerization & Cloud-Native Modernization) has since been fully scoped, and its file explicitly declines to absorb this feature (see P10's "Fixed/Recurring Deposits — Explicit Non-Scope Note": this Part is infrastructure/runtime-only, and introducing a banking feature here would break that discipline). Since P10 is currently the final Part in the roadmap with no further Part scoped or implied, Fixed Deposits & Recurring Deposits remain **permanently unscoped within P01–P10**. Any future addition requires a new, explicitly-proposed and scoped Part — this is not assumed to happen automatically.
- Part-2, Version 22 — **Resolved: PostgreSQL selected as the standard database for the DigiStack Bank Enterprise project across all environments.** Original source material referenced MySQL for this version's end-to-end flow; standardized to PostgreSQL project-wide. See MASTER INDEX's "Open Decisions" section for full context.
- Part-8 scope — **Resolved: Part-8 is Enterprise DevOps & End-to-End Automation** (Versions 49–53, renumbered from an initial 48–52 draft that collided with Part-7's frozen 44–48). Former "AWS Migration" placeholder content bumped to Part-9; Containerization bumped to Part-10. See MASTER INDEX's "Open Decisions" section for full context.
- Part-9 scope — **Resolved: Part-9 is Enterprise Hybrid Cloud & AWS Migration** (Versions 54–73, renumbered from an initial 53–72 draft that collided with Part-8's frozen 49–53). Source draft's MySQL references corrected to PostgreSQL/Amazon RDS for PostgreSQL, consistent with the project-wide standard. Containerization remains proposed as Part-10. See MASTER INDEX's "Open Decisions" section for full context.
- Part-6 title conflict — **Resolved: Multi-Region Enterprise Banking & Middleware Architecture is Part-6** (Versions 39–43, offset from an initial 38–42 draft). WebSphere Migration deferred to Part-7. See MASTER INDEX's "Open Decisions" section for full context.
- **Progress Tracker / Master Index sync drift (2026-07-19 cross-file audit) — Resolved.** Parts 2, 3, and 4's "Next Version To Build" columns were blank/inconsistent between this file and the Master Index. Both files now consistently show Version 15 (Part 2), Version 23 (Part 3), and Version 31 (Part 4). Part-3's title in the (then-named) Current Status Summary table was also corrected to match the canonical title used in the Master Index and the Part-3 file itself. *(Note, added post-restructure: this Current Status Summary table has since been superseded by the Folder Tracker / Multi-Part Folder Detail tables above; the historical fix described here still stands, just under the new table names.)*
- **Part-9 promotion model ambiguity (2026-07-19 cross-file audit) — Resolved.** doc 04 previously had no explicit promotion model for a Part that is simultaneously multi-region, phased, and pipeline-automated. Added a "Phased Cloud Migration Promotion" section to doc 04 clarifying that Part-9's phase capstones (v58, v63, v68) are Dev-only gates, while actual UAT/Prod promotion happens once, at the end (post-v73), across all three regions — with regional on-prem decommissioning tracked independently via the "AWS Migration Cutover Status" table above.
- Phase structure — Resolved: Project split into Phase-1 (P01-P03, plus
  suffix Part P03.1 for interview preparation), Phase-2 (P04-P09,
  Enterprise Operations), and Phase-3 (P10, Containerization & Cloud-
  Native Modernization). Confirmed no new banking modules exist after P03
  except P05 v38's idempotency-key addition (documented there as a
  resilience retrofit, not a feature) — P03.1 and P10 introduce no
  banking modules either. Design Documents / SDLC Blueprint / Sprint
  Planning are generated once per phase (P03.1 excluded, as it has no
  versions and produces no design-relevant artifacts), not once for the
  whole project — later phases' versions are deferred until that phase's
  implementation actually begins.


---

*Re-upload this file (updated) alongside the MASTER INDEX and current Part file at the start of every new chat.*