# 🎓 WebSphere Mastery Course — MERGED EDITION

**Profiles + Federation + Sync + Production + Interview — One Unified Path**
**42 Days @ 1 hr/day | Zero → Expert | BankCell01 Environment | WAS ND 8.5.5 / 9.0 | Banking-Ready**

> **Why this merged order works:** Both courses overlapped heavily on fundamentals, profiles, federation, and interview prep. This merged version removes duplication and blends the best of both — Profile mastery (Course 1) + Federation/Sync depth (Course 2) — into ONE journey:
>
> **Topology → Profiles → Build a Cell ⭐ → Internals → Federation → Synchronization → Production/Architect → Automation → Capstone + Interview**
>
> Nothing is skipped — overlapping topics (addNode, internals, logs, wsadmin, removeNode) appear **ONCE**, at the right depth, in the right week.

---

## 📊 Progress Tracker

| Order | Phase | Days | Status |
|-------|-------|------|--------|
| 1️⃣ | WAS + Cell Foundations | 1–7 | ⬜ |
| 2️⃣ | Creating Profiles (Hands-on) | 8–14 | ⬜ |
| 3️⃣ | ⭐ Federation — Build BankCell01 | 15–21 | ⬜ |
| 4️⃣ | ⭐ Synchronization | 22–28 | ⬜ |
| 5️⃣ | Internals Deep-Dive + Advanced | 29–35 | ⬜ |
| 6️⃣ | Automation + Capstone | 36–39 | ⬜ |
| 7️⃣ | Interview Pack + Mocks | 40–42 | ⬜ |

---

## 🟢 PHASE 1: WAS + CELL FOUNDATIONS — Week 1 (Days 1–7)

### Day 1 — What is WAS? Base vs ND + Topology Basics
- What WAS is, why banks use it
- WAS Base vs WAS ND — cell concept
- Cell, DMGR, Node, Node Agent, App Server — 🏦 "Head Office vs Branch" analogy
- ❓ "Explain Base vs ND to a non-tech manager"

### Day 2 — What is a Profile?
- Profile definition (runtime environment, not binaries)
- Binaries vs profiles — separation concept
- ❓ "Can one installation have multiple profiles?"

### Day 3 — Profile Types + Directory Structure
- Types: AppSrv, Dmgr, Custom, Managed, Secure Proxy, Job Manager, Admin Agent, Blank
- Default profile meaning + impact on commands
- Directory walkthrough: `config`, `/logs`, `/bin`, `installedApps`, `temp`, `tranlog`
- ❓ "Difference between Custom and AppSrv profile?"
- 📝 Draw the profile directory tree from memory

### Day 4 — How Config Really Works
- `server.xml`, `resources.xml`, `security.xml`, `variables.xml`, `serverindex.xml`
- Config vs runtime difference
- ❓ "Where do ports live on disk?"

### Day 5 — Single vs Multiple Profiles + Naming Standards
- Why banks use multiple profiles per host (isolation, patching, ports)
- Bank naming: `AppSrv01`, `Dmgr01`, `Custom01`, `BankNode01`
- 🏦 BankCell01: naming convention for a payments cell

### Day 6 — ✅ Revision + Mini-Quiz
- 15 rapid-fire questions on Days 1–5
- Self-test: draw full cell topology from memory

### Day 7 — LAB: Install WAS + Tooling Setup
- Install WAS 8.5.5 / 9.0 ND (IM or silent install)
- Verify: `versionInfo.sh`
- wsadmin launch + 10 hands-on commands (`AdminConfig`, `AdminControl`, `AdminTask`)
- ☑️ Week 1 complete

---

## 🟡 PHASE 2: CREATING PROFILES (HANDS-ON HEAVY) — Week 2 (Days 8–14)

### Day 8 — PMT Walkthrough
- Profile Management Tool: Typical vs Advanced wizard
- Create AppSrv + Dmgr profiles via PMT
- Port assignment options, "Launch First Steps" console
- ❓ "Typical vs Advanced profile creation?"

### Day 9 — manageprofiles.sh (CLI)
- Syntax: `-create -templatePath -profileName -profilePath -hostName ...`
- Dmgr creation via CLI, `-enableAdminSecurity true`
- Key flags: `-nodeName`, `-cellName`, `-adminUserName`
- 📝 Recreate Day 8's profiles CLI-only, no PMT

### Day 10 — Templates, Ports, Port Conflicts
- Templates location (`profileTemplates/`), starting ports, portdef files
- Port conflict detection & resolution
- ❓ "Two profiles on one host — how are ports managed?"

### Day 11 — Profile Management Commands
- `-listProfiles`, `-delete`, `-backupProfile`, `-restoreProfile`
- `-augment` / `-unaugment` — when banks use it
- 🏦 BankCell01: safely back up + delete a profile

### Day 12 — Silent Creation
- Response file creation (`response.file`), silent profile creation
- 🖥️ LAB: Build AppSrv01 + Dmgr01 silently, verify with `-listProfiles`

### Day 13 — Admin Console Tour + Connectivity & Logs
- System Administration → Nodes, Node Agents, Cell pages
- SOAP 8879 vs RMI 8880; DMGR↔Node connectivity
- Logs: `SystemOut.log`, `SystemErr.log`, FFDC, nodeagent log locations
- ❓ "wsadmin fails with SOAPException — first 3 checks?"

### Day 14 — ✅ Revision + Break/Fix Lab
- Create/delete/verify 2–3 profiles end-to-end
- 15-question rapid-fire quiz
- ☑️ Week 2 complete — profiles ready for federation

---

## 🔴 PHASE 3: ⭐ FEDERATION — BUILD BankCell01 — Week 3 (Days 15–21)

### Day 15 — Federation Theory
- What federation does: config copy to DMGR, cert/trust exchange, node agent creation
- Cell/node/DMGR relationship diagram
- 📝 Draw the federation flow from memory

### Day 16 — addNode.sh Deep Dive
- All flags: `-host`, `-port`, `-username`, `-password`, `-includeapps`, `-excludeapps`, `-asexistingnode`, `-corbaloginport`, `-profileName`
- SOAP 8879 — why it matters
- 🎯 **Trap:** Federation via Admin Console does **NOT** exist — CLI only!

### Day 17 — addNode Internals + Security-Enabled Federation
- Behind the scenes: config copy, trust exchange, node registration
- Federating with admin security ON — credentials, trust stores

### Day 18 — 🖥️ LAB: Build BankCell01
- Start DMGR, verify console (ports 9043/9044)
- `addNode.sh dmgrhost 8879` on Custom01 — verify node in console
- Start nodeagent, confirm status = synchronized
- ☑️ BankCell01 is live

### Day 19 — removeNode.sh + Node Agent Internals
- What stays behind, orphaned config cleanup
- Re-federation with `-asexistingnode` (keeps node name)

### Day 20 — Federation Failures + 🏦 Bank Change Window
- ADMU0111E, SOAP exceptions, cert errors, firewall ports, reading `addNode.log`
- 🏦 Weekend change window for "Loan App" node — ticket, pre-checks, rollback plan

### Day 21 — ✅ Revision + Practice
- Federate/unfederate 2–3 times in lab
- Write your own federation runbook
- ☑️ Week 3 complete

---

## 🟠 PHASE 4: ⭐ SYNCHRONIZATION — Week 4 (Days 22–28)

### Day 22 — Sync Theory
- DMGR = master config; node agent file sync service
- Pull vs push; sync intervals

### Day 23 — 🖥️ Hands-On: Sync
- Admin Console Full Resynchronize
- `syncNode.sh` + Jython script to sync all nodes

### Day 24 — "Changes Saved ≠ Changes Active"
- The #1 junior mistake
- When restart is needed vs not
- Delta sync vs full resync

### Day 25 — 🏦 Real Bank Scenario
- JVM heap change on Payments cluster — DMGR → sync → restart
- Change-freeze discipline

### Day 26 — Sync Troubleshooting
- "Not Synchronized" status — clock skew, disk full, permissions, stale nodeagent
- Config snapshots & comparison between nodes

### Day 27 — Backup/Recovery + DMGR HA
- `backupConfig` / `restoreConfig`
- What happens when DMGR is down (apps keep running, sync fails)
- DMGR backup/restore strategy

### Day 28 — ✅ Revision + Break/Fix Lab
- Deliberately break sync (stop nodeagent, wrong clock) and fix it
- 15-question quiz
- ☑️ Week 4 complete

---

## 🟣 PHASE 5: INTERNALS DEEP-DIVE + ADVANCED/PRODUCTION — Week 5 (Days 29–35)

### Day 29 — Config Files Deep-Dive + Profile Internals
- What happens during profile creation (folders, config copy, ports)
- `serverindex.xml` (ports!), `security.xml` (LTPA, roles), `profileRegistry.xml`
- ❓ "Profile deleted but still listed — why? Fix?"

### Day 30 — Node vs Profile vs Server + Env Scripts
- Conceptual separation + relationships
- `setupCmdLine.sh`, wsadmin env, profile scripts
- ❓ "One binaries install, 3 profiles — how do you start only one?"
- 🏦 Drill: "Find IB logs for Oct 5 — filesystem only, no console"

### Day 31 — Security + Troubleshooting Corruption
- Admin roles (Operator / Configurator / Administrator); SSL between DMGR & nodes
- Profile creation failures, corrupted profile recovery
- DMGR rebuild trick: recreate + restore config
- ❓ "Dmgr profile corrupted — walk me through recovery"

### Day 32 — Multi-Profile Mgmt + Migration + Cell Design
- Managing many profiles per host; 8.5.5 → 9.0 profile migration
- DR strategy: moving profiles, bank cell design principles, DC1/DC2 active-active
- Config drift governance & SOX audit needs

### Day 33 — Admin Agent, Job Manager + Large-Scale Cells
- Flexible Management — when a bank outgrows one cell
- 300–500 nodes: sync interval tuning, sync performance strategy
- ❓ "When would a bank use Flexible Management over one big cell?"

### Day 34 — Production Scenarios 1–5
- Bank-style incident/change scenarios, structured answers

### Day 35 — Production Scenarios 6–10 + Interview Walkthrough
- Answer out loud, then compare model answers
- "Build a new managed node in production" — end-to-end (Change ticket → addNode → verify → document)
- ☑️ Week 5 complete

---

## 🔵 PHASE 6: AUTOMATION + CAPSTONE — Days 36–39

### Day 36 — Scripting: Start/Stop + Jython Basics
- `startServer`/`stopServer` scripting, Jython fundamentals
- addNode/syncNode scripting

### Day 37 — Full Automation Script
- Silent build + federate + sync + start — one production-grade script
- Health-check script (processes, ports, logs, sync status)
- Ansible/Jenkins awareness — how banks automate WAS at scale
- 📝 Write and test it in your lab

### Day 38 — 🎓 Capstone (Part 1)
- Solo build: install → Dmgr01 → 2 Custom profiles → federate
- Create cluster, deploy test app, verify sync

### Day 39 — 🎓 Capstone (Part 2)
- Backup cell, DR restore, document everything as a bank runbook
- ☑️ Capstone complete — you can build BankCell01 blindfolded

---

## 🟣 PHASE 7: INTERVIEW PACK + MOCKS — Days 40–42

### Day 40 — Interview Pack (Part 1)
- 20 Core Q&A + 5 "recent issues" STAR answers
- 20 banking scenario questions

### Day 41 — Interview Pack (Part 2)
- 20 troubleshooting Qs + 5 war stories (Issue → Impact → Diagnosis → Fix → Lesson)
- 5 behavioral +5 architecture/design (lead level: cell design, sync for 300 nodes, DR)

### Day 42 — 🎯 Mock Interviews + Final Revision
- Mock #1 & #2 — self-timed, record yourself, fix weak areas
- Rapid-fire: all commands (`addNode`, `syncNode`, `removeNode`, `manageprofiles.sh`), ports, logs
- Build a one-page cheat sheet — **this becomes your interview asset**
- 🎯 Final self-assessment — **You're interview-ready.**
- ☑️ Course complete 🚀

---

## 📊 Order Summary Table

| Order | Phase | Days | Key Skill |
|-------|-------|------|-----------|
| 1️⃣ | WAS + Cell Foundations | 1–7 | Topology + concept clarity |
| 2️⃣ | Creating Profiles | 8–14 | Build profiles (PMT + CLI + silent) |
| 3️⃣ | ⭐ Federation | 15–21 | addNode / removeNode / BankCell01 live |
| 4️⃣ | ⭐ Synchronization | 22–28 | Sync theory + break/fix skills |
| 5️⃣ | Internals

