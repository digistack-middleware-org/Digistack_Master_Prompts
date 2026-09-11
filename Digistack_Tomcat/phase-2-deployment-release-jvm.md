# PHASE 2: Deployment, Release & JVM (Day 26–48)
Goal: Run bank releases with rollback plans; read thread/heap dumps like a pro.
Milestone Day 48: "App hanging — what do you do?" flows naturally. ⭐ #1 interview topic: JVM.

## Module 4: Deployment & Release Management (Day 26–34)
| Day | Topic | Lab |
|---|---|---|
| 26 | Manager App: enable, secure, IP restrict | Enable Manager on DEV instance; access via browser |
| 27 | Manager Text API: curl (deploy, start, stop, sessions) | Deploy/undeploy/reload entirely via curl |
| 28 | CI/CD: Jenkins pipeline concept for WAR deploys | Build fake pipeline (script + curl) end-to-end |
| 29 | Zero-downtime: Blue-Green, rolling restart, session drain | Simulate blue-green: 2 instances + Apache LB |
| 30 | Versioning & rollback (bank 15-min rollback SLA) | Deploy v2, rollback to v1 — must be <5 min |
| 31 | 🏦 Banking: Saturday release window role-play (10PM–2AM) | Write full change doc: steps, rollback, validation |
| 32 | Environment hierarchy: DEV→SIT→UAT→PROD; config drift | Create 3 env configs; document differences |
| 33 | Change management: CAB, ServiceNow, rollback plan docs | Write PROD change ticket template (bank style) |
| 34 | Hardening baseline: remove default apps, shutdown port security | Harden instance: remove docs/examples/manager |

## Module 5: JVM & Performance ⭐ (Day 35–48)
| Day | Topic | Lab |
|---|---|---|
| 35 | JVM memory: heap, Metaspace, stack; Xms/Xmx | Set heap flags via setenv.sh; verify with jinfo |
| 36 | Memory generations: young, old, survivor spaces | Draw memory diagram; watch live in VisualVM |
| 37 | GC collectors: Serial, Parallel, G1GC, ZGC | Run ParallelGC then G1GC; compare GC logs |
| 38 | GC log analysis (gceasy.io) | Generate load; analyze GC logs; find pause times |
| 39 | setenv.sh discipline; common JVM flag mistakes | Migrate all flags to setenv.sh properly |
| 40 | Thread dumps: jstack, kill -3 | Take 5 dumps under load; diff them |
| 41 | Reading dumps: RUNNABLE, WAITING, BLOCKED, deadlocks | Analyze a dump; find stuck threads |
| 42 | Thread dump + CPU mapping: top -H → nid | Force 100% CPU busy loop; find guilty thread |
| 43 | Heap dumps: jmap -dump, MAT tool | Capture heap dump; open in MAT; dominator tree |
| 44 | Memory leaks: create one on purpose (static collection) | Leak memory → OOM → catch via heap dump |
| 45 | 🏦 Banking: Monday 9AM full-GC freeze — full RCA | Reproduce: small heap + load = freeze; document RCA |
| 46 | Metrics & baselines: response time, throughput, thread/pool/heap | Build baseline checklist doc |
| 47 | JMX: JConsole/VisualVM remote; JavaMelody | Enable secure remote JMX; monitor live load test |
| 48 | Prometheus JMX Exporter + Grafana; capacity planning ("10x load?") | Metrics → Grafana dashboard; sizing exercise |

## ✅ Phase 2 Checkpoint (Day 48)
- Mock interview: "App is hanging — what do you do?" (thread dumps ×3 → compare → verdict)
- War stories so far: minimum 5 (GC freeze RCA is mandatory)
- Release checkpoint: full "release" with change doc + rollback plan
