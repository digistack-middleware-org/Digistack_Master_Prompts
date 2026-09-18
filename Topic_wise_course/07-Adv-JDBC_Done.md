# 🏆 THE MASTER COURSE v2.0 — WebSphere: Database + JDBC (COMPLETE)

> **68 Days | ~2 hrs/day | Lead-Level (10-Yr) Ready | Zero Gaps**
>
> Merged DB + JDBC syllabus with all senior/lead topics: Trusted Context, DB2 z/OS
> internals, HADR Client Reroute, FAN/FCF, DataStoreHelper, SSL to DB, Liberty,
> JCA/CICS, capacity planning, PMR process and more.

---

## 📊 Progress Tracker

- [ ] Phase A — Days 1–21 : DB + SQL Fundamentals
- [ ] Phase B Days 22–32 : Real-time DB Ops (DB2/Oracle)
- [ ] Phase C — Days 33–37 : JDBC Providers
- [ ] Phase D — Days 38–52 : DataSources ⭐
- [ ] Phase E — Days 53–59 : Pools + Tuning ⭐
- [ ] Phase F — Days 60–68 : Monitoring + HA/DR ⭐
- [ ] Phase G — Days 69–72 : Security
- [ ] Phase H — Days 73–77 : Troubleshooting Masterclass
- [ ] Phase I — Days 78–85 : wsadmin Automation + Capstone
- [ ] Phase J — Days 86–88 : Mock Interview 🎤

---

# 🟦 PHASE A: DATABASE FOUNDATIONS + SQL (Days 1–21)

| Day | Topic | Practice |
|-----|-------|----------|
| 1 | DB, Table, Row, Column; why banks need DBs | Draw CUSTOMER table |
| 2 | DB2 vs Oracle vs SQL Server in banks | Comparison table from memory |
| 3 | RDBMS + ACID | ₹5000 transfer story, break each letter |
| 4 | Primary Key, Foreign Key | CUSTOMER ↔ ACCOUNT design |
| 5 | Indexes — when they help/hurt | Employee directory analogy |
| 6 | Schema, Tablespace, Instance, client tools | Install DB2 Express-C / sandbox |
| 7 | COMMIT / ROLLBACK + **Week 1 Test** | BEGIN → UPDATE → ROLLBACK |
| 8–10 | SELECT, WHERE, ORDER BY, DISTINCT | 20 queries |
| 11–13 | INSERT, UPDATE, DELETE + bank approval caution | Safe-delete practice |
| 14 | **Level 1 SQL Test** — 15 queries, 30 min | Timed |
| 15–16 | INNER / LEFT / RIGHT JOIN | Orphan record hunt |
| 17 | 3-Table Join: Customer ↔ Account ↔ Txn | Full txn history |
| 18–19 | GROUP BY, HAVING vs WHERE | Branches >1000 txns |
| 20 | Subqueries + CASE WHEN | NEFT/IMPS/UPI mapping |
| 21 | **Level 2 Test** — 20 mixed queries, 60 min | Timed, aloud |

---

# 🟦 PHASE B: REAL-TIME DB OPERATIONS (Days 22–32)

| Day | Topic |
|-----|-------|
| 22 | SYSIBMADM.APPLICATIONS, LOCKWAITS — run + interpret |
| 23 | Oracle VSESSION,VSESSION, VSESSION,VSESSION_LONGOPS |
| 24 | EOD batch monitoring; why queries go slow |
| 25 | DB2: Instance, DB, Tablespaces, Bufferpools, Logs |
| 26 | `db2 connect`, `list applications`, `db2pd` |
| 27 | `db2diag.log` reading |
| 28 | DB2 locks: wait/timeout/deadlock; escalation, LOCKLIST, MAXLOCKS, MAXAPPLS |
| 29 | RUNSTATS, REORG, backup; HADR concept |
| 30 | SQLCODEs (-4499, -911, -1015); Oracle SGA/PGA, SID vs Service Name, Listener |
| 31 | ORA-12505/00020/01555; AWR/ASH reading |
| 32 | **Module Test:** Month-end deadlock + failover stale connections |

---

# 🟦 PHASE C: JDBC PROVIDERS (Days 33–37)

| Day | Topic |
|-----|-------|
| 33 | Type 2 vs Type 4; when Type 2 (RRS) is mandatory |
| 34 | Create JDBC Provider — Console + `AdminTask.createJDBCProvider()` |
| 35 | Scope: Cell/Node/Cluster/Server; Cell-level best practice |
| 36 | Templates: Oracle, DB2 Universal, DB2 z/OS RRS, SQL Server (DataDirect vs MS) |
| 37 | Driver files: ojdbc8.jar, db2jcc4.jar; WAS Variables, classpath mistakes |

---

# 🟦 PHASE D: DATASOURCES (Days 38–52) ⭐

| Day | Topic |
|-----|-------|
| 38 | DataSource creation — every field; JNDI `jdbc/app/env/DB`; Console + wsadmin |
| 39 | Full Oracle DS build; Test Connection internals (temp DS trick) |
| 40 | DB-specific properties: serverName, port, driverType=4, URL forms| 41 | Custom props: `oracle.net.CONNECT_TIMEOUT`, `defaultRowPrefetch`, `currentSQLID`, `commandTimeout` |
| 42 | J2C/JAAS Auth Aliases — `AdminTask.createAuthDataEntry()`; secure storage |
| 43 | Container vs Component-managed auth; mapping-configuration |
| 44 | 🆕 **DB2 Trusted Context + role-based auth** — end-user identity → DB audit |
| 45 | DB2 z/OS RRS theory: why mainframe cards use it |
| 46 | 🆕 **DB2 z/OS internals:** Packages, Plans, Collections, BINDs, RACF IDs ↔ WAS alias, DSNAOINI |
| 47 | RRS build: `RRSTransactional=true`, native path (Console + wsadmin) |
| 48 | RRS Recovery: recoveryAuthDataRef; WAS crash mid-txn on z/OS |
| 49 | **XA theory:** 1PC vs 2PC, last participant support |
| 50 | 💼 XA scenario: ₹50,000 transfer Savings → Credit Card DB — why XA is mandatory |
| 51 | XA Recovery: transaction logs, orphaned XA txns, WTRN errors playbook |
| 52 | **Module Test:** Rebuild Oracle XA DS from memory <30 min ✅ |

---

# 🟦 PHASE E: CONNECTION POOLS + TUNING (Days 53–59)

| Day | Topic |
|-----|-------|
| 53 | Pool settings: min/max, connectionTimeout, maxIdle, reapTime, unusedTimeout, agedTimeout; resource refs |
| 54 | 💼 Pool sizing math: 500 TPS + salary day/EOD; WAS Max vs DB MAXAPPLS |
| 55 | PreTest, purgePolicy, connectionTestQuery |
| 56 | Statement cache + 💼 open_cursors outage RCA (cache × pool × DS math) |
| 57 | PMI/TPV: PoolSize, FreePoolSize, WaitTime, percentUsed; Dynatrace; javacore waits |
| 58 | 🆕 **Capacity Planning (cell-wide):** threads→pool→DB MAXAPPLS ratio, EOD headroom, sizing methodology |
| 59 | 💼 Module Test: 9 AM rush pool maxed; Loan app connection leak |

---

# 🟦 PHASE F: MONITORING, HA/DR & SPECIAL TOPOLOGIES (Days 60–68)

| Day | Topic |
|-----|-------|
| 60 | JDBC trace: `com.ibm.ws.rsadapter.*=all`; slow query vs slow app proof |
| 61 | Pool exhaustion: DSRA0010E diagnosis; orphan transactions |
| 62 | Oracle RAC + 🆕 **FAN/FCF/ONS + UCP** — `fanEnabled`, SCAN, runtime load balancing; stale-connection storm fix |
| 63 | 🆕 **HADR Client Reroute deep-dive:** `clientRerouteAlternateServer/Port`, auto reroute on takeover, purge on reroute |
| 64 | DB Failover: DataGuard/HADR from WAS side; auto-retry props; DR runbook |
| 65 | SIB ME store on DB + outage; 🆕 **ME store schema + corruption recovery**; HTTP Session DB persistence |
| 66 | 🆕 **JPA/Hibernate on WAS:** provider, JPA DS vs JDBC DS, 2nd-level cache pitfalls |
| 67 | Maintenance windows, quiesce, backup; 🆕 Multi-DS LBDS/RRDS overview |
| 68 | **Module Test:** 40-sec page → 2 sec; DR drill scenario |

---

# 🟦 PHASE G: SECURITY (Days 69–72)

| Day | Topic |
|-----|-------|
| 69 | J2C security: PropFilePasswordEncoder, security.xml, Credential Vault, custom AES |
| 70 | ⭐ Password rotation: zero-downtime runbook; least privilege, audit tables |
| 71 | 🆕 **SSL/TLS to the database:** KeyStores/TrustStores, Oracle TCPS, DB2 SSL, sslConfig, cert-expiry outage playbook |
| 72 | 🆕 **Advanced auth:** Oracle Wallet, Kerberos to DB2, LDAP/AD service accounts; 💼 plaintext-password audit scenario |

---

# 🟦 PHASE H: TROUBLESHOOTING MASTERCLASS (Days 73–77)

| Day | Topic |
|-----|-------|
| 73 | Error drill: DSRA0010E/0040E/0174E/0302E, ORA-12505/28000/04031, SQL1032N/-911/-1224N |
| 74 | 🆕 **DataStoreHelper:** default vs custom; SQL error → `StaleConnectionException` mapping |
| 75 | 🆕 **Leak Detection depth leakDetectionThreshold, connection tracking, orphanResourceAssociation; full leak RCA |
| 76 | Stale connections, firewall idle-kill → agedTimeout; pool purge; 💼 2 AM NEFT outage war-room RCA |
| 77 | **Module Test:** open_cursors RCA walkthrough + XAER_RMERR playbook |

---

# 🟦 PHASE I: wsadmin AUTOMATION + CAPSTONE (Days 78–85)

| Day | Topic |
|-----|-------|
| 78 | wsadmin Jython: CRUD for providers, DS, aliases; `AdminControl.testConnection()` |
| 79 | 💼 Bulk password rotation across 100 DataSources; `extractConfigProperties` |
| 80 | Pool health script + CR process/backout plan |
| 81 | 🆕 **JDBC driver upgrades & Fixpack strategy:** cell-wide upgrade, backout, driver-vs-WAS matrix |
| 82 | 🆕 **IBM Support/PMR process:** mustgather, log collection, L2/L3; 🆕 config preservation, golden config / config-drift |
| 83 | 🆕 **Liberty vs Traditional WAS datasources:** `server.xml` `<dataSource>`, `<jdbcDriver>`, migration differences |
| 84 | 🆕 **JCA Resource Adapters + CICS/IMS connectivity:** RAR deployment, connection factories |
| 85 | Capstone: Internet Banking architecture doc + runbooks + 💼 Labs + **Grand Revision:** 20 rapid-fire + full diagram |

---

# 🟦 PHASE J: INTERVIEW PREP (Days 86–88)

| Day | Topic |
|-----|-------|
| 86 | 20 expert Q&A + 20 scenario rapid-fire |
| 87 | 20 troubleshooting rapid-fire + 5 outage war stories + 10-yr bar Qs (capacity, PMRs, config drift, driver upgrades) |
| 88 | **FULL MOCK INTERVIEW** 🎤 + behavioral/leadership + architecture Qs (Lead level) |

---

## 📏 Rules

1. Miss a day → shift everything by 1; use buffer week
2. Every config topic: **Console AND wsadmin** — hands-on mandatory
3. Scenarios answered **aloud**, interview style
4. Weekly/module tests sacred — retention drops without them
5. 🆕 For every outage story, write the **PMR/mustgather + RCA template** version too
6. 🆕 Every HA topic: practice the **failover drill**, not just theory

---

## 🧰 Toolkit (from Day 1)

| Tool | Use |
|------|-----|
| DB2 Express-C / Oracle XE | Sandbox DB |
| WAS Base/ND (9.0) or Liberty | Practice cell |
| wsadmin Jython scripts folder | Build incrementally |
| RCA / Runbook / PMR checklist templates | Reuse in war-room days |
| Diagram: App → WAS → Pool → Driver → DB | Redraw every phase |

---

## ✅ Final Self-Certification (Day 88 exit criteria)

- [ ] Build any DS (Oracle/DB2/z/OS RRS/XA) from memory <30 min
- [ ] Explain 2PC with ₹50K transfer story
- [ ] Answer "HADR takeover — does WAS auto-recover?" with clientReroute detail
- [ ] Do open_cursors RCA with full math
- [ ] Write pool health-check + bulk rotation wsadmin scripts
- [ ] Explain Trusted Context, Wallet, Kerberos trade-offs
- [ ] Compare Traditional vs Liberty DS config
- [ ] 5 personal outage war stories with metrics
- [ ] Pass full mock interview at Lead level
