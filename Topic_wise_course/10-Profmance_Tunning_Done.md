# ⚡ WebSphere Performance Tuning — Zero to Expert
## JVM • Threads • Connection Pools • Memory • GC • Database • PMI • Load Testing
**25-Yr WAS Admin/Architect @ Global Bank | WAS 8.5.5 / 9.0 ND | BankCell01**

> **Goal:** Crack a 10-yr level interview AND tune production alone without help.
> **Format:** Theory 80% → Banking Scenario 15% → Interview 5% | **Console + wsadmin every topic**
> **Running lab:** BankCell01 — DMGR, Node01/Node02, **PaymentCluster** (UPI/cards, latency-sensitive),
> **CustPortalCluster** (internet banking, traffic spikes on salary day)

---

## 📗 PHASE 1 — PERFORMANCE FOUNDATIONS (Zero Level) — Days 1–5

| # | Topic |
|---|---|
| 1 | What is "performance"? Response time vs throughput vs capacity — the bank teller analogy |
| 2 | The performance tuning lifecycle: measure → analyze → tune → validate (never tune blind!) |
| 3 | Where time goes: browser → network → web → app → DB — the latency waterfall |
| 4 | Key WAS components that affect performance: JVM, threads, pools, caches |
| 5 | PMI (Performance Monitoring Infrastructure) — what it is, why it's your first stop |
| 6 | Tivoli Performance Viewer (TPV) — how to watch a server live (console steps) |
| 7 | Baseline: what "normal" looks like for PaymentCluster — you can't fix what you don't baseline |
| 8 | Latency vs load vs memory issues — how to tell which one you have (the 3 disease types) |
| 9 | Lab: Enable PMI, open TPV, read live stats for PaymentCluster |

---

## 📗 PHASE 2 — JVM & MEMORY TUNING (⭐ Interview Gold) — Days 6–13

| # | Topic |
|---|---|
| 10 | JVM memory model: heap, native, metaspace — the 64-bit reality |
| 11 | Heap structure: nursery (eden + survivor) vs tenured — IBM J9 model explained simply |
| 12 | Garbage collection basics: what GC does, why pauses kill banking transactions |
| 13 | GC policies: `-Xgcpolicy:gencon` (default), `optavgpause`, `balanced`, `optthruput` — which and why |
| 14 | `-Xms / -Xmx` sizing — the golden rules, why ms=mx in production |
| 15 | Verbose GC: enabling, reading verbosegc.log, free heap %, GC frequency & pause times |
| 16 | Heap dumps: what causes OutOfMemoryError (heap vs native OOM), generating & analyzing dumps |
| 17 | Memory leak basics: how a leak looks in verbose GC (sawtooth that never recovers) |
| 18 | Thread dumps: javacore files — reading them, finding stuck threads & deadlocks |
| 19 | Native memory & `-Xlp` large pages — when heap is fine but server still dies (native OOM war story) |
| 20 | Lab: Set heap for PaymentCluster, enable verbose GC, read a real GC log |

---

## 📗 PHASE 3 — THREAD POOLS & WORKLOAD — Days 14–18

| # | Topic |
|---|---|
| 21 | Web container thread pool — how HTTP requests get served (minimum/maximum) |
| 22 | Sizing web container threads — the math (threads × DB connections × backend calls) |
| 23 | Other thread pools: ORB, default, J2C — what runs where |
| 24 | Stuck threads: detection, hung thread notification (`WSVR0605W`), stuckThreadCount |
| 25 | Thread dump analysis deep-dive: BLOCKED/WAITING states, finding the culprit in javacore |
| 26 | Lab: Load PaymentCluster, watch thread pool usage, trigger & analyze a javacore |

---

## 📗 PHASE 4 — CONNECTION POOLS & BACKEND TUNING — Days 19–25

| # | Topic |
|---|---|
| 27 | DataSource connection pool: min/max, how connections are borrowed & returned |
| 28 | Pool sizing: max connections vs DB capacity — the bank branch counter analogy |
| 29 | Connection timeout, wait vs fail behavior — pool exhausted (`J2CA0021E` / `DSRA8020E`) |
| 30 | Orphan/stale connection settings — reap time, unused timeout, aged timeout |
| 31 | Prepared statement cache — the easy 10% win everyone forgets |
| 32 | JDBC driver tuning: DB2/Oracle URL properties that matter in banks |
| 33 | MQ connection pooling & JMS tuning for payment messaging |
| 34 | J2C pool monitoring — console + TPV + wsadmin (`AdminControl j2c stats`) |
| 35 | Lab: Tune PaymentDB pool under load, observe exhausted-pool behavior safely |

---

## 📗 PHASE 5 — PMI, MONITORING & TOOLS DEEP-DIVE — Days 26–30

| # | Topic |
|---|---|
| 36 | PMI levels: none/basic/extended/all — overhead vs visibility (bank rule: never "all" in PROD) |
| 37 | The metrics that matter: response time, throughput, heap %, pool %, thread % — thresholds table |
| 38 | TPV / Health Center / IBM Support Assistant — tool walkthrough |
| 39 | Monitoring automation: `perfServlet` + scripts to capture metrics 24×7 |
| 40 | Session management tuning: session timeout, in-memory replication overhead, session cache |
| 41 | Dynamic caching (dynacache): what it is, cacheinstances, how CustPortal cuts DB load 60% |
| 42 | Lab: Build a daily metrics capture script for PaymentCluster |

---

## 📗 PHASE 6 — APPLICATION-LEVEL & INFRA TUNING — Days 31–35

| # | Topic |
|---|---|
| 43 | HTTP session vs stateless design — why payment APIs should be stateless |
| 44 | Web container tuning: keep-alive, max keep-alive requests, persistent connections |
| 45 | IHS/plugin-level factors: worker threads, plugin load balancing weight |
| 46 | Classloader & app packaging effects on startup & memory |
| 47 | Compression, static offload, CDN (concept level) — why banks offload static content |
| 48 | EJB/ORB tuning basics (ORB thread pool, pass-by-reference) — where still relevant |
| 49 | Lab: Baseline vs tuned comparison — measure the improvement |

---

## 📗 PHASE 7 — LOAD TESTING & CAPACITY PLANNING — Days 36–39

| # | Topic |
|---|---|
| 50 | Load test basics: steady state, peak (salary-day spike), stress, soak tests |
| 51 | Reading load test results: TPS, 90th/99th percentile response times (why averages lie!) |
| 52 | Capacity planning: "how many users can PaymentCluster handle?" — the sizing method |
| 53 | Vertical vs horizontal scaling — when to add heap vs add nodes |
| 54 | Lab: Simulate load, find PaymentCluster's breaking point, write a capacity report |

---

## 📗 PHASE 8 — ⭐ PERFORMANCE TROUBLESHOOTING (Interview Gold) — Days 40–48

*Each: Symptom → Investigation → Tools → Logs (GC/javacore/SystemOut codes) → Fix → Validation*

| # | Scenario |
|---|---|
| 55 | Slow response at peak hours only — CPU fine, what next? (methodical approach) |
| 56 | `OutOfMemoryError: Java heap` — leak vs undersize vs one-off — decision tree |
| 57 | Native OOM — heap has room but process dies (`WSVR0605W` + CORE files) |
| 58 | Server hangs after days — stuck threads → javacore → DB lock discovery |
| 59 | Connection pool exhaustion during salary-day spike — `DSRA8020E` war story |
| 60 | High CPU on one cluster member only — uneven load, javacore sampling |
| 61 | GC pauses > 2 seconds killing SLAs — gencon tuning walk-through |
| 62 | Slow DB queries blamed on WAS — how to prove it's SQL (with evidence!) |
| 63 | Memory leak found via week-long verbose GC — approach + hot fixes |
| 64 | After-deployment slowdown — classloader/session changes, dynacache invalidation |
| 65 | 🧪 Gauntlet: 5 mock performance incidents — you diagnose from logs/metrics |

---

## 📗 PHASE 9 — PRODUCTION PROCEDURES — Days 49–52

| # | Topic |
|---|---|
| 66 | Safe tuning change procedure: baseline → ticket → change → measure → keep/rollback |
| 67 | One change at a time — the rule that saves your job |
| 68 | Quarterly performance review — the report every bank expects |
| 69 | Peak-season readiness (festival/salary days): proactive pool & heap review |
| 70 | DR performance parity — tuned PROD vs untuned DR = failover surprise |

---

## 📗 PHASE 10 — EXPERT / LEAD CAPSTONE — Days 53–55

| # | Topic |
|---|---|
| 71 | Performance architecture: full-stack tuning map (IHS→plugin→WAS→DB) — draw & defend |
| 72 | Autonomic requests / workload management tuning in ND |
| 73 | **FINAL PROJECT:** Tune BankCell01 end-to-end — baseline report → tuned configs → before/after evidence pack |

---

## 🎯 PHASE 11 — INTERVIEW MASTERY PACK (after course)

| # | Pack | Count |
|---|---|---|
| 1 | Real interview Q&A (10-yr level) | 20 |
| 2 | "Recent performance issues you faced" answers | 5 |
| 3 | Banking scenario-based questions | 20 |
| 4 | Troubleshooting-based questions | 20 |
| 5 | Outage war stories (STAR format) | 5 |
| 6 | Behavioral/Leadership questions | 5 |
| 7 | Architecture/Design (Lead level) | 5 |

---

## 📅 Schedule Summary (~55 days @ 1 hr/day ≈ 11 weeks)

| Days | Phase | Pace |
|---|---|---|
| 1–5 | Foundations | 🐢 Full |
| 6–13 | ⭐ JVM & Memory | 🐢 Full — untouched |
| 14–18 | Thread Pools | 🐢 Full |
| 19–25 | Connection Pools & Backends | 🐢 Full |
| 26–30 | PMI & Monitoring | 🐢 Full |
| 31–35 | App & Infra Tuning | ⚡ Compressed |
| 36–39 | Load Testing & Capacity | 🐢 Full |
| 40–48 | ⭐ Troubleshooting | 🐢 Full — untouched |
| 49–52 | Procedures | ⚡ Compressed |
| 53–55 | Capstone | ⚡ Compressed |

---

## ✅ Completion Rule Per Topic

- [ ] Theory understood
- [ ] Console step done
- [ ] wsadmin step done
- [ ] Banking scenario discussed
- [ ] Interview Qs answered
- [ ] → Say `NEXT`

## 🔖 Commands

| Command | Action |
|---|---|
| `START` / `NEXT` | Move to next topic |
| `QUIZ` | Get assessed on current topic |
| `REVIEW <topic#>` | Revisit a topic |
| `EXERCISE` | Extra hands-on practice |

---
