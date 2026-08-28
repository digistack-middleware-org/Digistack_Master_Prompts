# DigiStack Bank — App Reference (Consolidated P01+P02+P03)

Replaces: P01_Foundation.md, P01_Sprint_Plan.md, P02_Middleware.md, P02_Sprint_Plan.md,
P03_Banking_Systems.md, P03_Sprint_Plan.md. Sprint-level (Sprint 1-6/App-Dev/DB-migration)
detail dropped — not needed for WAS admin labs. This file is the sole source of truth
for "what does the real app look like at version N" in this course.

Upload this ONE file to Project Knowledge instead of the six originals.

---

## Governing Discipline (all parts)
One deployable unit until v23 (`digistack-bank-vN.ear`). App features are the *minimum
vehicle* to exercise each WAS topic — never padded for banking realism. From v23: the app
splits into up to 9 independent deployables (7 WAS EARs + 2 Tomcat apps), governed by one
rule — **only CBS writes to `digistack_cbs`**; every other app calls CBS via REST/SOAP/EJB
or consumes its JMS/MQ events. Never a direct write from elsewhere.

---

## P01 — Foundation (v1–v14) — single EAR

| V | WAS Topic | App Delta | Deliverable (1-line) |
|---|---|---|---|
| 1 | EAR/WAR, context root, virtual host | Home page + `app_config` read | EAR deployed via Console, live PG read confirmed |
| 2 | JVM/app startup, sessions, redeploy | `users` table, Login/Logout, "Last login" | Login works, session set, v2 redeployed over v1 |
| 3 | Layered arch (Controller→Service→DAO), ClassLoader | `accounts` table, Balance/Deposit/Withdraw | End-to-end txn through all layers |
| 4 | Update Application, lifecycle, rollback | Zero (version-label only) | Real rollback v4→v3→v4 verified |
| 5 | Clustering (operational stand-up only) | Zero (reuse v3/v4) | 2-member cluster; kill-one-member session/txn survives |
| 6 | DMGR/federation/sync deep-dive, wsadmin | Freeze/Unfreeze flag on accounts | Freeze toggled via app AND via wsadmin script |
| 7 | JDBC Provider/DataSource/JNDI/pooling | Zero (migrate v1's direct JDBC) | All I/O via JNDI pool, no hardcoded creds |
| 8 | IHS install, plugin-cfg.xml, reverse proxy | One static asset + custom 404/500 | IHS fronts app; custom error pages confirmed |
| 9 | Sticky sessions, replication, failover | Session timeout (idle auto-logout) | Timeout enforced; session survives member restart |
| 10 | Admin security, registry, roles | Customer vs Administrator role split | Freeze unreachable by Customer role (enforced, not UI-hidden) |
| 11 | SSL basics, keystore/truststore, HTTPS | Zero (move existing pages to HTTPS) | HTTP→HTTPS redirect on IHS |
| 12 | SSL repertoires, mTLS, cert renewal | Zero (extend v11 end-to-end) | Full hop chain SSL; mTLS on ≥1 internal hop |
| 13 | JavaMail, SMTP, JNDI Mail Session | Withdraw triggers 1 email | Real email on Withdraw; failure/recovery demoed |
| 14 | JVM heap sizing, thread pool, GC/PMI | 1 large Transaction Report (stress test) | OOM baseline captured, then tuned — no OOM after |

**State after P01:** Home, Login/Logout, Balance, Deposit, Withdraw, Freeze/Unfreeze, 1
Report, 1 email — on a 2-node cluster behind IHS, SSL end-to-end, roles enforced, JNDI
DataSource + Mail Session, heap-tuned.

---

## P02 — Middleware Integration (v15–v22) — still single EAR

| V | WAS Topic | App Delta | Deliverable (1-line) |
|---|---|---|---|
| 15 | SIBus, JMS Queues, MDB, DLQ, async | Customer(multi-acct)→Beneficiary→Fund Transfer, async via MDB | Transfer returns immediately, MDB updates balance async, bad txn lands in DLQ |
| 16 | JAX-RS/JAX-WS, WSDL, REST/SOAP endpoints | Expose Balance Inquiry + Fund Transfer (REST), Account Statement (SOAP) | 2 REST + 1 SOAP endpoint live, WSDL published, Postman/SoapUI calls logged |
| 17 | Global/App Security, LDAP, LTPA, JAAS, CSRF/XSS | MFA/OTP, lockout, v16 endpoints require token, security-event log on rapid transfers | Endpoints secured; rapid-transfer audit event fires |
| 18 | HPEL/PMI/JMX, heap/thread dump, GC logs | Operations Dashboard (viewer only, no new infra) | Dashboard shows live JVM/session/queue/pool via PMI; dump captured |
| 19 | IBM MQ: QMgr, channels, CHLAUTH, MQ DLQ | External Beneficiary transfer routes via MQ (Payment Request/Response) | MQ QMgr live; external transfer round-trips via MQ |
| 20 | IHS advanced: rewrite, maintenance mode, health checks | Zero (existing app) | Rewrite rule demoed; maintenance toggle w/o restart; health URL live |
| 21 | L4/L7 LB, health checks, sticky sessions, blue-green | Zero (trivial cosmetic change for blue-green test) | NGINX fronts 2 IHS; blue-green zero-downtime; node-failure auto-routed |
| 22 | Full-stack integration Capstone | Zero (validates everything built) | Full request LB→IHS→Cluster→JMS/MQ→DB works; mock incident worked via runbook; backup/recovery tested |

**State after P02:** `digistack-bank-v22.ear` — Customer/Account/Beneficiary/Fund Transfer
(internal+external via MQ), Transaction History (REST+SOAP), MFA/hardened security,
PMI/JMX dashboard, MQ QMgr, IHS advanced admin, external LB with blue-green. Still 1 EAR.

---

## P03 — Enterprise Banking Systems (v23–v30) — **splits into 9 apps**

**The One Governing Rule (from v23):** Only CBS writes to `digistack_cbs`. Everyone else
(Portal, Payment Hub, Notification Svc, Reporting Svc, Branch Portal, Card Portal, Mobile,
ATM) calls CBS via REST/SOAP/EJB or consumes its JMS/MQ events — never a direct DB write.

| V | WAS Topic | App Delta | Deliverable (1-line) |
|---|---|---|---|
| 23 | Multi-EAR split, dedicated DataSource, cell/server scope, data migration | Portal + CBS become 2 separate EARs; v16 REST/SOAP + v15/v19 SIBus/MQ relocate to CBS | Portal has zero direct DB access; migration row-count reconciled; old Portal DataSource decommissioned |
| 24 | Data-sensitive multi-app access, deployment targeting | CIF & Account Lifecycle in CBS (Aadhaar/PAN gate) | Multi-account CIF live with ID verification gating creation |
| 25 | Saga/compensating-txn pattern, MQ, independent EAR deploy | Payment Hub — own EAR; NEFT (batch) + IMPS (real-time) | Payment Hub never writes balances directly; retry/DLQ proven both rails |
| 26 | Heterogeneous topology: IHS routing WAS+Tomcat, virtual hosts | Mobile Banking — Tomcat app, `mobile.digistack.cloud` | Mobile live on Tomcat, IHS-routed, REST-only to CBS |
| 27 | Same as v26, negative-test flows | ATM Simulator — Tomcat app, `atm.digistack.cloud` | ATM live; blocked/wrong-PIN negative test passes |
| 28 | Multi-EAR admin: independent deploy/rollback, classloader isolation | Card Portal — own WAS EAR (issue/activate/block/PIN/hotlist) | Card blocked here correctly fails at ATM Simulator (cross-app proof) |
| 29 | Scheduled/batch jobs via WAS, change-mgmt-worthy deploys | Branch Portal — own WAS EAR; teller ops; BOD/EOD batch | EOD reconciliation report generated by Reporting Svc; BOD/EOD run on schedule |
| 30 | wsadmin/automation; **[SUPPLEMENTARY: EJB Timer Service — not in 28-module syllabus, cover briefly under Module 24]** | Loan Management inside CBS — origination→disbursement, EMI auto-debit | Loan disbursed to CBS savings acct; ≥1 EMI auto-debits via EJB Timer, reduces principal |

**State after P03 (final):** 9 deployable apps — **7 WAS EARs**: Internet Banking Portal,
CBS (sole writer of `digistack_cbs`), Payment Hub, Notification Service, Reporting Service,
Branch Portal, Card Portal — **+ 2 Tomcat apps**: Mobile Banking, ATM Simulator. All routed
through one IHS/LB tier. This is the terminal state this course's labs should target for
every Arc 6 (Modules 25-28) exercise — genuine multi-EAR, multi-vendor (WAS+Tomcat)
production topology.

---

## Version Mapping — WAS Syllabus ↔ EAR Version (compact)

Use the *lowest* version with the needed capability. From v23, also pick the *specific app*
(don't default to CBS for everything — deliberately span multiple of the 7 EARs for
Module 10/26 cross-app practice).

| WAS Module | Use EAR Version(s) |
|---|---|
| 3–6 (Install/Profile/Deploy/DMGR) | v1–v7 |
| 5 (ClassLoader) | v3, v23+ (multi-EAR isolation) |
| 7–10 (Federation/Sync) | v5–v6 |
| 11–12 (Clustering/Deploy-to-cluster) | v5, v21 (blue-green) |
| 13–14 (HA/DR/Capacity) | v5, v22 |
| 15 (JDBC) | v7, v23 (dedicated DataSource) |
| 15.13 (Transaction Svc/tranlog) | v15, v25 (Saga pattern) |
| 16 (Messaging MQ/JMS/SIBus) | v15, v19 |
| 17 (IHS) | v8, v20, v26/27 (heterogeneous routing) |
| 18 (Session Mgmt) | v9 |
| 19 (Security) | v10, v17 |
| 20 (Perf/JVM) | v14, v18 |
| 21 (Logging/FFDC) | v18, Notification Svc (v23+) |
| 24 (wsadmin/Automation) | v6, v29 (scheduled jobs), v30 (EJB Timer, supplementary) |
| 25 (Change Mgmt/ITSM) | RFC discipline applies to all EAR versions from **Day 82** onward (per day-plan, not an EAR version number); v29 esp. real |
| 26 (Enterprise Integration) | v19 (MQ), v21 (LB/F5 stand-in), v25 |
| 27 (Compliance/Audit) | Whole stack at v22 or v30 (latest) |
| 28 (SSL/Cert DR) | v11, v12, v17 (LTPA) |

**Fallback rules (unchanged):**
- Needed capability not built/uploaded yet → proceed conceptually against latest available
  version, flagged **[USING LATEST AVAILABLE EAR VERSION — vN]**.
- Real artifact not uploaded yet → proceed from this spec, flagged **[ASSUMED FROM SPRINT
  PLAN — pending real artifact upload]**.
