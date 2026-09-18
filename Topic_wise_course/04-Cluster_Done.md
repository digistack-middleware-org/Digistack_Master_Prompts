# 🏦 WebSphere Clustering — Zero to Expert
## 41-Day Plan (1 hr/day) — M2 / M5 / M9 Protected (5 days each)

> **Daily rhythm:** 45 min theory + 15 min hands-on (wsadmin/console)
> **Format per topic:** Theory 80% → Banking Scenario 15% → Interview 5%
> **Legend:** 🔒 = Interview Goldmine | ⚡ = Compressed day (merged topics) | 🏦 = Banking Scenario

---

## 📅 WEEK 1 — Foundations + Cluster Creation

### Day 1 — M1.1: Cluster Basics
- [ ] Single server vs Cluster vs Node vs Cell
- [ ] Vertical vs Horizontal clusters (diagram both)
- [ ] HA / Failover / Load Balancing / Scalability — ATM & NetBanking examples
- 🎤 *Interview Q: "Why does a bank use horizontal clusters in Prod?"*

### Day 2 — M1.2: Topology + Federation
- [ ] Cell → Node → NodeAgent → DMGR → AppServer relationships
- [ ] Node federation process
- [ ] Banking example: DMGR in DR DC, nodes in Zone-A / Zone-B
- **Hands-on:** Create standalone server in console; run `AdminTask.listNodes()`, `AdminConfig.list("Server")`

### Day 3 — 🔒 M2.1: Create Cluster (Console)
- [ ] Servers → Clusters → New → add members (vertical + horizontal)
- [ ] Bank naming conventions: `PayCluster_A1`, `PayCluster_B1`
- **Hands-on:** Create 4-member cluster (2 vertical + 2 horizontal) in test cell

### Day 4 — 🔒 M2.1: Create Cluster (wsadmin)
- [ ] `AdminTask.createCluster()`
- [ ] `AdminTask.createClusterMember()`
- [ ] Scripted cluster build vs console build — pros/cons in change-managed environments
- **Hands-on:** Build same 4-member cluster fully via wsadmin; save script

### Day 5 — 🔒 M2.2: Cluster Topologies
- [ ] Vertical (Dev/Test, one box) vs Horizontal (Prod, LPARs/VMs)
- [ ] Preferred servers & failover direction
- [ ] Cluster of clusters: Web tier → App tier → DB tier

### Day 6 — 🔒 M2.3: Cluster Operations
- [ ] Start / Stop / **Ripplestart** — console + wsadmin
- [ ] `AdminControl.invoke(nodeAgent, "rippleStart")`
- [ ] Staggered rolling restarts in Prod change windows

### Day 7 — 🔒 M2.3: Member Deletion + Scenario
- [ ] Delete cluster members safely (order, cleanup, plugin regen)
- [ ] 🏦 **Scenario:** Payment cluster — one member down during IMPS burst → traffic fails over to Zone-B
- ✅ *End-of-module: redo cluster create + ripplestart from memory*

---

## 📅 WEEK 2 — WLM + Sessions + SIBus Start

### Day 8 — M3.1: HTTP Session WLM
- [ ] Plugin load balancing: Round Robin vs Random
- [ ] `plugin-cfg.xml` deep dive: `LoadBalance`, `RetryInterval`, `ServerIOTimeout`
- [ ] Full routing flow: Web Server → Plugin → Cluster

### Day 9 — ⚡ M3.2 + M3.3: EJB WLM + ODR
- [ ] ORB workload management for EJB clients
- [ ] `com.ibm.CORBA.requestTimeout`, client-side WLM
- [ ] ODR & SIP clusters — overview + when banks use them

### Day 10 — M3: Scenario + Plugin Log Practice
- [ ] 🏦 **Scenario:** NetBanking login storm — even distribution, one heavy node flagged via plugin logs
- [ ] Read a real plugin.log routing sample — trace one request end-to-end

### Day 11 — M4.1: Session Affinity (Sticky Sessions)
- [ ] Clone IDs, JSESSIONID structure, plugin affinity logic
- [ ] Preferred server dies → failover to clone (what actually happens)

### Day 12 — M4.2: Replication Options
- [ ] Memory-to-Memory (M2M): peer-to-peer vs client mode
- [ ] Database session persistence
- [ ] **Build comparison table:** cost / performance / recovery time — which banks use where

### Day 13 — M4.3: Replication Tuning + Scenario
- [ ] Replication domain, `replicationType`, time-based writes
- [ ] `invalidateAll`, session timeout impact during failover
- [ ] 🏦 **Scenario:** Customer mid-fund-transfer → node crash → M2M replication → transaction continues on clone

### Day 14 — ⚡ M6.1: SIBus Basics (Part 1)
- [ ] Bus, messaging engines, cluster bus members
- 🎤 *Interview Q: "What is a messaging engine?"*

---

## 📅 WEEK 3 — SIBus + HA Core Groups (Goldmine)

### Day 15 — M6.1: SIBus (Part 2)
- [ ] ME placement policy
- [ ] HA policy vs Scalability policy — when to pick which
- [ ] File store vs Data store

### Day 16 — M6.2: SIB Failover + Scenario
- [ ] ME movement between cluster members
- [ ] 🏦 **Scenario:** SWIFT queue — ME failover during patching, message ordering preserved

### Day 17 — 🔒 M5.1: HA Manager & Core Groups
- [ ] Core group concept, core group bridges, access points
- [ ] Draw the core group topology from memory

### Day 18 — 🔒 M5.1: Failover Policies + DMGR HA
- [ ] Preferred servers, failover policies
- [ ] DMGR in core group — manual failover vs IBM Tivoli System Automation (TSA)

### Day 19 — 🔒 M5.2: Transaction Log Failover
- [ ] Transaction logs on shared NFS/SAN
- [ ] RRM (recovery restart), peer recovery of hung transactions

### Day 20 — 🔒 M5.3: Singleton Services
- [ ] Scheduled tasks, JMS in a cluster — why only one member runs them
- [ ] How singleton failover works inside a core group

### Day 21 — 🔒 M5: Scenario + Revision
- [ ] 🏦 **Scenario:** Core Banking (Finacle/TCS BaNCS) MDB cluster — JMS processed by one member, failover on crash
- ✅ *End-of-module: explain core group + bridge + failover policy aloud, no notes*

---

## 📅 WEEK 4 — Deploy + Tuning + Architecture + M9 Start

### Day 22 — M7.1: Deploy to Cluster
- [ ] Console: Enterprise Applications → Install → map to cluster
- [ ] wsadmin: `AdminApp.install(..., ["-cluster", "PayCluster"])`
- [ ] Update / Rollout / Partial update — rolling vs all-at-once

### Day 23 — ⚡ M7.2 + M7.3: Rollback + Scope Traps
- [ ] Application edition & rollback best practices in Prod
- [ ] Shared libs / datasources / J2C — Cluster vs Node vs Cell scope
- [ ] ⚠️ Classic 10-yr interview trap: scope conflicts
- [ ] 🏦 **Scenario:** Loan app v4.2 rolling update on 8-member cluster, zero downtime

### Day 24 — M8: Heap & Pool Sizing
- [ ] JVM heap per member — vertical cluster memory math (do the math on paper)
- [ ] Thread pools, connection pools per member

### Day 25 — M8: Plugin Tuning + Monitoring
- [ ] `ServerIOTimeoutRetry`, `RemoveSpecialHeaders`, plugin refresh
- [ ] Session replication performance impact
- [ ] PMI metrics + cluster health monitoring (Tivoli / Wily / AppDynamics)

### Day 26 — M10: Multi-Cell Architecture
- [ ] Active-Active vs Active-Passive across DCs
- [ ] Global LB (F5/GTM) → Web servers → Plugin → Cluster flow

### Day 27 — ⚡ M10: DR + Capacity
- [ ] DR drills: cluster startup order, DB sync, session behavior in DR
- [ ] Consolidation: 8.5.5 → 9.5.5 migration of clusters
- [ ] Capacity planning: members per workload (TPS math)

### Day 28 — 🔒 M9.1: Log Analysis
- [ ] SystemOut per member, plugin logs, trace on cluster routing
- [ ] Know these codes cold: `HSRR0046W`, `SRVE0297E` (session failover errors)

---

## 📅 WEEK 5 — Troubleshooting (Goldmine) + Interview Start

### Day 29 — 🔒 M9.2: Common Problems (Part 1)
- [ ] Cluster members inconsistent state / not starting
- [ ] Session not failing over → replication misconfig checklist

### Day 30 — 🔒 M9.2: Common Problems (Part 2)
- [ ] Plugin not routing to new member (stale plugin-cfg.xml)
- [ ] Split brain / core group issues
- [ ] Ripplestart hanging on one member — diagnosis steps

### Day 31 — 🔒 M9.3: Tools
- [ ] wsadmin script for cluster health check (write it, save it)
- [ ] jstack / javacore / thread dump analysis on a hung member

### Day 32 — 🔒 M9: Outage Scenario + Revision
- [ ] 🏦 **Scenario:** 2 AM outage — plugin 503s, member hung, javacore shows DB pool exhaustion → remove from plugin → fix → re-add
- ✅ *End-of-module: troubleshoot a simulated hung member from memory*

### Day 33 — M11: Interview Q&A (Part 1)
- [ ] 20 real interview questions (10-yr level) — Q1–Q10
- [ ] Answer aloud first, THEN check notes

### Day 34 — M11: Interview Q&A (Part 2) + Recent Issues
- [ ] Q11–Q20
- [ ] 5 "recent issues you faced" answers — write YOUR stories from M9 scenarios

### Day 35 — M11: Banking Scenarios
- [ ] 20 banking scenario-based questions
- [ ] Focus: payment failover, session failover, SWIFT/SIB, DR

---

## 📅 WEEK 6 — Interview Pack + Buffer

### Day 36 — M11: Troubleshooting Questions
- [ ] 20 troubleshooting-based questions
- [ ] Pattern: Symptom → Log check → Root cause → Fix → Prevention

### Day 37 — ⚡ M11: War Stories + Behavioral
- [ ] 5 real outage war stories (STAR format)
- [ ] 5 behavioral/leadership questions

### Day 38 — M11: Architecture/Design (Lead Level)
- [ ] 5 architecture/design questions
- [ ] Whiteboard one full bank topology from memory

### Day 39 — M11: Mock Practice
- [ ] Pick 5 random questions from all packs — answer aloud, no notes, record yourself
- [ ] Review weak answers

### Day 40 — 🔄 Buffer: Weak Areas + Hands-on Redo
- [ ] Revisit weakest 3 days from tracker above
- [ ] Redo hands-on: cluster create → ripplestart → plugin refresh → health check script

### Day 41 — 🔄 Buffer: Final Self-Mock
- [ ] Full mock: 10 questions across M2 / M5 / M9 + one architecture whiteboard
- [ ] Final read of M2/M5/M9 notes — these WILL be asked

---

## ✅ Progress Tracker

| Week | Days | Modules | Status |
|---|---|---|---|
| 1 | 1–7 | M1 + M2 🔒 | ⬜ |
| 2 | 8–14 | M3 + M4 + M6 | ⬜ |
| 3 | 15–21 | M6 + M5 🔒 | ⬜ |
| 4 | 22–28 | M7 + M8 + M10 + M9 🔒 | ⬜ |
| 5 | 29–35 | M9 🔒 + M11 | ⬜ |
| 6 | 36–41 | M11 + Buffer | ⬜ |

---

## 🛡️ Golden Rules

1. **Never compress M2 / M5 / M9** — these are the interview goldmine modules. If you miss a day, push buffer days instead.
2. **Every topic = Console + wsadmin.** Never learn only one way.
3. **Every topic gets a banking scenario** — payments, NetBanking, SWIFT, cards, EOD batch, DR drill.
4. **End-of-module test:** redo hands-on from memory before moving on.

> **Teaching order (locked):**
> Week 1 (M1→M2) → Week 2 (M3→M4→M6) → Week 3 (M6→M5🔒) → Week 4 (M7→M8→M10→M9🔒) → Week 5 (M9🔒→M11) → Week 6 (M11→Buffer)
