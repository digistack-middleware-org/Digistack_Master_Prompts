# 📅 DAY-WISE PLAN — WebSphere Admin: Database + SQL Mastery

> **Duration:** 90 Days (+7 Bonus) | **Daily Effort:** 2–3 hours
> **Mix:** Theory 80% / Hands-on 15% / Interview Practice 5%

> **Weekly Rhythm:** Mon–Fri = Learn + Practice | Saturday = Revision + Mini-Test | Sunday = Rest/Buffer
> **Every module ends with:** Banking Scenario + 5 Interview Questions (answer aloud)

---

## 🟦 MODULE 1: Database Fundamentals (Days 1–7)

| Day  | Topic | Practice |
|------|-------|----------|
| Day 1 | What is DB, Table, Row, Column; why banks need DBs | Draw CUSTOMER table on paper: Account No, Name, Balance |
| Day 2 | DB2 vs Oracle vs SQL Server — which bank uses what, why | Make comparison table from memory |
| Day 3 | RDBMS + ACID properties | Story: ₹5000 transfer A→B, break each ACID letter |
| Day 4 | Primary Key, Foreign Key | Design CUSTOMER ↔ ACCOUNT tables with keys |
| Day 5 | Indexes — how lookup works, when index hurts | Analogy: index = bank employee directory |
| Day 6 | Schema, TableSpace, Instance; Client tools overview | Install DB2 Express-C or use online SQL sandbox |
| Day 7 | Transactions: COMMIT / ROLLBACK + **Module 1 Test** | Practice BEGIN → UPDATE → ROLLBACK |

**Weekend Milestone ✅:** Explain ACID + Index to a friend in 5 min without notes.

---

## 🟦 MODULE 2: SQL Queries (Days 8–28) — ⭐ Biggest Module, Practice Heavy

### Level 1 — Basics (Days 8–14)

| Day  | Topic | Practice Queries |
|------|-------|------------------|
| Day 8 | SELECT + WHERE | 5 queries on CUSTOMER table |
| Day 9 | WHERE operators (`=`, `>`, `IN`, `LIKE`, `BETWEEN`, `IS NULL`) | 10 queries: balances, statuses |
| Day 10 | ORDER BY, DISTINCT | Top 10 richest customers, unique branches |
| Day 11 | FETCH FIRST (DB2) vs LIMIT | Top 5 transactions by amount |
| Day 12 | INSERT + UPDATE | Insert customer, update balance |
| Day 13 | DELETE + Bank Caution Story (CR/DBA approval!) | Safe-delete practice with WHERE |
| Day 14 | COMMIT / ROLLBACK + **Level 1 Test (15 queries)** | Timed: 15 queries in 30 min |

### Level 2 — Intermediate (Days 15–22)

| Day  | Topic | Practice Queries |
|------|-------|------------------|
| Day 15 | INNER JOIN (Customer ↔ Account) | 5 join queries |
| Day 16 | LEFT / RIGHT JOIN (customers with no account) | Find orphan records |
| Day 17 | 3-Table Join: Customer ↔ Account ↔ Transaction | Full txn history query |
| Day 18 | GROUP BY + COUNT/SUM/AVG | Txn count per branch |
| Day 19 | HAVING vs WHERE | Branches with >1000 txns |
| Day 20 | Subqueries | Customers above avg balance |
| Day 21 | CASE WHEN | Categorize NEFT/IMPS/UPI |
| Day 22 | **Level 2 Test (20 mixed queries)** | Timed: 60 min |

### Level 3 — WAS Admin Real-Time Queries ⭐ (Days 23–28)

| Day  | Topic | Practice |
|------|-------|----------|
| Day 23 | SYSIBMADM.APPLICATIONS | Run + interpret output |
| Day 24 | SYSIBMADM.LOCKWAITS + SNAPDB | Simulate lock, read view |
| Day 25 | Oracle `VSESSION‘,‘VSESSION`, `VSESSION‘,‘VSESSION_LONGOPS` | Memorize + understand columns |
| Day 26 | Slow query re-run from JDBC trace (concept + timing) | Mock trace reading exercise |
| Day 27 | EOD batch progress tables | Night-batch check scenario |
| Day 28 | Why queries go slow + **MODULE 2 GRAND TEST (25 Qs)** | Answer aloud, interview style |

**Milestone ✅:** You can now write working SQL + explain pool troubleshooting queries. This alone beats most WAS admins.

---

## 🟦 MODULE 3: WebSphere ↔ DB Architecture (Days 29–38)

| Day  | Topic |
|------|-------|
| Day 29 | JDBC drivers: Type 2 vs Type 4 |
| Day 30 | JDBC Provider — what/why, types in WAS |
| Day 31 | DataSource deep concept — the bridge |
| Day 32 | Connection Pooling — lifecycle of a connection |
| Day 33 | J2C Authentication Aliases |
| Day 34 | Pool settings: Min/Max, Timeout, Reap, Orphan, Purge |
| Day 35 | Statement cache + resource references (web.xml) |
| Day 36 | XA vs Non-XA, JTA, Two-Phase Commit |
| Day 37 | Full diagram drawing: App → WAS → Pool → Driver → DB (from memory) |
| Day 38 | **Module Test** + Scenario: 9 AM rush, pool maxed at 100 |

---

## 🟦 MODULE 4: DB2 Deep Dive (Days 39–49)

| Day  | Topic |
|------|-------|
| Day 39 | DB2 architecture: Instance, DB, Tablespaces, Bufferpools, Logs |
| Day 40 | Commands: `db2 connect`, `db2 list applications` |
| Day 41 | `db2pd` + when a WAS admin uses it |
| Day 42 | `db2diag.log` reading practice |
| Day 43 | Locks: wait, timeout, deadlock (two tellers story) |
| Day 44 | Lock escalation + LOCKLIST, MAXLOCKS, MAXAPPLS |
| Day 45 | RUNSTATS, REORG, backup/restore basics |
| Day 46 | DB2 HADR concept (Chennai primary / Bangalore standby) |
| Day 47 | SQLCODEs: -4499, -911, -1015 (memorize + stories) |
| Day 48 | Mini-lab: connect, list apps, read diag log |
| Day 49 | **Module Test** + Scenario: month-end salary deadlock |

---

## 🟦 MODULE 5: Oracle Deep Dive (Days 50–58)

| Day  | Topic |
|------|-------|
| Day 50 | Oracle architecture: Instance, SGA, PGA, Redo logs |
| Day 51 | Tablespaces + ojdbc8.jar in WAS |
| Day 52 | SID vs Service Name, TNS, Listener |
| Day 53 | ORA-12505 / ORA-00020 / ORA-01555 |
| Day 54 | AWR/ASH reports — how you read them as WAS admin |
| Day 55 | Oracle RAC + SCAN — WAS datasource pointing |
| Day 56 | RAC failover → stale connection storm + purge fix |
| Day 57 | Mini-lab: read one sample AWR section |
| Day 58 | **Module Test** + Scenario: failover stale connections |

---

## 🟦 MODULE 6: WAS Datasource Configuration — Hands-On ⭐ (Days 59–68)

| Day  | Topic |
|------|-------|
| Day 59 | Create JDBC Provider (step-by-step, console) |
| Day 60 | Create DataSource — every field explained (DB2 URL) |
| Day 61 | Oracle URL + SQL Server URL formats |
| Day 62 | Custom properties: driverType, connectionTimeout, websphere-default-iso |
| Day 63 | Pool sizing formula: WAS Max vs DB MAXAPPLS |
| Day 64 | Test Connection — behind the button; per cluster vs per node |
| Day 65 | TPV → JDBC pool stats (in-use %, wait time) |
| Day 66 | Enable + read JDBC trace |
| Day 67 | Purge policy: EntirePool vs FailingConnectionOnly |
| Day 68 | **Module Test** + Scenario: Loan app connection leak |

> 💡 If you have WAS installed (or Docker Liberty), do Days 59–64 hands-on. If not, screenshot-walkthroughs — ask and get step-by-step console paths.

---

## 🟦 MODULE 7: Performance Tuning (Days 69–75)

| Day  | Topic |
|------|-------|
| Day 69 | Slow query vs slow app — proving with JDBC trace |
| Day 70 | Pool sizing math for salary day / EOD peak |
| Day 71 | Statement cache tuning + PREPARE_THRESHOLD |
| Day 72 | DB2 memory concept: Bufferpool, SORTHEAP |
| Day 73 | Orphan transactions killing pool |
| Day 74 | Monitoring: TPV, Dynatrace; JVM heap vs connection leak |
| Day 75 | **Module Test** + Scenario: 40-sec page → 2 sec |

---

## 🟦 MODULE 8: High Availability & DR (Days 76–81)

| Day  | Topic |
|------|-------|
| Day 76 | HADR / Data Guard / RAC failover from WAS side |
| Day 77 | Stale connection storm + auto-retry properties |
| Day 78 | HTTP Session DB persistence |
| Day 79 | SIB ME store on DB + Bus datasource outage impact |
| Day 80 | Maintenance windows, quiesce, backup strategy |
| Day 81 | **Module Test** + Scenario: DR drill runbook writing |

---

## 🟦 MODULE 9: Security (Days 82–84)

| Day  | Topic |
|------|-------|
| Day 82 | J2C encryption, PropFilePasswordEncoder, Credential Vault |
| Day 83 | Least-privilege users, SSL to DB, audit tables |
| Day 84 | **Module Test** + Scenario: audit flags plain-text password |

---

## 🟦 MODULE 10: Automation & Scripting (Days 85–88)

| Day  | Topic |
|------|-------|
| Day 85 | wsadmin Jython basics: create DataSource script |
| Day 86 | Password rotation + test-all-datasources scripts |
| Day 87 | Pool health-check script + CR process/backout plan |
| Day 88 | **Module Test** + Scenario: 60 datasources rotation |

---

## 🟦 MODULE 11: Troubleshooting Masterclass (Days 89–90)

| Day  | Topic |
|------|-------|
| Day 89 | Error → Root Cause Map (all errors from Modules 4–6) + war-room method |
| Day 90 | Scenario: 2 AM NEFT outage — full RCA write-up practice |

---

## 🟦 MODULE 12: Capstone + Interview Phase 2 (Days 91–97 — Bonus Week)

| Day  | Task |
|------|------|
| Day 91 | Capstone design: Internet Banking Portal architecture doc |
| Day 92 | Runbooks + RCA template |
| Day 93 | 20 interview Q&A practice (aloud) |
| Day 94 | 5 "recent issues" stories + 5 war stories (write YOUR versions) |
| Day 95 | 20 banking scenario Qs |
| Day 96 | 20 troubleshooting Qs + behavioral Qs |
| Day 97 | **FULL MOCK INTERVIEW** 🎤 |

---

## 📊 Progress Tracker

Module 1 ████ Days 1–7 Fundamentals
Module 2 █████████ Days 8–28 SQL ⭐ (longest — most practice)
Module 3 ████ Days 29–38 WAS-DB Architecture
Module 4 █████ Days 39–49 DB2
Module 5 ████ Days 50–58 Oracle
Module 6 ████ Days 59–68 Datasource Config ⭐ hands-on
Module 7 ███ Days 69–75 Tuning
Module 8 ███ Days 76–81 HA/DR
Module 9 ██ Days 82–84 Security
Module 10 ██ Days 85–88 Scripting
Module 11 ██ Days 89–90 Troubleshooting
Module 12 ███ Days 91–97 Capstone + Interview 🎤


---

## ✅ Rules for Success

1. Never skip the Saturday test — it makes knowledge stick
2. Every scenario question: answer **aloud** like an interview, not in your head
3. Missed a day? Use Sunday buffer — never skip ahead without finishing

---

**▶️ Next Step:** Say *"Start Day 1"* — What is a Database, with the CUSTOMER table 🚀