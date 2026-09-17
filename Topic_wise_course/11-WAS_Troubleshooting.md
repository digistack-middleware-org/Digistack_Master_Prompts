# ⚡ WebSphere Troubleshooting Mastery — 100-Day Plan

> **Goal:** From absolute zero to expert-level WebSphere Application Server ND (8.5.5 / 9.0) production troubleshooting.
> **Focus:** Banking / Financial Services production scenarios.
> **Trainer:** Ox Alpha

---

## ⚠️ Conditions for the 100-Day Plan

| Factor | Requirement |
|--------|-------------|
| Daily time | **3 hours/day** (not 2) |
| Days/week | **6 days + 1 flexible buffer day per week** |
| Lab | **Mandatory** — no lab, no 100-day plan possible |
| Style | Faster pace, same rule: **no phase skipped, no assessment skipped** |
| Revision | Built-in as quick 20-min recall drills instead of full buffer days |

> ⚠️ If you can only give 2 hrs/day, use the 230-day plan instead.

---

## 📌 Legend

| Icon | Meaning |
|------|---------|
| 📖 | Learn topic |
| 🧪 | Hands-on lab |
| 📝 | Assessment |
| 🔄 | Recall / revision drill |

---

## 🟢 BLOCK 1 — FOUNDATIONS (Days 1–8)

*Phase 1 + start of Phase 2*

| Day | Topic |
|-----|-------|
| 1 | Topic 1: What is WAS troubleshooting + 🧪 lab VM setup begins |
| 2 | Topic 2: Methodology + finish WAS ND install |
| 3 | Topic 3: Incident → Symptom → Evidence → Root Cause → Resolution |
| 4 | Topics 4–5: Failing layer identification + App/JVM/WAS/Network/DB/MQ |
| 5 | Topics 6–7: Symptom vs cause vs root cause + first-response procedure |
| 6 | Topics 8–10: Evidence collection, production mindset, severity assessment |
| 7 | 📝 **Phase 1 Assessment** + recall drill |
| 8 | Topic 11: Log architecture + 🧪 locate all log dirs in lab |

---

## 🟢 BLOCK 2 — LOGS (Days 9–16)

*Phase 2*

| Day | Topic |
|-----|-------|
| 9 | Topics 12–13: SystemOut + SystemErr — anatomy of a log line |
| 10 | Topics 14–15: FFDC + application logs |
| 11 | Topics 16–18: IHS logs, plugin log, trace logs |
| 12 | Topics 19–22: JVM logs, native logs, locations, rotation |
| 13 | Topics 23–25: Timestamps, reading errors, finding FIRST error |
| 14 | Topics 26–27: Cross-log correlation + timeline building + first/secondary/cascading errors |
| 15 | 🧪 Lab: Break something, identify first error vs cascade |
| 16 | 📝 **Phase 2 Assessment** |

---

## 🟢 BLOCK 3 — WAS ND TROUBLESHOOTING (Days 17–26)

*Phase 3*

| Day | Topic |
|-----|-------|
| 17 | Topic 28: DMGR troubleshooting + 🧪 stop/start/kill DMGR drill |
| 18 | Topics 29–30: Node Agent + sync problems + 🧪 force sync failure |
| 19 | Topics 31–32: App server/JVM + cluster states |
| 20 | Topics 33–36: Deployment state + start/stop/restart failures |
| 21 | Topics 37–39: JVM unavailable / hung / crash |
| 22 | Topics 40–43: Unexpected restarts, member unavailable, one-JVM-works, config inconsistency |
| 23 | 🧪 Lab: Full ND failure drill — kill nodeagent + DMGR, recover in order |
| 24 | 📖 8.5.5 vs 9.0 ND differences |
| 25 | Scenario drills: 3 ND incidents (written) |
| 26 | 📝 **Phase 3 Assessment** |

---

## 🟢 BLOCK 4 — APP STARTUP + CLASSLOADERS (Days 27–38)

*Phase 4 + Phase 5*

| Day | Topic |
|-----|-------|
| 27 | Topics 44–46: Won't start / starts-stops / init failure |
| 28 | Topics 47–49: EJB + servlet + listener init failures |
| 29 | Topics 50–52: Dependency failure + timeout + slow startup |
| 30 | Topics 53–54: Functionality fails + deployed-but-unavailable |
| 31 | 🧪 Lab: Break a deployed EAR 5 ways, fix each |
| 32 | 📝 **Phase 4 Assessment** + classloader fundamentals intro |
| 33 | Topics 55–56: ClassNotFoundException vs NoClassDefFoundError |
| 34 | Topics 57–58: NoSuchMethodError + ClassCastException |
| 35 | Topics 59–61: LinkageError + UnsupportedClassVersionError + jar conflicts |
| 36 | Topics 62–65: Duplicates, WAS vs app libs, Parent First/Last + 🧪 flip classloading |
| 37 | Topics 66–69: Shared libs, classloader policy, Java version compatibility |
| 38 | 📝 **Phase 5 Assessment** |

---

## 🟢 BLOCK 5 — HTTP / IHS / PLUGIN (Days 39–50)

*Phase 6*

| Day | Topic |
|-----|-------|
| 39 | Topics 70–71: Request flow + 🧪 install IHS + plugin |
| 40 | Topics 72–74: 200 / 301/302 / 400 |
| 41 | Topics 75–77: 401 / 403 / 404 |
| 42 | Topics 78–79: 500 / 502 |
| 43 | Topics 80–81: 503 / 504 — elimination chains |
| 44 | Topics 82–86: IHS + plugin + plugin-cfg.xml + generation/propagation |
| 45 | 🧪 Lab: Regenerate + propagate plugin, watch routing in plugin log |
| 46 | Topics 87–90: Routing, virtual hosts, host aliases, context roots |
| 47 | Topics 91–97: URI mapping, cluster routing, backend, timeouts, stale config |
| 48 | 🧪 Lab: The 4 classic scenarios (404 started, 503 started, direct-vs-IHS, one-member routing) |
| 49 | 🔄 Recall + scenario drills |
| 50 | 📝 **Phase 6 Assessment** — live lab troubleshooting |

---

## 🟡 BLOCK 6 — JVM TROUBLESHOOTING (Days 51–62)

*Phase 7*

| Day | Topic |
|-----|-------|
| 51 | Topics 98–100: JVM health, CPU, memory overview |
| 52 | Topics 101–105: High heap, OOM types, heap/metaspace/native |
| 53 | Topics 106–108: GC, long GC, Full GC + 🧪 verbose GC lab |
| 54 | Topics 109–111: Thread exhaustion, hung threads, pool exhaustion |
| 55 | Topics 112–114: Conn pool exhaustion + JVM restart vs crash |
| 56 | Topics 115–119: Dump types + which dump to collect |
| 57 | 🧪 Lab: Thread dump → find hung thread in javacore |
| 58 | 🧪 Lab: Heap dump → find biggest object in Memory Analyzer |
| 59 | Topic 120: Practical dump interpretation |
| 60 | 🧪 Lab: Memory-leak simulation → capture evidence |
| 61 | Drill: 5 "heap or threads?" decision cases |
| 62 | 📝 **Phase 7 Assessment** |

---

## 🟡 BLOCK 7 — PERFORMANCE + JDBC (Days 63–74)

*Phase 8 + Phase 9*

| Day | Topic |
|-----|-------|
| 63 | Topics 121–123: Slow app/response/intermittent |
| 64 | Topics 124–126: High CPU/memory + long GC |
| 65 | Topics 127–128: Thread pool + JDBC pool saturation |
| 66 | Topics 129–135: JMS/network/DB/MQ/external latency + bottleneck layer |
| 67 | Drill: App vs WAS vs DB vs Network elimination |
| 68 | 🧪 Lab: Artificial slow servlet → measure time per layer |
| 69 | 📝 **Phase 8 Assessment** |
| 70 | Topics 136–138: JDBC architecture + DataSource + JNDI failures |
| 71 | Topics 139–142: Connection failure, auth, timeout, pool exhaustion |
| 72 | Topics 143–146: Stale connections, DB down, slow DB, leaks + 🧪 leak lab |
| 73 | Topics 147–150: Driver problems, compatibility, SQL failures, UAT-vs-Prod |
| 74 | 📝 **Phase 9 Assessment** |

---

## 🟡 BLOCK 8 — JMS/MQ + SECURITY (Days 75–84)

*Phase 10 + Phase 11*

| Day | Topic |
|-----|-------|
| 75 | Topics 151–154: JMS architecture, CF, queues, JNDI |
| 76 | Topics 155–158: MQ connection, auth, channels, listeners |
| 77 | Topics 159–162: Delivery, backlog, consumers, JMS pools |
| 78 | Topics 163–165: Rollback, poison messages, "app works but no processing" + 🧪 lab |
| 79 | 📝 **Phase 10 Assessment** |
|  | Topics 166–168: AuthN vs AuthZ, 401 vs 403 |
| 81 | Topics 169–172: LDAP connectivity/auth/group mapping/role mapping |
| 82 | Topics 173–177: App security, JAAS, credentials, env differences |
| 83 | 🧪 Lab: Break LDAP URL, diagnose from evidence |
| 84 | 📝 **Phase 11 Assessment** |

---

## 🟡 BLOCK 9 — SSL + DEPLOYMENT (Days 85–93)

*Phase 12 + Phase 13 + Phase 14*

| Day | Topic |
|-----|-------|
| 85 | Topics 178–183: SSL basics, keystores, truststores, chains, expiry, wrong cert |
| 86 | Topics 184–188: Missing trust, handshake failures, TLS/cipher mismatch, hostname |
| 87 | Topics 189–191: Backend/WAS/IHS HTTPS + 🧪 SSL debug trace lab |
| 88 | 📝 **Phase 12 Assessment** |
| 89 | Topics 192–198: Install/update failures, won't start, old version, sync failure |
| 90 | Topics 199–205: Config mismatch, classloader/plugin/JDBC/JMS/SSL after deploy + UAT-vs-Prod + 🧪 one-member-old-version lab |
| 91 | 📝 **Phase 13 Assessment** |
| 92 | Topics 206–214: wsadmin, SOAP, scripts, AdminApp, Jython, save errors, sync scripting |
| 93 | 📝 **Phase 14 Assessment** |

---

## 🔴 BLOCK 10 — EXPERT LEVEL (Days 94–100)

*Phase 15 + Phase 16 + Phase 17 + Phase 18 + Phase 19 + Phase 20*

| Day | Topic |
|-----|-------|
| 94 | Phase 15: Banking incidents part 1 (payment down, login fail, 500 API, txn timeout) |
| 95 | Phase 15 part 2 (503, MQ stuck, DB pool exhausted, CPU 100%) |
| 96 | Phase 16: Full incident process + escalation matrix → 📝 narrative incident role-play |
| 97 | Phase 17: RCA, 5 Whys, timeline → 📝 write full RCA document |
| 98 | Phase 18: Advanced diagnostics — dumps deep dive, leaks, intermittent failures, correlation |
| 99 | Phase 19–20: Multi-symptom expert scenarios + team-blame evidence-based resolution |
| 100 | 🏁 **FINAL EXAMS**: Practical exam + incident simulation + senior admin interview (mock) |

---

## ✅ Daily Routine Template (3 hours)

| Time | Activity |
|------|----------|
| 0:00–0:20 | Recall yesterday's topic from memory (write it down) |
| 0:20–1:50 | Today's topic (teaching + discussion) |
| 1:50–2:40 | Hands-on lab (if scheduled) |
| 2:40–3:00 | Answer assessments / update troubleshooting journal |

---

## 🏁 Final Deliverables (Day 100)

- [ ] WebSphere Troubleshooting Practical Exam
- [ ] Production Incident Simulation
- [ ] RCA Exercise (full RCA document)
- [ ] Senior WebSphere Administrator Interview (mock)
- [ ] Expert Troubleshooting Interview (mock)
- [ ] Interview-readiness plan + CV/project articulation guidance

---

## 📜 Ground Rules

1. One topic at a time — never skip ahead.
2. Every assessment must be completed before moving on.
3. Keep a troubleshooting journal throughout.
4. Lab work is non-negotiable.
5. Never randomly restart JVMs or change configs — evidence first.
