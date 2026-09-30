# 🏦 DigiStack Bank — WebSphere ND 9.0.5.28 Administration Master Syllabus

> 27-week hands-on lab program — Solo-Job-Ready WAS Admin for Banking Environments
> All labs run on a personal 7-VM homelab replicating a production bank middleware stack.

---

## 🖥️ LAB INFRASTRUCTURE — 7s

| VM | Role | Syllabus Use |
|---|---|---|
| `dsb-dmgr` (+Node1) | Dmgr + Node1 + digistack-bank-v1.ear | Phases 2–7, 10–11 |
| `dsb-node02` | 2nd cluster member | Clustering, HA, XA recovery |
| `dsb-db` | PostgreSQL 16 | DataSources, XA, tranlog DB, session DB |
| `dsb-ihs` | IHS 9.0.5.28 | Web tier, plugin, SSL |
| `dsb-mq` | IBM MQ 9.3/94 Dev | Phase 8 |
| `dsb-monitor` | Prometheus/Grafana | Phase 10 (PMI → Prometheus) |
| `dsb-elk` | OpenSearch stack | Phase 4 (log aggregation) |

---

## 📌 PHASE 1 — FOUNDATION (Weeks 1–2)

| Lab | Content (your-lab version) |
|---|---|
| Lab 0.1 | Build all 7 VMs, static IPs, `/etc/hosts` (all 7 entries on every VM), firewalld, SELinux, NFS share from `dsb-dmgr` → `dsb-node02` |
| Lab 0.2 | Linux for WAS: `wasadmin` user, ulimits on all JVM VMs, ports (`ss -tlnp` for 9043/9443/1414), `ps -ef \| grep java`, `kill -3`, disk checks on thin-provisioned 40G disks |
| Lab 0.3 | Prereqs on `dsb-dmgr`/`dsb-node02`: RHEL 8 packages, `/apps/IBM/WebSphere/AppServer`, `/applogs`, `/appdata`; layout; PostgreSQL prereqs on `dsb-db`; MQ prereqs on `dsb-mq` |

---

## 📌 PHASE 2 — INSTALLATION & PROFILES (Weeks 3–4)

| Lab | Content |
|---|---|
| Lab 1.1 | IIM silent install of WAS ND 9.0.5.28 on `dsb-dmgr` + `dsb-node02`; verify `versionInfo.sh` |
| Lab 1.2 | `manageprofiles.sh`: Dmgr profile on `dsb-dmgr`; custom node on `dsb-node02` federated to Dmgr; delete/augment drills |
| Lab 1.3 | `startManager`/`startNode`/`startServer`; console on 9043; deploy HelloWorld.war; first wsadmin via `/apps/IBM/WebSphere/AppServer/bin/wsadmin.sh` |

---

## 📌 PHASE 3 — CORE ADMINISTRATION (Weeks 5–7)

| Lab | Content (your-lab version) |
|---|---|
| Lab 2.1 | Build cluster `DigiStackCluster`: 2 members (dsb-dmgr Node1 + dsb-node02); node groups, core groups |
| Lab 2.2 | Deploy `digistack-bank-v1.ear` every way: console, `AdminApp.install`, properties-driven; virtual hosts (fix 404s); same EAR → different context roots per cluster |
| Lab 2.3 | PostgreSQL JDBC (your bank's real stack): install pgJDBC driver, J2C alias, provider + DS + pool; XA vs non-XA (`pgjdbc` XADataSource class); Test Connection failure drills; custom props (`socketTimeout`, `connectTimeout`); create DS via Jython |
| Lab 2.4 | SIB bus on cluster, queues, MDB sample; queue depth monitoring; kill member mid-JMS-send → rollback proof |
| Lab 2.5 | `dumpNameSpace.sh`, mail sessions, URL providers, shared libs |

---

## 📌 PHASE 3B — TRANSACTIONS & XA (Weeks 7–8)

> **Setup:** deploy FundsTransfer app: Servlet → EJB (CMT) → two XA DataSources (`DEBIT_DS`, `CREDIT_DS`) → both pointing at PostgreSQL 16 on `dsb-db` (two schemas: `debit`, `credit`).

| Lab | Content |
|---|---|
| Lab T.1 | ACID via funds-transfer; debit OK, credit fails → rollback verified via `psql` |
| Lab T.2 | Local vs global XA; 1PC vs 2PC in trace; story |
| Lab T.3 | Tranlogs: locate `<profile>/tranlog/`; PostgreSQL-side: `pg_prepared_xacts` view to see in-doubt XA branches |
| Lab T.4 | Timeouts (total lifetime, client inactivity, max in-process) — console + Jython cluster-wide; simulate with sleep servlet |
| Lab T.5 | ⭐ XA recovery money lab: `kill -9` member mid-XA → in-doubt visible in `pg_prepared_xacts` → restart → WTRN recovery replay → committed. **Real interview differentiator** |
| Lab T.6 | Heuristic outcomes; force resolution; golden rule — coordinate with the "DBA" |
| Lab T.7 | WTRN cheatsheet: `WTRN0062E`, `WTRN0107W`, `WSVR0605W`; `Transaction=all` trace |
| Lab T.8 | War-game: JMeter → kill member mid-flight → recovery → zero lost transfers → post-mortem |

---

## 📌 PHASE 4 — OPS, TROUBLESHOOTING (Weeks 9–14)

| Lab | Content (your-lab version) |
|---|---|
| Lab 3.1 | wsadmin Jython toolkit `wasOps.py`: status, deploy, pool changes, tran timeouts; properties-driven |
| Lab 3.2 | Javacores (`kill -3`, TMDA), heap dumps + MAT, verbose GC; add WTRN patterns to cheatsheet |
| Lab 3.3 | Logs: SystemOut, `.log`; HPEL + `logViewer.sh`; ship WAS logs → OpenSearch on `dsb-elk` (Filebeat/Logstash) — build your central log dashboard |
| Lab 3.4 | JVM tuning: `-Xms/-Xmx`, GC policies on 2 GB node02 heap; WebContainer exhaustion drill |
| Lab 3.5 | Session management: memory-to-memory replication between cluster members; failover test; session vs transaction persistence trap |
| Lab 3.6 🆕 | Prometheus on `dsb-monitor`: expose PMI via JMX exporter; Grafana dashboards (heap, threads, DS pool, queue depth) |

---

## 📌 PHASE 5 — SECURITY (Weeks 15–17)

| Lab | Content |
|---|---|
| Lab 4.1 | Global security: file registry → OpenLDAP → federated repos; console roles (Admin/Operator/Monitor); app roles in `digistack-bank-v1.ear` |
| Lab 4.2 | SSL: keystores/truststores on Dmgr, nodes, and IHS; CSR + signed cert; cert expiry script; break SSL on purpose and fix |
| Lab 4.3 | LTPA + SSO between two apps across the cluster |

---

## 📌 PHASE 6 — WEB TIER & HA (Weeks 18–19)

| Lab | Content (your-lab version) |
|---|---|
| Lab 5.1 | IHS on `dsb-ihs` + plugin; `plugin-cfg.xml` generation/propagation; read it line by line; break routing (404/503) and fix; route to both cluster members |
| Lab 5.2 | HA drills: kill cluster member under load; kill node agent on `dsb-node02`; kill Dmgr — what still works; kill member mid-XA (Lab T.8 drill) |

---

## 📌 PHASE 7 — AUTOMATION, PATCHING & DR (Weeks 19–20)

| Lab | Content |
|---|---|
| Lab 6.1 | Silent installs + Fixpack to 9.0.5.28+ via IIM; rolling updates |
| Lab 6.2 | `extractConfigProperties`/`applyConfigProperties`; one-script cell rebuild |
| Lab 6.3 | `health-check-all.sh` across all 7 VMs: servers, ports, heap, disk, cert expiry, in-doubt XA check (`pg_prepared_xacts`), MQ CURDEPTH; cron + alerting |
| Lab 6.4 | `backupConfig`/`restoreConfig`; tranlog backup; PostgreSQL `pg_dump` strategy; DR drill: rebuild the whole cell from backups on fresh VMs |
| Lab 6.5 | Migration intro: WAS 8.5.5 → 9.0.5 |

---

## 📌 PHASE 8 — IBM MQ (Weeks 20–22) — on `dsb-mq`

| Lab | Content |
|---|---|
| Lab MQ.0 | Silent MQ 9.3/9.4 install; `mqm` user, `setmqenv`, `dspmqver`; WAS ↔ MQ ↔ "mainframe" QMGR (second QMGR on same VM) |
| Lab MQ.1 | `crtmqm`/`strmqm`; 20+ runmqsc commands; DLQ; `amqsput`/`amqsget`; CURDEPTH monitoring |
| Lab MQ.2 | Listener 1414, SVRCONN + CHLAUTH; SDR/RCVR between 2 QMGRs; break channel 3 ways; persistent message recovery test |
| Lab MQ.3 | WAS ↔ MQ: WebSphere MQ JMS provider CF + queues in WAS console; test servlet in `digistack-bank-v1.ear`; SIB MQ Link (foreign bus) — know both methods |
| Lab MQ.4 | AMQ codes (9999, 9208, 2035, 2059, 2085, 2538); DLQ via `amqsbcg`; queue-full drill; `mq-health.sh`; end-to-end: Browser → IHS → WAS → JMS → SDR → RCVR → reply |

---

## 📌 PHASE 9 — ADJACENT MIDDLEWARE (Weeks 23–24)

| Lab | Content |
|---|---|
| Lab AM.1 | Apache HTTPD + WAS plugin; kill member, verify reroute; ProxyTimeout vs session timeout bug |
| Lab AM.2 | Nginx reverse proxy + upstream LB; SSL termination; why shops move off IHS |
| Lab AM.3 | Tomcat: deploy same WAR; demo what fails (EJB + XA on PostgreSQL); why banks pay for WAS |
| Lab AM.4 | Liberty: `server.xml`, migrate one module of `digistack-bank-v1.ear`; Transformation Advisor |

---

## 📌 PHASE 10 — PERFORMANCE & CAPACITY (Weeks 25–26)

| Lab | Content (your-lab version) |
|---|---|
| Lab PC.1 | JMeter → `dsb-ihs` → cluster; 50→200→500 user ramps; watch nmon + Grafana dashboards + GC logs |
| Lab PC.2 | One lever at a time: heap, GC policy, WebContainer threads, DS pool, session mgmt; find breaking point → capture javacore/heapdump mid-failure |
| Lab PC.3 | One-page capacity report for `digistack-bank-v1` (baseline vs tuned) |
| Lab PC.3b | Rehearse the standup; "prove we need more vCPUs" |

---

## 📌 PHASE 11 — EXPERT & FINAL EXAM (Week 27)

| Lab | Content |
|---|---|
| Lab E.1 | Full JMeter + tuning cycle + capacity report |
| Lab E.2 | Memory leak hunt → MAT |
| Lab E.3 | Outage war-game — break 11 things, fix all, timed (cert, port conflict, DS down, corrupt config, dead node agent, full log disk, dead channel, queue full, hung thread, wrong vhost, in-doubt XA) |
| Lab E.4 | Core groups / HA manager: bridges, DRS |

---

 🎓 FINAL EXAM — On YOUR 7 VMs, fully scripted, from bare state

- [ ] WAS ND 9.0.5.28 2-node cluster (`dsb-dmgr` + `dsb-node02`)
- [ ] IHS on `dsb-ihs` with plugin routing + SSL end-to-end
- [ ] LDAP security + console role mapping
- [ ] PostgreSQL 16 XA DataSource on `dsb-db`
- [ ] IBM MQ on `dsb-mq` with SIB link to WAS
- [ ] digistack-bank-v1.ear FundsTransfer XA: kill server mid-2PC → recovery replay → zero lost/duplicated transactions
- [ ] End-to-end message flow: Browser → `dsb-ihs` → WAS → JMS → SDR → RCVR → reply
- [ ] JMeter capacity report on `dsb-monitor` (Grafana) with sizing justification
- [ ] `health-check-all.sh` + central logs visible on `dsb-elk`

---

## 🎤 MASTER INTERVIEW QUESTION BANK — Mapped to YOUR Lab

| | Question | Lab Proof (your VMs) |
|---|---|---|
| 1 | "Server died mid-payment — what happens to the money?" | T.5 / T.8 — in-doubt visible in `pg_prepared_xacts`, recovery replay in SystemOut |
| 2 | "Explain 2PC and the coordinator's role" | T.2 |
| 3 | "What's a heuristic hazard and what do you do?" | T.6 |
| 4 | "Why does tranlog size matter?" | T.3 |
| 5 | "Walk me through your worst production outage" | E.3 + T.8 post-mortem |
|6 | "WAS vs Tomcat — why do banks pay for WAS?" | AM.3 (your FundsTransfer app fails on Tomcat) |
| 7 | "How do you size a JVM? Prove it" | PC.1–PC.3 + Grafana dashboards |
| 8 | "MQ channel is down — walk me through triage" | MQ.2 / MQ.4 on `dsb-mq` |
| 9 | "Kill the Dmgr in production — what breaks?" | Lab 5.2 on `dsb-dmgr` |
| 10 | "Automation: rebuild your whole cell from zero" | Lab 6.2 + Final Exam |
| 11 🆕 | "How do you monitor WAS centrally?" | Lab 3.3 (`dsb-elk`) + Lab 3.6 (Prometheus/Grafana) |
| 12 🆕 | "Oracle vs PostgreSQL for XA — differences you've seen?" | T.3–T.5 (`pg_prepared_xacts`, pgjdbc XA class) |

---

## ✅ SOLO-JOB READINESS CHECKLIST

You're ready to hold the pager alone when you can answer YES to all:

- [ ] Rebuild entire cell from scripts (Final Exam)
- [ ] Explain what still works when Dmgr dies (Lab 5.2)
- [ ] Recover an in-doubt XA transaction and prove zero money lost (T.5/T.8)
- [ ] Triage a dead MQ channel and a full DLQ (MQ.4)
- [ ] Diagnose a hung prod JVM from javacore alone in <15 min (Lab 3.2)
- [ ] Renew an expiring cert end-to-end without notes (Lab 4.2)
- [ ] Say "I don't know, but here's how I'd find out — IBM PMR, traces, docs"

---

## ⚠️ NON-LAB GAPS (add 2–3 days of reading)

| Gap | What to know |
|---|---|
| **Change management** | CRQ/ticketing (ServiceNow), change windows, rollback plans — banks never let you touch prod without a CRQ |
| **Incident process** | Severity levels, escalation, comms cadence, post-mortems |
| **Runbooks** | Your `health-check-all.sh` + lab journal IS your runbook seed |
| **Vendor engagement** | IBM PMR workflow, mustGather, eCIRs, reading IBM technotes/APARs |

---

## 📋 DAILY LAB RULES (enforced)

1. **Every topic = hands-on on your VMs.** No reading without doing.
2. Snapshot each VM **before** every destructive lab (T.5, E.3, 5.2) — and repeat until fast.
3. Maintain `~/lab-journal/YYYY-MM-DD.md` — commands, errors, fixes.
4. Build your artifacts as you go: `wasOps.py`, `wtrn-error-cheatsheet.md`, `health-check-all.sh`, `mq-health.sh`, capacity report.
5. Thin disks fill fast on 40 GB — include `df -h` in your daily journal; clean FFDC/javacores/heapdumps after analysis.

---

**🚀 Start tonight: Lab 0.1 — bring up all 7 VMs and write `/etc/hosts`. Everything else builds on it.**
