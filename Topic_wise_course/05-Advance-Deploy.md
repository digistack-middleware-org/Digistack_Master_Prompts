# 📅 WebSphere Deployment Mastery — 30-Day Course in Correct Learning Order
> **Designed like a 10-Year WebSphere Admin/Architect at a Big MNC Bank**
>
> **Daily commitment:** 2 hrs weekdays | 4 hrs weekends
> **Daily structure:** 50% Theory → 40% Hands-on Lab → 10% Notes/Issue Diary

> **Why this order works:** Topology & labs first (build the cell) → Deployment basics
> (install/update/rollback) → Deployment-linked config (VH, IHS, datasource, classloading)
> → Advanced (automation, blue-green, sync, patching) → Troubleshooting + Capstone.
> You can't do Rollout Update (Day 12) without a cluster (Day 2), and you can't
> automate (Day 22) what you can't do manually (Days 8–12).

---

## 📊 Progress Tracker

| Order | Phase | Days | Status |
|-------|-------|------|--------|
| 1️⃣ | Foundation (Phase 0) | 1–7 | ⬜ |
| 2️⃣ | Deployment Fundamentals (Phase 1) | 8–14 | ⬜ |
| 3️⃣ | Deployment-Linked Configurations (Phase 2) | 15–21 | ⬜ |
| 4️⃣ | Advanced Deployment (Phase 3) | 22–27 | ⬜ |
| 5️⃣ | Troubleshooting + Mastery (Phase 4 & 5) | 28–30 | ⬜ |

---

## 🟢 WEEK 1 — Foundation (Phase 0)

### Day 1 — WebSphere ND Basics
- **Theory (1 hr):**
  - What is WAS, WAS ND, why banks use ND 8.5.5/9.x
  - Editions: Base ND vs Liberty
  - Components: Cell, DMGR, Node Agent, Node, App Server, Cluster
- **Lab (1 hr):**
  - Install WAS ND 9 trial (DMGR profile only today)
  - Login to console: `https://localhost:9043/ibm/console`
- ✅ **Exit check:** Can you draw a Cell topology on paper from memory?

### Day 2 — Banking Topology + Full Lab Setup
- **Theory (1 hr):**
  - Bank layout: CITIAPPS_Cell, txn-node01, TxnServer1/2, PaymentCluster
  - Clusters span 2+ nodes (HA, patching one node at a time)
- **Lab (3 hrs):**
  - Create DMGR profile + 2 custom node profiles
  - `addNode.sh txn-dmgr01 -username wasadmin -password ****`
  - Create PaymentCluster with 2 members (TxnServer1, TxnServer2)
- ✅ **Exit check:** Cluster shows green (running) in console

### Day 3 — EAR/WAR/JAR + Java EE Concepts
- **Theory (1 hr):**
  - WAR vs JAR vs EAR — what's inside PaymentApp.ear
  - Servlets, EJB, JNDI — how apps call DB and MQ
  - Classloader basics (parents, hierarchy)
- **Lab (3 hrs):**
  - Download/build a sample EAR; unzip and inspect `application.xml`, `web.xml`
  - Deploy it once just to see it appear in console
- ✅ **Exit check:** Explain in 2 mins what happens when a user hits `/payment`

### Day 4 — Environment Types + Bank Release Process
- **Theory (1.5 hr):**
  - DEV → SIT → UAT → DR → PROD flow
  - CAB, change tickets, freeze windows, rollback plans
  - What differs per environment (ports, clusters, approvals)
- **Lab (0.5 hr):**
  - Write a mock change ticket for a PaymentApp v2 deploy (risk, steps, rollback, verification)
- ✅ **Exit check:** Describe a full release night timeline (8 PM–2 AM)

### Day 5 — wsadmin Fundamentals
- **Theory (0.5 hr):**
  - wsadmin objects: AdminApp, AdminConfig, AdminControl, AdminTask, Help
  - SOAP vs JMX connector, port 8879
- **Lab (1.5 hr):**
  ```bash
  /opt/IBM/WebSphere/AppServer/profiles/Dmgr01/bin/wsadmin.sh -conntype SOAP -port 8879
  ```
  ```python
  print AdminApp.list()
  print AdminControl.completeObjectName('type=Server,*')
  print AdminTask.listServers()
  Help.AdminApp()
  ```
- ✅ **Exit check:** List all servers and apps in your cell via wsadmin

### Day 6 — Revision Week 1
- [ ] Redraw full topology diagram from memory
- [ ] Re-run every wsadmin command without notes
- [ ] Write summary notes (1 page per topic)

### Day 7 — Week 1 Capstone Lab
- [ ] **4-hr challenge:** From scratch — federate a node, create PaymentCluster, verify via wsadmin
- [ ] Write your first mini runbook
- 🎯 **Milestone 1 complete**

---

## 🟡 WEEK 2 — Deployment Fundamentals (Phase 1)

### Day 8 — Install Application via Console (Module 1.1)
- **Theory (0.5 hr):** Install wizard steps, fast path, master repository
- **Lab (1.5 hr):**
  - Applications → New Application → deploy PaymentApp.ear
  - Map modules to **all** cluster members → map virtual host → set context root
    `/payment` → Save → Start
- ✅ **Exit check:** App = Started on all members; WSVR0100I in SystemOut.log

### Day 9 — Install via wsadmin
- **Theory (0.5 hr):** AdminApp.install options
- **Lab (1.5 hr):**
  ```python
  AdminApp.install('/depot/apps/PaymentApp.ear',
    '[-cluster PaymentCluster -appname PaymentApp -usedefaultbindings]')
  AdminConfig.save()
  AdminApp.startApp('PaymentApp')
  ```
  - Compare console vs wsadmin results
- ✅ **Exit check:** Deploy the same app both ways; time each method

### Day 10 — Deployment Options Deep-Dive (Module 1.2)
- **Theory (1 hr):**
  - Pre-compile JSPs (9 AM branch-open slowness story)
  - Deploy EJB, distributeApp, binary config risks
- **Lab (1 hr):**
  - Deploy with `-preCompileJsps -deployejb`; compare first-request response time
- ✅ **Exit check:** List 5 install options and when you'd use each

### Day 11 — Updating Applications (Module 1.3 part 1)
- **Theory (0.5 hr):** Update operations: update, update-merge, add module, single-file
- **Lab (1.5 hr):**
  ```python
  AdminApp.update('PaymentApp', 'app',
    '[-operation update -contents /depot/PaymentApp_v2.ear]')
  AdminConfig.save()
  ```
- ✅ **Exit check:** v2 visible; old version still archived in `/depot/releases/`

### Day 12 — Rollout Update + Rollback (Module 1.3 part 2)
- **Theory (1 hr):**
  - Rollout Update = rolling restart, session preservation
  - Bank rollback standards (keep last 3 versions)
- **Lab (1 hr):**
  - Perform Rollout Update on PaymentCluster
  - Rollback to v1 using stored EAR
- ✅ **Exit check:** Complete update→rollback cycle in under 15 min

### Day 13 — Start/Stop/Edit + Startup Logs (Module 1.4)
- **Theory (0.5 hr):** WSVR0100I / WSVR0221I / J2CA0046E meanings
- **Lab (1.5 hr):**
  - Stop/start app via `AdminControl.invoke(... 'stopApplication' ...)`
  - Break the app (point datasource at bad host) → read SystemOut.log to diagnose
- ✅ **Exit check:** Diagnose a stopped app from logs alone

### Day 14 — Revision + Week 2 Mini Project
- [ ] **4-hr timed drill:** install → update → rollout → rollback → verify → document
- 🎯 **Milestone 2 complete:** You can deploy anything, anywhere, both ways

---

## 🟠 WEEK 3 — Deployment-Linked Configurations (Phase 2)

### Day 15 — Virtual Hosts (Module 2.1)
- **Theory (0.5 hr):** VH concept, host aliases, why 404s happen
- **Lab (1.5 hr):**
  - Create `vh_txn` with aliases `*:9443`, `*:443`
  - Re-map app to new VH; test via direct port vs VH port
- ✅ **Exit check:** Reproduce a "404 via URL, works on direct port" issue and fix it

### Day 16 — IHS + Plugin Routing (Module 2.2)
- **Theory (0.5 hr):** Browser → IHS → plugin-cfg.xml → cluster flow
- **Lab (1.5 hr):**
  - Install IHS + Web Server definition in DMGR
  - Generate & propagate plugin; test `https://host/payment`
- ✅ **Exit check:** Traffic flows through IHS to cluster members

### Day 17 — Shared Libraries + Classloading (Module 2.3)
- **Theory (1 hr):** PARENT_FIRST vs PARENT_LAST; Log4Shell story
- **Lab (1 hr):**
  - Create shared library with log4j-2.17.jar
  - Attach PARENT_LAST to app; verify app loads its own jar version
- ✅ **Exit check:** Explain classloader delegation in one sentence a manager understands

### Day 18 — Deployment Descriptors & Security Role Mapping (Module 2.4)
- **Theory (0.5 hr):** web.xml, ibm-*.xmi files, role → LDAP group mapping
- **Lab (1.5 hr):**
  - Map roles during install via wsadmin `-MapRolesToUsers`
  - Compare UAT vs PROD script differences; standardize one script
- ✅ **Exit check:** One script that deploys identically in both envs

### Day 19 — Datasources/JDBC (Deploy Prerequisite)
- **Theory (0.5 hr):** JDBC provider, datasource, JNDI, connection pool
- **Lab (1.5 hr):**
  - Create JDBC provider + datasource + J2C auth alias
  - Test connection; note ADMA0075E if you deploy without it
- ✅ **Exit check:** App connects to DB through datasource successfully

### Day 20 — Revision + Integration Lab (Part 1)
- [ ] Full pipeline: create VH → datasource → shared lib → deploy via wsadmin → IHS routing

### Day 21 — Integration Lab (Part 2) + Review
- [ ] Break and fix 3 things deliberately (wrong VH, missing role map, plugin not propagated)
- 🎯 **Milestone 3 complete:** You understand everything *around* deployment

---

## 🔴 WEEK 4 — Advanced Deployment (Phase 3)

### Day 22 — Jython Automation (Module 3.1)
- **Theory (0.5 hr):** Script structure, sys.argv, try/except
- **Lab (1.5 hr):**
  - Write production-grade `deploy.py` (stop → update → save → start → verify)
  - Add logging and error handling
- **Reference script:**
  ```python
  # deploy.py — run: wsadmin.sh -f deploy.py PaymentApp /depot/PaymentApp.ear
  import sys
  app = sys.argv[0]; ear = sys.argv[1]
  try:
      AdminControl.invoke(AdminControl.completeObjectName(
          'type=ApplicationManager,process=*'), 'stopApplication', app)
  except: pass
  AdminApp.update(app, 'app', '[-operation update -contents %s]' % ear)
  AdminConfig.save()
  AdminControl.invoke(AdminControl.completeObjectName(
      'type=ApplicationManager,process=*'), 'startApplication', app)
  print "Deployment complete"
  ```
- ✅ **Exit check:** One-command deployment works reliably

### Day 23 — CI/CD Integration + Secrets Handling
- **Theory (0.5 hr):** Jenkins/UrbanCode calling wsadmin; secrets via props file (chmod 600)
- **Lab (1.5 hr):**
  - Create Jenkins free-style job: parameterized (app, ear path, cluster) → runs deploy.py
- ✅ **Exit check:** Deploy triggered from Jenkins with zero passwords in scripts

### Day 24 — Application Editions / Blue-Green (Module 3.2)
- **Theory (1 hr):** Editions, dynamic clustering, traffic cutover — how banks do zero downtime
- **Lab (1 hr):**
  - Create edition 2.0 of PaymentApp → activate → cutover
- ✅ **Exit check:** Explain editions vs Rollout Update trade-offs

### Day 25 — Node Sync & Master Repository (Module 3.3)
- **Theory (0.5 hr):** Master config → node sync flow, WSVR0605W
- **Lab (1.5 hr):**
  - Stop node agent → make config change → observe "saved but not showing"
  - `syncNode.sh txn-dmgr01` → verify config.xml diff
- ✅ **Exit check:** Diagnose and fix a sync failure in under 10 min

### Day 26 — Sessions & Clustering During Deploy (Module 3.4)
- **Theory (0.5 hr):** Session affinity, memory-to-memory replication, affinity timeout
- **Lab (1.5 hr):**
  - Deploy a session-tracking test app; run Rollout Update while holding a session
  - Verify session survives
- ✅ **Exit check:** Zero dropped sessions across rollout

### Day 27 — Patching: iFix & Fixpacks (Module 3.5)
- **Theory (0.5 hr):** IBM Installation Manager, quarterly bank patch cycles
- **Lab (1.5 hr):**
  - Apply an iFix to WAS; roll back an iFix; verify via `versionInfo.sh`
- ✅ **Exit check:** Document a mock quarterly patch runbook
- 🎯 **Milestone 4 complete:** Automation + advanced deployment done

---

## 🟣 WEEK 5 — Troubleshooting + Mastery (Phase 4 & 5)

### Day 28 — Deployment Log Analysis + Common Errors (Modules 4.1–4.2)
- **Theory (1 hr):** ADMA5021I/5013I/5026E, FFDC, activity.log; memorize the failure table

  | Error | Cause | Fix |
  |-------|-------|-----|
  | ADMA0068E | EAR corrupted | Re-export EAR, validate |
  | ADMA5026E | Module not mapped | Re-map modules to all members |
  | ADMA0075E | DB/JDBC binding missing | Create datasource BEFORE deploy |
  | ClassNotFound in prod | Shared lib not attached | Attach lib + restart |

- **Lab (1 hr):**
  - Deliberately trigger: corrupted EAR, missing module mapping, missing datasource, ClassNotFound
  - Fix each within 15 min
- ✅ **Exit check:** Fix any ADMA* error thrown at you

### Day 29 — Verification Checklist + DR + Runbooks (Modules 4.3, 5)
- **Theory (1 hr):**
  - Bank SOP checklist, DR cell concepts, deployment metrics (MTTR, failure %)
- **Verification Checklist (Bank SOP):**
  - [ ] App status = Started on ALL cluster members
  - [ ] Smoke test URL via IHS works
  - [ ] Plugin regenerated & propagated
  - [ ] Logs clean (no ERROR in first 5 min)
  - [ ] DB connections pooled fine (J2CA0206W absent)
  - [ ] Change ticket updated, screenshots attached, rollback window open
- **Lab (1 hr):**
  - Write your complete personal PaymentApp deployment runbook
  - Do one deployment using ONLY the checklist
- ✅ **Exit check:** Runbook is good enough for a junior admin to follow

### Day 30 — FINAL CAPSTONE EXAM (4 hrs)
- **Task:** Full production-style release simulation
  1. Deploy PaymentApp v1 → verify
  2. Deploy v2 via Rollout Update with zero session loss
  3. "Smoke test fails" → rollback to v1 in 10 min
  4. Redeploy v2 with the fix → verify via checklist
  5. Update change ticket with screenshots
- **Then:** Self-test with the 20 Interview Q&A pack
- 🎓 **Milestone 5 — EXPERT LEVEL COMPLETE**

---

## 📊 Order Summary Table

| Order | Phase | Days | Key Skill |
|-------|-------|------|-----------|
| 1️⃣ | Foundation | 1–7 | Topology + wsadmin + lab cell built |
| 2️⃣ | Deployment Fundamentals | 8–14 | Install/update/rollback, both ways |
| 3️⃣ | Deployment-Linked Config | 15–21 | VH, IHS, datasource, classloading |
| 4️⃣ | Advanced Deployment | 22–27 | Automation, CI/CD, blue-green, sync, patching |
| 5️⃣ | Troubleshooting + Capstone | 28–30 | RCA + production release simulation |

---

## 🎯 Milestone Tracker

| Milestone | Day | Achievement |
|-----------|-----|-------------|
| M1 | Day 7 | Full topology built + runbook written |
| M2 | Day 14 | Deploy/update/rollback mastered both ways |
| M3 | Day 21 | All deployment-linked configs mastered |
| M4 | Day 27 | Automation + advanced deployment done |
| M5 | Day 30 | Capstone passed — EXPERT level 🎓 |

---

## 📌 Daily Rules for Success

1. **Issue Diary:** Write every error + fix — this becomes your interview war stories
2. **Never copy-paste commands** — type them, break them, fix them
3. **Every Friday:** Explain the week's topics out loud as if teaching a junior
4. **Missed a day?** Don't compress — extend the plan. Consistency > speed

---

⏭️ **Next:** After Day 30, say **"Done"** to receive:
- 20 Interview Q&A (10-yr level) — Deployment topic
- 5 "Recent Issues You Faced" answers
- 20 Real Banking Scenario-Based Questions
- 20 Real Banking Troubleshooting-Based Questions
- 5 Outage Scenarios / War Stories
- 5 Behavioral/Leadership Questions
- 5 Architecture/Design Questions (Lead level)
