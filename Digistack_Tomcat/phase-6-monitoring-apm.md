# PHASE 6: Monitoring & APM (Day 149–171)
Goal: Prove system health with numbers; find any bottleneck in minutes with APM and Splunk.
⚠️ Install APM + Splunk trials THIS WEEK (Days 161–171) — not earlier.
Milestone Day 171: Live Grafana dashboard + cross-layer transaction tracing + Splunk query from memory.

## Module 19: Monitoring Strategy for Banks (Day 149–153)
| Day | Topic | Lab |
|---|---|---|
| 149 | The monitoring pyramid: JVM, connector, DB pool, response time, error rate | Build master metrics checklist for Tomcat farm |
| 150 | Alert philosophy: threshold vs anomaly; P1 vs P3 vs P4 | Write alert-severity matrix for payments farm |
| 151 | Alert fatigue: suppression, dedup, escalation windows | "One alert = one action" policy; 5 noisy alerts you'd kill |
| 152 | ⭐ SLA/SLO math: 99.9% = 43.2 min/month; error budgets | Build SLA calculator (99.9/99.95/99.99) |
| 153 | 🏦 Banking: "UPI 99.95%" → dashboard that PROVES it to management | Draft management dashboard: uptime %, incidents, MTTR |

## Module 20: JMX & Open-Source Monitoring (Day 154–160)
| Day | Topic | Lab |
|---|---|---|
| 154 | JMX architecture: MBeans, MBeanServer, secure remote JMX | Enable remote JMX securely; connect JConsole |
| 155 | ⭐ The MBeans you watch: ThreadPool, DataSource, Session stats | Watch all three under JMeter load; note saturation behavior |
| 156 | VisualVM remote monitoring | Connect via JMX; observe heap under load |
| 157 | JavaMelody: app-level visibility | Deploy; find slowest SQL and URIs |
| 158 | Prometheus + JMX Exporter | Attach agent; verify /metrics; scrape |
| 159 | Grafana: build your Tomcat dashboard (heap, GC, threads, pool, 5xx) | Complete dashboard — screenshots = interview proof |
| 160 | GC log analysis: -Xlog:gc*, gceasy.io | Load with G1GC; analyze pauses; write findings |

## Module 21: Enterprise APM — Dynatrace/AppDynamics (Day 161–165)
| Day | Topic | Lab |
|---|---|---|
| 161 | APM concepts: agents, Business Transactions, flow maps, snapshots | Install trial agent on your Tomcat |
| 162 | Reading a Business Transaction | Load test; trace one request through Tomcat internals |
| 163 | Finding bottlenecks: slow DB, slow external calls, hotspots | Artificial Thread.sleep (fake gateway); find it in <5 min |
| 164 | Distributed tracing: F5 → Tomcat → DB; correlation IDs | Trace ID in logs ↔ APM trace; end-to-end proof |
| 165 | 🏦 War-room redirect: "Net Banking slow" → 80% time in external credit-score API → redirect in 10 min | Write war story (STAR); practice "BT shows 5s — find the bottleneck" |

## Module 22: Splunk / ELK (Day 166–171)
| Day | Topic | Lab |
|---|---|---|
| 166 | Splunk concepts: index, sourcetype, search; Universal Forwarder | Ingest catalina.log + access log; basic searches |
| 167 | Search language: stats count by, timechart, top | Requests/hour, top URIs, response time distribution |
| 168 | ⭐ rex field extraction from access logs | Write rex patterns for your LogFormat; save fields |
| 169 | Error dashboards + alerting (SQLException spike → page DBA) | Error dashboard + Splunk alert (email/webhook) |
| 170 | ⭐ Cross-layer correlation: one transaction ID across 4 layers | Add X-Request-ID at all layers; search in Splunk |
| 171 | 🏦 Banking: "Payment failed at 2:15 PM" → found in 5 min across 4 layers | Run the drill; time yourself; ELK comparison; rehearse war story |

## ✅ Phase 6 Checkpoints
- Day 153: "What metrics for a payments Tomcat farm and what thresholds?" (with numbers)
- Day 160: 🏦 Grafana shows DB pool maxed daily 12–1 PM → capacity added BEFORE outage. Write as promotion story + "Which MBeans do you watch and why?"
- Day 165: Whiteboard APM flow: BT → flow map → snapshot → hotspot → verdict
- Day 171: Write a Splunk query: Tomcat 5xx trend per hour by backend instance (from memory)
