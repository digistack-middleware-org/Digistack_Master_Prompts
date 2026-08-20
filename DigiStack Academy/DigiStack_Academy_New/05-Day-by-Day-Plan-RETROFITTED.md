# WAS Senior Admin — Full Day-by-Day Plan (Retrofitted with Gap-Review Fixes) — Day 0, Day 1 ... Day 389

Generated from `05-Day-by-Day-Plan-RENUMBERED.md` + the recommendations in `06-Syllabus-Review-Against-Master-Prompt.md`. **No days were added, removed, or reordered.** Every change below is a template/framing tag applied to an existing day, per Section 14 of the review ("this is a mechanical pass, not a redesign").

**Total: still 389 study days (plus Day 0 setup).**

---

## What changed from the previous version, and why

The review against the master prompt found the 389-day structure already sound, but flagged six gaps that don't require new days — only consistent framing applied to what already exists. This file closes all six:

| # | Gap closed | How it shows up below |
|---|---|---|
| 1 | No persistent case-study environment | Every relevant day now anchors to **DigiStack** (topology below) instead of a generic vignette |
| 2 | No named triage tool | Every incident day now requires a **Layer hypothesis (L1–L7)** field before evidence-gathering |
| 3 | Console/CLI/Automation not enforced | Every "Overall Module Lab" day carries a **C/W/A tagging** reminder |
| 4 | Interview prep backloaded | Every incident-closeout day folds in a **15-minute interview checkpoint** |
| 5 | RFC habit taught late (Module 25) | Tagged starting **Day 82** (first real deployment), Module 25 reframed as reinforcement |
| 6 | Documentation portfolio implicit | Key runbook days are tagged as **portfolio artifacts**; full checklist below |

---

## DigiStack — the fixed environment (lock this before Day 1)

```
Browser
  ↓
IHS (Layer 2) — httpd.conf, plugin-cfg.xml, virtual host: digistack.bank.local
  ↓
WebSphere Plugin
  ↓
WAS Cluster: DigiStackCluster (2+ members, cross-node from Arc 3)
  ↓
DigiStack EAR (DigiStackWeb.war + DigiStackEJB.jar + DigiStackBatch.jar)
  ↓
JDBC → PostgreSQL (DigiStackDS, XA)      JMS → IBM MQ (DIGISTACK.NEFT.QUEUE, DLQ)
```

Use these exact names everywhere a lab or incident references "the app," "the datasource," "the queue," or "the cluster." Arc 1–2 build it single-node; Arc 3 clusters and load-tests it; Arc 4 wires JDBC/MQ into it for real; Arc 5 fronts, secures, tunes, and monitors it; Arc 6 audits it. The final capstone (Day 389) injects multi-layer failures into exactly this environment — nothing new is stood up at the end.

## The 7-Layer Triage Model (mandatory first move on every incident)

| Layer | Scope |
|---|---|
| L1 — Network | DNS, IP, port, firewall, routing, TLS |
| L2 — IHS / Load Balancer | httpd.conf, plugin-cfg.xml, virtual host, SSL, routing |
| L3 — WebSphere | DMGR, node agent, server, cluster, sync, config |
| L4 — JVM | CPU, heap, GC, threads, thread/heap dumps |
| L5 — Application | EAR/WAR, ClassLoader, JNDI, sessions, app logs |
| L6 — Database | JDBC, connection pool, connectivity, timeouts, locks |
| L7 — IBM MQ | Queue manager, channel, queue, JMS, MDB, DLQ |

Every incident closeout (Symptom → Impact → Scope → **Layer hypothesis (before evidence) →** Evidence → Test → Root Cause → Fix → Validation → Prevention) uses this table.

## Console / wsadmin / Automation (C/W/A) tagging

For every "Overall Module Lab" day, log in the Progress Log which methods were actually exercised:
- **C** — Admin Console: navigation, values, save/sync/restart, verification
- **W** — wsadmin/CLI: equivalent script, expected output, verification, rollback
- **A** — Automation wrapper (where it fits): Jython → Ansible/Jenkins

## RFC / Change-Ticket template (required lab artifact from Day 82 onward)

`CHANGE → PRE-CHECK → RISK → BACKUP → IMPLEMENTATION → VALIDATION → ROLLBACK → POST-CHECK → DOCUMENTATION`

Applied to every real deployment/config-change lab starting with Module 6. Module 25 (Days 325–337) then teaches *why* this scales to a formal CAB process, reusing a habit already built rather than introducing it cold.

## Interview checkpoint (folded into every incident-closeout day)

15 minutes, drawn from a running question bank you build up over modules: 10 basic / 10 intermediate / 10 senior / 5 troubleshooting scenarios / 3 architecture / 2 design-choice questions. Missed questions get flagged "revisit," same mechanism as the existing Arc Review Flash Drills.

## Documentation portfolio (track as a running checklist in the Progress Log)

- **Architecture diagrams:** cell topology (Arc 1 capstone), DigiStack full-stack diagram (Arc 2), cluster diagram (Arc 3)
- **Runbooks:** install, profile creation, federation, sync, deployment/rollback, cluster ops, JDBC, MQ, IHS, SSL, backup, health-check, DR (tagged inline below at Days 170, 173, 200, 289)
- **Incident reports:** one per completed module's incident batch
- **Automation scripts:** deployment, cluster deploy, patching, health-check report generator, CI/CD pipeline
- **Change records:** one real RFC per major Arc 2+ deployment
- **Resume/interview artifacts:** Days 386–389

## DigiStack observability dashboard (stood up Days 252–257, extended per-module — tagged inline below)

Cluster health → connection-pool metrics → queue depth/DLQ → plugin routing/503s → GC/heap trends → FFDC/error rate, each added as the corresponding module completes, so by Arc 6 the dashboard itself is audit evidence.

---

Use this list to cross-check against `03-Progress-Log.md`. Each entry below = one day's single focus item (per Delivery Preferences: one topic at a time). *Italicized bracketed/parenthetical text is new — the retrofit from the gap review. Original day content is unchanged.*

---
- **Day 0** — Pre-Day-1 Lab Environment Setup Guide — VM spec, OS install (RHEL/CentOS Stream/Rocky), IIM download & install, disk layout for logs/tranlog, smoke test: install WAS 8.5.5 ND, create standalone profile, start server, hit Admin Console login *[Lock DigiStack topology & naming (Section: DigiStack Conventions) before Day 1 — highest-leverage single change, costs zero days]*

## === ARC 1 — Foundation & Architecture ===

- **Day 1** — Module 0: Target Environment Declaration — Topic 0.1 Version Anchor (WAS 8.5.5 ND primary / 9.0 ND secondary / Liberty awareness-only / IBM J9 / RHEL primary, AIX called out) + 0.2 Rule Going Forward + 0.3 start Version Divergence Cheat Sheet
- **Day 2** — Module 1: WAS Strategic Context — Topic 1.1 Product Family
- **Day 3** — Module 1: WAS Strategic Context — Topic 1.2 Fix Pack Strategy
- **Day 4** — Module 1: WAS Strategic Context — Topic 1.3 IIM Internals
- **Day 5** — Module 1: WAS Strategic Context — Topic 1.4 vs Competitors
- **Day 6** — Module 1: WAS Strategic Context — Topic 1.5 Admin's Real Role
- **Day 7** — Module 1: WAS Strategic Context — Topic 1.6 IBM Support Engagement
- **Day 8** — Module 1: WAS Strategic Context — Topic 1.7 Interview Trap Questions
- **Day 9** — Module 1: WAS Strategic Context — Overall Module Lab *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 10** — Module 1: WAS Strategic Context — Production Incidents (Batch 1 of 3+) *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 11** — Module 1: WAS Strategic Context — Production Incidents (Batch 2 of 3+, complete 6+) *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 12** — Supplementary — Foundational IT Literacy: ITIL basics — incident/problem/change management vocabulary used in banking IT
- **Day 13** — Supplementary — Foundational IT Literacy: ServiceNow / Jira / Confluence — how tickets, RFCs, and documentation actually flow in an enterprise
- **Day 14** — Supplementary — Foundational IT Literacy: Java fundamentals for admins — JDK vs JRE vs JVM, Heap, GC (conceptual, pre-WAS)
- **Day 15** — Supplementary — Foundational IT Literacy: File permissions & ownership (chmod, chown, umask) - WAS profile/keystore/log permission failure modes
- **Day 16** — Supplementary — Foundational IT Literacy: Process management (ps -ef, kill -3 for javacore, nohup, background jobs, process trees) - starting/stopping/diagnosing hung JVMs
- **Day 17** — Supplementary — Foundational IT Literacy: Log tailing & searching (tail -f, grep -A/-B, awk, sed basics) - SystemOut.log/FFDC/native_stderr.log triage
- **Day 18** — Supplementary — Foundational IT Literacy: ulimit (nofile, nproc) - number-one cause of "too many open files" incidents under load
- **Day 19** — Supplementary — Foundational IT Literacy: netstat/ss, lsof -i - port conflicts, orphaned listeners, "address already in use"
- **Day 20** — Supplementary — Foundational IT Literacy: df -h, mount points, /proc basics - disk-full-on-log-partition incidents, /proc/<pid>/limits
- **Day 21** — Supplementary — Foundational IT Literacy: top/vmstat/iostat fundamentals - baseline CPU/memory/IO reading before Module 20
- **Day 22** — Module 2: Architecture Internals — Topic 2.1 Cell/Node/Server Internals
- **Day 23** — Module 2: Architecture Internals — Topic 2.2 DMGR Internals
- **Day 24** — Module 2: Architecture Internals — Topic 2.3 Node Agent Internals
- **Day 25** — Module 2: Architecture Internals — Topic 2.4 Config Repository
- **Day 26** — Module 2: Architecture Internals — Topic 2.5 Scope Hierarchy
- **Day 27** — Module 2: Architecture Internals — Topic 2.6 Port Architecture
- **Day 28** — Module 2: Architecture Internals — Topic 2.7 ORB & IIOP
- **Day 29** — Module 2: Architecture Internals — Topic 2.8 Admin Console Internals
- **Day 30** — Module 2: Architecture Internals — Topic 2.9 Process Communication
- **Day 31** — Module 2: Architecture Internals — Topic 2.10 Config Locking
- **Day 32** — Module 2: Architecture Internals — Topic 2.11 MBean Architecture
- **Day 33** — Module 2: Architecture Internals — Topic 2.12 Architecture Design Decisions
- **Day 34** — Module 2: Architecture Internals — Overall Module Lab *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 35** — Module 2: Architecture Internals — Production Incidents (Batch 1 of 3+) *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 36** — Module 2: Architecture Internals — Production Incidents (Batch 2 of 3+, complete 6+) *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 37** — Arc 1 Capstone — Design a cell topology for a stated banking workload; defend every choice *[Explicitly scoped to DigiStack — see Milestone Plan]*
- **Day 38** — Arc 1 Review Flash Drill - 10 rapid-fire spaced-repetition questions covering the arc's highest-stakes recall items (self-scored; misses flagged 'revisit' in Progress Log)

## === ARC 2 — Build & Deploy ===

- **Day 39** — Module 3: Installation — Topic 3.1 Pre-Install Decisions
- **Day 40** — Module 3: Installation — Topic 3.2 OS Hardening
- **Day 41** — Module 3: Installation — Topic 3.3 IIM in Depth
- **Day 42** — Module 3: Installation — Topic 3.4 Silent Install
- **Day 43** — Module 3: Installation — Topic 3.5 Multi-Component Install
- **Day 44** — Module 3: Installation — Topic 3.6 Fix Pack Upgrade
- **Day 45** — Module 3: Installation — Topic 3.7 versionInfo.sh
- **Day 46** — Module 3: Installation — Topic 3.8 Install Rollback
- **Day 47** — Module 3: Installation — Topic 3.9 Install Documentation
- **Day 48** — Module 3: Installation — Topic 3.10 Common Install Failures
- **Day 49** — Module 3: Installation — Overall Module Lab *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 50** — Module 3: Installation — Production Incidents (Batch 1 of 3+) *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 51** — Module 3: Installation — Production Incidents (Batch 2 of 3+, complete 6+) *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 52** — Module 22: Patch & Vulnerability Management — Topic 22.1 CVE Monitoring *(patch the DigiStack DMGR/nodes under governance)*
- **Day 53** — Module 22: Patch & Vulnerability Management — Topic 22.2 Emergency iFix vs Scheduled Fix Pack *(patch the DigiStack DMGR/nodes under governance)*
- **Day 54** — Module 22: Patch & Vulnerability Management — Topic 22.3 Patch Testing Gate *(patch the DigiStack DMGR/nodes under governance)*
- **Day 55** — Module 22: Patch & Vulnerability Management — Topic 22.4 Rollback Decision Criteria *(patch the DigiStack DMGR/nodes under governance)*
- **Day 56** — Module 22: Patch & Vulnerability Management — Overall Module Lab *(patch the DigiStack DMGR/nodes under governance)* *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 57** — Module 22: Patch & Vulnerability Management — Production Incidents (Batch 1 of 3+) *(patch the DigiStack DMGR/nodes under governance)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 58** — Module 22: Patch & Vulnerability Management — Production Incidents (Batch 2 of 3+, complete 6+) *(patch the DigiStack DMGR/nodes under governance)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 59** — Module 4: Standalone Profile — Topic 4.1 Profile Architecture *(build on DigiStack's single-node profile)*
- **Day 60** — Module 4: Standalone Profile — Topic 4.2 Profile Directory Files *(build on DigiStack's single-node profile)*
- **Day 61** — Module 4: Standalone Profile — Topic 4.3 JVM Architecture *(build on DigiStack's single-node profile)*
- **Day 62** — Module 4: Standalone Profile — Topic 4.4 JVM Sizing *(build on DigiStack's single-node profile)*
- **Day 63** — Module 4: Standalone Profile — Topic 4.5 Process Management *(build on DigiStack's single-node profile)*
- **Day 64** — Module 4: Standalone Profile — Topic 4.6 Environment Variables *(build on DigiStack's single-node profile)*
- **Day 65** — Module 4: Standalone Profile — Topic 4.7 Logging Config *(build on DigiStack's single-node profile)*
- **Day 66** — Module 4: Standalone Profile — Topic 4.8 Admin Security Day 1 *(build on DigiStack's single-node profile)*
- **Day 67** — Module 4: Standalone Profile — Overall Module Lab *(build on DigiStack's single-node profile)* *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 68** — Module 4: Standalone Profile — Production Incidents (Batch 1 of 3+) *(build on DigiStack's single-node profile)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 69** — Module 4: Standalone Profile — Production Incidents (Batch 2 of 3+, complete 6+) *(build on DigiStack's single-node profile)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 70** — Module 5: Deployment Concepts / ClassLoader — Topic 5.1 EAR/WAR/JAR Packaging *(DigiStack EAR: DigiStackWeb.war + DigiStackEJB.jar + DigiStackBatch.jar)*
- **Day 71** — Module 5: Deployment Concepts / ClassLoader — Topic 5.2 ClassLoader Deep Dive *(DigiStack EAR: DigiStackWeb.war + DigiStackEJB.jar + DigiStackBatch.jar)*
- **Day 72** — Module 5: Deployment Concepts / ClassLoader — Topic 5.3 ClassLoader Diagnosis *(DigiStack EAR: DigiStackWeb.war + DigiStackEJB.jar + DigiStackBatch.jar)*
- **Day 73** — Module 5: Deployment Concepts / ClassLoader — Topic 5.4 Virtual Host Architecture *(DigiStack EAR: DigiStackWeb.war + DigiStackEJB.jar + DigiStackBatch.jar)*
- **Day 74** — Module 5: Deployment Concepts / ClassLoader — Topic 5.5 ibm-web-bnd.xml vs web.xml *(DigiStack EAR: DigiStackWeb.war + DigiStackEJB.jar + DigiStackBatch.jar)*
- **Day 75** — Module 5: Deployment Concepts / ClassLoader — Topic 5.6 Shared Libraries *(DigiStack EAR: DigiStackWeb.war + DigiStackEJB.jar + DigiStackBatch.jar)*
- **Day 76** — Module 5: Deployment Concepts / ClassLoader — Topic 5.7 Edition Control *(DigiStack EAR: DigiStackWeb.war + DigiStackEJB.jar + DigiStackBatch.jar)*
- **Day 77** — Module 5: Deployment Concepts / ClassLoader — Topic 5.8 Descriptor Hierarchy *(DigiStack EAR: DigiStackWeb.war + DigiStackEJB.jar + DigiStackBatch.jar)*
- **Day 78** — Module 5: Deployment Concepts / ClassLoader — Overall Module Lab *(DigiStack EAR: DigiStackWeb.war + DigiStackEJB.jar + DigiStackBatch.jar)* *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 79** — Module 5: Deployment Concepts / ClassLoader — Production Incidents (Batch 1 of 3+) *(DigiStack EAR: DigiStackWeb.war + DigiStackEJB.jar + DigiStackBatch.jar)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 80** — Module 5: Deployment Concepts / ClassLoader — Production Incidents (Batch 2 of 3+, complete 6+) *(DigiStack EAR: DigiStackWeb.war + DigiStackEJB.jar + DigiStackBatch.jar)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 81** — Topic 6.0-A wsadmin Survival Primer (launching wsadmin, Jython basics, AdminConfig/AdminControl/AdminApp/AdminTask overview, AdminConfig.save(), end-to-end start/status/stop example)
- **Day 82** — Module 6: Deploy to Standalone — Topic 6.1 Deployment Strategy Types *(deploy the DigiStack EAR itself, not a sample app)* *[First use of the RFC template (CHANGE→PRE-CHECK→RISK→BACKUP→IMPLEMENTATION→VALIDATION→ROLLBACK→POST-CHECK→DOCUMENTATION) — required as lab artifact format from here forward]*
- **Day 83** — Module 6: Deploy to Standalone — Topic 6.2 wsadmin Deployment *(deploy the DigiStack EAR itself, not a sample app)*
- **Day 84** — Module 6: Deploy to Standalone — Topic 6.3 Deployment Scripts *(deploy the DigiStack EAR itself, not a sample app)*
- **Day 85** — Module 6: Deploy to Standalone — Topic 6.4 App Validation *(deploy the DigiStack EAR itself, not a sample app)*
- **Day 86** — Module 6: Deploy to Standalone — Topic 6.5 Rollback Procedure *(deploy the DigiStack EAR itself, not a sample app)*
- **Day 87** — Module 6: Deploy to Standalone — Topic 6.6 Deployment Logging *(deploy the DigiStack EAR itself, not a sample app)*
- **Day 88** — Module 6: Deploy to Standalone — Topic 6.7 Zero-Downtime Concepts *(deploy the DigiStack EAR itself, not a sample app)*
- **Day 89** — Module 6: Deploy to Standalone — Topic 6.8 Config via wsadmin *(deploy the DigiStack EAR itself, not a sample app)*
- **Day 90** — Module 6: Deploy to Standalone — Overall Module Lab *(deploy the DigiStack EAR itself, not a sample app)* *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 91** — Module 6: Deploy to Standalone — Production Incidents (Batch 1 of 3+) *(deploy the DigiStack EAR itself, not a sample app)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 92** — Module 6: Deploy to Standalone — Production Incidents (Batch 2 of 3+, complete 6+) *(deploy the DigiStack EAR itself, not a sample app)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 93** — Module 7: DMGR & Custom Profile — Topic 7.1 DMGR Internals *(DigiStack DMGR/custom profile)*
- **Day 94** — Module 7: DMGR & Custom Profile — Topic 7.2 Custom Profile Internals *(DigiStack DMGR/custom profile)*
- **Day 95** — Module 7: DMGR & Custom Profile — Topic 7.3 Profile Strategy *(DigiStack DMGR/custom profile)*
- **Day 96** — Module 7: DMGR & Custom Profile — Topic 7.4 DMGR HA *(DigiStack DMGR/custom profile)*
- **Day 97** — Module 7: DMGR & Custom Profile — Topic 7.5 Creating Profiles Production Way *(DigiStack DMGR/custom profile)*
- **Day 98** — Module 7: DMGR & Custom Profile — Topic 7.6 Naming Conventions *(DigiStack DMGR/custom profile)*
- **Day 99** — Module 7: DMGR & Custom Profile — Topic 7.7 Post-Creation Baseline *(DigiStack DMGR/custom profile)*
- **Day 100** — Module 7: DMGR & Custom Profile — Overall Module Lab *(DigiStack DMGR/custom profile)* *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 101** — Module 7: DMGR & Custom Profile — Production Incidents (Batch 1 of 3+) *(DigiStack DMGR/custom profile)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 102** — Module 7: DMGR & Custom Profile — Production Incidents (Batch 2 of 3+, complete 6+) *(DigiStack DMGR/custom profile)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 103** — Module 8: Federation — Topic 8.1 Federation Internals *(federate the DigiStack nodes)*
- **Day 104** — Module 8: Federation — Topic 8.2 Pre-Federation Checklist *(federate the DigiStack nodes)*
- **Day 105** — Module 8: Federation — Topic 8.3-A SSL Foundations for WAS Admins (keystore vs truststore, self-signed vs CA-signed, handshake basics, DMGR-to-Node-Agent trust) *(federate the DigiStack nodes)*
- **Day 106** — Module 8: Federation — Topic 8.3 Trust/Certificates *(federate the DigiStack nodes)*
- **Day 107** — Module 8: Federation — Topic 8.4 addNode Parameters *(federate the DigiStack nodes)*
- **Day 108** — Module 8: Federation — Topic 8.5 Post-Federation Verification *(federate the DigiStack nodes)*
- **Day 109** — Module 8: Federation — Topic 8.6 removeNode *(federate the DigiStack nodes)*
- **Day 110** — Module 8: Federation — Topic 8.7 Federation in Pipelines *(federate the DigiStack nodes)*
- **Day 111** — Module 8: Federation — Topic 8.8 Federation Troubleshooting *(federate the DigiStack nodes)*
- **Day 112** — Module 8: Federation — Topic 8.9 Multi-Node Architecture *(federate the DigiStack nodes)*
- **Day 113** — Module 8: Federation — Overall Module Lab *(federate the DigiStack nodes)* *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 114** — Module 8: Federation — Production Incidents (Batch 1 of 3+) *(federate the DigiStack nodes)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 115** — Module 8: Federation — Production Incidents (Batch 2 of 3+, complete 6+) *(federate the DigiStack nodes)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 116** — Module 9: Synchronization — Topic 9.1 Sync Architecture *(sync DigiStack's cell config)*
- **Day 117** — Module 9: Synchronization — Topic 9.2 Sync Protocol *(sync DigiStack's cell config)*
- **Day 118** — Module 9: Synchronization — Topic 9.3 Auto Sync Config *(sync DigiStack's cell config)*
- **Day 119** — Module 9: Synchronization — Topic 9.4 Manual Sync Scenarios *(sync DigiStack's cell config)*
- **Day 120** — Module 9: Synchronization — Topic 9.5 syncNode.sh *(sync DigiStack's cell config)*
- **Day 121** — Module 9: Synchronization — Topic 9.6 Sync Failure Diagnosis *(sync DigiStack's cell config)*
- **Day 122** — Module 9: Synchronization — Topic 9.7 Partial Sync/Split-Brain *(sync DigiStack's cell config)*
- **Day 123** — Module 9: Synchronization — Topic 9.8 Configuration Drift *(sync DigiStack's cell config)*
- **Day 124** — Module 9: Synchronization — Topic 9.9 Sync/Deploy Timing *(sync DigiStack's cell config)*
- **Day 125** — Module 9: Synchronization — Overall Module Lab *(sync DigiStack's cell config)* *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 126** — Module 9: Synchronization — Production Incidents (Batch 1 of 3+) *(sync DigiStack's cell config)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 127** — Module 9: Synchronization — Production Incidents (Batch 2 of 3+, complete 6+) *(sync DigiStack's cell config)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 128** — Module 10: Deploy to Federated Server — Topic 10.1 Managed App Server *(DigiStack EAR to a managed server)*
- **Day 129** — Module 10: Deploy to Federated Server — Topic 10.2 Deployment Targeting *(DigiStack EAR to a managed server)*
- **Day 130** — Module 10: Deploy to Federated Server — Topic 10.3 Deploy to Managed Server *(DigiStack EAR to a managed server)*
- **Day 131** — Module 10: Deploy to Federated Server — Topic 10.4 Sync After Deploy *(DigiStack EAR to a managed server)*
- **Day 132** — Module 10: Deploy to Federated Server — Topic 10.5 App State Management *(DigiStack EAR to a managed server)*
- **Day 133** — Module 10: Deploy to Federated Server — Topic 10.6 Multi-Server Non-Cluster *(DigiStack EAR to a managed server)*
- **Day 134** — Module 10: Deploy to Federated Server — Topic 10.7 Troubleshooting Managed Server *(DigiStack EAR to a managed server)*
- **Day 135** — Module 10: Deploy to Federated Server — Overall Module Lab *(DigiStack EAR to a managed server)* *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 136** — Module 10: Deploy to Federated Server — Production Incidents (Batch 1 of 3+) *(DigiStack EAR to a managed server)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 137** — Module 10: Deploy to Federated Server — Production Incidents (Batch 2 of 3+, complete 6+) *(DigiStack EAR to a managed server)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 138** — Arc 2 Capstone — Federate 2 nodes, deploy an app, diagnose an injected sync failure *[Explicitly scoped to DigiStack — see Milestone Plan]*
- **Day 139** — Arc 2 Review Flash Drill - 10 rapid-fire spaced-repetition questions covering the arc's highest-stakes recall items (self-scored; misses flagged 'revisit' in Progress Log)

## === ARC 3 — Scale & Availability ===

- **Day 140** — Module 11: Clustering — Topic 11.1 Cluster Design Architecture *(build DigiStackCluster)*
- **Day 141** — Module 11: Clustering — Topic 11.2 Cluster Internals *(build DigiStackCluster)*
- **Day 142** — Module 11: Clustering — Topic 11.3 Port Management *(build DigiStackCluster)*
- **Day 143** — Module 11: Clustering — Topic 11.4 rippleStart vs Full Start *(build DigiStackCluster)*
- **Day 144** — Module 11: Clustering — Topic 11.5 Config Propagation *(build DigiStackCluster)*
- **Day 145** — Module 11: Clustering — Topic 11.6 Cluster SPOF *(build DigiStackCluster)*
- **Day 146** — Module 11: Clustering — Topic 11.7 Cross-Node Clustering *(build DigiStackCluster)*
- **Day 147** — Module 11: Clustering — Topic 11.8 Weights/Load Distribution *(build DigiStackCluster)*
- **Day 148** — Module 11: Clustering — Topic 11.9 Health Monitoring *(build DigiStackCluster)*
- **Day 149** — Module 11: Clustering — Topic 11.10 Anti-Patterns *(build DigiStackCluster)*
- **Day 150** — Module 11: Clustering — Overall Module Lab *(build DigiStackCluster)* *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 151** — Module 11: Clustering — Production Incidents (Batch 1 of 3+) *(build DigiStackCluster)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 152** — Module 11: Clustering — Production Incidents (Batch 2 of 3+, complete 6+) *(build DigiStackCluster)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 153** — Module 12: Deploy to Cluster — Topic 12.1 Cluster Deploy Internals *(deploy DigiStack to DigiStackCluster)*
- **Day 154** — Module 12: Deploy to Cluster — Topic 12.2 Deployment Options *(deploy DigiStack to DigiStackCluster)*
- **Day 155** — Module 12: Deploy to Cluster — Topic 12.3 Rolling Deployment *(deploy DigiStack to DigiStackCluster)*
- **Day 156** — Module 12: Deploy to Cluster — Topic 12.4 Maintenance Mode *(deploy DigiStack to DigiStackCluster)*
- **Day 157** — Module 12: Deploy to Cluster — Topic 12.5 Verification Checklist *(deploy DigiStack to DigiStackCluster)*
- **Day 158** — Module 12: Deploy to Cluster — Topic 12.6 Update Strategies *(deploy DigiStack to DigiStackCluster)*
- **Day 159** — Module 12: Deploy to Cluster — Topic 12.7 Cluster Deploy via wsadmin *(deploy DigiStack to DigiStackCluster)*
- **Day 160** — Module 12: Deploy to Cluster — Topic 12.8 Failure Isolation *(deploy DigiStack to DigiStackCluster)*
- **Day 161** — Module 12: Deploy to Cluster — Topic 12.9 Change Management *(deploy DigiStack to DigiStackCluster)*
- **Day 162** — Module 12: Deploy to Cluster — Overall Module Lab *(deploy DigiStack to DigiStackCluster)* *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 163** — Module 12: Deploy to Cluster — Production Incidents (Batch 1 of 3+) *(deploy DigiStack to DigiStackCluster)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 164** — Module 12: Deploy to Cluster — Production Incidents (Batch 2 of 3+, complete 6+) *(deploy DigiStack to DigiStackCluster)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 165** — Module 13: HA & DR — Topic 13.1 HA Architecture Patterns *(DigiStack DR runbook/backups)*
- **Day 166** — Module 13: HA & DR — Topic 13.2 DMGR SPOF Mitigation *(DigiStack DR runbook/backups)*
- **Day 167** — Module 13: HA & DR — Topic 13.3 DMGR Backup Strategy *(DigiStack DR runbook/backups)*
- **Day 168** — Module 13: HA & DR — Topic 13.4 Node Agent Resilience *(DigiStack DR runbook/backups)*
- **Day 169** — Module 13: HA & DR — Topic 13.5 Cluster Member Failover *(DigiStack DR runbook/backups)*
- **Day 170** — Module 13: HA & DR — Topic 13.6 DR Runbook *(DigiStack DR runbook/backups)* *[Portfolio artifact: DigiStack DR runbook]*
- **Day 171** — Module 13: HA & DR — Topic 13.7 Backup Automation *(DigiStack DR runbook/backups)*
- **Day 172** — Module 13: HA & DR — Topic 13.8 Recovery Scenarios *(DigiStack DR runbook/backups)*
- **Day 173** — Module 13: HA & DR — Topic 13.9 Complete WAS Backup Architecture *(DigiStack DR runbook/backups)* *[Portfolio artifact: DigiStack backup runbook]*
- **Day 174** — Module 13: HA & DR — Overall Module Lab *(DigiStack DR runbook/backups)* *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 175** — Module 13: HA & DR — Production Incidents (Batch 1 of 3+) *(DigiStack DR runbook/backups)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 176** — Module 13: HA & DR — Production Incidents (Batch 2 of 3+, complete 6+) *(DigiStack DR runbook/backups)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]* *[Add cluster-health panel to DigiStack dashboard]*
- **Day 177** — Module 14: Capacity Planning & Sizing — Topic 14.1 Load-to-Node Translation *(size DigiStackCluster against a stated DigiStack load profile)*
- **Day 178** — Module 14: Capacity Planning & Sizing — Topic 14.2 Heap Sizing Formula *(size DigiStackCluster against a stated DigiStack load profile)*
- **Day 179** — Module 14: Capacity Planning & Sizing — Topic 14.3 Thread Pool Sizing vs Downstream Limits *(size DigiStackCluster against a stated DigiStack load profile)*
- **Day 180** — Module 14: Capacity Planning & Sizing — Topic 14.4 Headroom Planning *(size DigiStackCluster against a stated DigiStack load profile)*
- **Day 181** — Module 14: Capacity Planning & Sizing — Topic 14.5 Documenting Sizing Decisions *(size DigiStackCluster against a stated DigiStack load profile)*
- **Day 182** — Module 14: Capacity Planning & Sizing — Overall Module Lab *(size DigiStackCluster against a stated DigiStack load profile)* *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 183** — Module 14: Capacity Planning & Sizing — Production Incidents (Batch 1 of 3+) *(size DigiStackCluster against a stated DigiStack load profile)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 184** — Module 14: Capacity Planning & Sizing — Production Incidents (Batch 2 of 3+, complete 6+) *(size DigiStackCluster against a stated DigiStack load profile)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 185** — Arc 3 Capstone — Size and design HA for a stated load profile; justify every number *[Explicitly scoped to DigiStack — see Milestone Plan]*
- **Day 186** — Arc 3 Review Flash Drill - 10 rapid-fire spaced-repetition questions covering the arc's highest-stakes recall items (self-scored; misses flagged 'revisit' in Progress Log)
- **Day 187** — Incident Pattern Library Checkpoint — Categories 1,2,4,5 (JVM/Memory, ClassLoader, Clustering/HA, Federation/Sync) *[Apply layer-hypothesis field retroactively to these patterns]*

## === ARC 4 — Data & Integration ===

- **Day 188** — Module 15: JDBC — Topic 15.1 Pool Architecture *(DigiStackDS against PostgreSQL)*
- **Day 189** — Module 15: JDBC — Topic 15.2 JDBC Providers *(DigiStackDS against PostgreSQL)*
- **Day 190** — Module 15: JDBC — Topic 15.3 XA vs Non-XA *(DigiStackDS against PostgreSQL)*
- **Day 191** — Module 15: JDBC — Topic 15.4 J2C Auth Alias *(DigiStackDS against PostgreSQL)*
- **Day 192** — Module 15: JDBC — Topic 15.5 Pool Sizing *(DigiStackDS against PostgreSQL)*
- **Day 193** — Module 15: JDBC — Topic 15.6 Pool Timeout Params *(DigiStackDS against PostgreSQL)*
- **Day 194** — Module 15: JDBC — Topic 15.7 Connection Leak Detection *(DigiStackDS against PostgreSQL)*
- **Day 195** — Module 15: JDBC — Topic 15.8 Stale Connection Handling *(DigiStackDS against PostgreSQL)*
- **Day 196** — Module 15: JDBC — Topic 15.9 Cell vs Server Scope *(DigiStackDS against PostgreSQL)*
- **Day 197** — Module 15: JDBC — Topic 15.10 DB Failover *(DigiStackDS against PostgreSQL)*
- **Day 198** — Module 15: JDBC — Topic 15.11 JDBC Diagnosis *(DigiStackDS against PostgreSQL)*
- **Day 199** — Module 15: JDBC — Topic 15.12 wsadmin JDBC Automation *(DigiStackDS against PostgreSQL)*
- **Day 200** — Module 15: JDBC — Topic 15.13 Transaction Service & tranlog Management *(DigiStackDS against PostgreSQL)* *[Portfolio artifact: DigiStack tranlog/in-doubt-transaction runbook]*
- **Day 201** — Module 15: JDBC — Overall Module Lab *(DigiStackDS against PostgreSQL)* *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 202** — Module 15: JDBC — Production Incidents (Batch 1 of 3+) *(DigiStackDS against PostgreSQL)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 203** — Module 15: JDBC — Production Incidents (Batch 2 of 3+, complete 6+) *(DigiStackDS against PostgreSQL)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]* *[Add connection-pool wait-time/utilization panel to DigiStack dashboard]*
- **Day 204** — Module 16: Messaging — MQ/JMS/SIBus — Topic 16.1 SIBus vs External MQ (incl. SIBus-vs-external-MQ clarification note - interview-frequent distinction) *(DIGISTACK.NEFT.QUEUE + DLQ)*
- **Day 205** — Module 16: Messaging — MQ/JMS/SIBus — Topic 16.2 JMS Connection Factories/Activation Specs *(DIGISTACK.NEFT.QUEUE + DLQ)*
- **Day 206** — Module 16: Messaging — MQ/JMS/SIBus — Topic 16.3 MQ Queue Manager Integration *(DIGISTACK.NEFT.QUEUE + DLQ)*
- **Day 207** — Module 16: Messaging — MQ/JMS/SIBus — Topic 16.4 MDB Deployment/Sizing *(DIGISTACK.NEFT.QUEUE + DLQ)*
- **Day 208** — Module 16: Messaging — MQ/JMS/SIBus — Topic 16.5 Poison Message/DLQ Handling *(DIGISTACK.NEFT.QUEUE + DLQ)*
- **Day 209** — Module 16: Messaging — MQ/JMS/SIBus — Topic 16.6 Diagnosing Stuck Queues *(DIGISTACK.NEFT.QUEUE + DLQ)*
- **Day 210** — Module 16: Messaging — MQ/JMS/SIBus — Overall Module Lab *(DIGISTACK.NEFT.QUEUE + DLQ)* *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 211** — Module 16: Messaging — MQ/JMS/SIBus — Production Incidents (Batch 1 of 3+) *(DIGISTACK.NEFT.QUEUE + DLQ)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 212** — Module 16: Messaging — MQ/JMS/SIBus — Production Incidents (Batch 2 of 3+, complete 6+) *(DIGISTACK.NEFT.QUEUE + DLQ)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]* *[Add queue-depth/DLQ panel to DigiStack dashboard]*
- **Day 213** — Supplementary — IBM MQ Deep Dive: Channel Authentication (CHLAUTH) — locking down MQ channel access
- **Day 214** — Supplementary — IBM MQ Deep Dive: MQ Clustering — cluster queue managers, workload balancing across queue managers
- **Day 215** — Supplementary — IBM MQ Deep Dive: Lab: Multi-Instance Queue Manager — active/standby pair, fail over and verify
- **Day 216** — Arc 4 Capstone — Trace a transaction across JDBC (XA) + MQ; identify where a 2PC failure surfaces *[Explicitly scoped to DigiStack — see Milestone Plan]*
- **Day 217** — Arc 4 Review Flash Drill - 10 rapid-fire spaced-repetition questions covering the arc's highest-stakes recall items (self-scored; misses flagged 'revisit' in Progress Log)
- **Day 218** — Incident Pattern Library Checkpoint — Category 3 (JDBC / Connection Pool) *[Apply layer-hypothesis field retroactively to these patterns]*

## === ARC 5 — Operate & Defend ===

- **Day 219** — Module 17: IHS — Topic 17.1 Role in Architecture *(front DigiStackCluster, virtual host digistack.bank.local)*
- **Day 220** — Module 17: IHS — Topic 17.2 Production Install *(front DigiStackCluster, virtual host digistack.bank.local)*
- **Day 221** — Module 17: IHS — Topic 17.3 httpd.conf Mastery *(front DigiStackCluster, virtual host digistack.bank.local)*
- **Day 222** — Module 17: IHS — Topic 17.4 plugin-cfg.xml *(front DigiStackCluster, virtual host digistack.bank.local)*
- **Day 223** — Module 17: IHS — Topic 17.5 Plugin Generation *(front DigiStackCluster, virtual host digistack.bank.local)*
- **Day 224** — Module 17: IHS — Topic 17.6 Plugin Error Interval *(front DigiStackCluster, virtual host digistack.bank.local)*
- **Day 225** — Module 17: IHS — Topic 17.7 Plugin Retry Interval *(front DigiStackCluster, virtual host digistack.bank.local)*
- **Day 226** — Module 17: IHS — Topic 17.8 SSL/TLS Architecture *(front DigiStackCluster, virtual host digistack.bank.local)*
- **Day 227** — Module 17: IHS — Topic 17.9 Virtual Hosting *(front DigiStackCluster, virtual host digistack.bank.local)*
- **Day 228** — Module 17: IHS — Topic 17.10 Tuning *(front DigiStackCluster, virtual host digistack.bank.local)*
- **Day 229** — Module 17: IHS — Topic 17.11 Log Analysis *(front DigiStackCluster, virtual host digistack.bank.local)*
- **Day 230** — Module 17: IHS — Topic 17.12 IHS HA *(front DigiStackCluster, virtual host digistack.bank.local)*
- **Day 231** — Module 17: IHS — Topic 17.13 Troubleshooting *(front DigiStackCluster, virtual host digistack.bank.local)*
- **Day 232** — Module 17: IHS — Overall Module Lab *(front DigiStackCluster, virtual host digistack.bank.local)* *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 233** — Module 17: IHS — Production Incidents (Batch 1 of 3+) *(front DigiStackCluster, virtual host digistack.bank.local)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 234** — Module 17: IHS — Production Incidents (Batch 2 of 3+, complete 6+) *(front DigiStackCluster, virtual host digistack.bank.local)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]* *[Add plugin-routing/503 panel to DigiStack dashboard]*
- **Day 235** — Supplementary — External Load Balancers: Layer 4 vs Layer 7 load balancing, VIP, persistence, SSL offloading (concept)
- **Day 236** — Supplementary — External Load Balancers: Lab: stand up NGINX or HAProxy in front of a WAS cluster
- **Day 237** — Module 18: Session Management — Topic 18.1 Session Lifecycle/JSESSIONID *(DigiStack session behavior)*
- **Day 238** — Module 18: Session Management — Topic 18.2 Affinity via Plugin *(DigiStack session behavior)*
- **Day 239** — Module 18: Session Management — Topic 18.3 Failover Without Replication *(DigiStack session behavior)*
- **Day 240** — Module 18: Session Management — Topic 18.4 Replication vs DB Persistence *(DigiStack session behavior)*
- **Day 241** — Module 18: Session Management — Topic 18.5 Timeout Precedence *(DigiStack session behavior)*
- **Day 242** — Module 18: Session Management — Topic 18.6 When Session Design Is Wrong *(DigiStack session behavior)*
- **Day 243** — Module 18: Session Management — Overall Module Lab *(DigiStack session behavior)* *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 244** — Module 18: Session Management — Production Incidents (Batch 1 of 3+) *(DigiStack session behavior)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 245** — Module 18: Session Management — Production Incidents (Batch 2 of 3+, complete 6+) *(DigiStack session behavior)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 246** — Module 19: User Management & Security — Topic 19.1 Security Model *(DigiStack security/registry config)*
- **Day 247** — Module 19: User Management & Security — Topic 19.2 User Registry Architecture *(DigiStack security/registry config)*
- **Day 248** — Module 19: User Management & Security — Topic 19.3 LDAP Integration *(DigiStack security/registry config)*
- **Day 249** — Module 19: User Management & Security — Topic 19.4 Federated Repository *(DigiStack security/registry config)*
- **Day 250** — Module 19: User Management & Security — Topic 19.5 Admin Roles *(DigiStack security/registry config)*
- **Day 251** — Module 19: User Management & Security — Topic 19.6 Role-to-Group Mapping *(DigiStack security/registry config)*
- **Day 252** — Module 19: User Management & Security — Topic 19.7 Java 2 Security *(DigiStack security/registry config)* *[Stand up persistent DigiStack Grafana/Prometheus dashboard — extended per-module below, not a one-off lab]*
- **Day 253** — Module 19: User Management & Security — Topic 19.8 SSL Config Architecture *(DigiStack security/registry config)* *[Stand up persistent DigiStack Grafana/Prometheus dashboard — extended per-module below, not a one-off lab]*
- **Day 254** — Module 19: User Management & Security — Topic 19.9 Admin Security Recovery *(DigiStack security/registry config)* *[Stand up persistent DigiStack Grafana/Prometheus dashboard — extended per-module below, not a one-off lab]*
- **Day 255** — Module 19: User Management & Security — Topic 19.10 wsadmin with Security On *(DigiStack security/registry config)* *[Stand up persistent DigiStack Grafana/Prometheus dashboard — extended per-module below, not a one-off lab]*
- **Day 256** — Module 19: User Management & Security — Topic 19.11 Security Audit Checklist *(DigiStack security/registry config)* *[Stand up persistent DigiStack Grafana/Prometheus dashboard — extended per-module below, not a one-off lab]*
- **Day 257** — Module 19: User Management & Security — Overall Module Lab *(DigiStack security/registry config)* *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]* *[Stand up persistent DigiStack Grafana/Prometheus dashboard — extended per-module below, not a one-off lab]*
- **Day 258** — Module 19: User Management & Security — Production Incidents (Batch 1 of 3+) *(DigiStack security/registry config)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 259** — Module 19: User Management & Security — Production Incidents (Batch 2 of 3+, complete 6+) *(DigiStack security/registry config)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 260** — Supplementary — Identity Protocols (Conceptual): Kerberos and SPNEGO — conceptual, where they intersect WAS admin security
- **Day 261** — Supplementary — Identity Protocols (Conceptual): OAuth/SAML basics — conceptual, modern SSO context for a banking portal
- **Day 262** — Module 20: Performance Tuning & JVM Diagnostics — Topic 20.1 GC Deep Dive *(DigiStack JVM/GC/thread tuning)*
- **Day 263** — Module 20: Performance Tuning & JVM Diagnostics — Topic 20.2 verboseGC Parsing *(DigiStack JVM/GC/thread tuning)*
- **Day 264** — Module 20: Performance Tuning & JVM Diagnostics — Topic 20.3 Thread Pool Architecture *(DigiStack JVM/GC/thread tuning)*
- **Day 265** — Module 20: Performance Tuning & JVM Diagnostics — Topic 20.4 Thread Dump Analysis *(DigiStack JVM/GC/thread tuning)*
- **Day 266** — Module 20: Performance Tuning & JVM Diagnostics — Topic 20.5 Heap Dump Analysis *(DigiStack JVM/GC/thread tuning)*
- **Day 267** — Module 20: Performance Tuning & JVM Diagnostics — Topic 20.6 PMI *(DigiStack JVM/GC/thread tuning)*
- **Day 268** — Module 20: Performance Tuning & JVM Diagnostics — Topic 20.7 Pool Monitoring *(DigiStack JVM/GC/thread tuning)*
- **Day 269** — Module 20: Performance Tuning & JVM Diagnostics — Topic 20.8 CPU/Memory Baseline *(DigiStack JVM/GC/thread tuning)*
- **Day 270** — Module 20: Performance Tuning & JVM Diagnostics — Topic 20.9 Diagnosis Methodology *(DigiStack JVM/GC/thread tuning)*
- **Day 271** — Module 20: Performance Tuning & JVM Diagnostics — Topic 20.10 Web Container Tuning *(DigiStack JVM/GC/thread tuning)*
- **Day 272** — Module 20: Performance Tuning & JVM Diagnostics — Overall Module Lab *(DigiStack JVM/GC/thread tuning)* *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 273** — Module 20: Performance Tuning & JVM Diagnostics — Production Incidents (Batch 1 of 3+) *(DigiStack JVM/GC/thread tuning)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 274** — Module 20: Performance Tuning & JVM Diagnostics — Production Incidents (Batch 2 of 3+, complete 6+) *(DigiStack JVM/GC/thread tuning)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]* *[Add GC-pause/heap-trend panel to DigiStack dashboard]*
- **Day 275** — Supplementary — Dynacache, Core Dumps & Monitoring: Dynacache architecture — Distributed Map, Cache Instances, Servlet Cache
- **Day 276** — Supplementary — Dynacache, Core Dumps & Monitoring: Lab: Cache replication (DRS) and Cache Monitor — configure and verify
- **Day 277** — Supplementary — Dynacache, Core Dumps & Monitoring: Core dump basics — native_stderr.log, crash signatures, when it's not a heap issue
- **Day 278** — Supplementary — Dynacache, Core Dumps & Monitoring: Lab: force a native crash, walk the core dump back to root cause
- **Day 279** — Supplementary — Dynacache, Core Dumps & Monitoring: Metrics/Logs/Traces concept, alert design
- **Day 280** — Supplementary — Dynacache, Core Dumps & Monitoring: Lab: wire a basic Prometheus/Grafana dashboard tied back to PMI/JMX
- **Day 281** — Module 21: Logging & FFDC — Topic 21.1 Log Architecture *(DigiStack logs/FFDC)*
- **Day 282** — Module 21: Logging & FFDC — Topic 21.2 Trace String Config *(DigiStack logs/FFDC)*
- **Day 283** — Module 21: Logging & FFDC — Topic 21.3 Trace String Syntax *(DigiStack logs/FFDC)*
- **Day 284** — Module 21: Logging & FFDC — Topic 21.4 FFDC *(DigiStack logs/FFDC)*
- **Day 285** — Module 21: Logging & FFDC — Topic 21.5 Log Rotation/Retention *(DigiStack logs/FFDC)*
- **Day 286** — Module 21: Logging & FFDC — Topic 21.6 Cross-Component Correlation *(DigiStack logs/FFDC)*
- **Day 287** — Module 21: Logging & FFDC — Topic 21.7 IBM Message Decoder *(DigiStack logs/FFDC)*
- **Day 288** — Module 21: Logging & FFDC — Topic 21.8 Log-Based Incident Analysis *(DigiStack logs/FFDC)*
- **Day 289** — Module 21: Logging & FFDC — Topic 21.9 Daily & Weekly Health Check Runbook *(DigiStack logs/FFDC)* *[Portfolio artifact: DigiStack health-check runbook]*
- **Day 290** — Module 21: Logging & FFDC — Overall Module Lab *(DigiStack logs/FFDC)* *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 291** — Module 21: Logging & FFDC — Production Incidents (Batch 1 of 3+) *(DigiStack logs/FFDC)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 292** — Module 21: Logging & FFDC — Production Incidents (Batch 2 of 3+, complete 6+) *(DigiStack logs/FFDC)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]* *[Add FFDC/error-rate panel to DigiStack dashboard]*
- **Day 293** — Module 23: Licensing & Modernization Framework — Topic 23.1 PVU/Core Licensing *(DigiStack modernization case)*
- **Day 294** — Module 23: Licensing & Modernization Framework — Topic 23.2 WAS ND vs Liberty vs OpenLiberty *(DigiStack modernization case)*
- **Day 295** — Module 23: Licensing & Modernization Framework — Topic 23.3 Migration Effort Estimation *(DigiStack modernization case)*
- **Day 296** — Module 23: Licensing & Modernization Framework — Topic 23.4 Containerization Context *(DigiStack modernization case)*
- **Day 297** — Module 23: Licensing & Modernization Framework — Topic 23.5 Building the Business Case *(DigiStack modernization case)*
- **Day 298** — Module 23: Licensing & Modernization Framework — Topic 23.6 Liberty Migration Deep Dive (WCT walkthrough, auto vs manual rework, phased coexistence, rollback-to-ND safety net) *(DigiStack modernization case)*
- **Day 299** — Module 23: Licensing & Modernization Framework — Overall Module Lab *(DigiStack modernization case)* *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 300** — Module 23: Licensing & Modernization Framework — Production Incidents (Batch 1 of 3+) *(DigiStack modernization case)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 301** — Module 23: Licensing & Modernization Framework — Production Incidents (Batch 2 of 3+, complete 6+) *(DigiStack modernization case)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 302** — Supplementary — Liberty Profile & Migration Tooling: Liberty vs traditional WAS — server.xml, featureManager
- **Day 303** — Supplementary — Liberty Profile & Migration Tooling: Lab: run a Liberty server from dropins
- **Day 304** — Supplementary — Liberty Profile & Migration Tooling: Liberty in Docker/Kubernetes — containerization awareness for tWAS-to-Liberty migration
- **Day 305** — Supplementary — Liberty Profile & Migration Tooling: WCT (WebSphere Customization Toolbox) + WASPreUpgrade/WASPostUpgrade scripts
- **Day 306** — Supplementary — Liberty Profile & Migration Tooling: Lab: migrate a profile end-to-end using WCT
- **Day 307** — Module 24: wsadmin / Automation Mastery — Topic 24.1 wsadmin Architecture *(automate DigiStack operations)*
- **Day 308** — Module 24: wsadmin / Automation Mastery — Topic 24.2 Jython in WAS *(automate DigiStack operations)*
- **Day 309** — Module 24: wsadmin / Automation Mastery — Topic 24.3 AdminConfig *(automate DigiStack operations)*
- **Day 310** — Module 24: wsadmin / Automation Mastery — Topic 24.4 AdminControl *(automate DigiStack operations)*
- **Day 311** — Module 24: wsadmin / Automation Mastery — Topic 24.5 AdminApp *(automate DigiStack operations)*
- **Day 312** — Module 24: wsadmin / Automation Mastery — Topic 24.6 AdminTask *(automate DigiStack operations)*
- **Day 313** — Module 24: wsadmin / Automation Mastery — Topic 24.7 MBean Querying *(automate DigiStack operations)*
- **Day 314** — Module 24: wsadmin / Automation Mastery — Topic 24.8 Script Library Patterns *(automate DigiStack operations)*
- **Day 315** — Module 24: wsadmin / Automation Mastery — Topic 24.9 Automation Framework *(automate DigiStack operations)*
- **Day 316** — Module 24: wsadmin / Automation Mastery — Topic 24.10 Interview Jython Scripts *(automate DigiStack operations)*
- **Day 317** — Module 24: wsadmin / Automation Mastery — Overall Module Lab *(automate DigiStack operations)* *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 318** — Module 24: wsadmin / Automation Mastery — Production Incidents (Batch 1 of 3+) *(automate DigiStack operations)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 319** — Module 24: wsadmin / Automation Mastery — Production Incidents (Batch 2 of 3+, complete 6+) *(automate DigiStack operations)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 320** — Supplementary — CI/CD Pipelines: Ansible + Jenkins pipeline concepts for WAS automation
- **Day 321** — Supplementary — CI/CD Pipelines: Lab: turn a wsadmin script into a Jenkins-triggered deployment pipeline
- **Day 322** — Arc 5 Capstone (Final) — Full incident simulation from a log bundle: root cause, fix, post-incident report *[Explicitly scoped to DigiStack — see Milestone Plan]*
- **Day 323** — Arc 5 Review Flash Drill - 10 rapid-fire spaced-repetition questions covering the arc's highest-stakes recall items (self-scored; misses flagged 'revisit' in Progress Log)
- **Day 324** — Incident Pattern Library Checkpoint — Category 6 (IHS / Plugin) *[Apply layer-hypothesis field retroactively to these patterns]*

## === ARC 6 — Enterprise Maturity ===

- **Day 325** — Module 25: Change Management & ITSM — Topic 25.1 Why Change Management Exists in Banking *(formalizes the RFC habit already used on DigiStack since Module 6)*
- **Day 326** — Module 25: Change Management & ITSM — Topic 25.2 ITSM Ticket Types *(formalizes the RFC habit already used on DigiStack since Module 6)*
- **Day 327** — Module 25: Change Management & ITSM — Topic 25.3 RFC Lifecycle *(formalizes the RFC habit already used on DigiStack since Module 6)*
- **Day 328** — Module 25: Change Management & ITSM — Topic 25.4 CAB Presentation *(formalizes the RFC habit already used on DigiStack since Module 6)*
- **Day 329** — Module 25: Change Management & ITSM — Topic 25.5 Deployment Window Management *(formalizes the RFC habit already used on DigiStack since Module 6)*
- **Day 330** — Module 25: Change Management & ITSM — Topic 25.6 Rollback Plan Writing *(formalizes the RFC habit already used on DigiStack since Module 6)*
- **Day 331** — Module 25: Change Management & ITSM — Topic 25.7 Post-Implementation Review (PIR) *(formalizes the RFC habit already used on DigiStack since Module 6)*
- **Day 332** — Module 25: Change Management & ITSM — Topic 25.8 Emergency Change Process *(formalizes the RFC habit already used on DigiStack since Module 6)*
- **Day 333** — Module 25: Change Management & ITSM — Topic 25.9 Change Freeze Periods *(formalizes the RFC habit already used on DigiStack since Module 6)*
- **Day 334** — Module 25: Change Management & ITSM — Topic 25.10 ServiceNow for WAS Admins *(formalizes the RFC habit already used on DigiStack since Module 6)*
- **Day 335** — Module 25: Change Management & ITSM — Overall Module Lab *(formalizes the RFC habit already used on DigiStack since Module 6)* *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 336** — Module 25: Change Management & ITSM — Production Incidents (Batch 1 of 3+) *(formalizes the RFC habit already used on DigiStack since Module 6)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 337** — Module 25: Change Management & ITSM — Production Incidents (Batch 2 of 3+, complete 6+) *(formalizes the RFC habit already used on DigiStack since Module 6)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 338** — Module 26: Enterprise Infrastructure Integration — Topic 26.1 F5/NetScaler Load Balancer Integration *(integrate DigiStack with these systems)*
- **Day 339** — Module 26: Enterprise Infrastructure Integration — Topic 26.2 F5 Health Monitor Tuning *(integrate DigiStack with these systems)*
- **Day 340** — Module 26: Enterprise Infrastructure Integration — Topic 26.3 APM Agent Integration *(integrate DigiStack with these systems)*
- **Day 341** — Module 26: Enterprise Infrastructure Integration — Topic 26.4 Tivoli/IBM Monitoring *(integrate DigiStack with these systems)*
- **Day 342** — Module 26: Enterprise Infrastructure Integration — Topic 26.5 CyberArk / PAM Vault Integration *(integrate DigiStack with these systems)*
- **Day 343** — Module 26: Enterprise Infrastructure Integration — Topic 26.6 Splunk / SIEM Log Forwarding *(integrate DigiStack with these systems)*
- **Day 344** — Module 26: Enterprise Infrastructure Integration — Topic 26.7 ServiceNow CMDB *(integrate DigiStack with these systems)*
- **Day 345** — Module 26: Enterprise Infrastructure Integration — Topic 26.8 Ansible / Infrastructure as Code *(integrate DigiStack with these systems)*
- **Day 346** — Module 26: Enterprise Infrastructure Integration — Topic 26.9 SSH Bastion / Jump Server *(integrate DigiStack with these systems)*
- **Day 347** — Module 26: Enterprise Infrastructure Integration — Topic 26.10 NFS / Shared Storage *(integrate DigiStack with these systems)*
- **Day 348** — Module 26: Enterprise Infrastructure Integration — Overall Module Lab *(integrate DigiStack with these systems)* *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 349** — Module 26: Enterprise Infrastructure Integration — Production Incidents (Batch 1 of 3+) *(integrate DigiStack with these systems)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 350** — Module 26: Enterprise Infrastructure Integration — Production Incidents (Batch 2 of 3+, complete 6+) *(integrate DigiStack with these systems)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 351** — Module 27: Compliance & Audit Readiness — Topic 27.1 Regulatory Landscape for WAS in Indian Banking *(audit DigiStack)*
- **Day 352** — Module 27: Compliance & Audit Readiness — Topic 27.2 SSL/TLS Compliance *(audit DigiStack)*
- **Day 353** — Module 27: Compliance & Audit Readiness — Topic 27.3 Admin Console in Production *(audit DigiStack)*
- **Day 354** — Module 27: Compliance & Audit Readiness — Topic 27.4 J2 Security Audit *(audit DigiStack)*
- **Day 355** — Module 27: Compliance & Audit Readiness — Topic 27.5 Password & Credential Audit *(audit DigiStack)*
- **Day 356** — Module 27: Compliance & Audit Readiness — Topic 27.6 Patch Compliance Evidence *(audit DigiStack)*
- **Day 357** — Module 27: Compliance & Audit Readiness — Topic 27.7 Access Control Audit *(audit DigiStack)*
- **Day 358** — Module 27: Compliance & Audit Readiness — Topic 27.8 Log Retention Compliance *(audit DigiStack)*
- **Day 359** — Module 27: Compliance & Audit Readiness — Topic 27.9 Audit Trail for Config Changes *(audit DigiStack)*
- **Day 360** — Module 27: Compliance & Audit Readiness — Topic 27.10 IS Audit Walkthrough Simulation *(audit DigiStack)*
- **Day 361** — Module 27: Compliance & Audit Readiness — Topic 27.11 Remediation Documentation *(audit DigiStack)*
- **Day 362** — Module 27: Compliance & Audit Readiness — Overall Module Lab *(audit DigiStack)* *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 363** — Module 27: Compliance & Audit Readiness — Production Incidents (Batch 1 of 3+) *(audit DigiStack)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 364** — Module 27: Compliance & Audit Readiness — Production Incidents (Batch 2 of 3+, complete 6+) *(audit DigiStack)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 365** — Supplementary — Compliance — Named Frameworks: PCI DSS control mapping specific to a WAS-fronted payment application
- **Day 366** — Supplementary — Compliance — Named Frameworks: SOX control mapping for change/config audit trails on WAS
- **Day 367** — Module 28: SSL & Certificate Disaster Recovery — Topic 28.1 WAS SSL Architecture Deep Dive *(DigiStack certificate lifecycle)*
- **Day 368** — Module 28: SSL & Certificate Disaster Recovery — Topic 28.2 Certificate Lifecycle in Banking *(DigiStack certificate lifecycle)*
- **Day 369** — Module 28: SSL & Certificate Disaster Recovery — Topic 28.3 ikeycmd / gskcmd Mastery *(DigiStack certificate lifecycle)*
- **Day 370** — Module 28: SSL & Certificate Disaster Recovery — Topic 28.4 Certificate Expiry — The Silent Killer *(DigiStack certificate lifecycle)*
- **Day 371** — Module 28: SSL & Certificate Disaster Recovery — Topic 28.5 Zero-Downtime Certificate Rotation *(DigiStack certificate lifecycle)*
- **Day 372** — Module 28: SSL & Certificate Disaster Recovery — Topic 28.6 LTPA Token & SSL Relationship *(DigiStack certificate lifecycle)*
- **Day 373** — Module 28: SSL & Certificate Disaster Recovery — Topic 28.7 SSL Handshake Failure Diagnosis *(DigiStack certificate lifecycle)*
- **Day 374** — Module 28: SSL & Certificate Disaster Recovery — Topic 28.8 SSL DR Scenario 1 — DMGR Keystore Lost *(DigiStack certificate lifecycle)*
- **Day 375** — Module 28: SSL & Certificate Disaster Recovery — Topic 28.9 SSL DR Scenario 2 — Cert Expired at Midnight *(DigiStack certificate lifecycle)*
- **Day 376** — Module 28: SSL & Certificate Disaster Recovery — Topic 28.10 SSL DR Scenario 3 — CA Root Cert Expired *(DigiStack certificate lifecycle)*
- **Day 377** — Module 28: SSL & Certificate Disaster Recovery — Topic 28.11 SSL DR Scenario 4 — IHS kdb Corrupted *(DigiStack certificate lifecycle)*
- **Day 378** — Module 28: SSL & Certificate Disaster Recovery — Topic 28.12 SSL Backup Strategy *(DigiStack certificate lifecycle)*
- **Day 379** — Module 28: SSL & Certificate Disaster Recovery — Topic 28.13 SSL in DR Site *(DigiStack certificate lifecycle)*
- **Day 380** — Module 28: SSL & Certificate Disaster Recovery — Overall Module Lab *(DigiStack certificate lifecycle)* *[Tag Console (C) / wsadmin (W) / Automation (A) coverage in Progress Log]*
- **Day 381** — Module 28: SSL & Certificate Disaster Recovery — Production Incidents (Batch 1 of 3+) *(DigiStack certificate lifecycle)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 382** — Module 28: SSL & Certificate Disaster Recovery — Production Incidents (Batch 2 of 3+, complete 6+) *(DigiStack certificate lifecycle)* *[Layer hypothesis (L1–L7) required before evidence-gathering; close with 15-min interview checkpoint (10 basic/10 intermediate/10 senior/5 troubleshooting/3 architecture/2 design-choice, drawn from running question bank)]*
- **Day 383** — Incident Pattern Library Checkpoint — Categories 7,8 (SSL/Certificates, Change Management Failures) *[Apply layer-hypothesis field retroactively to these patterns]*
- **Day 384** — Arc 6 Capstone — Enterprise Readiness Audit *[Explicitly scoped to DigiStack — see Milestone Plan]* *[Dashboard becomes part of the audit evidence pack, alongside 27.10]*
- **Day 385** — Arc 6 Review Flash Drill - 10 rapid-fire spaced-repetition questions covering the arc's highest-stakes recall items (self-scored; misses flagged 'revisit' in Progress Log)
- **Day 386** — Supplementary — Career Readiness: Resume rewrite tailored to Senior WAS Admin roles, gap narrative
- **Day 387** — Supplementary — Career Readiness: Architecture whiteboard prep — redraw Cell/Node/Cluster/IHS from memory under time pressure
- **Day 388** — Supplementary — Career Readiness: Documentation portfolio assembly — SOPs, runbooks, incident reports as work samples
- **Day 389** — Supplementary — Career Readiness: Mock interview day — scenario Q&A, STAR delivery drill, salary/negotiation

---

## Notes on Content-Only Additions (no separate day, folded into existing content)

- **Incident Pattern Library — Category 8 expansion**: patterns 7 (Change advisory approval expired) and 8 (Concurrent conflicting changes) are added to Category 8 in `02-WAS-Senior-Admin-Syllabus.md`. Studied as part of the existing Incident Pattern Library Checkpoint days (Days 383, and the earlier Category-8-adjacent checkpoints) — no new day required.

