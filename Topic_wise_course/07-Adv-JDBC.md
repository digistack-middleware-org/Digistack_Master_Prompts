# 📚 WebSphere JDBC — Zero to Expert Course Plan
**Duration:** 40 Days @ 1 hr/day
**Structure per lesson:** Theory (80%) → Real Banking Scenario (15%) → Interview (5%)
**Every topic shown in:** Admin Console steps + wsadmin (Jython) steps

---

## 🟢 PHASE 1: FOUNDATIONS — Module 1 (Days 1–5)

### Day 1 — What is JDBC & Why WebSphere Needs It
- App → WAS → JDBC Provider → DataSource → DB flow
- Trace an Internet Banking "Account Balance" query through the full path

### Day 2 — JDBC Architecture: The 3 Layers
- JDBC Provider vs DataSource vs Connection Pool
- Roles, relationships, dependencies

### Day 3 — Databases Used in Banks
- Oracle (Core Banking), DB2 z/OS (Cards/Accounts mainframe), DB2 LUW, MS SQL Server, Sybase (legacy)

### Day 4 — JDBC Driver Types
- Type 2 vs Type 4
- Why banks default to Type 4 thin drivers; when Type 2 (DB2 z/OS RRS) is mandatory

### Day 5 — Driver Files & Placement
- ojdbc8.jar, db2jcc4.jar
- $WAS_HOME location vs shared library
- WAS Variables (`ORACLE_JDBC_DRIVER_PATH`)

---

## 🟢 PHASE 2: JDBC PROVIDERS — Module 2 (Days 6–10)

### Day 6 — Creating a JDBC Provider
- Console: Resources → JDBC → JDBC Providers → New
- wsadmin: `AdminTask.createJDBCProvider()`

### Day 7 — Scope Discussion
- Cell / Node / Cluster / Server scope
- Bank best practice: Cell-level standardization — and why

### Day 8 — Provider Templates & Types
- Oracle, DB2 Universal, DB2 z/OS RRS, SQL Server (DataDirect vs MS driver)

### Day 9 — Isolation Levels
- READ_COMMITTED vs REPEATABLE_READ vs SERIALIZABLE
- 💼 Scenario: Money transfer double-debit prevention

### Day 10 — Classpath, Native Path, Shared Libraries
- Configuring driver references properly; common mistakes

---

## 🟡 PHASE 3: DATASOURCES (THE HEART) — Module 3 (Days 11–28)

### Day 11 — DataSource Creation
- Console: Resources → JDBC → Data sources → New
- Bank JNDI naming standard: `jdbc/app/env/DB`
- wsadmin: `AdminTask.createDatasource()`

### Day 12 — DataSource Hands-On
- Full Oracle DS build end-to-end
- Scope choice; Test Connection button internals (temp DS trick)

### Day 13 — DB-Specific Properties
- serverName, portNumber, databaseName, driverType=4, URL form
- Oracle vs DB2 vs SQL Server property sheets

### Day 14 — Custom Properties
- Oracle: `oracle.net.CONNECT_TIMEOUT`, `defaultRowPrefetch`
- DB2: `currentSQLID`, `commandTimeout`

### Day 15 — J2C / JAAS Authentication Aliases
- Storing DB credentials securely
- wsadmin: `AdminTask.createAuthDataEntry()`

### Day 16 — Auth Mapping
- Container-managed vs Component-managed auth (res-auth = Container)
- mapping-configuration; when apps override the alias

### Day 17 — DB2 z/OS RRS — Theory
- Type 2 driver, RRS transactional semantics
- Why mainframe cards systems use it

### Day 18 — DB2 z/OS RRS — Build
- Full RRS DataSource setup (console + wsadmin)
- `RRSTransactional=true`; native library path

### Day 19 — RRS Recovery
- recoveryAuthDataRef, recovery context
- WAS crash mid-transaction on z/OS

### Day 20 — XA vs Non-XA Theory
- 1PC vs 2PC flows; when each is allowed; last participant support

### Day 21 — XA — Banking Scenario
- 💼 Scenario: ₹50,000 transfer Savings → Credit Card DB — why XA is mandatory

### Day 22 — XA Recovery
- Transaction log location, recoveryAuthDataRef
- DataSource role in recovery; orphaned XA transactions

### Day 23 — Connection Pooling — Settings
- min/max connections, connectionTimeout, maxIdle, reapTime, unusedTimeout, agedTimeout

### Day 24 — Pool Sizing Math
- 💼 Scenario: 500 TPS payments app → sizing formula
- Dangers of pools too big vs too small

### Day 25 — Pool Protection
- PreTest connection, purgePolicy (EntirePool vs FailingConnectionOnly), connectionTestQuery

### Day 26 — Statement Cache
- Default 10; per-DB sizing
- 💼 Story: open_cursors exceeded Oracle outage (cache × pool × DS math)

### Day 27 — Supporting Config
- WAS Variables recap, shared libraries, test connection via wsadmin

### Day 28 — ✅ Revision (Modules 1–3)
- Rapid-fire 20 questions
- Rebuild Oracle XA DataSource from memory in <30 min

---

## 🟠 PHASE 4: MONITORING & TUNING — Module 4 (Days 29–32)

### Day 29 — PMI Counters
- CreateCount, DestroyCount, PoolSize, FreePoolSize, WaitTime, percentUsed, JDBCTime
- Reading stats in Tivoli / Performance viewer

### Day 30 — Pool Exhaustion
- DSRA0010E diagnosis; wait timeout & aged timeout tuning; real tuning case

### Day 31 — Oracle RAC in WebSphere
- Multiple URLs, Fan enabled/disabled, failed node handling

### Day 32 — DB Failover (DataGuard / HADR)
- Switchover behavior; purge needed after failover

---

## 🔴 PHASE 5: SECURITY — Module 5 (Days 33–35)

### Day 33 — J2C Alias Security
- Alias lifecycle; PropFilePasswordEncoder; security.xml; custom AES password encryption

### Day 34 — Password Rotation ⭐ (Big Bank Topic)
- Zero-downtime DB password rotation: alias update → node sync → dynamic reload
- Bank runbook

### Day 35 — Service Accounts
- LDAP/AD integrated DB accounts; least privilege; bank audit requirements

---

## 🔴 PHASE 6: TROUBLESHOOTING — Module 6 (Days 36–38)

### Day 36 — Error Code Drill + JDBC Trace
- DSRA0010E, DSRA0040E, DSRA0174E, DSRA0302E/0304E, DSRA8040I
- Oracle: ORA-12505, ORA-28000, ORA-00020, ORA-04031
- DB2: SQL1032N, SQL1224N, SQL1042C
- Trace: `com.ibm.ws.rsadapter.*=all`

### Day 37 — Stale Connections, Leaks, Hangs
- Stale connection handling after DB restart; pool purge
- Leak detection (deferred cleanup, custom props)
- Firewall idle-kill → agedTimeout fix (classic bank story)
- Reading javacores for `getConnection()` waits

### Day 38 — Advanced Failures
- open_cursors RCA full walkthrough
- XAER_RMERR, orphaned transactions, WTRN errors — recovery playbook

---

## 🟣 PHASE 7: CAPSTONE LABS + wsadmin — Modules 7+8 (Day 39)

### Day 39 — End-to-End Labs + wsadmin Mastery
- Labs: Internet Banking → Oracle DS; Cards → DB2 z/OS RRS; RAC failover test; EOD batch pool sizing; DR drill
- wsadmin: Jython CRUD for providers, DS, aliases
- 💼 Bulk password rotation script across 100 DataSources
- `AdminControl.testConnection()`, `extractConfigProperties`, audit scripts

---

## 🏁 PHASE 8: INTERVIEW PREP — Module 9 (Day 40)

### Day 40 — Interview & War Stories
- 20 expert-level interview Q&A
- 5 "recent issues faced" answers
- 20 scenario + 20 troubleshooting rapid-fire
- 5 outage war stories
- 5 behavioral/leadership + 5 architecture/design (Lead level)

---

## 📊 Order Summary

| Order | Phase | Module | Days |
|-------|-------|--------|------|
| 1️⃣ | Foundations | M1 | 1–5 |
| 2️⃣ | JDBC Providers | M2 | 6–10 |
| 3️⃣ | **DataSources (Heart)** | M3 | 11–28 |
| 4️⃣ | Monitoring & Tuning | M4 | 29–32 |
| 5️⃣ | Security | M5 | 33–35 |
| 6️⃣ | Troubleshooting | M6 | 36–38 |
| 7️⃣ | Capstone Labs + wsadmin | M7+M8 | 39 |
| 8️⃣ | Interview Prep | M9 | 40 |

---

## ✅ Rules of the Course
1. Miss a day → don't skip content, shift everything by 1
2. Revision days (Day 28) are sacred — skip them and retention drops to ~50%
3. Lab environment is mandatory — reading alone = 40% retention; doing = 90%
4. Every lesson: practice in BOTH Admin Console AND wsadmin

**▶ Say "Start Day 1" to begin.**
