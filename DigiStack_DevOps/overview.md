# 🗓️ WebSphere DevOps Automation — 84-Day Master Plan

> **Format:**-wise · 84 Days / 12 Weeks
> **Daily commitment:** 2 hrs weekdays + 3–4 hrs weekends
> ⚠️ **Rule #1:** Stick to sequence; do **NOT** skip Days in A.2 — everything depends on wsadmin.

---

## 📑 Table of Contents

- [Phase A — Automation Foundations (Days 1–21)](#-phase-a--automationations-days-121)
- [Phase B — Configuration as Code (Days 15–42)](#-phase-b--configuration-as-code-days-1542)
- [Phase C — CI/CD Automation (Days 39–63)](#-phase-c--cicd-automation-days-3963)
- [Phase D — Operational Automation (Days 59–77)](#-phase-d--operational-automation-days-5977)
- [Phase E — Expert / Lead Level Automation (Days 78–84)](#-phase-e--expert--lead-level-automation-days-7884)
- [Interview Preparation Bank (Days 85–98)]-interview-preparation-bank-days-8598-post-plan)
- [Golden Rules](#-golden-rules-throughout)

---

## ✅ Progress Tracker

| Phase | Focus | Days | Status |
|-------|-------|------|--------|
| A | Automation Foundations | 1–21 | ⬜ |
| B | Configuration as Code | 15–42 | ⬜ |
| C | CI/CD Automation | 39–63 | ⬜ |
| D | Operational Automation | 59–77 | ⬜ |
| E | Expert / Lead Level | 78–84 | ⬜ |
| — | Interview Prep | 85–98 | ⬜ |

---

# 📦 PHASE A — Automation Foundations (Days 1–21)

## Module A.1 — Why Automation in a Bank WAS Estate (Days 1–2)

- [ ] **Day 1:** Manual vs scripted config — drift, human error, audit pain. Map your current estate (note how many JVMs/cells you manage). Write down **5 painful manual tasks** you do monthly.
- [ ] **Day2:** Automation maturity ladder (manual → scripts → pipelines → IaC → self-healing). Draw where your bank sits today. **Scenario exercise:** 400 JVMs / 8 environments — document the *"Prod changed, UAT forgot"* incident class and how each maturity level prevents it.

## Module A.2 — wsadmin Deep Dive (Days 3–10) ⭐ *The most important 8 days*

- [ ] **Day3:** ws launch modes (`-lang jython`, `-conntype SOAP/IPC`, `-host/-port`, `-profile`). Jython vs Jacl — why Jython won. Run interactive wsadmin against your dmgr.
- [ ] **Day 4:** `AdminControl` — MBeans, `completeObjectName`, `invoke`, `getAttribute`. **Practice:** get JVM heap, thread pool stats, stop/start a server via AdminControl.
- [ ] **Day 5:** `AdminConfig` — `getid`, `list`, `create`, `modify`, `remove`, attributes vs types, `showall`. Explore a server's parent/child config chain (**Cell → Node → Server → ProcessDef**).
- [ ] **Day 6:** `AdminTask` — list commands per task group (`help('-commands')`), interactive mode vs batch mode parameters. **Practice:** `createDataSource`, `createCluster`, `modifyClusterMember`.
- [ **Day 7:** `AdminApp` — `install`, `update`, `edit`, `list`, `taskInfo`, options (`mapModulesToServers`, `MapWebModToVH`). Install an EAR from script with bindings.
- [ ] **Day 8:** Config save/rollback: `save()`, `reset()`, `-archives` with AdminConfig. Reading scopes correctly (cell/node/server scope objects). Modify JVM args on one server vs cluster.
- [ ] **Day 9:** Error handling: `try/except`, `WSException`, `AdminControl.testConnection`, exit codes, logging via log4j-style logger or print to file Convert interactive commands into a `.py` file.
- [ ] **Day 10:** 🏁 **Capstone A.2:** Write parameterized script `create_datasource.py` (scope, jndi, URL, user via properties file) that provisions a DataSource across **12 JVMs identically**. Test in Dev, dry-run in SIT.

## Module A.3 — Python/Jython Essentials for WAS Admins (Days 11–14)

- [ ] **Day 11:** Variables, lists, dicts, loops, functions in Jython. Parse `SystemOut.log` and count ERROR lines.
- [ ] **Day 12:** Exception handling, file I/O, string manipulation, `os.popen`/`subprocess` for shell commands from wsadmin. Build a log-summarizer script.
- [ ] **Day 13:** Properties-file driven config (`java.Properties`), environment switching (`dev.props`/`prod.props`), argument parsing via `sys.argv`.
- [ ] **Day 14:** 🏁 **Capstone A.3:** Design `was_lib.py` — shared functions: `getServerId()`, `setJVMHeap()`, `checkAppStatus()`, `createDS()`. Structure: **lib + env props + main scripts**. Document it like a bank shared repo.

---

# 📦 PHASE B — Configuration as Code (Days 15–42)

## Module B.1 — Automating WebSphere Configuration (Days 15–22)

- [ ] **Day 15:** Script cells/nodes/clusters: `createCluster`, `createClusterMember`, set JVM generic args, thread pools, process definitions.
- [ ] **Day 16:** DataSources + JMS: queues, topics, connection factories, activation specs, listener (WAS 8.5 vs 9 differences), SIBus creation.
- [ ] **Day 17:** Virtual hosts, context, generate plugin (`AdminTask.generatePluginCfg` / wsadmin + IHS), `propagatePluginCfg`.
- [ ] **Day 18:** Security automation: SSL repertoires, keystores/certs via AdminTask, global security on/off, LDAP federated repository config via script.
- [ ] **Day 19:** Export/import: `AdminTask.exportServer`, `exportWasprofile`, ConfigArchive (`AdminConfig.export`/extraction), validated config import between envs.
- [ ] **Day 20:** Build `build_env.py` — one master script + env properties + `was_lib.py` calls.
- [ ] **Day 21:** 🏁 **Capstone B.1:** KYC app onboarding run your script in a clean environment — entire WAS side (cluster, DS, JMS, vhost, security) in **< 20 min**. Time it and log it.
- [ ] **Day 22:** Review/refactor day — clean your library, add comments, peer-review style self-check.

## Module B.2 — Environment Parity Drift Detection (Days 23–26)

- [ ] **Day 23:** Theory: config baselining, what to compare (JVM args, pools, DS, security attrs). Design your comparison schema (JSON output per env).
- [ ] **Day 24:** Write `drift_report.py`: dump config objects from UAT + Prod via wsadmin → normalize to JSON → diff. Highlight differences with severity.
- [ ] **Day 25:** Scheduled drift runs (cron), report format for auditors, auto-remediation options (report-only vs fix mode — discuss guardrails).
- [ ] **Day 26:** 🏁 **Capstone B.2:** Scenario: auditor claims *Prod heap ≠ UAT*. Run drift report → prove/fix in minutes. Save evidence output.

## Module B.3 — Ansible for WebSphere (Days 27–34)

- [ ] **Day 27:** Ansible crash course for WAS admins: inventory, playbooks, roles, variables, `ansible-vault`. Install, target two test VMs.
- [ ] **Day 28:** Ansible + WAS basics: manage services, file distribution (EARs, props), `command`/`shell` modules calling wsadmin.
- [ ] **Day 29:** IBM WAS Ansible collections (`ibmwebsphere/was` collection) — explore modules for profile creation, federation, fixpacks.
- [ ] **Day 30:** Play: profile creation + `addNode` federation across hosts, idempotency checks (`stat`/`creates`/`when`).
- [ ] **Day 31:** Playbook: IHS + plugin propagation + webserver definition creation.
- [ ] **Day 32:** Fixpack/CVE patch playbook: pre-checks → stop rolling cluster members → install via IM/updateInstaller → start → verify → next member (`serial` keyword, `max_fail_percentage`).
- [ ] **Day 33:** JVM restart playbooks with health-check waits (`uri module `retries`/`until`), failure handling.
- [ ] **Day 34:** 🏁 **Capstone B.3:** Scenario: **CVE patch on 120 hosts in 4 hrs Sunday**. Build rolling patch playbook with `serial: 4`, pre/post checks, Vault for creds. Run against test inventory.

## Module B.4 — Provisioning Full Stacks by Script (Days 35–38)

- [ ] **Day 35:** Design end-to-end build: install WAS → `manageprofiles` (response files) → dmgr/custom profiles → federation → IHS → plugin → app deploy.
- [ ] **Day 36:** Orchestrate: Ansible for OS-levels, wsadmin for cell config, single entry point (`build_cell.yml` calling roles).
- [ ] **Day 37:** IHS + plugin + WAS automated end-to-end; validate `curl` + admin console screenshots.
- [ ] **Day 38:** 🏁 **Capstone B.4:** DR drill rebuild a full cell in **< 4 hrs** from Git scripts only. Document actual time achieved.

---

# 📦 PHASE C — CI/CD Automation (Days 39–63)

## Module C.1 — Jenkins Pipelines for WAS (Days 39–46)

- [ ] **Day 39:** Jenkins for admins: jobs vs pipelines (Jenkinsfile), agents/nodes, workspace. Set up Jenkins against your lab.
- [ ] **Day 40:** Declarative pipeline syntax: stages, steps, `when`, `post`, `parallel`. Write hello-world + a stage calling wsadmin (`sh + Jython script')
## Module C.1 — CI/CD Pipelines with Jenkins (Days 41–46)

- [ ] **Day 41:** Credentials plugin + Vault: storing WAS admin creds, DB passwords — **never in scripts**. SSH keys to WAS hosts.
- [ ] **Day 42:** Deployment pipeline: Build (pull EAR from Nexus/Artifactory) → Deploy via `AdminApp` to cluster → Smoke test → Promote (copy between cell inventories).
- [ ] **Day 43:** Rollback stage: keep last-good-EAR registry (file/Git), automated revert script + config restore, `post { failure }` blocks.
- [ ] **Day 44:** Multi-environment pipeline: Dev → SIT → UAT → Prod with manual approval gate (`input` step) before Prod.
- [ ] **Day 45:** Notifications: email/Teams/Slack on stage failures; tagging builds with `app+version+env`.
- [ ] **Day 46:** 🏁 **Capstone C.1:** Cards-team pipeline: **30 releases/month** flow with auto-rollback when smoke test fails. Demo to yourself end-to-end.

## Module C.2 — Blue-Green & Zero-Downtime Deployments (Days 47–51)

- [ ] **Day 47:** Theory: Blue/Green pattern with WAS clusters + IHS plugin traffic switch; session persistence, DB backward-compatibility rules.
- [ ] **Day 48:** Build Blue/Green setup in lab: duplicate cluster, plugin routing, custom `http` plugin-cfg or routing rules.
- [ ] **Day 49:** Traffic switch automation: script pluginen/reload, verify, cutover/rollback in one Jenkins stage.
- [ ] **Day 50:** Canary: weight-based routing to 5% (IHS rules / Plugin custom properties), monitoring gate, ramp **5 → 50 → 100**.
- [ ] **Day 51:** 🏁 **Capstone C.2:** Internet banking Friday-evening release simulation: deploy to Green, switch, smoke, rollback drill — **measure downtime (target: zero)**.

## Module C.3 — Automated Testing Gates & Quality (Days 52–55)

- [ ] **Day 52:** Smoke test design: `curl` health URLs, login flow via curl/requests, DB ping, expected response codes. Script `smoke_test.py`.
- [ ] **Day 53:** Pre-deploy config validation: port availability, DS reachable, disk space, cert validity. Script `preflight_check.py`.
- [ ] **Day 54:** gates into Jenkins: fail stage on any preflight/smoke failure; capture evidence logs per deployment.
- [ ] **Day 55:** 🏁 **Capstone C.3:** Scenario: **bad DB password — pipeline blocks before Prod**. Simulate and prove.

## Module C.4 — GitOps & Version-Controlled WAS Config (Days 56–58)

- [ ] **Day 56:** Structure a Git repo for WAS config `/lib`, `/envs`, `/apps`, `/pipelines`. Branching for banks (feature → UAT → release tags).
- [ ] **Day 57:** Pull requests as change control: required reviewers, `CODEOWNERS`, CI on PR (jython syntax check, dry-run mode).
- [ ] **Day 58:** 🏁 **Capstone C.4:** Scenario: *"Who changed connection pool in Prod in March?"* — practice `git blame`/`log`/`tag` answering in **10 seconds**. Tag each release.

---

# 📦 PHASE D — Operational Automation (Days 59–77)

## Module D.1 — Health Checks & Self-Healing (Days 59–62)

- [ ] **Day 59:** Health check script: port, ping, app URL, heap via `AdminControl`, thread pool, DS test connection. JSON/HTML output.
- [ ] **Day 60:** Watchdog design: cron/systemd service, guardrails (max restarts/hour, don't restart during patch window, grace period), auto-restart failed JVM/nodeagent.
- [ ] **Day 61:** Auto-ticketing: ServiceNow REST API from script (curl/requests) — create incident with logs attached on failure.
- [ ] **Day 62:** 🏁 **Capstone D.1:** Kill a node agent at *"3 AM"* — watchdog restarts + opens ServiceNow ticket with evidence. Test guardrail: **no restart storm**.

## Module D.2 — Log Rotation, Archiving & Cleanup ( 63–65)

- [ ] **Day 63:** WAS log policies: RAS tracing settings via script (SystemOut rolling, max size), FFDC, javacore/heapdump cleanup.
- [ ] **Day 64:** Disk space guard: threshold script → clean old dumps → alert before 90% full. Log shipping: filebeat/fluentd to Splunk/ELK basics.
- [ ] **Day 65:** 🏁 **Capstone D.2:** Scenario: payments JVM from `/var` full of javacores — install your guard, simulate disk fill, verify clean + alert.

## Module D.3 — Scheduled & Batch Operations (Days 66–69)

- [ ] **Day 66:** Scheduled restarts: cron/Control-M patterns, Ansible scheduled runs (`at`/cron` module or AWX schedules).
- [ ] **Day 67:** Coordinated rolling restarts: stop member → health wait → next (`serial` in Ansible / loop in Jython). Node agent + dmgr ordering rules.
- [ ] **Day :** Maintenance window automation: quiesce (stop apps, drain sessions) → patch/restart → unquiesce → verify — one runbook script.
- [ ] **Day 69:** 🏁 **Capstone D.3:** Nightly 1 AM restart of **12 payment JVMs** — zero downtime, full evidence log, no manual runbook.

## Module D.4 — Certificate & Security Automation (Days 70–73)

- [ ] **Day 70:** Cert inventory: script scan all keystores (`gskcmd`/iKeyman, `AdminTask` query) → expiry report + alert at **30/15/7/1 days**.
- [ ] **Day 71:** Automated renewal: generate CSR, import signed, replace in SSL repertoires, dynamic reload — per cell script.
- [ ] **Day 72:** LTPA key expiry monitoring + LTPA key export/import automation between cells; SSL repertoires & keystore path automation across all JVMs via one script.
- [ ] **Day 73:** 🏁 **Capstone D.4:** Scenario: **expired intermediate cert kills internet banking at midnight** — build full chain: expiry report (email at 30/15//1 days) + scripted renewal runbook. Simulate an expiry and renew it via script only.

## Module D.5 — Automation for Outages & DR (Days 74–77)

- [ ] **Day 74:** Theory: scripted DR runbooks vs document runbooks. Identify every manual DR step in your bank's plan → scriptable or not?
- [ ] **Day 75:** Build DR promotion script: verify DR cell config sync (compare DS/JMS/security DC vs DR), start cluster, point plugin/DNS, verify apps.
- [ ] **Day 76:** Post-DR smoke tests + evidence collection (timestamps, command output, screenshots) packaged for management/regulators.
 [ ] **Day 77:** 🏁 **Capstone D.5:** DR event simulation: your scripted runbook switches payments to DR in **< 45 minutes** with evidence logs. Time it and record gaps.

---

# 📦 PHASE E — Expert / Lead Level Automation (Days 78–84)

## Module E.1 — Automation Architecture & Governance (Days 78–80)

- [ ] **Day 78:** Framework design: standards (naming conventions, `envs.props` format), shared library ownership ownership, code review process, versioning of was_lib.py. Draw your framework architecture diagram.
## Module E.1 — Automation Architecture & Governance (Days 78–80)

- [ ] **Day 78:** Framework design: standards (naming conventions, `envs.props` format), shared library ownership, code review process, versioning of `was_lib.py`. **Draw your framework architecture diagram.**
- [ ] **Day 79:** Guardrails: what NOT to automate (prod destructive ops, approval gates, DR human decision points), dry-run modes, audit logging of every automated action, break-glass procedures.
- [ ] **Day 80:** 🏁 **Capstone E1:** Write your **3-year automation roadmap presentation**: maturity ladder stages, 80% automation target, DORA-style metrics, headcount redeployment story. **10-slide deck, leadership tone.**

## Module E.2 — Modernization Automation (Days 81–82)

- [ ] **Day 81:** WAS → Liberty migration automation: `server.xml` generation from traditional config (script translating DS/JMS/security config), Liberty feature mapping, Liberty admin style differences. Build a **converter script for one app**.
- [ ] **Day 82:** Containers: Helm chart structure for Liberty apps, OpenShift deployment pipeline (BuildConfig/odo or CI builds → image → Helm deploy → route check). 🏁 **Capstone E.2:** Conversion pipeline handles config translation for a sample app (**~70% auto**).

## Module E.3 — Metrics of Automation (Day 83)

- [ ] **Day 83:** 🏁 **Capstone E.3:** DORA metrics for WAS: deployment frequency, lead time, MTTR, change failure rate. Build a simple metrics dashboard (Jenkins + spreadsheet/Grafana). Leadership slide: *"2 weeks → 1 day lead time; failed deploys 15% → 2%."*

## 🏁 Day 84 — Final Integration Day

- [ ] **Full end-to-end dress rehearsal:** run your complete toolchain in one flow —
  `Git → Jenkins → preflight → deploy → smoke → drift check → health report → evidence package`
- [ ] **Archive:** your `was_lib.py`, capstone scripts, runbooks, roadmap deck = **your interview portfolio**.

---

# 🎯 Interview Preparation Bank (Days 85–98, Post-Plan)

| Days    | Focus                                                                  |
|---------|------------------------------------------------------------------------|
| 85–86   | 20 Real Interview Q&A (10-yr level) — answer out loud, **record yourself** |
| 87      | 5 *"Recent Issues You Faced"* — use your capstones as the real answers  |
| 88–90   | 20 Banking **Scenario-Based** Questions (1–2/day practice + written answers) |
| 91–93   | 20 Banking **Troubleshooting-Based** Questions                          |
| 94      5 Real Outage Scenarios / War Stories — **STAR format**, with evidence artifacts |
| 95      | 5 Behavioral/Leadership Questions                                       |
| 96–97   | 5/Design Questions — **whiteboard practice**               |
| 98      | 🎤 **Mock interview:** full 90-min simulation using your portfolio as proof |

---

# 📜 Golden Rules Throughout

>1. **Every capstone produces (script + evidence log)** — are your interview war stories.
> 2. **Always test in Dev first** — a `--dry-run` flag is built into every script from Day 10 onward.
> 3. **Everything goes to Git from Day 14 onward** — your repo IS your portfolio.
> 4. **Time every manual task you automate** — before/after numbers feed your Day 83 metrics story.
> 5. **If a module runs over, cut polish work — never cut a capstone.**

---

*Made with ☕ for WebSphere admins who want to automate themselves out of 2 AM calls.*

