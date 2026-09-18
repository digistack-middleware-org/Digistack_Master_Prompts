# 📅 Day-Wise Plan — MODULE-WISE (WebSphere DEPLOYMENT Course)

**11 Modules + Final Mastery | ~84 Days | ~2 hours/day | 6 study days + 1 revision day per**

> Each module ends with a Revision Day: self-test + interview Qs out loud + war story rewrite

---

## 📦 MODULE 1: Application Packaging (EAR/WAR/JAR) — 7 Days

| Day | Topic | Hands-On Task |
|-----|-------|---------------|
| 1 | What is JAR vs WAR vs EAR; why banks use EAR | Draw all 3 structures on paper |
| 2 | Inside a WAR: `WEB-INF/web.xml`, classes, lib, `ibm-web-bnd.xml`, `ibm-web-ext.xml` | Open a sample WAR with 7-Zip, explore |
| 3 | Inside an EAR: `META-INF/application.xml`, `deployment.xml`, `ibmconfig/` | Open a sample EAR, locate descriptors |
| 4 | Context root — 3 places it can be defined & which one wins | Change context root in application.xml, verify |
| 5 | Shared libraries vs bundled JARs | Create a shared library, note difference from bundled JAR |
| 6 | Java EE vs WAS version matrix (8.5.5 = EE6, 9 = EE7) | Make your compatibility cheat-sheet |
| 7 | 🔄 REVISION — LoanPortal.ear scenario + interview Qs | Answer out loud: WAR vs EAR, context root, EE levels |

---

## 📦 MODULE 2: Console Deployment — 7 Days

| Day | Topic | Hands-On Task |
|-----|-------|---------------|
| 8 | Fast Path vs Detailed — when to use which | Note the differences in your handbook |
| 9 | Install steps 1–3: select file, path choice, MapModulesToServers | Lab: Fast Path install of a sample EAR |
| 10 | Install steps 4–9: VH mapping, context root, shared libs, JSP pre-compile, save, sync | Lab: Detailed install, map to cluster |
| 11 | Shared library scope: server vs node vs cluster vs cell | Lab: create cluster-scoped shared library |
| 12 | What the console writes under the hood (config repository) | Browse `config/cells/.../applications/` |
| 13 | CardOffers.ear scenario — admin WAR vs public WAR to 2 clusters | Draw the mapping diagram from memory |
| 14 | 🔄 REVISION — full console install, no notes | Time yourself: under 15 minutes |

---

## 📦 MODULE 3: wsadmin Deployment — 7 Days

| Day | Topic | Hands-On Task |
|-----|-------|---------------|
| 15 | wsadmin 4 objects: AdminApp, AdminConfig, AdminControl, AdminTask | Run `wsadmin.sh`, try `AdminApp.list()` |
| 16 | `AdminApp.install()` core options: `-appname`, `-cluster`, `-contextroot`, `-virtualhost` | Install an EAR with ONE command |
| 17 | Advanced: `-MapModulesToServers`, `-MapWebModToVH`, `-MapResRefToEJB` | Install multi-module EAR to 2 clusters |
| 18 | `AdminApp.installInteractive()` — safe option discovery | Run interactive mode, log every question |
| 19 | Bindings XML files — bank "standardized bindings" pattern | Install using your own bindings file |
| 20 | `isAppReady()`, `AdminApp.edit()`, `AdminConfig.save()` — cost of NOT saving | Lab: install without save → see what's lost |
| 21 | 🔄 REVISION — write deployment script from memory | install → isAppReady → sync → verify |

---

## 📦 MODULE 4: Class Loading — 7 Days

| Day | Topic | Hands-On Task |
|-----|-------|---------------|
| 22 | Classloader hierarchy: Application → WAR → WAS extensions → JVM | Draw hierarchy diagram |
| 23 | PARENT_FIRST vs PARENT_LAST (EAR vs WAR level) | Deploy app with newer bundled JAR → see conflict |
| 24 | Shared library `isolated=true` vs referenced | Test both settings, compare |
| 25 | Single vs multiple classloader policy | Flip policy on sample app, note behavior |
| 26 | Errors: `ClassNotFoundException` vs `NoClassDefFoundError` vs `LinkageError` | Flashcards: error → cause → fix |
| 27 | PaymentsUI.ear scenario — NoSuchMethodError → PARENT_LAST fix | Write a 5-line incident ticket |
| 28 | 🔄 REVISION + interview drill | "NoClassDefFoundError after deploy — walk me through" |

---

## 📦 MODULE 5: Update, Rollout & Rollback — 7 Days

| Day | Topic | Hands-On Task |
|-----|-------|---------------|
| 29 | Install new vs Update existing; full vs partial update | Update a single JSP in a running app |
| 30 | Metadata-only updates; update vs reinstall — what's preserved | Test both; note session/binding behavior |
| 31 | **Rollout Update** (`rolloutAppUpdate`) — member-by-member, session affinity | Lab: rollout on your 2-member cluster |
| 32 | `AdminApp.export`/import — backup before every change | Bad update → roll back using your export |
| 33 | Application versioning — why banks track it | Draft a version register |
| 34 | Mobile banking 2AM release — wrong way (outage) vs right way (rollout) | Write the full release runbook |
| 35 | 🔄 REVISION + mock interview | Record 3-min answer: "zero-downtime deployment" |

---

## 📦 MODULE 6: Virtual Hosts — 7 Days

| Day | Topic | Hands-On Task |
|-----|-------|---------------|
| 36 | What is a VH; default_host vs custom | Explore VHs in console |
| 37 | Host aliases — port matching logic | Add/remove aliases, observe effect |
| 38 | The classic 404: alias doesn't cover incoming port | Break it deliberately, then fix it |
| 39 | VH + IHS + plugin relationship | Read plugin-cfg.xml, find your VH |
| 40 | Map modules to VH via console AND wsadmin | Do it both ways |
| 41 | LoanDocs.ear 404 scenario | Write the troubleshooting runbook |
| 42 | 🔄 REVISION + self-test | From memory: "404 after deploy — checklist?" |

---

## 📦 MODULE 7: Deployment Targets & Multi-Cluster — 7 Days

| Day | Topic | Hands-On Task |
|-----|-------|---------------|
| 43 | Server vs cluster deployment — deployment.xml differences | Compare both files side by side |
| 44 | One EAR, modules to DIFFERENT clusters | Lab: split modules across 2 clusters |
| 45 | Vertical vs horizontal members — deployment view | Note when banks use each |
| 46 | Config scope & cluster-wide settings | Change cluster-scope setting, check members |
| 47 | Node sync — verifying it REALLY happened | Compare config timestamps node vs Dmgr |
| 48 | Corp banking scenario — DMZ + internal clusters, 2 firewalls | Draw architecture from memory |
| 49 | 🔄 REVISION + mock interview | "Deploy one app to two clusters" |

---

## 📦 MODULE 8: Resource Binding — 7 Days

| Day | Topic | Hands-On Task |
|-----|-------|---------------|
| 50 | Resource references: `res-ref` in web.xml vs server JNDI | Trace datasource ref in sample app |
| 51 | `MapResRefToEJB` full walkthrough | Deploy with datasource mapping |
| 52 | Env-entries, `MapResEnvRefToRes`, JPA datasource mapping | Set an env-entry during install |
| 53 | JMS connection factory & destination mapping | Map a queue CF during install |
| 54 | Pre-provided binding files — "bindings package" pattern | Build a bindings package |
| 55 | NameNotFoundException scenario — UAT vs Prod JNDI mismatch | Write your JNDI registry standard |
| 56 | 🔄 REVISION — Stage 3 mini-exam | End-to-end deploy: correct VH + targets + bindings |

---

## 📦 MODULE 9: Deployment Automation & CI/CD — 7 Days

| Day | Topic | Hands-On Task |
|-----|-------|---------------|
| 57 | Where apps live: installedApps vs config repository | Explore both directories |
| 58 | Binary vs loose configuration | Test "use binary configuration" setting |
| 59 | wsadmin Ant tasks | Simple Ant file calling wsadmin |
| 60 | CI/CD concept: Jenkins → wsadmin → smoke test | Draw your pipeline on paper |
| 61 | Properties-driven promotion: same script, 3 environments | Create env-specific properties files |
| 62 | Deployment validation checklist (pre-checks, smoke URL, plugin, logs) | Write YOUR checklist — interview gold |
| 63 | 🔄 REVISION — full automation | One command: install → verify → sync |

---

## 📦 MODULE 10: Failed Deployments & Troubleshooting — 7 Days

| Day | Topic | Hands-On Task |
|-----|-------|---------------|
| 64 | Reading failures: ADMA errors, EARValidator, descriptor errors | Deploy a broken EAR, read the error |
| 65 | Failure patterns: duplicate context root, JNDI conflict, out-of-sync | Create each failure once — recognize forever |
| 66 | App stuck in Starting/Stopping: isAppReady, force stop | Simulate and recover |
| 67 | FFDC, SystemOut, amj logs | Read each log type in lab |
| 68 | Key insight: FAILED install leaves running app untouched | Prove it: bad install while app runs |
| 69 | 2AM prod failure scenario + clean rollback procedure | Write the incident timeline (war story) |
| 70 | 🔄 REVISION + mock interview | "Deployment failed mid-way — first action?" |

---

## 📦 MODULE 11: Lifecycle Governance (Architect Level) — 7 Days

| Day | Topic | Hands-On Task |
|-----|-------|---------------|
| 71 | Change management: CAB, change windows, freeze periods | Draft a change request |
| 72 | Registries: context root, JNDI, VH naming standards | Create your registry template |
| 73 | Environment parity — why "works in UAT" fails in prod | List 8 Dev/UAT/Prod differences from memory |
| 74 | Runbooks, peer reviews, ownership matrix | Write a full runbook for one app |
| 75 | Config drift detection; backupConfig history | Take backupConfig, note comparison method |
| 76 | Clean decommissioning: plugin, VH aliases, shared libs, dirs | Fully remove an app, verify nothing left |
| 77 | 🔄 REVISION — year-end freeze scenario | Write governance approach in 10 bullets |

---

## 📦 FINAL MASTERY WEEK — 7 Days (Days 78–84)

| Day | Activity |
|-----|----------|
| 78 | Full drill: fresh EAR → console deploy → wsadmin deploy → rollout → rollback (timed) |
| 79 | Failure drill: create 5 failures yourself, fix each |
| 80 | Rewrite ALL 11 banking scenarios as first-person war stories |
| 81 | Mock interview: Module 1–6 interview Qs out loud |
| 82 | Mock interview: Module 7–11 interview Qs out loud |
| 83 | Update mastery checklist; fix weak modules |
| 84 | 🎉 FINAL TEST: end-to-end deploy with automation + governance + rollback, narrated like an expert |

---

## ⭐ Daily Routine (Every Day — 2 Hours)

| Time | Activity |
|------|----------|
| 0–15 min | Revise previous day's notes |
| 15–75 min | Today's theory + banking scenario |
| 75–110 min | Hands-on lab |
| 110–120 min | Write: "What I learned + 1 interview line" |

---

## 📊 Module Tracker (Copy This)

| Module | Topic | Days | Done? | Confidence (1–5) |
|--------|-------|------|-------|------------------|
| 1 | Packaging | 1–7 | ☐ | |
| 2 | Console Deploy | 8–14 | ☐ | |
| 3 | wsadmin Deploy | 15–21 | ☐ | |
| 4 | Class Loading | 22–28 | ☐ | |
| 5 | Update/Rollout | 29–35 | ☐ | |
| 6 | Virtual Hosts | 36–42 | ☐ | |
| 7 | Multi-Cluster | 43–49 | ☐ | |
| 8 | Resource Binding | 50–56 | ☐ | |
| 9 | Automation | 57–63 | ☐ | |
| 10 | Troubleshooting | 64–70 | ☐ | |
| 11 | Governance | 71–77 | ☐ | |
| 12 | Final Mastery | 78–84 | ☐ | |

---

> **Rule:** Confidence must be ≥ 4 before moving to the next module. Weak module = one extra revision day.
