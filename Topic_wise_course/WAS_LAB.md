# WebSphere Application Server Administrator — FULL COURSE SYLLABUS (v2.1)

> **Zero to Expert | LAB-Only | Bank-Grade (WAS ND + MQ + Transactions + Adjacent Middleware + Capacity Engineering)**
> Designed from a 25-Year WebSphere Admin/Architect perspective (Big MNC Bank)

| Item | Detail |
|---|---|
| **Duration** | 27 weeks (2–3 hrs/day) |
| **Lab Setup** | VirtualBox/VMware + 3 Linux VMs (RHEL 8/Rocky): `was-node1`, `was-node2`, `mq-server` |
| **Goal** | Crack 10-year WAS admin interviews + run production alone |
| **Rule** | Every topic = LAB. No reading without hands-on. Maintain a daily lab. |

---

## 📌 PHASE 1 — FOUNDATION (Weeks 1–2)

### Level 0 — Environment & Linux Basics

| Lab | Content |
|---|---|
| Lab 0.1 | Build lab: 3 RHEL VMs, static IPs, `/etc/hosts`, hostname, firewalld, SELinux basics, NFS share between VMs |
| Lab 0.2 | Linux for WAS admins: users/groups (`wasadmin`), chmod/chown, ulimits, vi, grep, awk, `tail -f`, find, tar; ports (`netstat`, `ss`, `lsof`); processes (`ps -ef \| grep java`, `kill -3`); disk (`df -h`, `du -sh`) |
| Lab 0.3 | Prerequisites: OS packages for WAS, kernel tuning, filesystem layout (`/opt/IBM`, `/applogs`, `/appdata`), install user/groups |

---

## 📌 PHASE 2 — INSTALLATION & PROFILES (Weeks 3–4)

### Level 1 — Install & Build

| Lab | Content |
|---|---|
| Lab 1.1 | Installation Manager (IIM) silent install; WAS ND 9 binaries via response files; verify `versionInfo.sh`, `historyInfo.sh` |
| Lab 1.2 | Profiles: Dmgr profile, Custom node + federation, standalone profile; `manageprofiles.sh` with response files; delete/augment profiles |
| Lab 1.3 | Start/stop scripts (`startManager`/`startNode`/`startServer`); Admin Console access (9043); deploy `HelloWorld.war`; test 9080; first wsadmin command |

---

## 📌 PHASE 3 — CORE ADMINISTRATION (Weeks 5–7)

### Level 2 — Daily WAS Administration

| Lab | Content |
|---|---|
| Lab 2.1 | Topology (build 3x from scratch): 1 Dmgr + 2 nodes + 4 servers; cluster across 2 nodes; node groups; core groups basics |
| Lab 2.2 | Application deployment: console deploy (all options), wsadmin `AdminApp.install/update`; virtual hosts, context roots, host aliases (fix 404s); update/rollback/partial update; roles, shared libraries, classloader policies; same app → different context roots on different clusters |
| Lab 2.3 | DataSources (bank favorite): JDBC driver install, J2C alias, provider + DataSource + pool settings; **XA vs non-XA datasource selection**; Test Connection — troubleshoot every failure; custom props (`oracle.net.CONNECT_TIMEOUT`); create DS via Jython script |
| Lab 2.4 | JMS & SIB: Service Integration Bus bus members, queues; JMS CFs + queues; MDB sample app; queue depth monitoring, message browse/delete; kill member mid-JMS-send → rollback proof |
| Lab 2.5 | JNDI & resources: `dumpNameSpace.sh`; mail sessions, URL providers, resource env entries; shared libraries at server vs cell level |

---

## 📌 PHASE 3B — TRANSACTIONS & XA 🆕 (Weeks 7–8)

### Level 2.5 — Transaction Service (The Payment Path)

> **Setup:** Deploy a "FundsTransfer" app: Servlet → EJB (CMT) → two XA DataSources (DEBIT_DS, CREDIT_DS). This ONE app drives every lab below.

| Lab | Content |
|---|---|
| Lab T.1 | **ACID via funds-transfer:** debit succeeds, credit fails (bad SQL on 2nd DS) → verify rollback = both unchanged. Then break ACID: non-XA DS + autocommit → show orphaned debit |
| Lab T.2 | **Local vs Global (XA):** convert both DS to XA; compare 1PC vs 2PC in trace; write the coordinator story: prepare → decision log → commit |
| Lab T.3 | **Transaction logs:** locate tranlog (cell-level & server-level `<profile>/tranlog/`); recovery log sizing; why undersized tranlog hangs transactions under load; backup strategy |
| Lab T.4 | **Timeouts:** total transaction lifetime, client inactivity, max in-process — console + wsadmin; simulate timeout (sleep servlet) → capture exact WTRN error; Jython script to set all three cluster-wide |
| Lab T.5 | **XA recovery — the money lab:** kill server mid-XA transaction (in-doubt: prepared, not committed) → restart → watch recovery log replay (`WTRN` messages in SystemOut) → verify DB committed. The "server died mid-payment" story |
| Lab T.6 | **Heuristic outcomes:** force (manual DB commit while in-doubt); observe heuristic hazard/committed/rolledback; safe resolution via AdminControl (retry/commit/forget); golden rule: *never resolve heuristic hazard without the DBA* |
| Lab T.7 | **WTRN/WSVR error codes + tracing:** enable `Transaction=all` trace; decode WTRN0062E, WTRN0107W, WSVR0605W; build your `wtrn-error-cheatsheet.md` |
| Lab T.8 | **Full war-game:** JMeter load on FundsTransfer → `kill -9` member mid-flight → verify in-doubt in Oracle → restart → recovery replay → zero lost transfers → write post-mortem. Time yourself |

---

## 📌 PHASE 4 — OPS, SCRIPTING & TROUBLESHOOTING (Weeks 9–14)

### Level 3 — The 10-Year Admin's Bread & Butter

| Lab | Content |
|---|---|
| Lab 3.1 | wsadmin Jython (MOST IMPORTANT): `AdminControl`, `AdminConfig`, `AdminApp`, `AdminTask`, `Help`; scripts for start/stop, status listing, deploy/undeploy, pool changes, tracing, **transaction timeout changes**; build your `wasOps.py` toolkit; properties-file-driven scripts (no hardcoded env) |
| Lab 3.2 | Monitoring & troubleshooting: thread dumps (`kill -3`, wsadmin), analyze with TMDA; heap dumps + HeapAnalyzer/Eclipse MAT — find a memory leak; read 5 javacores (stuck threads, deadlocks); verbose GC parsing; **grep WTRN patterns added to cheatsheet** |
| Lab 3.3 | Logs & trace: SystemOut/Err, FFDC, activity.log; log rolling; PMI counters; HPEL enable + `logViewer.sh`; trace strings (`com.ibm.ws.webcontainer=all`, `Transaction=all`) |
| Lab 3.4 | JVM tuning: `-Xms`/`-Xmx` experiments, OOM simulation; GC policies (gencon/optthruput/balanced) compared via GC logs thread pool sizing — simulate WebContainer exhaustion |
| Lab 3.5 | Session management: memory-to-memory vs DB persistence; cluster session failover test (kill member mid-session!); timeout config; **contrast session persistence vs transaction persistence — interview trap** |

---

## 📌 PHASE 5 — SECURITY (Weeks 15–17)

### Level 4 — Bank-Grade Security

| Lab | Content |
|---|---|
| Lab 4.1 | Global security: file registry → standalone LDAP (OpenLDAP) → federated repositories; console role mapping (Admin/Operator/Monitor); app security roles; Java 2 security + fixing PermissionErrors |
| Lab 4.2 | SSL/TLS: keystores/truststores, self-signed + chained certs; CSR + signed cert import; SSL configs, cert expiry monitoring script; deliberately break SSL ("trust anchor not found") and fix |
| Lab 4.3 | LTPA & SSO: token expiry, key export/import across cells; SSO between two apps |

---

## 📌 PHASE 6 — WEB TIER & HA (Weeks 18–19)

### Level 5 — Load Balancing & Failover

| Lab | Content |
|---|---|
| Lab 5.1 | IHS + WebSphere Plugin: install via IIM; `plugin-cfg.xml` generation/propagation; manage IHS from Dmgr (unmanaged node); read `plugin-cfg.xml` line by line; break routing (404/503) and fix |
| Lab 5.2 | HA drills: kill cluster member under load; kill node agent; kill Dmgr — document what still works and why; **add "kill member mid-XA" to the drill (ref Lab T.8)** |

---

## 📌 PHASE 7 — AUTOMATION, PATCHING & MIGRATION (Weeks 19–20)

### Level 6 — Senior-Level Differentiators

| Lab | Content |
|---|---|
| Lab 6.1 | Silent installs + Fixpack/iFix patching via IIM (install, rollback, rolling updates); IHS patching |
| Lab 6.2 | Config as code: `extractConfigProperties`/`applyConfigProperties`; one-script full environment build; ws_ant deployments |
| Lab 6.3 | Shell automation: `health-check-all.sh` (servers, ports, heap, disk, cert expiry, **in-doubt transaction check**); log rotation to NFS; cron alerting; wsadmin + shell wrappers |
| Lab 6.4 | Backup/DR: `backupConfig.sh`/`restoreConfig.sh`; **tranlog backup/restore**; app export; DR drill — rebuild cell from backup on second VM |
| Lab 6.5 | Migration intro: WAS 8.5.5 → 9.0.5 (`WASPreUpgrade`/`WASPostUpgrade`) (deep dive continues in Level 7.5) |

---

## 📌 PHASE 8 — IBM MQ 🆕 (Weeks 20–22)

### Level 7 — Non-Negotiable in Banks

| Lab | Content |
|---|---|
| Lab MQ.0 | Install IBM MQ 9 (silent, developer edition); `mqm` user, `setmqenv`, `dspmqver`; 3rd VM topology: WAS ↔ MQ ↔ "mainframe" QMGR |
| Lab MQ.1 | QMGRs: `crtmqm`/`strmqm`; runmqsc fluency (20+ commands): QLOCAL, QALIAS, QREMOTE, DLQ (USAGE(XMITQ), DEADQ); `amqsput`/`amqsget`; CURDEPTH monitoring |
| Lab MQ.2 | Channels & listeners: LISTENER(TCP:1414), SVRCONN + CHLAUTH rules; SDR/RCVR pairs with XMITQ between 2 QMGRs; DISPLAY CHSTATUS; break channel 3 ways and fix; **persistent message recovery test (ties to XA/transaction durability)** |
| Lab MQ.3 | WAS ↔ MQ integration: WebSphere MQ JMS provider (CF + queue) in WAS console, test servlet; SIB MQ Link (foreign bus) — know BOTH methods and when to use each |
| Lab MQ.4 | Troubleshooting: AMQ error codes (9999, 9208, 9541, 2035, 2059, 2085, 2538); DLQ inspection (`amqsbcg`, DLH headers, requeue); queue-full drill; `mq-health.sh` cron script; full end-to-end flow test: Browser → WAS → JMS → SDR → RCVR → reply |

---

## 📌 PHASE 9 — ADJACENT MIDDLEWARE 🆕 (Weeks 23–24)

### Level 7.5 — Job Scope Creep Reality

| Lab | Content |
|---|---|
| Lab AM.1 | Apache HTTPD + WAS plugin; IHS vs mod_proxy/mod_jk — build one-page comparison; front cluster with Apache, kill member, verify reroute; ProxyTimeout vs session timeout bug |
| Lab AM.2 | Nginx: reverse proxy + upstream load balancing; SSL termination at Nginx; worker tuning; why shops moved off IHS (licensing, EoS) — 30-sec interview answer |
| Lab AM.3 | Tomcat (interview trap): install, deploy same WAR; `server.xml` connectors, AJP, `setenv.sh`; trap answers: why move off WAS / why NOT / why banks pay for WAS; demo app that fails on Tomcat (EJB + XA) |
| Lab AM.4 | Liberty deep dive: server create/start; `server.xml` mastery (featureManager, endpoints, keystore, datasource, JMS); `<include>`, `server.env`, `bootstrap.properties`; binaryScanner + Transformation Advisor; migrate ONE traditional app end-to-end; server dump/javacore/pause; collectives basics |

---

## 📌 PHASE 10 — PERFORMANCE & CAPACITY 🆕 (Weeks 25–26)

### Level 8 — The Architect Layer

| Lab | Content |
|---|---|
| **Lab PC.1** | JMeter: test plan via Nginx/IHS → WAS; 50→200→500 user ramps; baseline throughput/p95/error rate; watch nmon, GC logs, PMI during load |
| **Lab PC.2** | Tuning cycle (one lever at a time, re-test each): heap sizes, GC policy, WebContainer threads, DS pool, session mgmt, web-tier workers; find breaking point deliberately → capture javacore/heapdump mid-failure → analyze |
| **Lab PC.3** | One-page capacity report: baseline vs tuned numbers, CPU/heap/threads/connections at target load, headroom, scale-out trigger |
| **Lab PC.3b** | Practice saying it: record 3-min standup ("changed Y, improved Z by N%"); rehearse manager questions ("prove we need 4 more vCPUs", "what breaks first — CPU, heap, or connections?"). **Managers buy numbers, not tuning.** |

---

## 📌 PHASE 11 — EXPERT & FINAL EXAM (Week 27)

### Level 9 — Expert Labs

| Lab | Content |
|---|---|
| **Lab E.1** | JMeter load test + full tuning cycle + capacity report (PC labs if skipped) |
| **Lab E.2** | Memory leak hunt: leaky app → heap dumps over time → identify leak class in MAT |
| **Lab E.3** | Outage war-game: break 11 things — fix all, time yourself:<br>1. Expired cert<br>2. Port conflict<br>3. DS down<br>4. Corrupted config<br>5. Dead node agent<br>6. Full log disk<br>7. Stopped channel<br>8. Queue full<br>9. Hung thread<br>10. Wrong virtual host<br>11. In-doubt transaction needing recovery |
| **Lab E.4** | Core groups / HA manager: core group bridges, DRS settings |

---

## 🎓 FINAL EXAM (One Day, From Bare VMs)

**Build, fully scripted:**

- [ ] WAS ND 2-node cluster
- [ ] Nginx/IHS front end with plugin routing
- [ ] LDAP security + console role mapping
- [ ] SSL/TLS end-to-end
- [ ] Oracle XA DataSource
- [ ] IBM MQ with SIB link
- [ ] FundsTransfer XA app: kill server mid-2PC → recovery replay → prove **ZERO lost/duplicated transactions**
- [ ] End-to-end message flow: Browser → WAS → JMS → SDR → RCVR → reply
- [ ] JMeter capacity report with sizing justification

---

## 📅 FINAL SCHEDULE MAP (27 Weeks)

| Weeks | Phase | Focus |
|---|---|---|
| 1–2 | Phase 1 | Linux & environment foundation |
| 3–4 | Phase 2 | Install, profiles, first app |
| 5–7 | Phase 3 | Core admin: topology, deploy, DS, JMS/SIB, JNDI |
| 7–8 | Phase 3B 🆕 | Transactions & XA (T.1–T.8) |
| 9–14 | Phase 4 | wsadmin Jython, troubleshooting, JVM tuning, sessions |
| 15–17 | Phase 5 | Security: LDAP, SSL, LTPA/SSO |
| 18–19 | Phase 6 | IHS/plugin, HA drills |
| 19–20 | Phase 7 | Patching, automation, backup/DR, migration |
| 20–22 | Phase 8 | IBM MQ (install → channels → WAS integration → troubleshooting) |
| 23–24 | Phase 9 | Adjacent middleware: Apache, Nginx, Tomcat, Liberty |
| 25–26 | Phase 10 | Performance & capacity engineering |
| 27 | Phase 11 | Expert labs + FINAL EXAM |

---

## 🎤 MASTER INTERVIEW QUESTION BANK (What This Syllabus Proves)

| # | Question | Lab Proof |
|---|---|---|
| 1 | "Server died mid-payment — what happens to the money?" | T.5 / T.8 |
| 2 | "Explain 2PC and the coordinator's role" | T.2 |
| 3 | "What's a heuristic hazard and what do you do?" | T.6 |
| 4 | "Why does tranlog size matter?" | T.3 |
| 5 | "Walk me through your worst production outage" | E.3 + T.8 post-mortem |
| 6 | "WAS vs Tomcat — why do banks pay for WAS?" | AM.3 |
| 7 | "How do you size a JVM? Prove it" | PC.1–PC.3 |
| 8 | "MQ channel is down — walk me through triage" | MQ.2 / MQ.4 |
| 9 | "Kill the Dmgr in production — what breaks?" | Lab 5.2 |
| 10 | "Automation: rebuild your whole cell from zero" | Lab 6.2 + Final Exam |
