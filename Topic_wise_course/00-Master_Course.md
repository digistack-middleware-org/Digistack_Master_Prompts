# 🏦 WebSphere ND Administration — Master Course
**Zero → Expert | All 8 Courses Merged & Deduplicated | BankCell01 Environment**
**WAS ND 8.5.5 / 9.0 | Banking Production Ready**

> **476 Raw Days → 374 Master Days | Saves 102 Days (21%) | Zero Content Lost**
> **Daily mix: Theory 80% → Banking Scenario 15% → Interview 5%**
> **Every topic: Admin Console + wsadmin — BOTH, never one only**

---

## 📊 Phase Overview

| Phase | Title | Source Course(s) | Original | Master | Saved |
|-------|-------|-----------------|----------|--------|-------|
| 0 | Platform Foundations | Extracted from Install | — | 3d | NEW |
| 1 | Installation & Fix Packs | 01-Install | 30d | 20d | 10d |
| 2 | Profiles, Federation & Sync | 02-Profiles | 42d | 30d | 12d |
| 3 | wsadmin Fundamentals | ALL 8 courses | ~15d scattered | 5d | ~10d |
| 4 | Clustering & High Availability | 04-Cluster | 41d | 27d | 14d |
| 5 | Application Deployment | 05-Deploy | 84d | 60d | 24d |
| 6 | IHS, Plugin & Session Management | 06-IHS+Session | 78d | 60d | 18d |
| 7 | Database & JDBC | 07-JDBC | 88d | 68d | 20d |
| 8 | SSL / TLS — Full Stack | 09-SSL | 43d | 33d | 10d |
| 9 | User Management & Auth | 08-UserMgmt | 70d | 48d | 22d |
| 10 | Grand Interview & Capstone | All courses | ~50d spread | 20d | ~30d |
| | **TOTAL** | | **476d** | **374d** | **102d ↓21%** |

---

## ✂️ What Was Merged & Why

| Duplication | Where It Appeared | Resolution |
|------------|------------------|------------|
| **wsadmin object basics** | 7 of 8 courses, 2-3d each | Phase 3 (5d dedicated) — all phases reference it |
| **OS/VM setup** | Install Days 3-4 | Phase 0 (3d once) |
| **Session Management (deep)** | Cluster M3-M4 (7d) + IHS course | Phase 6 owns it; Phase 4 = 2d overview only |
| **Plugin deep-dive** | Cluster Day 8 + IHS Phase 5 | Phase 6 owns it; Phase 4 = 1d reference |
| **SSL full depth** | IHS (4d) + UserMgmt (2d) + JDBC (4d) + SSL course | Phase 8 owns it; others = brief context only |
| **LDAP/Registry theory** | UserMgmt + SSL Day 23 + IHS | Phase 9 owns it; Phase 8 = ldaps config step only |
| **App deploy basics** | Cluster M7 + Deploy full | Phase 5 owns it; Phase 4 = 2d cluster-scope only |
| **Backup/Restore** | Install + Profiles + Deploy | Phase 1 = fix-pack backup; Phase 2 = cell config |
| **Log basics** | Install + Profiles + Cluster | Phase 1 sets it up; others add topic-specific logs |
| **Interview prep** | 3-10d at end of EVERY course (~50d total) | Phase 10 (20d structured mock marathon) |

---

## 🗓️ PHASE 0 — Platform Foundations
**3 Days | Pre-install OS Readiness | Extracted from Install Course**

### Days 1-2 — OS Preparation
- RHEL/AIX OS sizing — CPU, RAM, disk partitioning (separate /opt, /var, /tmp for WAS)
- Kernel params: file descriptors (`ulimit -n 65536`), max processes, core dump settings
- Create `wasadmin` OS user + group; sudoers entry; `.bash_profile` with WAS_HOME
- Mount points, filesystem layout best practice for enterprise WAS installs

### Day 3 — Port Matrix & VM Readiness
- Port planning: 9060/9043 (Admin Console), 9080/9443 (App), 8879 (SOAP), 8880 (RMI), 9100 (ORB), 7276/7286 (DRS), 2809 (Bootstrap)
- Firewall pre-open checklist; VirtualBox/VMware VM ready; snapshot before install
- 🏦 Bank scenario: "Prepare 2 VMs for BankCell01 (DMGR node + App node)"

---

## 🗓️ PHASE 1 — Installation & Fix Packs
**20 Days (from 30) | Source: 01-Install Course**
**✂️ Cut: OS prep → Phase 0 (3d); wsadmin intro → Phase 3 (2d); interview + buffer → Phase 10 (5d)**

### Week 1 — Days 1-7 | IM + Silent Install
- Installation Manager (IM) architecture: repositories, packages, shared resources
- Local repository creation from downloaded ZIPs (DMZ servers — no internet)
- Silent install with response file: create, test, run; `imcl listInstalledPackages`
- WAS ND v9 install via response file; `versionInfo.sh` — baseline output
- WAS Base vs ND vs Liberty — feature comparison; which banks use which and why
- Golden image concept: one master response file template, reuse for 60 servers
- 🏦 "How do you install WAS on 60 DMZ servers with no internet?"

### Week 2 — Days 8-13 | Post-Install Basics
- First Dmgr profile creation (brief — deep dive in Phase 2); start/stop; verify console 9043
- Read `SystemOut.log`, `startServer.log`, `startManager.log` — what a clean start looks like
- Admin console first look: cell topology view
- 🏦 "Walk me through a fresh WAS install in a change window"

### Week 3 — Days 14-20 | Fix Packs — The Core
- Fix Pack vs iFix vs Refresh Pack; version matrix (9.0.5.x → `historyInfo.sh`)
- Pre-patch: `profileBackup`, `backupConfig`, versionInfo baseline, change ticket
- Apply fix pack — silent method; **correct order: DMGR → NodeAgent → App Servers**
- Rolling patch for zero downtime: patch node1 cluster members → smoke test → node2
- Java SDK patching — separate from WAS FP; verify `java -version` levels match
- Post-patch verification checklist + deliberate ROLLBACK lab (the war story maker)
- Multi-env strategy: DEV→UAT→PROD parity; soak period; DR parity
- Automation: shell script wrapping `imcl` for bulk patching across 200 JVMs
- 🏦 "How do you patch internet banking with zero downtime?" — write the runbook

---

## 🗓️ PHASE 2 — Profiles, Federation & Sync
**30 Days (from 42) | Source: 02-Profiles Course**
**✂️ Cut: wsadmin intro → Phase 3 (2d); interview pack → Phase 10 (3d); redundant recaps compressed (7d)**

### Week 1 — Days 1-7 | Profile Theory & Directory Structure
- Profile definition vs binaries (runtime vs installation); one install → multiple profiles
- Profile types: AppSrv, Dmgr, Custom, Managed, Admin Agent, Job Manager, Blank
- Directory structure: `/config`, `/logs`, `/bin`, `installedApps`, `temp`, `tranlog`
- Config files deep dive: `server.xml`, `resources.xml`, `security.xml`, `variables.xml`, `serverindex.xml`
- Bank naming standards: `Dmgr01`, `AppSrv01`, `Custom01`, `BankNode01` — why standards matter
- Single vs multiple profiles per host (isolation, patching, ports)
- Lab: Install WAS 9.0 ND; tooling setup; verify with `versionInfo.sh`

### Week 2 — Days 8-14 | Creating Profiles (Hands-On Heavy)
- PMT (Profile Management Tool): Typical vs Advanced wizard
- `manageprofiles.sh -create` key flags: `-templatePath -profileName -profilePath -hostName -nodeName -cellName -enableAdminSecurity`
- Templates location (`profileTemplates/`), port conflict detection, portdef files
- Profile lifecycle: `-listProfiles`, `-delete`, `-backupProfile`, `-restoreProfile`, `-augment/-unaugment`
- Silent profile creation with response file; verify with `-listProfiles`
- Admin console tour: Nodes, NodeAgents, Cell pages; SOAP 8879 vs RMI 8880
- Logs: SystemOut, SystemErr, FFDC locations; "SOAPException — first 3 checks?"
- Break/fix lab: create/delete/verify 2-3 profiles end-to-end

### Week 3 — Days 15-21 | ⭐ Federation — Build BankCell01
- Federation theory: config copy to DMGR, cert/trust exchange, node agent creation
- `addNode.sh` all flags: `-host -port -username -password -includeapps -excludeapps -asexistingnode -corbaloginport -profileName`
- SOAP 8879 — why it matters for federation
- **🎯 TRAP: Federation via Admin Console does NOT exist — CLI only!**
- Federating with admin security ON — credentials, trust stores
- 🏦 LAB: Build BankCell01 — start DMGR, `addNode.sh` on Custom01, verify node in console, start nodeagent
- `removeNode.sh` internals + re-federation with `-asexistingnode` (keeps node name)
- Federation failures: ADMU0111E, SOAP exceptions, cert errors, firewall ports, reading `addNode.log`
- 🏦 Weekend change window for "Loan App" node — ticket, pre-checks, rollback plan

### Week 4 — Days 22-30 | ⭐ Synchronization + Cell Internals
- DMGR = master config; node agent file sync service; pull vs push; sync intervals
- Admin Console Full Resynchronize; `syncNode.sh`; Jython script to sync all nodes
- **"Changes Saved ≠ Changes Active"** — the #1 junior mistake; delta vs full resync
- 🏦 JVM heap change on Payments cluster: DMGR → sync → restart sequence
- Sync troubleshooting: clock skew, disk full, permissions, stale nodeagent, "Not Synchronized"
- `backupConfig` / `restoreConfig`; what happens when DMGR is down (apps run, sync fails)
- Profile internals: `serverindex.xml` (ports!), `profileRegistry.xml`, config corruption recovery
- DMGR rebuild trick: recreate + restore config
- Break/fix lab: stop nodeagent, wrong clock, stale config — diagnose and fix

---

## 🗓️ PHASE 3 — wsadmin Fundamentals
**5 Days (NEW — Extracted from ALL 8 Courses)**
**This phase replaces wsadmin intro blocks in 7 of 8 courses (~15 scattered days → 5 focused days)**

### Day 1 — Overview + Connection Modes
- wsadmin vs Admin Console: when to use which; banks use wsadmin for everything repeatable
- Connection modes: `-conntype SOAP -host dmgr -port 8879` vs NONE (offline) vs RMI 8880
- Invocation modes: interactive (`wsadmin.sh`), script file (`-f script.py`), inline (`-c "..."`)
- `wsadmin.properties`; setting default host/port; running as wasadmin

### Day 2 — AdminApp
- `AdminApp.list()`, `install()`, `installInteractive()`, `edit()`, `update()`, `export()`, `uninstall()`
- `AdminApp.isAppReady('AppName')` — never assume deploy is done without checking
- Key install options: `-appname -cluster -contextroot -virtualhost -MapModulesToServers`

### Day 3 — AdminConfig
- `AdminConfig.list('Server')`, `getid()`, `create()`, `modify()`, `remove()`
- **THE SAVE TRAP: `AdminConfig.save()` MUST be called or all changes are lost on wsadmin exit**
- Navigating config IDs; modifying JVM heap, thread pools via wsadmin

### Day 4 — AdminControl + AdminTask
- `AdminControl.startServer('server1','node1')`, `stopServer()`, `getAttribute()`, `invoke()`
- AdminTask: `createCluster()`, `createClusterMember()`, `createJDBCProvider()`, `createAuthDataEntry()`, `mapUsersToAdminRole()`
- AdminTask vs AdminConfig — when to use which (AdminTask abstracts the config complexity)

### Day 5 — Jython Scripting Patterns
- Variables, loops (`for x in AdminConfig.list(…).splitlines()`), conditions, error handling (try/except)
- Reading properties files; building reusable functions; passing arguments to scripts
- Lab: Write a health-check script (server status + sync status + app status) — save it, reuse every phase
- 20 essential wsadmin commands cheat sheet

---

## 🗓️ PHASE 4 — Clustering & High Availability
**27 Days (from 41) | Source: 04-Cluster Course**
**✂️ Cut: Session deep-dive M3-M4 → Phase 6 (7d); Plugin deep-dive → Phase 6 (1d overview kept); Interview M11 + buffer → Phase 10 (8d)**

### Week 1 — Days 1-6 | 🔒 M1+M2 Cluster Creation (Goldmine)
- Single server vs Cluster vs Node vs Cell; Vertical vs Horizontal clusters (diagram both)
- HA / Failover / Load Balancing / Scalability — ATM & NetBanking examples
- Bank topology: DMGR in DR DC, nodes in Zone-A / Zone-B
- Create cluster via console: bank naming `PayCluster_A1`, `PayCluster_B1`
- Create cluster via wsadmin: `AdminTask.createCluster()`, `createClusterMember()`
- Cluster operations: Start / Stop / **Ripplestart** — console + wsadmin
- `AdminControl.invoke(nodeAgent, "rippleStart")`; staggered rolling restarts in Prod
- Delete cluster members safely (order, cleanup, plugin regen)
- BankCell01 lab: 4-member cluster (2 vertical + 2 horizontal)

### Week 2 — Days 7-13 | WLM Overview + Session Intro + SIBus
- HTTP WLM: plugin Round Robin vs Random; full routing flow Web→Plugin→Cluster (deep dive → Phase 6)
- Session affinity overview: Clone IDs, JSESSIONID structure (2d overview — full depth in Phase 6)
- SIBus: Bus, Messaging Engines, cluster bus members; HA policy vs Scalability policy; file store vs data store
- ME placement policy; SWIFT queue scenario — ME failover during patching
- SIBus lab: create bus + ME on PaymentCluster; verify ME starts on one member

### Week 3 — Days 14-20 | 🔒 M5 Core Groups & HA Manager (Goldmine)
- Core group concept, core group bridges, access points — draw topology from memory
- Preferred servers, failover policies; DMGR in core group
- Transaction log failover: shared NFS/SAN, RRM (recovery restart), peer recovery of hung transactions
- Singleton services: scheduled tasks, JMS in a cluster — why only one member runs; singleton failover
- 🏦 Core Banking MDB cluster: JMS processed by one member, failover on crash
- IBM TSA (Tivoli System Automation) awareness — how banks automate DMGR HA

### Week 4 — Days 21-27 | Deploy-to-Cluster + 🔒 M9 Troubleshooting
- Deploy to cluster: map modules to cluster target; cluster-scope shared libs/datasources (**scope trap!**)
- JVM heap math per cluster member; thread pools + connection pools sizing
- 🔒 M9: cluster members inconsistent state; session not failing over; stale `plugin-cfg.xml`
- Split brain / core group issues; ripplestart hanging — diagnosis steps
- javacore on hung member; wsadmin cluster health-check script (builds on Phase 3)
- 🏦 2 AM outage: plugin 503s, member hung, javacore shows DB pool exhaustion → remove → fix → re-add

---

## 🗓️ PHASE 5 — Application Deployment
**60 Days (from 84) | Source: 05-Deploy Course**
**✂️ Cut: wsadmin basics → Phase 3 (2d); CI/CD scripting patterns → Phase 3 covers (3d); Final Mastery Week + interview → Phase 10 (7d); per-module compression (12d)**

### M1 — Days 1-6 | EAR/WAR/JAR Packaging
- JAR vs WAR vs EAR structures; why banks use EAR (shared libs, multiple WARs, one deploy unit)
- Inside WAR: `WEB-INF/web.xml`, `ibm-web-bnd.xml`, `ibm-web-ext.xml`, classes/, lib/
- Inside EAR: `META-INF/application.xml`, `deployment.xml`, `ibmconfig/`
- Context root: 3 places it's defined + which one wins
- Shared libraries vs bundled JARs; Java EE vs WAS version matrix (8.5.5=EE6, 9=EE7)
- LoanPortal.ear banking scenario

### M2 — Days 7-13 | Console Deployment
- Fast Path vs Detailed — when to use which
- Install steps 1-9: file → MapModulesToServers → VH mapping → context root → shared libs → JSP pre-compile → save → sync
- Shared library scope: server vs node vs cluster vs cell
- Config repository: `config/cells/.../applications/`
- CardOffers.ear: admin WAR to cluster1, public WAR to cluster2 — draw the mapping
- Lab: full console install, no notes, under 15 min — time yourself

### M3 — Days 14-18 | wsadmin Deployment (references Phase 3)
- `AdminApp.install()` with `-MapModulesToServers`, `-MapWebModToVH`, `-MapResRefToEJB`
- `AdminApp.installInteractive()` — safe option discovery
- Bindings XML files — bank "standardized bindings" pattern
- `isAppReady()`, `edit()`, `AdminConfig.save()` — cost of NOT saving
- Deploy script: install → isAppReady → sync → verify

### M4 — Days 19-25 | Class Loading
- Classloader hierarchy: Application → WAR → WAS extensions → JVM
- PARENT_FIRST vs PARENT_LAST (EAR vs WAR level)
- Shared library `isolated=true` vs referenced; single vs multiple classloader policy
- Errors: `ClassNotFoundException` vs `NoClassDefFoundError` vs `LinkageError` — flashcards: error → cause → fix
- PaymentsUI.ear: NoSuchMethodError → PARENT_LAST fix; write a 5-line incident ticket

### M5 — Days 26-31 | Update, Rollout & Rollback
- Install new vs Update existing; full vs partial update; metadata-only updates
- **Rollout Update (`rolloutAppUpdate`)** — member-by-member with session affinity
- `AdminApp.export`/import — backup before every change; bad update → rollback from export
- Application versioning — why banks track it
- 🏦 Mobile banking 2AM release — wrong way (outage) vs right way (rollout); write the runbook

### M6 — Days 32-38 | Virtual Hosts (Full Depth)
- What is a VH; `default_host` vs custom; host aliases — port matching logic
- The classic 404: alias doesn't cover incoming port — break it deliberately, then fix it
- VH + IHS + plugin relationship; read `plugin-cfg.xml`, find your VH
- Map modules to VH via console AND wsadmin
- LoanDocs.ear 404 scenario — troubleshooting runbook

### M7 — Days 39-44 | Multi-Cluster Deployment
- One EAR, modules to DIFFERENT clusters; deployment.xml differences
- Scope conflicts — **classic 10-yr interview trap**
- Node sync: verifying it REALLY happened (compare config timestamps)
- 🏦 Corp banking scenario: DMZ + internal clusters, 2 firewalls — draw architecture

### M8 — Days 45-51 | Resource Binding
- Resource refs: `res-ref` in web.xml vs server JNDI; `MapResRefToEJB` full walkthrough
- Env-entries, `MapResEnvRefToRes`, JPA datasource mapping
- JMS connection factory & destination mapping
- Pre-provided binding files — "bindings package" pattern
- 🏦 NameNotFoundException: UAT vs Prod JNDI mismatch; write your JNDI registry standard

### M9-M11 — Days 52-60 | CI/CD + Troubleshooting + Governance
- CI/CD: Jenkins → wsadmin Ant tasks → smoke test; properties-driven promotion (same script, 3 envs)
- Deployment validation checklist (pre-checks, smoke URL, plugin, logs) — **interview gold**
- Failed deployments: ADMA errors, duplicate context root, JNDI conflict, stuck app (`isAppReady`, force stop)
- FFDC, SystemOut, amj logs; FAILED install leaves running app untouched — prove it in lab
- 🏦 2AM prod failure + clean rollback procedure — write the incident timeline (war story)
- Governance: change management/CAB, context root/JNDI/VH naming registries, environment parity
- Config drift detection; clean decommissioning: plugin, VH aliases, shared libs, dirs

---

## 🗓️ PHASE 6 — IHS, Plugin & Session Management
**60 Days (from 78) | Source: 06-IHS+Session Course**
**✂️ Cut: SSL depth (Days 25-28) → Phase 8, 2d intro kept here; Interview marathon Phase 17 (10d) → Phase 10; recap compression (6d)**

### Days 1-10 | Web Foundations + IHS Basics + httpd.conf Deep Dive
- HTTP/HTTPS flow, ports, DNS; status codes 200/301/302/403/404/500/502/503
- Trace: Browser → Firewall → IHS → Plugin → WAS → DB
- What is IHS, why in front of WAS, Plugin as bridge; DMZ architecture
- IHS install (IM + silent); managed vs unmanaged node; directory structure, error_log/access_log
- 🔒 httpd.conf deep dive (80% of daily IHS job): ServerRoot, Listen, ServerName, LoadModule
- VirtualHosts: retail + corporate on one IHS (theory + hands-on: edit → configtest → restart → test)
- DocumentRoot, Alias, Redirect, Include, Timeout, KeepAlive, MaxClients for bank peak load
- `apachectl`: start/stop/restart/graceful/configtest — **always configtest before restart**

### Days 11-22 | ⭐ Plugin + Session Affinity (Heart of the Merge)
- plugin-cfg.xml Part 1: `<Config>`, `<Log>`, `<VirtualHostGroup>`, `<ServerCluster>`
- plugin-cfg.xml Part 2: `<Server>`, `<Transport>`, `<UriGroup>`, `<Route>`
- Load balancing: Round Robin, weighted; **session affinity theory (teller analogy)**
- **Clone IDs decoded 🔥** — JSESSIONID `:` separator, sticky sessions end-to-end
- Generate & propagate plugin (Console + manual); hand-editing plugin safely
- `RetryInterval`, `ServerIOTimeout`, `ServerIOTimeoutRetry` — bank peak tuning
- 🏦 4 JVMs, 50K NetBanking users — "your" JVM dies mid-transfer → Primary/Backup, RetryInterval
- HTTP session basics: JSESSIONID birth (Set-Cookie), sessions in JVM memory, login/logout, timeout
- Full WAS–IHS integration: web server definition → plugin → deploy test app → verify via 80/443
- 🔥 JVM restart = sessions LOST demo; `http_plugin.log` reading; 503 diagnosis

### Days 23-24 | SSL Intro (Full Depth → Phase 8)
- Why HTTPS between Browser→IHS and Plugin→WAS; where SSL terminates; handshake overview (2d only)
- 📌 Full cert management, keystores, WAS SSL internals → **Phase 8**; here just: what is a cert, why it expires

### Days 25-37 | ⭐ M-to-M Replication + DB Persistence
- M-to-M: photocopy teller analogy; replication domain; replica number/backup count
- Modes (both/server/client); timeout, frequency, disk offload
- Console config step-by-step (cluster → replica domain); wsadmin/Jython DRSSettings via AdminConfig
- PaymentCluster 4-JVM backup map
- ⚠️ **Affinity ≠ Replication ≠ Persistence — the classic trick Q decoded**
- DB persistence: prerequisites (schema, DataSource, JNDI); persistence schedule; performance reality
- Console + wsadmin config for DB persistence; M-to-M vs DB decision guide
- Tuning replica count/frequency/trigger; session cache vs store; 🏦 capacity math: 50K users
- Security-integrated sessions; what survives failover vs forces re-login

### Days 38-51 | IHS Security + HA + ⭐ Failover Mastery
- Hardening 1: ServerTokens Prod, disable TRACE, restrict methods
- Hardening 2: htpasswd, IP control; 🏦 PCI-DSS — kill TLS 1.0 on 40 servers; HTTP→HTTPS redirect
- mod_rewrite: RewriteRule/RewriteCond, flags; force HTTPS + old→new app redirect
- 🏦 Maintenance page: 2 AM EOD batch rewrite except internal IPs
- HA: IHS behind F5, plugin backups; 🏦 DC+DR 11AM peak failure
- Performance: MPM, ThreadsPerChild, KeepAlive, mod_deflate; 🏦 10x festival spike
- 🔒 **Full failover deep dive:** JVM1 dies mid-transfer → user lands on JVM2 (whiteboard walk-through)
- M-to-M failover timeline (ms reconstruction) vs DB persistence timeline; what's STILL lost
- Anti-patterns that break failover — **the #1 10-yr interview question**

### Days 52-60 | Troubleshooting + PROD Ops + Expert Design
- 503 & 404 flowcharts; session scenarios S1-S10:
  - Random logouts at peak; rolling restart gone wrong
  - Re-login loop on JVM2 (clone ID!); UAT works PROD fails (config drift)
  - Logout every 30 min; OutOfMemoryError from sessions
  - DRS not replicating; session collision after VM clone
  - 🏦 DataSource down → logins fail; 🏦 payment lost mid-transfer on failover
- 🏦 Rolling restart with zero session loss (drain); zero-session-loss deployment
- Session monitoring (PMI, TPV); UAT vs PROD comparison; audit/timeout compliance
- Session strategy design for 3-tier bank app; 🏦 DR across sites — what's realistic
- Session sizing math: users × size × replicas; "Design for PaymentCluster" answer template

---

## 🗓️ PHASE 7 — Database & JDBC
**68 Days (from 88) | Source: 07-JDBC Course**
**✂️ Cut: SQL Phase A 21d→7d (compressed practice); wsadmin basics → Phase 3 (2d); Interview Phase J → Phase 10 (3d); minor compression (8d)**

### Phase A — Days 1-7 | DB + SQL Foundations (Compressed)
- DB2 vs Oracle vs SQL Server in banks; RDBMS + ACID (₹5000 transfer story, break each letter)
- Primary Key, Foreign Key; Indexes — when they help/hurt; Schema, Tablespace, Instance
- COMMIT / ROLLBACK; SELECT, WHERE, INNER/LEFT/RIGHT JOIN, GROUP BY, HAVING, Subqueries, CASE WHEN
- Focused SQL lab: 20 banking queries (customer, account, transaction tables)
- Install DB2 Express-C / Oracle XE sandbox

### Phase B — Days 8-18 | Real-Time DB Operations
- DB2: `SYSIBMADM.APPLICATIONS`, `LOCKWAITS` — run + interpret
- Oracle: `V$SESSION`, `V$SESSION_LONGOPS`
- EOD batch monitoring; why queries go slow
- DB2: Instance, DB, Tablespaces, Bufferpools, Logs; `db2 connect`, `list applications`, `db2pd`
- `db2diag.log` reading; DB2 locks: wait/timeout/deadlock; escalation, LOCKLIST, MAXLOCKS, MAXAPPLS
- RUNSTATS, REORG, backup; HADR concept
- SQLCODEs: -4499, -911, -1015; Oracle SGA/PGA, SID vs Service Name, Listener, AWR/ASH reading
- ORA-12505/00020/01555; Month-end deadlock + failover stale connections scenario

### Phase C — Days 19-23 | JDBC Providers
- Type 2 vs Type 4; when Type 2 (RRS) is mandatory (mainframe)
- Create JDBC Provider: Console + `AdminTask.createJDBCProvider()`
- Scope: Cell/Node/Cluster/Server — Cell-level best practice
- Templates: Oracle, DB2 Universal, DB2 z/OS RRS, SQL Server (DataDirect vs MS)
- Driver files: `ojdbc8.jar`, `db2jcc4.jar`; WAS Variables, classpath mistakes

### Phase D — Days 24-38 | ⭐ DataSources (Full Depth)
- DataSource creation: every field; JNDI naming `jdbc/app/env/DB`; Console + wsadmin
- Full Oracle DS build; Test Connection internals (temp DS trick)
- DB-specific props: serverName, port, driverType=4, URL forms
- Custom props: `oracle.net.CONNECT_TIMEOUT`, `defaultRowPrefetch`, `currentSQLID`, `commandTimeout`
- J2C/JAAS Auth Aliases — `AdminTask.createAuthDataEntry()`; secure storage
- Container vs Component-managed auth; mapping-configuration
- **DB2 Trusted Context** + role-based auth — end-user identity → DB audit trail
- DB2 z/OS RRS theory: why mainframe cards use it
- DB2 z/OS internals: Packages, Plans, Collections, BINDs, RACF IDs ↔ WAS alias, DSNAOINI
- RRS build: `RRSTransactional=true`, native path (Console + wsadmin)
- RRS Recovery: recoveryAuthDataRef; WAS crash mid-txn on z/OS
- **XA theory:** 1PC vs 2PC; last participant support
- 🏦 ₹50,000 transfer Savings → Credit Card — why XA is mandatory
- XA Recovery: transaction logs, orphaned XA txns, WTRN errors playbook
- Module Test: Rebuild Oracle XA DS from memory in <30 min

### Phase E — Days 39-45 | ⭐ Connection Pools + Tuning
- Pool settings: min/max, connectionTimeout, maxIdle, reapTime, unusedTimeout, agedTimeout; resource refs
- 🏦 Pool sizing math: 500 TPS + salary day/EOD; WAS Max vs DB MAXAPPLS
- PreTest, purgePolicy, connectionTestQuery
- Statement cache + 🏦 open_cursors outage RCA (cache × pool × DS math)
- PMI/TPV: PoolSize, FreePoolSize, WaitTime, percentUsed; Dynatrace; javacore waits
- **Capacity Planning (cell-wide):** threads→pool→DB MAXAPPLS ratio, EOD headroom
- Module Test: 9 AM rush pool maxed; Loan app connection leak

### Phase F — Days 46-53 | ⭐ Monitoring + HA/DR + Special Topologies
- JDBC trace: `com.ibm.ws.rsadapter.*=all`; slow query vs slow app proof
- Pool exhaustion: DSRA0010E diagnosis; orphaned transactions
- **Oracle RAC + FAN/FCF/ONS + UCP** — `fanEnabled`, SCAN, runtime load balancing; stale-connection storm fix
- **HADR Client Reroute:** `clientRerouteAlternateServer/Port`, auto reroute on takeover, purge on reroute
- DB Failover from WAS side: DataGuard/HADR; auto-retry props; DR runbook
- SIB ME store on DB + outage; ME store schema + corruption recovery
- HTTP Session DB persistence (cross-reference Phase 6)
- **JPA/Hibernate on WAS:** provider, JPA DS vs JDBC DS, 2nd-level cache pitfalls
- Maintenance windows, quiesce, backup; Multi-DS LBDS/RRDS overview
- Module Test: 40-sec page → 2 sec; DR drill scenario

### Phase G — Days 54-57 | Security
- J2C security: PropFilePasswordEncoder, security.xml, Credential Vault, custom AES
- ⭐ Password rotation: zero-downtime runbook (2-alias trick); least privilege, audit tables
- **SSL/TLS to the DB:** KeyStores/TrustStores, Oracle TCPS, DB2 SSL, sslConfig — *cert theory → Phase 8*
- **Oracle Wallet, Kerberos to DB2, LDAP/AD service accounts**; 🏦 plaintext-password audit scenario

### Phase H — Days 58-62 | Troubleshooting Masterclass
- Error drill: DSRA0010E/0040E/0174E/0302E, ORA-12505/28000/04031, SQL1032N/-911/-1224N
- **DataStoreHelper:** default vs custom; SQL error → `StaleConnectionException` mapping
- **Leak detection:** `leakDetectionThreshold`, connection tracking, `orphanResourceAssociation`; full leak RCA
- Stale connections, firewall idle-kill → agedTimeout; pool purge
- 🏦 2 AM NEFT outage war-room RCA; open_cursors RCA walkthrough + XAER_RMERR playbook

### Phase I — Days 63-68 | wsadmin Automation + Capstone
- wsadmin Jython: CRUD for providers, DS, aliases; `AdminControl.testConnection()`
- 🏦 Bulk password rotation across 100 DataSources; `extractConfigProperties`
- Pool health script + CR process/backout plan
- **JDBC driver upgrades & Fix Pack strategy:** cell-wide upgrade, backout, driver-vs-WAS matrix
- **IBM Support/PMR process:** mustgather, log collection, L2/L3; config preservation
- **Liberty vs Traditional WAS datasources:** `server.xml` `<dataSource>`, `<jdbcDriver>`, migration differences
- **JCA Resource Adapters + CICS/IMS connectivity:** RAR deployment, connection factories
- Capstone: Internet Banking architecture doc + runbooks + Grand Revision: 20 rapid-fire + full diagram

---

## 🗓️ PHASE 8 — SSL / TLS — Full Stack
**33 Days (from 43) | Source: 09-SSL Course**
**✂️ Cut: IHS cert config overlap with Phase 6 (3d condensed); Interview pack → Phase 10 (5d); phase compression (2d)**

### Days 1-5 | SSL Foundations
- SSL/TLS: vault handshake analogy; symmetric vs asymmetric encryption
- What's inside a certificate: CN, SAN, issuer, validity, public/private key pairs
- CA vs self-signed (bank rules); what a truststore really means
- Handshake step-by-step; TLS versions & cipher suites (why banks ban TLSv1.0)
- One-way vs mutual SSL; Lab: create & inspect cert with iKeyman + keytool

### Days 6-10 | Keystores & Cert Management
- Keystore vs Truststore — **the #1 confusion**; PKCS12/JKS/CMS-KDB types
- iKeyman vs keytool vs openssl — when to use which
- WAS default certs: DefaultPersonal, DefaultRoot — what they are and why replace them
- Chained certs: root→intermediate→server; importing signer certs (console + wsadmin)
- CSR generation in WAS + bank CA signing; cert expiry monitoring script
- SAN/wildcard certs for PaymentCluster
- Lab: full CSR→sign→replace default cert on DMGR

### Days 11-18 | WAS SSL Internals + Full Browser→WAS Chain
- SSL config objects in security.xml: SSLConfig, key sets, the object model
- SSL config groups & scopes: cell→node→server inheritance (**the scope trap**)
- Outbound vs inbound SSL — how WAS picks a config; admin console SSL (9043)
- Node agent ↔ DMGR SSL (sync/file transfer failures); CSIv2/SAS for EJB/IIOP
- Safe default-cert replacement procedure; dynamic outbound endpoint (`ssl.Default`)
- Console + wsadmin: `getSSLConfig`, `createSSLConfig`, change key/trust
- Browser → IHS: enabling HTTPS 443, virtual host mapping
- IHS keystore (kdb/GSKit): server cert + chain; `plugin-cfg.xml` Secure transport
- End-to-end HTTPS trace: browser dev tools + plugin log + SystemOut
- Lab: full HTTPS IHS → PaymentCluster with valid chain

### Days 19-23 | Backend SSL: WAS→LDAP, MQ, DB
- WAS → LDAP over SSL (ldaps:636): signer import, test, why login dies on expiry
- WAS → MQ SSL: **cipher suite name mapping WAS↔MQ — the notorious mismatch**
- WAS → DB2/Oracle over SSL: JDBC URL props, per-DataSource truststore; J2C alias + SSL
- Certificate-based DB auth (no passwords); scoped outbound SSL configs per backend
- Lab: enable ldaps for registry + SSL DataSource for PaymentDB

### Days 24-31 | ⭐ SSL Troubleshooting Mastery
- `SSLHandshakeException: certificate_unknown` — trust missing (the daily classic)
- **Certificate expired** — console + app + backend all down at once (war story)
- `unable to find valid certification path` — incomplete chain (intermediate missing)
- Hostname verification failure — CN/SAN mismatch (CertPathChecker)
- `no cipher suites in common` — TLS version/cipher mismatch diagnosis
- Node agent won't sync after cert change; plugin log handshake failure → 503 on HTTPS only
- LDAP SSL broke → nobody can login; MQ channel cipher mismatch walkthrough
- Enabling SSL trace (`-Djavax.net.debug`) + reading handshake dumps
- 🧪 Gauntlet: 5 mock SSL outages from real logs

### Days 32-33 | Production Hardening + Capstone
- Zero-downtime cert renewal: backup→import→swap→validate; bank ITIL change flow
- Disable TLSv1.0/1.1 & weak ciphers: audit compliance (console + ssl.xml + JVM props)
- Cert expiry monitoring automation across all nodes; DR keystore/cert parity
- Multi-cell SSL architecture: DMGR chains, plugin trust, PROD/UAT/DR design
- Capstone: Secure BankCell01 fully — all default certs replaced, TLSv1.2 only, ldaps + SSL DB, expiry monitoring

---

## 🗓️ PHASE 9 — User Management & Authentication
**48 Days (from 70) | Source: 08-UserMgmt Course**
**✂️ Cut: SSL section → reference Phase 8 (1d); wsadmin basics → Phase 3 (2d); Interview pack → Phase 10 (8d); role/LTPA/SSO modules compressed (11d)**

### Days 1-3 | Foundations (Compressed)
- What is WAS security; AuthN vs AuthZ (bank gate analogy); Realm/Principal/Credential/Subject
- What is a User Registry; 3 registry types comparison
- Enable/Disable Global Security: console + wsadmin; what breaks when security turns ON
- Lab: enable security in BankCell01 safely

### Days 4-17 | ⭐ Registries — Full Pace (14 Days, Untouched)
- Local OS registry — how it works, why banks reject it
- Standalone LDAP: concepts, full console config, full wsadmin config
- Federated Repositories: why it's the banking standard
- Supported LDAPs: AD, TDS, OID, eDirectory — bank usage patterns
- Bind DN & password — the service account that can kill your cell
- Base DN + user/group/member filters — decoded with AD example
- Federated repo: multiple LDAPs + file repository in one realm
- FileRegistry.xml; Realm config & primary realm
- LDAP failover — multiple hosts, SSL (ldaps:636) — *cert details → Phase 8*
- 🏦 Changing registries in production: TDS→AD zero-downtime story
- Lab: Connect BankCell01 to AD, login with AD user
- Registry assessment: config quiz + fix a broken LDAP config

### Days 18-21 | Admin Roles
- 7 admin roles + full capability matrix (banking staff examples)
- Add users/groups to roles: console + `mapUsersToAdminRole`; groups-first practice
- Multi-admin risk; Admin Security Manager; authorization groups
- Password change procedure + **lockout recovery & break-glass**
- Lab: build role structure for BankCell01 (WAS_Admins, WAS_Ops, WAS_Auditors)

### Days 22-25 | Application Users
- App vs admin security; security roles in EAR/WAR
- Map app roles: console + wsadmin; Everyone vs All Authenticated
- RunAs roles & service IDs; `isUserInRole` internals; J2EE vs registry groups
- Lab: Secure PaymentApp with Teller/Supervisor/Auditor roles

### Days 26-29 | LTPA & Passwords
- LTPA tokens & keys; `ltpa.jceks` export/import — **DR must match PROD!**
- `{xor}` password encoding — the audit trap
- J2C aliases for DB2/MQ: console + wsadmin
- Zero-downtime rotation (2-alias trick); LTPA expiry outage story
- Lab: rotate PaymentCluster DB password with zero downtime

### Days 30-32 | SSO + SSL Reference
- SSO config, LtpaToken2 cookie, domain name; cross-cell SSO (PROD+DR keys must match)
- Token vs session timeout; web vs app SSO; SPNEGO/proxy concepts
- SSL for LDAP (ldaps:636) config steps — *cert theory → Phase 8 (1d reference only)*
- Lab: single login across console + PaymentApp

### Days 33-44 | ⭐⭐ Troubleshooting — Full Pace (12 Days)
- Each: Symptoms → Causes → Investigation → Logs (CWWIM/CWWIS/SECJ codes) → Fix → Validation
- Cannot login to Admin Console
- User locked / not found in registry
- LDAP connection refused / bind DN failed — **server won't start!**
- Users invisible after registry change
- Login works but buttons missing (Monitor vs Administrator)
- Role mapping lost after federated repo change
- 401/403 on PaymentApp
- Re-login every request (LTPA/SSO broken)
- SSO broken between PROD and DR
- J2C wrong password → DataSource fails → **payments down**
- Cert expired → LDAP SSL failure → nobody can login
- Admin password forgotten — full recovery walkthrough
- 🏦 Pre-audit access review failure — the 2-day war story
- 🧪 Troubleshooting gauntlet: 5 mock outages, diagnose from logs

### Days 45-48 | Production Procedures + wsadmin + Capstone
- Enable security in live bank cell (cutover + rollback); add/remove admin procedure
- LDAP migration plan; quarterly password rotation campaign
- Audit access review + evidence pack; break-glass policy; change flow
- wsadmin: `mapUsersToAdminRole`, searchUsers/searchGroups, LTPA export/import script, J2C alias scripts
- Multi-cell strategy; DR design; hardening
- **Final Project: Secure BankCell01 end-to-end** (registry + roles + LTPA + SSO + certs)

---

## 🗓️ PHASE 10 — Grand Interview & Capstone Marathon
**20 Days | Consolidated from All 8 Courses (~50 spread days → 20 structured days)**

### Week 1 — Days 1-5 | Platform Stack Q&A
- **Installation & Fix Packs** — 20 Q&A: silent install, rolling patch, rollback plan, version drift, CVE patching
- **Profiles & Federation** — 20 Q&A: addNode internals, sync failures, DMGR rebuild, config corruption
- **wsadmin scripting** — 20 rapid-fire commands; answer aloud, then write the script
- **Clustering & HA** — 20 Q&A: ripplestart, core group, SIBus failover, hung member RCA

### Week 2 — Days 6-10 | Application + Web Tier Q&A
- **Deployment** — 20 Q&A: class loading, rollout update, scope trap, JNDI mismatch, governance
- **IHS + Plugin** — 20 Q&A: affinity, clone IDs, plugin-cfg, M-to-M replication, failover design
- **Session Management** — 20 Q&A: affinity ≠ replication ≠ persistence — the trick Q block
- **Trick questions decoded:** session restart ≠ failover; graceful ≠ normal; PARENT_FIRST traps

### Week 3 — Days 11-15 | Data + Security Q&A
- **JDBC + DB** — 20 Q&A: pool sizing math, XA recovery, HADR reroute, statement cache, open_cursors
- **SSL** — 20 Q&A: handshake failures, cert chain, mutual SSL, cipher mismatch, expiry war stories
- **User Management** — 20 Q&A: LDAP failover, LTPA DR, role mapping, SSO broken, registry change
- **Banking scenarios rapid-fire:** 30 cross-topic scenarios (payments down, NEFT burst, DR drill, EOD batch)

### Week 4 — Days 16-20 | War Stories + Architecture + Full Mocks
- Write 5 outage war stories (STAR format): one from IHS/Session, JDBC, SSL, UserMgmt, Cluster
- 5 Behavioral/Leadership Q&A: "How did you handle a failed production patch?" type stories
- 5 Architecture/Design Q&A (Lead level): cell design, sync for 300 nodes, DR strategy, capacity planning
- 🎤 **Mock 1:** Levels 1-2 (basics, install, cluster, session) — record yourself, review gaps
- 🎤 **Mock 2:** Levels 3-5 (troubleshooting + design + war stories) — full 45-min simulated interview
- Final one-page cheat sheet: all commands, all ports, all logs, all error codes — your interview asset
- 🎯 Self-assessment: can you run BankCell01 PROD alone, from a blank VM, without notes?

---

## 📊 Progress Tracker

| Phase | Title | Days | Status | Confidence (1-5) |
|-------|-------|------|--------|------------------|
| 0 | Platform Foundations | 3 | ☐ | |
| 1 | Installation & Fix Packs | 20 | ☐ | |
| 2 | Profiles, Federation & Sync | 30 | ☐ | |
| 3 | wsadmin Fundamentals | 5 | ☐ | |
| 4 | Clustering & High Availability | 27 | ☐ | |
| 5 | Application Deployment | 60 | ☐ | |
| 6 | IHS, Plugin & Sessions | 60 | ☐ | |
| 7 | Database & JDBC | 68 | ☐ | |
| 8 | SSL / TLS Full Stack | 33 | ☐ | |
| 9 | User Management & Auth | 48 | ☐ | |
| 10 | Grand Interview & Capstone | 20 | ☐ | |
| | **TOTAL** | **374** | | |

> **Rule:** Confidence must be ≥ 4 before moving to next phase. Weak phase = one extra revision day.

---

## 🛡️ Golden Rules of the Master Course

1. **Always do the lab.** Banks hire admins who have done it, not read it.
2. **Every topic: Console + wsadmin — BOTH, never one only.** The interviewer WILL ask "now do it via wsadmin."
3. **Every topic = 1 banking scenario** in your own words. This is your interview arsenal.
4. **Phase 3 (wsadmin) is your foundation.** Revisit it at the start of every phase.
5. **Never compress the goldmine phases:** Phase 2 Federation, Phase 4 Core Groups, Phase 6 Plugin+Affinity+Failover, Phase 7 Connection Pools, Phase 9 Troubleshooting.
6. **If a day is missed, extend — never skip.** Consistency > speed.
7. **Maintain 3 running documents from Day 1:** Build Book, Patch Runbook, Rollback Runbook.
8. **After Phase 10:** You're not just interview-ready — you can run BankCell01 production alone.

---

## ⏰ Daily Routine Template (Every Phase)

| Time | Activity |
|------|----------|
| 0-15 min | Revise previous day's notes |
| 15-75 min | Today's theory + banking scenario |
| 75-110 min | Hands-on lab (Console + wsadmin) |
| 110-120 min | Write: "What I learned + 1 interview line" |

---