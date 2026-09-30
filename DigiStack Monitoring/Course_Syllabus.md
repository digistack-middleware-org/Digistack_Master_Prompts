# 📅 Day-Wise Study Plan — WebSphere Monitoring: Zero to Expert
## Prometheus + Grafana + ELK

> **17 Weeks | ~2–3 hrs/day (weekdays) + 4 hrs (Saturdays) | Sundays = Review/Rest**

---

## 🏁 PHASE 0: Foundations (Week 1 — Days 1–7)

| Day | Module | What You Do |
|-----|--------|-------------|
| Day 1 | 0.1 | WebSphere ND topology: Cell, DMGR, Node Agent, Cluster, IHS. Draw your own cell diagram from memory. |
| Day 2 | 0.1 | PMI deep dive: enable PMI in admin console, explore all metric categories. Read `server.xml`, `jvm.options` locations. |
| Day 3 | 0.1 | wsadmin hands-on: Jacl/Jython scripts to list servers, clusters, check status. "What dies vs what breaks" exercise. |
| Day 4 | 0.2 | Linux CPU: load average, `%wa` iowait, `top`, `vmstat`. Run them on a VM and interpret output. |
| Day 5 | 0.2 | Linux Memory & Disk: RSS vs virtual, `df -h`, `iostat`, ulimits. Why swap = death for JVM. |
| Day 6 | 0.2 | Network + practice: `netstat`, `ss`. Mini-lab: generate load with `stress`, watch all tools live. |
| Day 7 | Review | Self-test: 10 questions on Phase 0 (write answers, don't Google). Rest day off. |

---

## ⚙️ PHASE 1: JVM Fundamentals (Weeks 2–3 — Days 8–21)

### Week 2 (Days 8–14): Module 1.1 — JVM Memory & GC

| Day | Topic |
|-----|-------|
| Day 8 | Heap structure: Young Gen (Eden + 2 Survivor), Old Gen, Metaspace, Native memory. Draw the memory diagram from memory. |
| Day 9 | GC concepts: Minor vs Major vs Full GC. How objects move between generations. |
| Day 10 | GC Policies: gencon (default), balanced, optavgpause. When to choose which. Set gencon on lab JVM. |
| Day 11 | Verbose GC: enable `-verbose:gc`, read GC log lines, understand pause times & freed memory. |
| Day 12 | Memory leaks: sawtooth pattern, Old Gen climbing after Full GC. Lab: write app with static List leak. |
| Day 13 | OutOfMemoryError types: Java heap, native, metaspace, unable to create thread. Trigger each in lab. |
| Day 14 | Review + Heap dump & MAT: take heap dump, open in Eclipse MAT, find dominators. Find your Day-12 leak. |

### Week 3 (Days 15–21): Modules 1.2 & 1.3 — Threads + JDBC Pools

| Day | Topic |
|-----|-------|
| Day 15 | **Module 1.2:** Thread pools (WebContainer, ORB, Default, WMQ). Pool exhaustion = hung app. Configure pool sizes. |
| Day 16 | Thread dumps: generate javacore, read states (wait/lock/blocked/deadlock). Use IBM Thread & Monitor Dump Analyzer. |
| Day 17 | Hot threads: map high-CPU thread ID → javacore. Lab: busy-loop app, find the hot thread. |
| Day 18 | Module 1.2 lab: simulate WebContainer exhaustion (Thread.sleep servlet), watch pool hit 100%, take javacore, prove DB wait. |
| Day 19 | **Module 1.3:** JDBC connection pool theory — pool size, timeouts, prepared statement cache, waiters/queuing. |
| Day 20 | Module 1.3 lab: shrink pool to 5, run slow query, watch in-use=5, waiters queue, timeouts fire. |
| Day 21 | Phase 1 review: rebuild the "NEFT batch + slow DB" scenario on paper end-to-end. Self-test 15 questions. |

---

## 📊 PHASE 2: Prometheus (Weeks 4–6 — Days 22–42)

### Week 4 (Days 22–28): Module 2.1 — Prometheus Fundamentals

| Day | Topic |
|-----|-------|
| Day 22 | Pull vs push model, TSDB concepts, Prometheus architecture (server, exporters, Alertmanager, retention). |
| Day 23 | Install Prometheus from tarball + systemd unit. Write first `prometheus.yml`. |
| Day 24 | Targets, jobs, static config. Add node_exporter to a VM, verify in `/targets`. |
| Day 25 | Scrape intervals, retention flags, relabeling basics. |
| Day 27 | Lab: scrape 3 fake targets, break one, observe "target down". |
| Day 28 | Review + PromQL intro: metric types (gauge, counter, histogram, summary). |

### Week 5 (Days 29–35): Module 2.2 — JMX Exporter for WebSphere ★ Core Skill

| Day | Topic |
|-----|-------|
| Day 29 | JMX theory: MBeans, MBeanServer. WebSphere MBeans with JConsole. |
| Day 30 | Enable PMI: set Basic vs All, understand custom PMI levels, cost of "All". |
| Day 31 | Download `jmx_prometheus_javaagent`, attach via jvm.options/admin console JVM args. Verify `/metrics` endpoint. |
| Day 33 | Map WebSphere PMI MBeans: heap, WebContainer pools, JDBC pools, servlet response times, sessions. |
| Day 34 | Lab: full setup on your 2-JVM cluster — heap + thread + JDBC pool metrics visible in Prometheus. |
| Day 35 | Review: reproduce full JMX exporter setup from scratch in under 60 min (interview-level muscle memory). |

### Week 6 (Days 36–42): Modules 2.3 & 2.4 — PromQL + Alerting

| Day | Topic |
|-----|-------|
| Day 36 | PromQL: `rate()`, `irate()`, `increase()` on counters. Practice 10 queries node_exporter data. |
| Day 37 | PromQL: `_over_time`, `max_over_time`, `sum by()`, label filters, `offset`. |
| Day 38 | Banking queries: heap %, GC time %, `count(up==1)` cluster health, JVM comparisons. Write all 10 example queries from syllabus. |
| Day 39 | Histograms: p95/p99 response time math (`histogram_quantile`). |
| Day 40 | Alert rules: write YAML for JVM down, heap >85%, WebContainer >90%. `for:` clauses. |
| Day 41 | Alertmanager: install, routing trees, grouping, silences, escalation to email/Slack/PagerDuty. |
| Day 42 | Alert design lab: build the P1/P2/P3 table from the course, fire a test alert end-to-end. Learn rate vs irate answer cold. |

---

## 📊 PHASE 3: Grafana (Weeks 7–8 — Days 43–56)

### Week 7 (Days 43–49): Module 3.1 — Fundamentals + Start Dashboard

| Day | Topic |
|-----|-------|
| Day 43 | Install Grafana, add Prometheus datasource, explore UI, time ranges, auto-refresh. |
| Day 44 | Panels & visualization types: stat, timeseries, table, gauge, bar gauge. |
| Day 45 | Variables: `cluster‘,‘cluster`, `cluster‘,‘node`, `$server` dropdowns with `label_values()` queries. |
| Day 46 | Build Panel 1–3: Cluster health table (up/down green-red), Heap used vs max, GC time %. |
| Day 47 | Build Panel 4–6: WebContainer/ORB thread pools, JDBC pool + waiters, servlet response time (avg + p95). |
| Day 48 | Build Panel 7–9: Session count, host CPU/Mem/Disk (node exporter), live transaction rate. |
| Day 49 | Polish: rows, thresholds coloring, repeat panels per server, import community JVM dashboard and compare. |

### Week 8 (Days 50–56): Module 3.3 — Alerting, Variables, Reporting

| Day | Topic |
|-----|-------|
| Day 50 | Grafana alerting vs Prometheus alerting — pros/cons, which one for a bank and why (pick one source of truth). |
| Day 51 | Build Grafana alert on heap panel + notification channel (email/Teams webhook). |
| Day 52 | Dashboard folder & permission structure: prod vs non-prod, viewer/editor roles for NOC vs devs. |
| Day 53 | Scheduled PDF reports for weekly capacity review (reporting plugin/setup). |
| Day 54 | Build the "NOC Wall Board": one screen, 400 JVMs green/red — recreate with your 2-JVM lab. |
| Day 55 | Kiosk mode + TV setup practice; document your dashboard with panel descriptions. |
| Day 56 | Phase 3 review: rebuild entire dashboard from blank in one session. |

---

## 🔍 PHASE 4: ELK Stack (Weeks 9–12 — Days 57–84)

### Week 9 (Days 57–63): Module 4.1 — Elasticsearch

| Day | Topic |
|-----|-------|
| Day 57 | Elasticsearch concepts: index, document, inverted index (how search works). |
| Day 58 | Install single-node ES, verify with curl, understand JVM heap settings for ES. |
| Day 59 | Shards, replicas, cluster health (green/yellow/red). Create index with custom shards. |
| Day 60 | Multi-node cluster in lab (3 nodes), roles: master, data, ingest. |
| Day 61 | Mappings & data types: keyword vs text, why mappings matter for Kibana. |
| Day 62 | ILM: hot → warm → delete policy. Build a 30-day delete policy for logs (1-year audit = snapshot tier). |
| Day 63 | Review + disk sizing math: 50GB/day logs → what cluster do you need? |

### Week 10 (Days 64–70): Module 4.2 — Filebeat & Logstash

| Day | Topic |
|-----|-------|
| Day 64 | Beats architecture: Filebeat vs Metricbeat. Install Filebeat on WebSphere VM. |
| Day 65 | Filebeat config: paths (SystemOut.log), fields, tags, output to Logstash. |
| Day 66 | Multiline codec — Java stack traces as one event. Practice until flawless (this takes time). |
| Day 67 | Logstash pipeline: input → filter → output. First working pipeline ES ← Logstash ← Filebeat. |
| Day 68 | Grok patterns: parse WebSphere SystemOut.log format (timestamp, thread, level, message). |
| Day 69 | Filters: mutate, drop noisy lines, add fields (hostname, env, app). |
| Day 70 | PCI masking lab: grok + replace to mask 16-digit card numbers in logs. |

### Week 11 (Days 71–77): Module 4.3 — Kibana

| Day | Topic |
|-----|-------|
| Day 71 | Index patterns, Discover, KQL basics: search exceptions, filter by server. |
| Day 72 | KQL advanced: AND/OR, wildcards, field ranges. Search "customerID AND Exception" scenario. |
| Day 73 | Visualizations: line (errors over time), pie (exception types), table (top failing URIs). |
| Day 74 | Build "Error Command Center" dashboard — pie + line + top-10 slow URI table. |
| Day 75 | Compare ERROR count vs yesterday (timelion/offset) — deploy-goes-bad detection. |
| Day 76 | Kibana alerting: threshold anomaly detection rules. |
| Day 77 | Role-based access in Kibana: read-only auditor role, dev vs prod spaces. |

### Week 12 (Days 78–84): Module 4.4 — WebSphere Log Strategy

| Day | Topic |
|-----|-------|
| Day 78 | SystemOut.log rotation, FFDC, activity.log — what each contains, what to ship. |
| Day 79 | trace.log: how to enable, its CPU/disk cost, why prod = INFO not ALL. |
| Day 80 | Ship GC logs to ELK; correlate GC pauses with response-time alerts. |
| Day 81 | Correlation ID strategy: web server → plugin → JVM → EJB → DB. Configure log correlation. |
| Day 82 | Lab: trace one transaction across 3 tiers using one correlation ID in Kibana. |
| Day 83 | Log volume tuning: drop DEBUG lines in Logstash, measure savings. |
| Day 84 | Phase 4 review: full lab — generate app exception → see it in Kibana in <1 min → build alert. |

---

## 🏗️ PHASE 5: Integration & Architecture (Weeks 13–14 — Days 85–98)

### Week 13 (Days 85–91): Modules 5.1 & 5.2 — Full Architecture + HA

| Day | Topic |
|-----|-------|
| Day 85 | Draw the complete architecture on paper: 400 JVMs → JMX exporter → Prometheus → Alertmanager → PagerDuty + Grafana; logs → Filebeat → Logstash → ES → Kibana. |
| Day 86 | Explain the "car dashboard vs flight recorder" analogy — Grafana answers WHERE, Kibana answers WHY. |
| Day 87 | Prometheus HA: 2 instances, same targets, dedup at Alertmanager/Grafana — why not in TSDB. |
| Day 88 | Alertmanager clustering: gossip protocol, ha_peer setup, 3-instance cluster, dedup behavior. |
| Day 89 | Elasticsearch HA: 3 master-eligible nodes, replica=1, snapshot lifecycle. Take first snapshot to NFS/S3. |
| Day 90 | Grafana HA: Postgres/MySQL backend, 2 instances behind LB, session sharing. |
| Day 91 | Retention & sizing math: samples/sec × retention × bytes/sample. Calculate disk for your lab, then extrapolate to 400 JVMs. |

### Week 14 (Days 92–98): Module 5.3 — DC-DR Monitoring

| Day | Topic |
|-----|-------|
| Day 92 | Active-Active vs Active-Passive DR concepts; what monitoring each site needs. |
| Day 93 | Separate monitoring for DR site; how Grafana shows both DCs (datasource per site or federated). |
| Day 94 | DR drill watch-list: replication lag, cold-cache heap behavior, JIT warmup, thread pools spiking. |
| Day 95 | The "expected 15-minute cold start" pattern — document it as a runbook so juniors don't panic. |
| Day 96 | Lab: simulate DR — stop "Mumbai" JVMs, watch "Chennai" JVMs absorb load in Grafana, note the cold-start graph. |
| Day 97 | Interview prep on HA/DR: practice whiteboard answers for "Prometheus dies?" and "Design monitoring for 500 JVMs across 2 DCs." |
| Day 98 | Phase 5 review: draw the ENTIRE architecture + HA design from memory on a whiteboard. Film yourself explaining it in 10 min. |

---

## 🎓 PHASE 6: Expert Level (Weeks 15–16 — Days 99–112)

### Week 15 (Days 99–105): Modules 6.1 & 6.2 — Capacity + Tuning

| Day | Topic |
|-----|-------|
| Day 99 | `predict_linear()`, `avg_over_time()` forecasting. Run 30-day heap forecast on your lab. |
| Day 100 | When to add JVMs: heap trend, thread trend, response-time trend — write the decision rules. |
| Day 101 | Build your first monthly capacity report (PDF): current usage, trend line, recommendation. |
| Day 102 | GC policy selection using GC graphs: gencon vs balanced for your workload profile. |
| Day 103 | Heap sizing — the 70% rule; thread pool tuning with percent settings, queue + stall time. |
| Day 104 | Session tuning + DynaCache monitoring; object cache hit/miss metrics. |
| Day 105 | Plugin/web tier: IHS access logs → ELK; lab: simulate uneven routing (one JVM 2× traffic), detect it in Grafana, fix weights. |

### Week 16 (Days 106–112): Modules 6.3–6.5 — Security, Runbooks, Capstone

| Day | Topic |
|-----|-------|
| Day 106 | **Module 6.3:** PCI-DSS in monitoring — masking rules, audit finding case study. Verify your Day-70 masking works. |
| Day 107 | RBAC everywhere: Grafana roles, Kibana roles, read-only auditors; TLS on exporters/Prometheus/ES. |
| Day 108 | **Module 6.4:** Write runbooks for your 5 core alerts (VM down, heap, thread pool, response time, disk). Every alert → runbook link. |
| Day 109 | Build the symptom→diagnosis→ map from the course as your personal cheat sheet. |
| Day 110 | Capstone 1+2: 3-VM lab with full stack (if not done), deploy sample app, enable PMI + JMX exporter, full dashboard. |
| Day 111 | Capstone 3+4: Deliberate memory leak → Grafana → alert → heap dump → MAT. Filebeat → multiline → Kibana stack-trace search. |
| Day 112 | Capstone 5+6: DB slowdown simulation → thread/JDBC alerts → runbook. full solo outage drill: break something, fix using ONLY dashboards/logs, write the RCA doc. |

---

## 🎤 PHASE7: Interview Mastery (Week 17 — Days 113–119)

| Day | Activity |
|-----|----------|
| Day 113 | Screening round prep: architecture + HA questions (Modules 2.1, 5.1, 5.2). Answer aloud, record yourself. |
| Day 114 | Technical round prep: PromQL drills — write 15 queries from memory. rate irate, histogram_quantile, predict_linear cold. |
| Day 115 | Scenario round: "Payment app slow — troubleshoot" full walkthrough using Module 6.4 map. Practice the golden story: "Net banking slow at 11 AM." |
| Day 116 | War stories: write YOUR 5 stories (leak, pool exhaustion, DR drill, disk-full, bad deploy) in RCA format — claimable experience. |
| Day 117 | Design round: whiteboard "500 JVMs, 2 DCs" — practice 3 times until under 10 min, clean, and confident. |
| Day 118 | Final mock interview (self or friend), gap list, then say "Generate Phase 2" for the 20 Q&A + scenario bank. |

---

## ✅ Daily Habits (Throughout)

- **30 min PromQL practice daily** from Day 36 onward — non-negotiable
- **One banking scenario re-told aloud** each review day (payday peak, NEFT batch, DR drill)
- **Every hands-on lab ends with:** one screenshot for your "evidence portfolio" (interviews love this)
- **Sundays:** rest + 15-question self-test only

---

## 📈 Progress Tracker

| Phase | Weeks | Days | Status |
|-------|-------|------|--------|
| Phase 0 — Foundations | 1 | 1–7 | ⬜ |
| Phase 1 — JVM Fundamentals | 2–3 | 8–21 | ⬜ |
| Phase 2 — Prometheus | 4–6 | 22–42 | ⬜ |
| Phase 3 — Grafana | 7–8 | 43–56 | ⬜ |
| Phase 4 — ELK Stack | 9–12 | 57–84 | ⬜ |
| Phase 5 — Integration & HA | 13–14 | 85–98 | ⬜ |
| Phase 6 — Expert Level | 15–16 | 99–112 | ⬜ |
| Phase7 — Interview Mastery | 17 | 113–119 | ⬜ |

> Mark ⬜ → ✅ as you complete each phase. Good luck! 🚀
