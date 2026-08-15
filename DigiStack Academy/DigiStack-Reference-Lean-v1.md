# DIGISTACK REFERENCE (LEAN) v1
Replaces: P01_Foundation.md, P02_Middleware.md, P03_Banking_Systems.md,
Architecture-Reference.md, Team-Org-Chart.md.
Purpose unchanged — reference/example source only, no coding/deployment
happens in this Project. Pull real names/artifacts from here for Sprints
3/4/5/7/8 wherever `Day-to-Version-Mapping.md` marks an overlay.

---

## 1. TEAM (replaces Team-Org-Chart.md)

**Escalation:** Venkatesh (you, Sr WAS Admin, 8yr, L2/L3) → Padol (WAS Lead) → Gireesh (Middleware Mgr) → Srikanth (Infra Mgr)
**Delegation:** Venkatesh → Ganesh (Sr WAS Admin) / Chaitanya (WAS Admin) → Sree Lakshmi (Jr WAS Admin) → Kavya (Middleware Ops)
**MQ team:** Sethu (Lead/L3) → Pandu / Bathi (L2) — use for any MQ incident
**Linux/DB/Network teams:** generic, no named contact
**Business stakeholders:** CBS Team, Payments Team, Digital Team raise tickets → route through Middleware Team (WAS: Padol/Venkatesh; MQ: Sethu/Pandu/Bathi)
**Fictional business-side names** (non-middleware roles): Priya Raghunathan, Karthik Subramaniam, Arjun Mehta, Deepa Nair, Neha Kapoor
**Prod nodes:** IHS01/IHS02 → plugin-cfg.xml → DMGR01 → Node01(JVM01,JVM02)/Node02(JVM03,JVM04) → CLUSTER → PostgreSQL + IBM MQ → DIGISTACK CBS
Use these exact node names from Day 65 (IHS) onward, retroactively for Clustering (39–51) too.

---

## 2. VERSION TABLE — P01 (Foundation, single EAR `digistack-bank-vN.ear`, v1–14)

| V | WAS Topic | Key Artifacts | Deliverable (1-line) |
|---|---|---|---|
| 1 | First EAR deploy | `app_config` table, context root `/digistack-bank` | EAR live, Home page confirms PostgreSQL read |
| 2 | JVM/session start | `users`(username,password_hash) | Login/logout; session shows "Last login" |
| 3 | App layering, classloader | `accounts`(account_id,balance) | Balance/Deposit/Withdraw via Controller→Service→DAO→DB |
| 4 | Update/Rollback/Lifecycle | version label "v4" on Home | Real rollback v4→v3→v4 verified |
| 5 | Clustering | `dsb-dmgr`, `dsb-node01/02`, 2-member `AppCluster` | Kill-member mid-session; Deposit/Withdraw + session survive |
| 6 | DMGR/federation/wsadmin | Freeze/Unfreeze flag on `accounts` | Freeze toggled via app + 1x via wsadmin script |
| 7 | JDBC/JNDI | `jdbc/BankDS`, JAAS alias; pool=2×20=40 vs max_connections=100 | All reads/writes via JNDI pool, no hardcoded creds |
| 8 | IHS | plugin-cfg.xml, custom 404/500 | IHS fronts cluster; static asset served by IHS not WAS |
| 9 | Session mgmt | session timeout | Timeout enforced; session survives member restart (m2m repl) |
| 10 | Users/Groups/Security | Customer + Administrator roles | Freeze/Unfreeze blocked for Customer role (real enforcement) |
| 11 | SSL basics | self-signed cert | HTTPS enforced on IHS, HTTP→HTTPS redirect |
| 12 | SSL end-to-end | NodeDefaultSSLSettings/CellDefaultSSLSettings | mTLS on ≥1 internal hop; cert renewal tested once |
| 13 | JavaMail/JNDI Mail | WAS Mail Session | Withdraw triggers real email via SMTP |
| 14 | JVM heap/reports | Transaction Report (multi-thousand rows) | Report runs w/o OOM; heap tuned, PMI/GC before/after |

**P01 exit state:** Home/Login/Balance/Deposit/Withdraw/Freeze/Report/Email, on 2-member cluster behind IHS, SSL end-to-end, roles enforced, JNDI DataSource + Mail Session.

---

## 3. VERSION TABLE — P02 (Middleware, still 1 EAR, v15–22)

| V | WAS Topic | Key Artifacts | Deliverable (1-line) |
|---|---|---|---|
| 15 | SIBus/JMS/MDB/DLQ | `BANK.FUNDTRANSFER.Q`, `FundTransferMDB`; new: Customer(customer_id), multi-Account, Beneficiary, Fund Transfer | Transfer returns immediate accept; async debit via MDB; failed txn → DLQ |
| 16 | REST/SOAP | 2 REST (Balance Inquiry, Fund Transfer) + 1 SOAP (Account Statement) | WSDL published; Postman/SoapUI calls all 3, logged |
| 17 | Security hardening | MFA/OTP, lockout, API token on v16 endpoints | LTPA validated across cluster; rapid-transfer → audit event |
| 18 | Monitoring | Ops Dashboard via PMI/JMX (JVM, sessions, `BANK.FUNDTRANSFER.Q` depth, `jdbc/BankDS` pool) | Dashboard live; manual thread+heap dump captured |
| 19 | IBM MQ | MQ Queue Manager, CHLAUTH+SSL, Payment Request/Response queues | External-flagged transfer round-trips via MQ |
| 20 | IHS advanced admin | URL rewrite, maintenance page, health check URL | All 3 demonstrated without WAS restart |
| 21 | Load Balancer | NGINX/HAProxy over 2× IHS | Blue-green deploy, zero downtime; simulated IHS failure auto-routed |
| 22 | Capstone | full stack validation, no new features | E2E Fund Transfer through every layer; mock incident + runbook; backup/restore of WAS config |

**P02 exit state (`digistack-bank-v22.ear`):** adds Customer(multi-account), Beneficiary, Fund Transfer (internal+external via MQ), Txn History (REST+SOAP), SIBus/JMS, Web Services engine, hardened security, PMI/JMX dashboard, MQ QM, IHS advanced admin, external LB.

---

## 4. VERSION TABLE — P03 (Banking Systems, multi-app split, v23–30)

**Governing Rule (from v23 on):** Only CBS writes to `digistack_cbs`. Every other app calls CBS via REST/SOAP/EJB or consumes CBS events — never touches the DB directly.

| V | WAS Topic | Key Artifacts | Deliverable (1-line) |
|---|---|---|---|
| 23 | Service layer, XA txns, ownership migration | New DB `digistack_cbs`, `jdbc/CBSDataSource`; Portal DataSource decommissioned | Portal calls CBS exclusively; migration verified; Notification Svc + Reporting Svc stood up as own EARs |
| 24 | Multi-module EAR, CIF | CIF (Aadhaar/PAN verification, Primary Holder, Nominee) | 1 CIF → multiple linked accounts; CIF/Account Service separated modules |
| 25 | JMS/MQ, multi-EAR startup deps | `digistack-paymenthub-vN.ear` (own EAR) | IMPS real-time, NEFT batch; failed payment → retry → reviewable queue; PH never writes balances |
| 26 | Tomcat, API-first | Mobile app @ `mobile.digistack.cloud` | Login/Balance/Mini-Statement/IMPS Quick Pay via REST only, zero direct DB |
| 27 | Tomcat, conn pool for high-freq | ATM Sim @ `atm.digistack.cloud` | Balance/Withdraw/Statement/PIN-change; blocked-PIN negative case proven |
| 28 | 7th WAS EAR, virtual host routing | Card Portal @ `card.digistack.cloud` (own EAR) | Issue/Activate/Block/PIN-reset via UI; blocked card fails at ATM Sim |
| 29 | Batch scheduling, JMS batch queue | Branch Portal (own EAR); BOD/EOD jobs | Teller cash ops; EOD reconciliation report (NEFT/IMPS tie-out) via WAS schedule |
| 30 | EJB Timer, batch accrual | Loan Service inside CBS (Personal/Home Loan) | Apply→approve→disburse to CBS account; ≥1 EMI auto-debits via EJB Timer |

**P03 exit state:** 9 deployable apps total — 7 WAS EARs (Portal, CBS, Payment Hub, Notification Svc, Reporting Svc, Branch Portal, Card Portal) + 2 Tomcat apps (Mobile, ATM). CBS stays a single (non-split) application for the life of the roadmap.

---

## 5. ARCHITECTURE — ONE current diagram only (grows/changes per Day; superseded diagrams deleted, not stacked)

```
Browser/Channel
   │
   ▼
Enterprise LB (v21) → IHS01/IHS02 (v8, +v20 adv admin) → plugin-cfg.xml
   │
   ▼
WAS ND Cluster: DMGR01 → Node01(JVM01,JVM02)/Node02(JVM03,JVM04) → CLUSTER
   │                                  (Tomcat: Mobile/ATM sims, v26/27,
   │                                   routed by IHS virtual host, NOT plugin)
   ▼
[pre-v23: single digistack-bank-vN.ear]
[v23+: Portal / CBS(sole DB writer) / Payment Hub / Notification Svc /
 Reporting Svc / Branch Portal / Card Portal — 7 EARs, CBS-centric]
   │
   ▼
PostgreSQL: jdbc/BankDS (P01 v7, pre-v23) → digistack_cbs via
jdbc/CBSDataSource (v23+)   +   IBM MQ (v19)   +   SIBus/JMS (v15, moves
to CBS at v23)
```
Use this single diagram, mentally rolling it back to the pre-v23 or
pre-v19/v21 state for early Days — don't maintain separate per-version
diagrams; the version table above already carries the version-specific
artifact names.

---

## 6. DAY→VERSION→PHASE — see `Day-to-Version-Mapping.md` (unchanged, already lean; ranges also implicitly define the 15 phases — no separate phase file needed)

---

## 7. Retired files
Delete from Project knowledge after confirming this file covers your
needs: `P01_Foundation.md`, `P02_Middleware.md`, `P03_Banking_Systems.md`,
`Architecture-Reference.md`, `Team-Org-Chart.md`, `Phase-Map-v1.md`.
Keep: `Project-Instructions-v13.md`, `Progress-Log-v13.md`,
`Day-to-Version-Mapping.md`, this file.
