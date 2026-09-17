# WebSphere Application Server (WAS) Admin — Interview Prep
## Zero → 10-Yr Expert | Banking Environment (Citibank-style MNC Bank)

> Format: Every topic = "What interviewers ACTUALLY ask" + 1 banking example to quote in your answer
> Daily commitment: 2–3 hours | End each day with "Say it Loud" practice

---

## 📑 TABLE OF CONTENTS

- [Section 1: Basics (1st Round)](#-section-1-basics)
- [Section 2: Core Admin (2nd Round)](#-section-2-core-admin)
- [Section 3: Security](#-section-3-security)
- [Section 4: Troubleshooting ⭐⭐](#-section-4-troubleshooting-)
- [Section 5: Clustering, HA & Performance](#-section-5-clustering-ha--performance)
- [Section 6: Scripting (wsadmin + Jython)](#-section-6-scripting)
- [Section 7: Upgrades & Migration](#-section-7-upgrades--migration)
- [Section 8: Architect / Lead Round](#-section-8-architect--lead-round)
- [Interview Prep Priority Order](#-interview-prep-priority-order)
- [30-Day Day-wise Plan](#-30-day-websphere-interview-prep-plan)
- [Daily Habit](#-daily-habit-all-30-days--15-min)

---

## 🔹 SECTION 1: BASICS (Fresh_check questions — 1st round)

| # | Topic | Interview Questions Asked |
|---|-------|---------------------------|
| 1 | JVM, JDK, JRE | What is JVM? Heap vs Stack? What is GC? |
| 2 | Web Server vs App Server | Why IHS + WAS? What is the plug-in? |
| 3 | WAS Architecture ⭐ | Explain Cell, Node, DMGR, NodeAgent, AppServer. What is a Profile? |
| 4 | Topology | Standalone vs Federated? Vertical vs Horizontal cluster? |
| 5 | Ports | Key WAS ports (9043 admin, 9080/9443 app, 7277/8880 SOAP)? How to find them? |
| 6 | Directory structure | Important files/folders under profiles? Where is SystemOut.log? |

**Banking line to say:**
> "In our bank, netbanking hits IHS → plug-in routes to a 6-node WAS cluster → app pulls customer data from DB."

---

## 🔹 SECTION 2: CORE ADMIN (2nd round — must-answer-all)

| # | Topic | Interview Questions |
|---|-------|---------------------|
| 7 | Profile creation | `manageprofiles.sh` commands? How to federate a node (`addNode.sh`)? |
| 8 | DMGR issues | DMGR down — does app traffic stop? (NO — trick question!) NodeAgent down impact? |
| 9 | Application deployment | WAR vs EAR? Deploy steps via console AND wsadmin? How to rollback a deploy? |
| 10 | Virtual host | What is virtual host? Context root? |
| 11 | Plug-in ⭐ | What is plugin-cfg.xml? How to regenerate/propagate? How does plug-in route requests? Session affinity? |
| 12 | Data sources | JDBC provider vs DataSource? Connection pool settings? "Connection wait timeout" error? |
| 13 | JMS / SIBus / MQ | What is SIBus? Queue vs Topic? How WAS connects to IBM MQ? (Banking = payments) |
| 14 | JVM tuning | `-Xms -Xmx`, GC policies (gencon), verbose:gc, where to set them? |
| 15 | Session management | Session affinity, memory-to-memory replication, DB session persistence? |
| 16 | Class loading | PARENT_FIRST vs PARENT_LAST? When did YOU use it? |

---

## 🔹 SECTION 3: SECURITY (Banking interviews LOVE this section)

| # | Topic | Interview Questions |
|---|-------|---------------------|
| 17 | Global security | Admin security on/off? LTPA? What is federated repository / LDAP? |
| 18 | SSL ⭐ | Truststore vs Keystore? Signer certificate? How to add/import certs? "Certificate expired — app down — what did you do?" |
| 19 | J2C alias | What is it? Where used? (DB auth) |
| 20 | App security | Role mapping, JAAS? |
| 21 | Compliance | PCI-DSS/SOX impact on your patching & access? |

---

## 🔹 SECTION 4: TROUBLESHOOTING ⭐⭐ (THE 10-YR DIFFERENTIATOR — 50% of senior interviews)

| # | Issue | Expected Answer |
|---|-------|-----------------|
| 22 | OutOfMemoryError | Verbose GC → heapdump → MAT analysis → leak vs undersized heap |
| 23 | Server HUNG / stuck threads | Thread dump via `kill -3` → ThreadDumpAnalyzer → find blocked thread → usually DB/pool |
| 24 | High CPU | Multiple thread dumps → find hot thread (`top -H -p`) |
| 25 | Slow response | Where do you check first? (Thread pool, connection pool, GC, DB) |
| 26 | NodeAgent down | Steps to restart, `syncNode.sh` |
| 27 | App not reachable | End-to-end path: LB → IHS → plug-in → WAS — check each hop |
| 28 | Log files | SystemOut, FFDC, trace.log — when do you enable trace? |
| 29 | Deployment failed | Common causes (context root clash, JDBC, memory) |
| 30 | SSL handshake failure | Which logs, how to debug (`-Djavax.net.debug`) |

**Golden rule to say:**
> "First I check SystemOut.log and thread dump before restarting anything — restart hides root cause."

---

## 🔹 SECTION 5: CLUSTERING, HA & PERFORMANCE

| # | Topic | Interview Questions |
|---|-------|---------------------|
| 31 | Clustering | How to create a cluster? Rolling restart — why? |
| 32 | WLM | How does load balancing work in WAS? Round-robin? |
| 33 | DR | What is your DR setup? RTO/RPO? How do you sync configs to DR? |
| 34 | DynaCache | What is it? Where used? |
| 35 | PMI / Tivoli / Dynatrace | How do you monitor WAS in production? |

---

## 🔹 SECTION 6: SCRIPTING (wsadmin + Jython)

| # | Topic | Interview Questions |
|---|-------|---------------------|
| 36 | wsadmin objects | AdminApp vs AdminTask vs AdminConfig vs AdminControl — explain each |
| 37 | Jython scripts | Write a script to: change heap, restart a server, deploy an EAR (asked live!) |
| 38 | Automation | How did you automate in your bank? (Jenkins/Ansible = bonus) |

---

## 🔹 SECTION 7: UPGRADES & MIGRATION (Senior-level questions)

| # | Topic | Interview Questions |
|---|-------|---------------------|
| 39 | Fixpacks | How to apply fixpack/iFix? Rollback? Downtime process? |
| 40 | WAS 8.5.5 → 9.x | In-place vs side-by-side migration — which and why? |
| 41 | Java upgrade | Java 7 → 8 migration issues you faced? |

---

## 🔹 SECTION 8: ARCHITECT / LEAD ROUND (Final round)

| # | Question Type | Examples |
|---|---------------|----------|
| 42 | Design | "Design WAS infra for a new payments app expecting 500 TPS" |
| 43 | Sizing | How many nodes? Heap? Cluster topology? DMZ layout? |
| 44 | War story | "Tell me about the worst outage you handled" (Prepare 3!) |
| 45 | Leadership | "Jr admin can't fix an issue at 2 AM — what do you do?" |
| 46 | Modernization | Liberty profile, containers — have you evaluated? |

---

## 🎯 INTERVIEW PREP PRIORITY ORDER

1. **Troubleshooting (Section 4)** — decides 10-yr hiring
2. **Architecture (Section 1) + Plug-in + Clustering** — asked every time
3. **SSL/Cert + Data source issues** — most common real incidents
4. **wsadmin live scripting** — 2nd round filter
5. **3 prepared war stories** — memory-leak outage, expired-cert outage, month-end slow-batch story

---

# 📅 30-DAY WebSphere Interview Prep Plan (Zero → 10-Yr Level)

---

## 🗓 WEEK 1 — FOUNDATIONS + ARCHITECTURE (Section 1)

### Day 1 — JVM, JDK, JRE
- [ ] Learn: JVM vs JDK vs JRE, Heap vs Stack, GC basics (gencon), classloading basics
- [ ] Prepare answer: "What happens when a Java app starts?"
- [ ] 🗣 Say it loud: Explain JVM to a non-IT person in 2 min

### Day 2 — Web Server vs App Server + IHS + Plug-in intro
- [ ] Learn: IHS role, why web server in DMZ, what plug-in does (plugin-cfg.xml overview)
- [ ] Banking line: Netbanking request flow (IHS → plug-in → WAS → DB) — **memorize this flow today, use it all 30 days**

### Day 3 — WAS Architecture ⭐ (MOST IMPORTANT DAY)
- [ ] Learn: Cell, Node, DMGR, NodeAgent, AppServer, Profile — draw the diagram 3 times on paper
- [ ] Trick Q: "DMGR down = traffic stopped?" → NO (apps keep running)
- [ ] 🗣 Practice: Full topology explanation in under 3 minutes

### Day 4 — Topology + Profiles
- [ ] Standalone vs Federated, Vertical vs Horizontal clusters
- [ ] `manageprofiles.sh` commands: `-create -templatePath ...`, delete, listProfiles
- [ ] `addNode.sh` federation steps

### Day 5 — Ports + Directory Structure
- [ ] Ports: 9043/9044 (admin console), 9080/9443 (app), 8880 (SOAP), 7277 (ORB), 9353 (bootstrap), 2809 (CORBA)
- [ ] How to find: `serverindex.xml`, `netstat`
- [ ] Key paths: `/opt/IBM/WebSphere/AppServer/profiles/<profile>/logs/<server>/SystemOut.log`
- [ ] Files: `server.xml`, `plugin-cfg.xml`, `resources.xml`, `security.xml`

### Day 6 — REVISION DAY 1
- [ ] Re-draw: Cell/Node/DMGR diagram + request flow from memory
- [ ] Self-quiz all Section 1 questions (write answers, don't just read)
- [ ] ✅ Checkpoint: Answer all 6 Section-1 topics without notes

### Day 7 — REST / Buffer / Catch-up

---

## 🗓 WEEK 2 — CORE ADMIN (Section 2)

### Day 8 — DMGR/NodeAgent operations
- [ ] start/stop: `startManager.sh`, `startNode.sh`, `startServer.sh`
- [ ] NodeAgent down impact, `syncNode.sh`, when to use `removeNode.sh`
- [ ] Trick Qs: DMGR down impact vs NodeAgent down impact

### Day 9 — Application Deployment
- [ ] WAR vs EAR vs JAR, deploy via console (memorize clicks) AND wsadmin (`AdminApp.install`)
- [ ] Rollback: previous version, `AdminApp.update`
- [ ] Virtual host + context root — how URL maps (`/netbanking`)

### Day 10 — Plug-in Deep Dive ⭐
- [ ] plugin-cfg.xml structure (ServerCluster, URI groups, Route)
- [ ] Generate & propagate (when/why), plug-in refresh interval
- [ ] Session affinity (JSESSIONID), retry behavior, failover

### Day 11 — Data Sources
- [ ] JDBC provider vs DataSource vs J2C alias
- [ ] Pool settings: min/max, connection timeout, reap time
- [ ] Error drills: "Connection wait timeout exceeded", "No pooled connection available"

### Day 12 — JMS / SIBus / MQ
- [ ] SIBus concepts: bus, bus member, queues, topics, connection factories, activation specs
- [ ] WAS ↔ IBM MQ (MQ link, client channel)
- [ ] Banking story: stuck SWIFT payment messages in queue

### Day 13 — JVM Tuning + Session Management
- [ ] `-Xms/-Xmx`, gencon, verbose:gc, where to set (console + server.xml)
- [ ] Session affinity vs replication: memory-to-memory vs DB persistence
- [ ] Class loading: PARENT_FIRST vs PARENT_LAST + real use case (app needs newer jar than WAS ships)

### Day 14 — REVISION DAY 2
- [ ] Self-quiz all 10 Section-2 topics
- [ ] 🗣 Say it loud: "Deploy an EAR" end-to-end + "regenerate plugin" — full answers
- [ ] ✅ Checkpoint: Section 2 done

---

## 🗓 WEEK 3 — SECURITY + TROUBLESHOOTING ⭐⭐

### Day 15 — Global Security + LDAP
- [ ] Admin security, LTPA tokens (expiry, keys), federated repositories, LDAP/AD integration
- [ ] "Admin console password forgot?" recovery answer

### Day 16 — SSL/Certificates ⭐
- [ ] Keystore vs Truststore vs Keyring, signer certs, chained certs
- [ ] Import cert steps (console + `security.xml` location)
- [ ] Expired cert outage story — **write your own version today, reused in interviews**

### Day 17 — J2C alias, App security, Compliance
- [ ] Role mapping, JAAS, J2C alias for DB auth
- [ ] PCI-DSS/SOX: patching SLAs, least privilege, audit evidence

### Day 18 — Troubleshooting Part 1: Memory
- [ ] OOM: verbose GC → heapdump (`-Xdump`), MAT analysis, leak vs sizing
- [ ] High CPU: `top -H -p` → nid → thread dump matching

### Day 19 — Troubleshooting Part 2: Hung/Slow
- [ ] Thread dumps: `kill -3`, javacore files, ThreadDumpAnalyzer, stuck threads, deadlocks
- [ ] Slow response checklist order: thread pool → connection pool → GC logs → DB
- [ ] FFDC, trace.log, when to enable trace

### Day 20 — Troubleshooting Part 3: Connectivity
- [ ] App not reachable: LB → IHS → plug-in → WAS hop-by-hop checks
- [ ] NodeAgent down recovery, SSL handshake failure (`-Djavax.net.debug=ssl`)
- [ ] Deployment failure causes

### Day 21 — REVISION DAY 3 ⭐
- [ ] Self-quiz all Section 3 + 4 questions
- [ ] 🗣 Prepare & rehearse your **3 war stories**: memory-leak outage, expired-cert outage, month-end slow batch
- [ ] ✅ Checkpoint: You can handle the 10-yr troubleshooting round

---

## 🗓 WEEK 4 — CLUSTERING, SCRIPTING, UPGRADES, ARCHITECT

### Day 22 — Clustering + WLM
- [ ] Create cluster (steps), cluster members, rolling restart logic
- [ ] Round-robin WLM, weight-based routing

### Day 23 — DR + HA + Performance
- [ ] Active-passive DR, RTO/RPO, config sync to DR, core groups/HAManager basics
- [ ] DynaCache, PMI, Tivoli Performance Viewer, Dynatrace

### Day 24 — wsadmin + Jython Part 1
- [ ] AdminApp vs AdminTask vs AdminConfig vs AdminControl (memorize one-line definition each)
- [ ] **Write by hand:** script to change heap, restart server, deploy EAR (asked live!)

### Day 25 — wsadmin Part 2 + Automation
- [ ] Run scripts: `wsadmin.sh -lang jython -f script.py`
- [ ] Automation story: Jenkins/Ansible pipeline deploy, bulk JVM property change across 40 servers

### Day 26 — Fixpacks + Migration
- [ ] Fixpack/iFix apply via Installation Manager (`imcl`), rollback, downtime window, pre/post checks
- [ ] WAS 8.5.5 → 9.x: in-place vs side-by-side (say: side-by-side for banks = safe rollback)
- [ ] Java 7→8 issues (GC behavior, deprecated APIs)

### Day 27 — Architect Round Prep
- [ ] Design answer: 500 TPS payments app → sizing (nodes, heap, cluster, DMZ layout) — **write your standard design answer once, reuse**
- [ ] Liberty/containers modernization talking points

### Day 28 — Behavioral/Leadership Prep
- [ ] 2 AM issue with junior admin, outage communication, change management (CR process), conflicting priorities
- [ ] Prepare 5 answers using STAR format (Situation-Task-Action-Result)

### Day 29 — FULL MOCK INTERVIEW
- [ ] Have someone (or self-record) ask all 46 questions randomly
- [ ] Mark any question you fumbled → revise today

### Day 30 — FINAL REVISION
- [ ] Re-read: war stories (3), design answer, troubleshooting playbook, Jython scripts
- [ ] 🗣 Say every golden line out loud:
  - "I check logs and thread dumps before restarting — restart hides root cause"
  - "DMGR down doesn't stop traffic"
  - "In our bank, netbanking hits IHS → plug-in → 6-node cluster → DB"

---

## 📊 Daily Habit (all 30 days — 15 min)

- [ ] 1 random troubleshooting question → answer out loud
- [ ] 1 Jython snippet → write from memory
- [ ] Update your "weak list" notebook → clear it on revision days

---

## 🏦 GOLDEN BANKING LINES (Memorize — quote these in every interview)

| Situation | What to Say |
|-----------|-------------|
| Request flow | "In our bank, netbanking hits IHS → plug-in routes to a 6-node WAS cluster → app pulls customer data from DB" |
| Troubleshooting | "I check SystemOut.log and thread dumps before restarting — restart hides root cause" |
| DMGR trick question | "DMGR down doesn't stop traffic — it's only for config changes" |
| Payments | "SWIFT payment messages flow via IBM MQ → our WAS apps pick them up — a stuck queue means failed transfers" |
| Migration | "For banks, we prefer side-by-side migration — instant rollback if cutover fails" |

---

*Last updated: [date] | Target: 10-Yr WAS Admin/Architect Interview — Big MNC Bank*
