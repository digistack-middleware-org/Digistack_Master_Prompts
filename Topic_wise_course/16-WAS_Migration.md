# 📅 40-Day Plan — WAS Admin Scripting (Zero → 10-Year Bank Expert)

> **Goal:** Crack 10-Year WebSphere Admin Interview + Run Production Solo
> **Format:** 80% Theory | 15% Real Banking Scenarios | 5% Interview Prep
> **Daily Time:** 4–4.5 hours → 60 min Theory + 2.5 hrs-on Lab + 30 min Bank Scenario Notes & Interview Q&A

---

## ⚙️ Setup Instructions

### Lab Setup (Before Day 1)
- VirtualBox → 1–2 RHEL/CentOS VMs
- WAS ND 9.0.5 trial (free from IBM)
- DB2 or Oracle XE for datasource labs
- This setup = your personal **"mini Citibank"**

### Golden Rules
1. **Labs first, always.** Cut theory reading if short on time — never cut typing commands.
2. **Buffer rule:** Missed a day? Use Day 5, 15, 25, 35 evenings half-day catch-up. Max stretch: **42 days**.
3. **Daily output:** 1 script saved to GitHub + 5 interview Q&A in your "Bank Runbook" notebook.
4. **Day 40 result:** 40 scripts + 200 Q&A = your interview arsenal.

---

## 🗓️ WEEK 1 (Days 1–5) — Foundation + Profiles [Modules 1–3]

| Day | Learn (60m) | Lab (2.5h) | 🏦 Bank Connect (30m) |
|-----|-------------|------------|------------------------|
| 1 | WAS editions, why banks use WAS, web vs app server | Install WAS ND via Installation Manager | Citibank: Internet Banking + Payments run on WAS ND |
| 2 | Topology: Cell → Node → DMgr → NodeAgent → Server → Cluster (draw from memory) | Explore profile dirs, bin commands | Draw "PROUS01 Cell: 5 nodes, NY + DR" — your 2-min interview answer |
| 3 | Silent install + response files + profile types (Dmgr/AppSrv/Custom) | Silent install using response file | No GUI on bank servers — response files + change tickets only |
| 4 | manageprofiles full syntax, ports, federation (addNode), startup order **DMgr→NA→Servers** | Script: create Dmgr + custom profile silently; federate node | New Payment Gateway servers → zero-touch build; wrong startup order = outage |
| 5 | Linux for WAS: grep/awk/sed, ps, netstat, kill -3, df -h, cron, ulimit | Write `check_disk.sh` + `find_jvm_process.sh` + **Week recap + 25 Q&A** | 3 AM page: JVM 100% CPU → `kill -3` thread dump is daily life |

**✅ Milestone 1:** Full WAS cell built from scratch, silently.

---

## 🗓️ WEEK 2 (Days 6–10) — wsadmin + Jython [Modules 4–5]

| Day | Learn | Lab | 🏦 Bank Connect |
|-----|-------|-----|------------------|
| 6 | wsadmin: 4 connectors (SOAP/RMI/JMX/IPC), 5 objects overview | Connect to DMgr via SOAP 8879; `AdminControl.getCell()` | Jump server → SOAP → DMgr is your lifeline in a secure bank network |
| 7 | Jython: variables, if-else, loops, lists, dictionaries | 20 exercises; print all servers in a loop | Loop = "do it to 40 JVMs at once" — the whole point of scripting |
| 8 | Jython: functions, try/except, file read/write, `-f` script mode | Build `myscript.py` with logging + error handling | Scripts must log + fail gracefully — SOX audit requirement |
| 9 | **AdminControl:** getCell, listServers, completeObjectName, invoke | Build `list_all_servers.py`; get server MBean | Runtime view vs config view — know this cold |
| 10 | **AdminControl:** start/stop server, thread dump, heap dump via script | `restart_server.py` + `threaddump.py` + **recap + 25 Q&A** | Credit Card JVM hung before EOD batch → auto dump + restart script |

**✅ Milestone 2:** Control any JVM in the cell via script.

---

## 🗓️ WEEK 3 (Days 11–15) — AdminConfig + AdminApp [Modules 6–7]

| Day | Learn | Lab | 🏦 Bank Connect |
|-----|-------|-----|------------------|
| 11 | Config IDs, getid, list, showall, attributes, config object tree | Fully explore one server's config tree | Understanding config tree = understanding WAS itself |
| 12 | AdminConfig: create, modify, remove, save | Add JVM custom property to 40 servers in one loop | Audit-driven estate-wide change = 1 loop, not 40 clicks |
| 13 | AdminApp: install, options file, context root, virtual host, classloading | Install sample EAR to cluster; practice `update`/`edit` | Weekly release: InternetBankingV13.ear in 1 AM–3 AM Sunday window |
| 14 | Deployment script design: pre-checks → install → verify → **rollback** | Build `deploy.py` with try/except + auto-rollback | Every bank change ticket demands a rollback plan — script it |
| 15 | Role mapping via `AdminApp.edit`; app start/stop/status via script | **Mini Project 1:** Full deploy script (backup → install → health check → rollback) + **25 Q&A** | The #1 most-asked 10-yr interview task: "walk me through your deployment script" |

**✅ Milestone 3:** Deploy any app with rollback — the #1 daily bank task.

---

## 🗓️ WEEK 4 (Days 16–20) — AdminTask + Clusters/HA [Modules 8–9]

| Day | Learn | Lab | 🏦 Bank Connect |
|-----|-------|-----|------------------|
| 16 | AdminTask: help, listCommands, command vs step syntax | Explore 15 most-used AdminTask commands | AdminTask = the "easy button" — interviewers ask which you prefer and why |
| 17 | `createCluster`, `createClusterMember`, `setJVMProperties`, GC policies | Build **PaymentsCluster** (6 JVMs, 3 nodes) + heap 4096/8192 in one script | New payments platform onboarding = cluster build in change window; no config drift |
| 18 | Node agent mgmt, `syncNode.sh`, federation troubleshooting | Break federation on purpose → fix via script | Node agent down = deployment fails bank-wide |
| 19 | WLM/routing, static vs dynamic clusters, ripplestart vs rolling restart | Kill a member → watch WLM route traffic; write `rolling_restart.py` | NY node dies at 2 PM → customers never notice; banks never bounce full clusters |
| 20 Cluster health scripting | **Mini Project 2:** Cluster build + JVM config + health check in one script + **25 Q&A** | "How would you build a new cluster for a new app?" — full answer ready |

**✅ Milestone 4:** Design & build bank-grade HA clusters alone.

---

## 🗓️ WEEK 5 (Days 21–25) — DataSources + JMS/MQ [Modules 10–11] ⭐ Bank Critical

| Day | Learn | Lab | 🏦 Bank Connect |
|-----|-------|-----|------------------|
| 21 | JDBC provider, datasource, J2C alias theory; bank DB architecture | Create Oracle JDBC provider via wsadmin | Datasource = the bridge between WAS and the bank's money data |
| 22 | `createDatasource`, test connection via script | Full provisioning script: provider + datasource + J2C alias + test | New Loan Origination app onboarding = this exact script |
| 23 | Connection pool tuning: min/max, timeouts, preTestSQL, purge policy, aged timeout | Load test the pool; break it; tune it | NEFT peak-hour `ConnectionNotAvailableException` → pool 50→120 |
| 24 | JMS: queues, CFs, listener ports vs activation specs; SIBus vs IBM MQ | Script: create bus, queue, connection factory; simulate dead queue + replay | Payment instruction = message; Loan app drops msgs to `LOAN.Q.DEAD` → replay |
| 25 | MQ provisioning + recap | **Mini Project 3:** One script provisions datasource + JMS + both + **25 Q&A** | "Provision middleware for a new banking app" — done end-to-end |

**✅ Milestone 5:** Provision the full data + messaging layer of any bank app.

---

## 🗓️ WEEK 6 (Days 26–30) — Security + SSL [Module 12] ⭐ Bank Mandatory

| Day | Learn | Lab | 🏦 Bank Connect |
-----|-------|-----|------------------|
| 26 | Global security, LTPA, LDAP/AD, SSO; lockout recovery theory | Enable global security in lab (document disable steps!) | Locked out of DMgr with on = classic horror story |
| 27 | LDAP federation + security enable/disable via script | Script: configure AD federation + enable security | Bank admins live in Active Directory — never local users |
| 28 | Role mapping (Admin/Operator/Monitor) via script; password encoding | Bulk-downgrade 12 admins → Operator; export evidence; `PropFilePasswordEncoder` | Audit finding → script fix → SOX evidence; plain-text DB password = instant violation |
| 29 | SSL: keystore vs truststore, cert chains, SSL configs | Generate certs via script; explore keystores | Every bank connection is TLS — certs everywhere |
| 30 | Cert expiry monitoring security audit scripting | **Mini Project 4:** `cert_expiry_check.py` + security audit script + **25 Q&A** | Expired gateway cert = card transactions fail = P1 outage |

**✅ Milestone 6:** Secure a cell and pass a SOX audit.

---

## 🗓️ WEEK 7 (Days 31–35) — Performance + IHS [Modules 13–14]

| Day | Learn | Lab | 🏦 Bank Connect |
|-----|-------|-----|------------------|
| 31 | Heap vs native, OOM types, GC policies, verbose:gc | Induce OOM on purpose; enable verbose GC; read GC log | Internet Banking slow at 12 PM — you're the fixer |
| 32 | Thread dump + heap dump analysis (TMDA, MAT); PMI counters | Take dumps via script; find blocked thread + memory leak | "Walk me through a thread dump you analyzed" — guaranteed question |
| 33 | IHS + plugin architecture; pluginfg.xml; routing hops | Install IHS; map every hop: Browser→IHS→Plugin→WAS | Know every hop for every outage bridge call |
| 34 | Plugin generation/propagation via AdminTask | Script genPluginCfg + propagate; break plugin → fix → 404 drill | New member added → stale plugin → customers get 404 → fix in 5 min |
| 35 | Performance diagnosis method + recap | **Mini Project 5:** Slow-app runbook (heap→→pools→threads→DB) + **25 Q&A** | This systematic method IS the 10-year admin skill |

**✅ Milestone 7:** Diagnose any performance issue methodically.

---

## 🗓️ WEEK 8 (Days 36–40) — Operations, DR, Capstone [Modules 15–19]

| Day | Learn | Lab | 🏦 Bank Connect |
|-----|-------|-----|------------------|
| 36 | Fixpacks/iFixes, imcl silent, patch rollout strategy | Apply + rollback a fixpack silently; write `patch_rollout.sh` | Quarterly CVE patch to 200 JVMs, DEV→UAT→PROD with health checks |
| 37 | Logging/trace/HPEL + `backupConfig`/`restoreConfig` | Script: enable trace on demand; nightly backup + 30-day retention; 90-day payment log archive | Compliance requires it; corrupted config → restore in 40 min vs 2-day rebuild |
| 38 | Automation: startup order scripts, auto-heal, CI/CD (Jenkins calling wsadmin) | Build `start_stack.sh` + `auto_heal.sh` (detect dead JVM → restart email) | DR drill: full banking stack up in 20 min; 3 AM self-healing = why the bank keeps you |
| 39 | **Capstone:** "Bank Health Check Suite" | Build it — JVM status, heap %, DB pool, MQ listeners, cert expiry, disk → email report | Your GitHub portfolio centerpiece — real banks pay seniors to build this |
| 40 | **Mock Interviews:** topology, deployment, clusters, datasource, outage war stories, troubleshooting drills | Answer out loud, record yourself; final revision plan | Practice "In my bank we..." — that phrasing sells 10 years |

**✅ Final Milestone:** Job-ready alone + interview-ready.

---

## 📌 Phase 2 (After Day 40) — 7-Part Deep Dive Per Topic

Go topic by topic, **1 topic every 3–4 days** using this 7-part format:

1. 20 real interview Q&A (10-yr level)
2. 5 "Recent issues you faced" questions with model answers
3. 20 banking scenario questions
4. 20 banking troubleshooting questions
5. 5 outage war stories
6. 5 behavioral/leadership questions
7. 5 architecture/design questions (Lead level)

**Priority Order:**
Clusters → Deployment → DataSource → Security → Performance → MQ → IHS → Patching → DR

---

## ✅ Final Checklist

- [ ] Lab VMs with WAS ND installed
- [ ] 40+ scripts saved in GitHub
- [ ] 200 interview Q&A in Bank Runbook
- [ ] 5 Mini Projects completed
- [ ] Bank Health Check Suite (Capstone) built
- [ ] 2 mock interviews recorded

**🚀 Next Step: Say "Start Day 1" and begin the journey.**
