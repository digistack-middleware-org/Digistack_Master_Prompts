# ✅ WebSphere Profiles — Course in Correct Learning Order
**42 Days @ 1 hr/day | Zero → Expert | BankCell01 Environment | WAS ND 8.5.5 / 9.0**

> **Why this order works:** Fundamentals (what a profile IS) → Creation (build one) →
> Internals (what's inside) → Federation (join to a cell ⭐) → Advanced/Production →
> Automation → Capstone + Interview.
> Each phase depends on the previous one — you can't federate (Day 22) a profile you
> can't create (Day 8), and you can't troubleshoot internals (Day 32) without knowing
> the config files (Day 17).

---

## 📊 Progress Tracker

| Order | Phase | Days | Status |
|-------|-------|------|--------|
| 1️⃣ | Fundamentals | 1–7 | ⬜ |
| 2️⃣ | Creating Profiles (Hands-on) | 8–14 | ⬜ |
| 3️⃣ | Internals | 15–21 | ⬜ |
| 4️⃣ | ⭐ Federation | 22–28 | ⬜ |
| 5️⃣ | Advanced + Production | 29–35 | ⬜ |
| 6️⃣ | Scripting & Automation | 36–38 | ⬜ |
| 7️⃣ | Capstone + Interview Pack | 39–42 | ⬜ |

---

## 🟢 PHASE 1: FUNDAMENTALS — Week 1 (Days 1–7)

### Day 1 — What is WAS? Base vs ND
- [ ] What WAS is, why banks use it
- [ ] WAS Base vs WAS ND — difference, cell concept intro
- ❓ "Explain Base vs ND to a non-tech manager"

### Day 2 — What is a Profile?
- [ ] Profile definition (runtime environment, not the binaries)
- [ ] Binaries vs profiles — the separation concept
- ❓ "Can one installation have multiple profiles?"

### Day 3 — Profile Contents & Directory Structure
- [ ] What a profile contains: config, logs, installedApps, temp, tranlog
- [ ] Directory walkthrough: `<ProfileHome>/config`, `/logs`, `/bin`
- 📝 Draw the profile directory tree from memory

### Day 4 — Profile Types + Default Profile
- [ ] Types: AppSrv, Dmgr, Custom, Managed, Secure Proxy, Job Manager, Admin Agent, Blank
- [ ] Default profile meaning + impact on commands (omit `-profileName`)
- ❓ "Difference between Custom and AppSrv profile?"

### Day 5 — Single vs Multiple Profiles + Naming Standards
- [ ] Why banks use multiple profiles per host (isolation, patching, ports)
- [ ] Bank naming: `AppSrv01`, `Dmgr01`, `Custom01`, `BankNode01`
- 🏦 BankCell01: naming convention for a payments cell

### Day 6 — ✅ Revision + Mini-Quiz
- [ ] Rapid-fire: 15 questions on Days 1–5
- [ ] Fix weak areas identified

### Day 7 — LAB: Install WAS
- [ ] Install WAS 8.5.5 / 9.0 ND (IM or silent install)
- [ ] Verify: `versionInfo.sh`
- ☑️ Week 1 complete

---

## 🟡 PHASE 2: CREATING PROFILES (HANDS-ON HEAVY) — Week 2 (Days 8–14)

### Day 8 — PMT Walkthrough (Part 1)
- [ ] Profile Management Tool: launch, Typical vs Advanced wizard
- [ ] Create AppSrv profile via PMT — step by step

### Day 9 — PMT Walkthrough (Part 2)
- [ ] Create Dmgr profile via PMT
- [ ] Port assignment options, "Launch First Steps" console
- ❓ "Typical vs Advanced profile creation?"

### Day 10 — `manageprofiles.sh` (Part 1)
- [ ] Syntax: `-create -templatePath -profileName -profilePath -hostName ...`
- [ ] Create AppSrv profile via CLI (match Day 8's PMT result)

### Day 11 — `manageprofiles.sh` (Part 2)
- [ ] Dmgr creation via CLI, `-enableAdminSecurity true`
- [ ] Key flags: `-nodeName`, `-cellName`, `-adminUserName`
- 📝 Practice: Create both profiles CLI-only, no PMT

### Day 12 — Templates, Ports, Port Conflicts
- [ ] Templates location (`profileTemplates/`), default vs starting port
- [ ] Port conflict detection & resolution, portdef files
- ❓ "Two profiles on one host — how are ports managed?"

### Day 13 — Profile Management Commands
- [ ] `-listProfiles`, `-delete`, `-backupProfile`, `-restoreProfile`
- [ ] `-augment` / `-unaugment` — when banks use it
- 🏦 BankCell01: safely delete + back up a profile

### Day 14 — Silent Creation + LAB
- [ ] Response file creation (`response.file`), silent profile creation
- 🖥️ LAB: Build AppSrv01 + Dmgr01 silently, verify with listProfiles
- ☑️ Week 2 complete

---

## 🟡 PHASE 3: INTERNALS — Week 3 (Days 15–21)

### Day 15 — Profile Creation Internals (Part 1)
- [ ] What actually happens during creation (folders, config copy, ports, scripts)
- [ ] Why internals matter for troubleshooting

### Day 16 — profileRegistry.xml
- [ ] Location, structure, what's registered per profile
- [ ] Corruption scenarios + bank recovery
- ❓ "Profile deleted but still listed — why? Fix?"

### Day 17 — Config Files Deep-Dive
- [ ] `serverindex.xml` — ports per server (every WAS admin must know this)
- [ ] `security.xml` — admin security, LTPA, roles

### Day 18 — Node vs Profile vs Server
- [ ] Conceptual separation + relationships
- [ ] `-profileName` usage everywhere (startServer, stopServer, wsadmin)
- ❓ "One binaries install, 3 profiles — how do you start only one?"

### Day 19 — Env Scripts & Logs
- [ ] `setupCmdLine.sh`, wsadmin env, profile scripts
- [ ] Logs: SystemOut.log, SystemErr.log, FFDC, startserver.log — locations & use

### Day 20 — wsadmin + Connectivity
- [ ] `wsadmin -profileName`; SOAP (8879) vs RMI (8880) — difference, when each
- ❓ "wsadmin fails with SOAPException — your first 3 checks?"

### Day 21 — ✅ 🏦 Banking Drill + Quiz
- [ ] Drill: "Find IB logs for Oct 5 — filesystem only, no console"
- [ ] 15 rapid-fire quiz questions
- ☑️ Week 3 complete

---

## 🔴 PHASE 4: ⭐ FEDERATION — MOST IMPORTANT WEEK (Days 22–28)

### Day 22 — Federation Theory
- [ ] What federation does: node files → DMGR, cert/trust exchange, node agent
- [ ] Cell/node/DMGR relationship diagram
- 📝 Draw the federation flow from memory

### Day 23 — `addNode.sh` Full Parameters
- [ ] `-host, -port, -username, -password, -apps, -excludeapps, -asexistingnode, -corbaloginport, -profileName`
- [ ] SOAP 8879 — why it matters
- 🎯 Trap: Federation via Admin Console does NOT exist — CLI only!

### Day 24 — addNode Internals + Security-Enabled Federation
- [ ] Behind the scenes: config copy, trust exchange, node registration
- [ ] Federating with admin security ON — credentials, trust stores

### Day 25 — removeNode + Node Agent Internals
- [ ] `removeNode.sh` — what stays behind, orphaned config cleanup
- [ ] Node agent role post-federation, augment options

### Day 26 — Cloning, Re-federating, DMZ Banking Setup
- [ ] Cloning nodes; re-federation with `-asexistingnode` (keeps node name)
- 🏦 Cross-host / DMZ banking topology patterns

### Day 27 — 🖥️ LAB: Build BankCell01 (Part 1)
- [ ] Create Dmgr01, start DMGR, verify console (ports 9043/9044)

### Day 28 — 🖥️ LAB: Build BankCell01 (Part 2)
- [ ] Create Custom01, federate to Dmgr01, verify node in console
- [ ] Start nodeagent, confirm status = synchronized
- ☑️ Week 4 complete — **BankCell01 is live**

---

## 🟠 PHASE 5: ADVANCED + PRODUCTION — Week 5 (Days 29–35)

### Day 29 — Admin Agent + Job Manager
- [ ] Concept, differences, use cases
- ❓ "When would a bank use Flexible Management over one big cell?"

### Day 30 — Multi-Profile Mgmt + Migration
- [ ] Managing many profiles per host (naming, scripts, ports)
- [ ] Migration 8.5.5 → 9.0 profile migration approach

### Day 31 — Backup/Recovery, DR Move, Cell Design
- [ ] `backupConfig` / `restoreConfig`, profile backups
- [ ] DR strategy: moving profiles, bank cell design principles

### Day 32 — Troubleshooting Creation & Corruption
- [ ] Profile creation failures (logs, common errors), corrupted profile recovery
- [ ] DMGR rebuild trick: recreate + restore config
- ❓ "Dmgr profile corrupted — walk me through recovery"

### Day 33 — Production Scenarios 1–5
- [ ] Bank-style incident/change scenarios, structured answers

### Day 34 — Production Scenarios 6–10
- [ ] Answer out loud, then compare model answers

### Day 35 — ✅ 🏦 Interview Walkthrough
- [ ] "Build a new managed node in production" — full end-to-end
- [ ] (Change ticket → addNode → verify → document)
- ☑️ Week 5 complete

---

## 🔵 PHASE 6: SCRIPTING & AUTOMATION — Week 6 (Days 36–38)

### Day 36 — Scripted Start/Stop + Jython Basics
- [ ] startServer/stopServer scripting, wsadmin Jython fundamentals
- [ ] addNode scripting

### Day 37 — Full Automation Script
- [ ] Silent build + federate + start — one production-grade script
- 📝 Write and test it in your lab

### Day 38 — Health-Check + CI/CD Awareness
- [ ] Health-check script (processes, ports, logs, sync status)
- [ ] Ansible/Jenkins awareness — how banks automate WAS at scale
- ☑️ Week 6 complete

---

## 🟣 PHASE 7: CAPSTONE + INTERVIEW PACK — Week 7 (Days 39–42)

### Day 39 — 🎓 Capstone (Part 1)
- [ ] Solo build: install → Dmgr01 → 2 Custom profiles → federate
- [ ] Create cluster, deploy test app

### Day 40 — 🎓 Capstone (Part 2)
- [ ] Backup cell, DR restore, document everything as a bank runbook
- ☑️ Capstone complete — you can build BankCell01 blindfolded

### Day 41 — Interview Pack (Part 1)
- [ ] 20 Core Q&A + 5 "recent issues" STAR answers + 20 scenario questions

### Day 42 — Interview Pack (Part 2)
- [ ] 20 troubleshooting Qs + 5 war stories + 5 behavioral + 5 architecture/design Qs
- 🎯 Final self-assessment + mock interview

---

## 📊 Order Summary Table

| Order | Phase | Days | Key Skill |
|-------|-------|------|-----------|
| 1️⃣ | Fundamentals | 1–7 | Concept clarity |
| 2️⃣ | Creating Profiles | 8–14 | Build profiles (PMT + CLI + silent) |
| 3️⃣ | Internals | 15–21 | Config files, logs, wsadmin |
| 4️⃣ | ⭐ Federation | 22–28 | addNode / removeNode / cell build |
| 5️⃣ | Advanced + Production | 29–35 | Recovery, migration, scenarios |
| 6️⃣ | Scripting & Automation | 36–38 | Production-grade scripts |
| 7️⃣ | Capstone + Interview | 39–42 | Job-ready |

---

## 📌 The Daily Ritual (every topic, every day)

1. 📖 **Theory** — simple English, zero assumptions
2. 🏦 **BankCell01 scenario** — real banking context
3. ⚙️ **Console steps** + 🖥️ **CLI/wsadmin steps** — hands-on
4. ❓ **10-yr level interview Q&A**
5. ☑️ **Checklist passed → next topic**

---

## ✅ PER-TOPIC COMPLETION RULE

- [ ] Explanation understood
- [ ] Hands-on done (or lab walkthrough)
- [ ] Banking scenario walked through
- [ ] Interview question answered (10-yr level)
- [ ] → Next topic

---

## ⚠️ Rules of the Course

1. Miss a day → don't skip content, shift everything by 1
2. Revision days (6, 21, 35) are sacred
3. Lab environment mandatory — doing = 90% retention vs reading = 40%
4. Every phase builds on the previous — follow the order strictly

---

**Planning is done. No more preparation — time to learn. 🚀**

**▶ Reply "Start Topic 1" to begin:**
**Topic 1 — What is WebSphere? (WAS Base vs ND, banker-friendly)**
