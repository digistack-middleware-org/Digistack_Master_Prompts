# WAS Senior Admin — Progress Log (v5, complete)

Upload this alongside the syllabus in Project Knowledge. Update it yourself after each session (or ask Claude to update it and re-upload), so any new chat in the project knows exactly where you left off.

**How to use:** Mark topics ✅ as completed, note the date, and add a one-line note if something needs revisiting. When a module's overall lab + its 22-sprint incident batch are done, check the module row too.

**v4 changelog:** Fixed missing Foundational IT Literacy rows (was tracking 3 of 10 items); corrected stale day-range labels on all Supplementary blocks to match the current Day-by-Day-Plan numbering. No status data was lost — all fixes are structural/labeling only.

**v5 changelog (full gap-analysis pass, cross-checked against all 5 project files):**
- Relabeled all 24 module "6+ Incidents" rows to "Incidents (22-Sprint batch)" to match the 22-Sprint Incident Delivery Structure in Project Instructions §4 (superseded the old flat "6+ incidents" model).
- Added Arc Review Flash Drill rows (Days 38/139/191/225/334/396) to all 6 Arc Milestones tables — these existed in the day plan but had no tracking row.
- Added new Module 0 tracking block (Day 1: Topics 0.1/0.2/0.3) — previously untracked.
- Added Day 81 wsadmin Survival Primer (6.0-A) tracking row before Module 6 — previously untracked.
- Added Day 105 SSL Foundations (8.3-A) tracking row inside Module 8 — previously untracked.
- Added Day 214 MQ Standalone Install & MQSC Basics Lab as its own tracked Supplementary block inside Module 16 (previously only Days 221–223 MQ Deep Dive was tracked).
- Corrected all remaining stale Supplementary day-range headers: MQ Deep Dive → 221–223; External LBs → 244–245; Identity Protocols → 271–272; Dynacache/Core Dumps → 286–291; Liberty/Migration → 313–317; CI/CD → 331–332; Compliance Named Frameworks → 376–377; Career Readiness → 397–400.
- Added two new standalone tracking rows for Enterprise Incident Response & On-Call — Full Intro (Day 38-A, after Arc 1) and Senior Refresh (Day 334-A, after Arc 5) — these sessions previously existed in Project Instructions but not in the day plan or this tracker; both are now present in all three files.
No prior ✅/date status data was lost — all v5 fixes are structural/labeling/insertion only.

---

## 🔒 Locked Environment Baseline (set Day 0 — do not change without updating this file)

| Item | Value |
|---|---|
| WAS version anchor | **9.0.5.28 ND** (IBM J9 JDK 8) — upgraded from original 8.5.5 plan; all architecture/labs are version-agnostic between the two, differences called out explicitly when they matter |
| WAS install path | **`/apps/IBM/WebSphere/AppServer`** (non-default — use this path in every command, not `/opt/IBM/...`) |
| OS | RHEL 8 |
| OS process owner | **`wasadmin`** (non-root) — WAS was initially installed as root; ownership was reassigned to `wasadmin` and all commands run as `su - wasadmin` from Day 0 onward |
| Disk layout | **Default / profile-embedded** (no separate `/wasdata`, `/logs`, `/tranlog` mounts) — documented as an intentional gap, to be discussed conceptually in Module 13 (Backup Strategy) and Module 27 (Audit Readiness) as "what I'd change and why" |
| Current profile | `AppSrv01` (standalone) — confirmed present under `/apps/IBM/WebSphere/AppServer/profiles/` |
| Host RAM | 16 GB total |

**VM plan (revised, DMGR/Node1 split into separate VMs):**

| VM | RAM |
|---|---|
| digistack-dmgr | 2 GB |
| digistack-node1 | 2 GB |
| digistack-node2 | 2 GB |
| IHS | 1 GB |
| PostgreSQL | 2 GB |
| IBM MQ | 1.5 GB |
| Monitoring | 1.5 GB |
| ELK Stack | 1.5 GB |
| **Total (Arc 6 peak)** | **13.5 GB** |

*Tomcat VM removed from plan — not used anywhere in this course; repurpose as second IHS node for Module 17.13 (IHS HA) if desired.*

> **Note on `05-Day-by-Day-Plan-RETROFITTED.md`:** Day 0 and Day 1 in that file still reference the pre-upgrade "WAS 8.5.5 ND" text from before the version anchor was locked to 9.0.5.28. When you next touch that file, update:
> - Day 0 → "...smoke test: install **WAS 9.0.5.28 ND**, create standalone profile, start server, hit Admin Console login..."
> - Day 1 → "Topic 0.1 Version Anchor (**WAS 9.0.5.28 ND primary** — 8.5.5 ND legacy-awareness only / Liberty awareness-only / IBM J9 JDK 8 / RHEL 8 primary, AIX called out) + 0.2 Rule Going Forward + 0.3 start Version Divergence Cheat Sheet"

---

## Day 0 — Pre-Lab Environment Setup

| Item | Status | Date | Notes |
|---|---|---|---|
| VM planning / disk layout reviewed | ✅ | | Went with Option A: default layout, documented gap |
| OS prerequisites / ulimits | ✅ | | |
| wasadmin OS user created, non-root | ✅ | | Fixed after initial root install |
| IIM install | ✅ | | |
| WAS ND install | ✅ | | 9.0.5.28, path `/apps/IBM/WebSphere/AppServer` |
| Standalone profile created (AppSrv01) | ✅ | | |
| Server starts / Admin Console reachable | ☐ | | **Pending final verification — paste versionInfo.sh / manageprofiles.sh output to confirm and close Day 0** |
| DigiStack naming convention locked | ✅ | | See syllabus / Day-by-Day-Plan file for full table |

**Day 0 status: Nearly complete — awaiting final command output to formally close.**

---

## ARC 1 — Foundation & Architecture

### Module 0: Target Environment Declaration (Day 1)
| Topic | Status | Date | Notes |
|---|---|---|---|
| 0.1 Version Anchor | ☐ | | |
| 0.2 Rule Going Forward | ☐ | | |
| 0.3 Version Divergence Cheat Sheet (start) | ☐ | | |

### Module 1: WAS Strategic Context
| Topic | Status | Date | Notes |
|---|---|---|---|
| 1.1 Product Family | ☐ | | |
| 1.2 Fix Pack Strategy | ☐ | | |
| 1.3 IIM Internals | ☐ | | |
| 1.4 vs Competitors | ☐ | | |
| 1.5 Admin's Real Role | ☐ | | |
| 1.6 IBM Support Engagement | ☐ | | |
| 1.7 Interview Trap Questions | ☐ | | |
| **Module 1 Overall Lab** | ☐ | | |
| **Module 1: Incidents (22-Sprint batch)** | ☐ | | |

**Supplementary — Foundational IT Literacy** (Days 12–21)
| Item | Status | Date | Notes |
|---|---|---|---|
| ITIL basics (incident/problem/change vocabulary) | ☐ | | |
| ServiceNow / Jira / Confluence ticket flow | ☐ | | |
| Java fundamentals for admins (JDK/JRE/JVM, Heap, GC concept) | ☐ | | |
| File permissions & ownership (chmod/chown/umask) | ☐ | | WAS profile/keystore/log permission failure modes |
| Process management (ps -ef, kill -3 for javacore, nohup, process trees) | ☐ | | Starting/stopping/diagnosing hung JVMs |
| Log tailing & searching (tail -f, grep -A/-B, awk, sed) | ☐ | | SystemOut.log/FFDC/native_stderr.log triage |
| ulimit (nofile, nproc) | ☐ | | #1 cause of "too many open files" incidents under load |
| netstat/ss, lsof -i | ☐ | | Port conflicts, orphaned listeners, "address already in use" |
| df -h, mount points, /proc basics | ☐ | | Disk-full-on-log-partition incidents, /proc/<pid>/limits |
| top/vmstat/iostat fundamentals | ☐ | | Baseline CPU/memory/IO reading before Module 20 |

### Module 2: Architecture Internals
| Topic | Status | Date | Notes |
|---|---|---|---|
| 2.1 Cell/Node/Server Internals | ☐ | | |
| 2.2 DMGR Internals | ☐ | | |
| 2.3 Node Agent Internals | ☐ | | |
| 2.4 Config Repository | ☐ | | |
| 2.5 Scope Hierarchy | ☐ | | |
| 2.6 Port Architecture | ☐ | | |
| 2.7 ORB & IIOP | ☐ | | |
| 2.8 Admin Console Internals | ☐ | | |
| 2.9 Process Communication | ☐ | | |
| 2.10 Config Locking | ☐ | | |
| 2.11 MBean Architecture | ☐ | | |
| 2.12 Architecture Design Decisions | ☐ | | |
| **Module 2 Overall Lab** | ☐ | | |
| **Module 2: Incidents (22-Sprint batch)** | ☐ | | |

### Arc Milestones
| Milestone | Status | Date | Notes |
|---|---|---|---|
| **Arc 1 Capstone** — Design a cell topology for a stated banking workload; defend every choice | ☐ | | |
| **Arc 1 Review Flash Drill** (Day 38) | ☐ | | |
| **Enterprise Incident Response & On-Call — Full Intro** (Day 38-A) | ☐ | | |

---

## ARC 2 — Build & Deploy

### Module 3: Installation
| Topic | Status | Date | Notes |
|---|---|---|---|
| 3.1 Pre-Install Decisions | ☐ | | |
| 3.2 OS Hardening | ☐ | | |
| 3.3 IIM in Depth | ☐ | | |
| 3.4 Silent Install | ☐ | | |
| 3.5 Multi-Component Install | ☐ | | |
| 3.6 Fix Pack Upgrade | ☐ | | |
| 3.7 versionInfo.sh | ☐ | | |
| 3.8 Install Rollback | ☐ | | |
| 3.9 Install Documentation | ☐ | | |
| 3.10 Common Install Failures | ☐ | | |
| **Module 3 Overall Lab** | ☐ | | |
| **Module 3: Incidents (22-Sprint batch)** | ☐ | | |

### Module 4: Standalone Profile
| Topic | Status | Date | Notes |
|---|---|---|---|
| 4.1 Profile Architecture | ☐ | | |
| 4.2 Profile Directory Files | ☐ | | |
| 4.3 JVM Architecture | ☐ | | |
| 4.4 JVM Sizing | ☐ | | |
| 4.5 Process Management | ☐ | | |
| 4.6 Environment Variables | ☐ | | |
| 4.7 Logging Config | ☐ | | Revisit disk-layout gap here (WAS_LOGDIR) |
| 4.8 Admin Security Day 1 | ☐ | | |
| **Module 4 Overall Lab** | ☐ | | |
| **Module 4: Incidents (22-Sprint batch)** | ☐ | | |

### Module 5: Deployment Concepts / ClassLoader
| Topic | Status | Date | Notes |
|---|---|---|---|
| 5.1 EAR/WAR/JAR Packaging | ☐ | | |
| 5.2 ClassLoader Deep Dive | ☐ | | |
| 5.3 ClassLoader Diagnosis | ☐ | | |
| 5.4 Virtual Host Architecture | ☐ | | |
| 5.5 ibm-web-bnd.xml vs web.xml | ☐ | | |
| 5.6 Shared Libraries | ☐ | | |
| 5.7 Edition Control | ☐ | | |
| 5.8 Descriptor Hierarchy | ☐ | | |
| **Module 5 Overall Lab** | ☐ | | |
| **Module 5: Incidents (22-Sprint batch)** | ☐ | | |

### Supplementary — wsadmin Survival Primer (Day 81)
| Item | Status | Date | Notes |
|---|---|---|---|
| 6.0-A wsadmin Survival Primer (launch wsadmin, Jython basics, AdminConfig/AdminControl/AdminApp/AdminTask overview, AdminConfig.save(), end-to-end start/status/stop example) | ☐ | | |

### Module 6: Deploy to Standalone
| Topic | Status | Date | Notes |
|---|---|---|---|
| 6.1 Deployment Strategy Types | ☐ | | |
| 6.2 wsadmin Deployment | ☐ | | |
| 6.3 Deployment Scripts | ☐ | | |
| 6.4 App Validation | ☐ | | |
| 6.5 Rollback Procedure | ☐ | | |
| 6.6 Deployment Logging | ☐ | | |
| 6.7 Zero-Downtime Concepts | ☐ | | |
| 6.8 Config via wsadmin | ☐ | | |
| **Module 6 Overall Lab** | ☐ | | |
| **Module 6: Incidents (22-Sprint batch)** | ☐ | | |

### Module 7: DMGR & Custom Profile
| Topic | Status | Date | Notes |
|---|---|---|---|
| 7.1 DMGR Internals | ☐ | | |
| 7.2 Custom Profile Internals | ☐ | | |
| 7.3 Profile Strategy | ☐ | | |
| 7.4 DMGR HA | ☐ | | |
| 7.5 Creating Profiles Production Way | ☐ | | |
| 7.6 Naming Conventions | ☐ | | |
| 7.7 Post-Creation Baseline | ☐ | | |
| **Module 7 Overall Lab** | ☐ | | |
| **Module 7: Incidents (22-Sprint batch)** | ☐ | | |

### Module 8: Federation
| Topic | Status | Date | Notes |
|---|---|---|---|
| 8.1 Federation Internals | ☐ | | |
| 8.2 Pre-Federation Checklist | ☐ | | |
| 8.3-A SSL Foundations for WAS Admins (keystore vs truststore, self-signed vs CA-signed, handshake basics, DMGR-to-Node-Agent trust) | ☐ | | |
| 8.3 Trust/Certificates | ☐ | | |
| 8.4 addNode Parameters | ☐ | | |
| 8.5 Post-Federation Verification | ☐ | | |
| 8.6 removeNode | ☐ | | |
| 8.7 Federation in Pipelines | ☐ | | |
| 8.8 Federation Troubleshooting | ☐ | | |
| 8.9 Multi-Node Architecture | ☐ | | |
| **Module 8 Overall Lab** | ☐ | | |
| **Module 8: Incidents (22-Sprint batch)** | ☐ | | |

### Module 9: Synchronization
| Topic | Status | Date | Notes |
|---|---|---|---|
| 9.1 Sync Architecture | ☐ | | |
| 9.2 Sync Protocol | ☐ | | |
| 9.3 Auto Sync Config | ☐ | | |
| 9.4 Manual Sync Scenarios | ☐ | | |
| 9.5 syncNode.sh | ☐ | | |
| 9.6 Sync Failure Diagnosis | ☐ | | |
| 9.7 Partial Sync/Split-Brain | ☐ | | |
| 9.8 Configuration Drift | ☐ | | |
| 9.9 Sync/Deploy Timing | ☐ | | |
| **Module 9 Overall Lab** | ☐ | | |
| **Module 9: Incidents (22-Sprint batch)** | ☐ | | |

### Module 10: Deploy to Federated Server
| Topic | Status | Date | Notes |
|---|---|---|---|
| 10.1 Managed App Server | ☐ | | |
| 10.2 Deployment Targeting | ☐ | | |
| 10.3 Deploy to Managed Server | ☐ | | |
| 10.4 Sync After Deploy | ☐ | | |
| 10.5 App State Management | ☐ | | |
| 10.6 Multi-Server Non-Cluster | ☐ | | |
| 10.7 Troubleshooting Managed Server | ☐ | | |
| **Module 10 Overall Lab** | ☐ | | |
| **Module 10: Incidents (22-Sprint batch)** | ☐ | | |

### Arc Milestones
| Milestone | Status | Date | Notes |
|---|---|---|---|
| **Arc 2 Capstone** — Federate 2 nodes, deploy an app, diagnose an injected sync failure | ☐ | | |
| **Arc 2 Review Flash Drill** (Day 139) | ☐ | | |

---

## ARC 3 — Scale & Availability

### Module 11: Clustering
| Topic | Status | Date | Notes |
|---|---|---|---|
| 11.1 Cluster Design Architecture | ☐ | | |
| 11.2 Cluster Internals | ☐ | | |
| 11.3 Port Management | ☐ | | |
| 11.4 rippleStart vs Full Start | ☐ | | |
| 11.5 Config Propagation | ☐ | | |
| 11.6 Cluster SPOF | ☐ | | |
| 11.7 Cross-Node Clustering | ☐ | | |
| 11.8 Weights/Load Distribution | ☐ | | |
| 11.9 Health Monitoring | ☐ | | |
| 11.10 Anti-Patterns | ☐ | | |
| 11.11 Core Groups & HAManager Architecture | ☐ | | |
| 11.12 Workload Management (WLM) — Web Container & EJB Tier | ☐ | | |
| 11.13 Work Manager, Asynchronous Beans & Resource Environment Providers | ☐ | | |
| 11.14 On Demand Router (ODR) & Intelligent Management Concepts | ☐ | | |
| 11.15 Dynamic Clusters (ND Auto-Scaling) | ☐ | | |
| **Module 11 Overall Lab** | ☐ | | |
| **Module 11: Incidents (22-Sprint batch)** | ☐ | | |

### Module 12: Deploy to Cluster
| Topic | Status | Date | Notes |
|---|---|---|---|
| 12.1 Cluster Deploy Internals | ☐ | | |
| 12.2 Deployment Options | ☐ | | |
| 12.3 Rolling Deployment | ☐ | | |
| 12.4 Maintenance Mode | ☐ | | |
| 12.5 Verification Checklist | ☐ | | |
| 12.6 Update Strategies | ☐ | | |
| 12.7 Cluster Deploy via wsadmin | ☐ | | |
| 12.8 Failure Isolation | ☐ | | |
| 12.9 Change Management | ☐ | | |
| **Module 12 Overall Lab** | ☐ | | |
| **Module 12: Incidents (22-Sprint batch)** | ☐ | | |

### Module 13: HA & DR
| Topic | Status | Date | Notes |
|---|---|---|---|
| 13.1 HA Architecture Patterns | ☐ | | |
| 13.2 DMGR SPOF Mitigation | ☐ | | |
| 13.3 DMGR Backup Strategy | ☐ | | |
| 13.4 Node Agent Resilience | ☐ | | |
| 13.5 Cluster Member Failover | ☐ | | |
| 13.6 DR Runbook | ☐ | | Portfolio artifact: DigiStack DR runbook |
| 13.7 Backup Automation | ☐ | | |
| 13.8 Recovery Scenarios | ☐ | | |
| 13.9 Complete WAS Backup Architecture | ☐ | | Discuss disk-layout gap here explicitly; portfolio artifact: DigiStack backup runbook |
| **Module 13 Overall Lab** | ☐ | | |
| **Module 13: Incidents (22-Sprint batch)** | ☐ | | Add cluster-health panel to DigiStack dashboard on incident batch 2 |

### Module 14: Capacity Planning & Sizing
| Topic | Status | Date | Notes |
|---|---|---|---|
| 14.1 Load-to-Node Translation | ☐ | | |
| 14.2 Heap Sizing Formula | ☐ | | |
| 14.3 Thread Pool Sizing vs Downstream Limits | ☐ | | |
| 14.4 Headroom Planning | ☐ | | |
| 14.5 Documenting Sizing Decisions | ☐ | | |
| **Module 14 Overall Lab** | ☐ | | |
| **Module 14: Incidents (22-Sprint batch)** | ☐ | | |

### Arc Milestones
| Milestone | Status | Date | Notes |
|---|---|---|---|
| **Arc 3 Capstone** — Size and design HA for a stated load profile; justify every number | ☐ | | |
| **Arc 3 Review Flash Drill** (Day 191) | ☐ | | |
| **Incident Pattern Library** (Day 192) — Categories 1, 2, 4, 5 (JVM/Memory, ClassLoader, Clustering/HA, Federation/Sync) | ☐ | | |

---

## ARC 4 — Data & Integration

### Module 15: JDBC
| Topic | Status | Date | Notes |
|---|---|---|---|
| 15.1 Pool Architecture | ☐ | | |
| 15.2 JDBC Providers | ☐ | | |
| 15.3 XA vs Non-XA | ☐ | | |
| 15.4 J2C Auth Alias | ☐ | | |
| 15.5 Pool Sizing | ☐ | | |
| 15.6 Pool Timeout Params | ☐ | | |
| 15.7 Connection Leak Detection | ☐ | | |
| 15.8 Stale Connection Handling | ☐ | | |
| 15.9 Cell vs Server Scope | ☐ | | |
| 15.10 DB Failover | ☐ | | |
| 15.11 JDBC Diagnosis | ☐ | | |
| 15.12 wsadmin JDBC Automation | ☐ | | |
| 15.13 Transaction Service & tranlog Management | ☐ | | Revisit disk-layout gap here (WAS_TRANSACTION_DIR); portfolio artifact: DigiStack tranlog/in-doubt-transaction runbook |
| 15.14 JNDI Naming & Lookup Conventions (`jdbc/`, `jms/`, `mail/`, `url/`, `ejb/` namespaces) | ☐ | | |
| 15.15 JNDI Lab — Bind & Look Up Resources by JNDI Name | ☐ | | |
| **Module 15 Overall Lab** | ☐ | | |
| **Module 15: Incidents (22-Sprint batch)** | ☐ | | Add connection-pool wait-time/utilization panel to DigiStack dashboard on incident batch 2 |

### Module 16: Messaging — MQ/JMS/SIBus
| Topic | Status | Date | Notes |
|---|---|---|---|
| 16.1 SIBus vs External MQ | ☐ | | |
| 16.2 JMS Connection Factories/Activation Specs | ☐ | | |
| 16.3 MQ Queue Manager Integration | ☐ | | |
| 16.4 MDB Deployment/Sizing | ☐ | | |
| 16.5 Poison Message/DLQ Handling | ☐ | | |
| 16.6 Diagnosing Stuck Queues | ☐ | | |
| **Module 16 Overall Lab** | ☐ | | |
| **Module 16: Incidents (22-Sprint batch)** | ☐ | | Add queue-depth/DLQ panel to DigiStack dashboard on incident batch 2 |

**Supplementary — IBM MQ Standalone Install & MQSC Basics Lab** (Day 214)
| Item | Status | Date | Notes |
|---|---|---|---|
| MQ Standalone Install & MQSC Basics Lab — install a Queue Manager from scratch (MQ 9.3.x/9.4.x), create Local/Remote/Alias/Dead Letter Queue objects via `runmqsc`, start a Listener, verify connectivity with `amqsput`/`amqsget` | ☐ | | Prerequisite bridging lab before 16.3 MQ Queue Manager Integration; pure MQ foundations, no WAS involved |

**Supplementary — IBM MQ Deep Dive** (Days 221–223)
| Item | Status | Date | Notes |
|---|---|---|---|
| Channel Authentication (CHLAUTH) | ☐ | | |
| MQ Clustering (cluster QMs, workload balancing) | ☐ | | |
| Lab: Multi-Instance Queue Manager (active/standby failover) | ☐ | | |

### Arc Milestones
| Milestone | Status | Date | Notes |
|---|---|---|---|
| **Arc 4 Capstone** — Trace a transaction across JDBC (XA) + MQ; identify where a 2PC failure surfaces | ☐ | | |
| **Arc 4 Review Flash Drill** (Day 225) | ☐ | | |
| **Incident Pattern Library** (Day 226) — Category 3 (JDBC / Connection Pool) | ☐ | | |

---

## ARC 5 — Operate & Defend

### Module 17: IHS
| Topic | Status | Date | Notes |
|---|---|---|---|
| 17.1 Role in Architecture | ☐ | | |
| 17.2 Production Install | ☐ | | |
| 17.3 httpd.conf Mastery | ☐ | | |
| 17.4 Apache Module Directives — mod_rewrite, mod_headers, mod_alias, mod_status & KeepAlive | ☐ | | |
| 17.5 plugin-cfg.xml | ☐ | | |
| 17.6 Plugin Generation | ☐ | | |
| 17.7 Plugin Error Interval | ☐ | | |
| 17.8 Plugin Retry Interval | ☐ | | |
| 17.9 SSL/TLS Architecture | ☐ | | |
| 17.10 Virtual Hosting | ☐ | | |
| 17.11 Tuning | ☐ | | |
| 17.12 Log Analysis | ☐ | | |
| 17.13 IHS HA | ☐ | | Consider repurposed Tomcat VM as 2nd IHS node |
| 17.14 Troubleshooting | ☐ | | |
| **Module 17 Overall Lab** | ☐ | | |
| **Module 17: Incidents (22-Sprint batch)** | ☐ | | Add plugin-routing/503 panel to DigiStack dashboard on incident batch 2 |

**Supplementary — External Load Balancers** (Days 244–245)
| Item | Status | Date | Notes |
|---|---|---|---|
| Layer 4 vs Layer 7, VIP, persistence, SSL offloading (concept) | ☐ | | |
| Lab: NGINX or HAProxy in front of a WAS cluster | ☐ | | |

### Module 18: Session Management
| Topic | Status | Date | Notes |
|---|---|---|---|
| 18.1 Session Lifecycle/JSESSIONID | ☐ | | |
| 18.2 Affinity via Plugin | ☐ | | |
| 18.3 Failover Without Replication | ☐ | | |
| 18.4 Replication vs DB Persistence | ☐ | | |
| 18.5 Timeout Precedence | ☐ | | |
| 18.6 When Session Design Is Wrong | ☐ | | |
| **Module 18 Overall Lab** | ☐ | | |
| **Module 18: Incidents (22-Sprint batch)** | ☐ | | |

### Module 19: User Management & Security
| Topic | Status | Date | Notes |
|---|---|---|---|
| 19.1 Security Model | ☐ | | |
| 19.2 User Registry Architecture | ☐ | | |
| 19.3 LDAP Integration | ☐ | | |
| 19.4 Federated Repository | ☐ | | |
| 19.5 Admin Roles | ☐ | | |
| 19.6 RunAs Roles & J2C Authentication Alias Deep Dive | ☐ | | |
| 19.7 Role-to-Group Mapping | ☐ | | |
| 19.8 Java 2 Security | ☐ | | Stand up persistent DigiStack Grafana/Prometheus dashboard starting here (extended per-module) |
| 19.9 SSL Config Architecture | ☐ | | |
| 19.10 Admin Security Recovery | ☐ | | |
| 19.11 wsadmin with Security On | ☐ | | |
| 19.12 CSIv2/IIOP Security Deep Dive | ☐ | | |
| 19.13 Security Audit Checklist | ☐ | | |
| **Module 19 Overall Lab** | ☐ | | |
| **Module 19: Incidents (22-Sprint batch)** | ☐ | | |

**Supplementary — Identity Protocols (Conceptual)** (Days 271–272)
| Item | Status | Date | Notes |
|---|---|---|---|
| Kerberos and SPNEGO (conceptual, WAS admin security intersection) | ☐ | | |
| OAuth/SAML basics (conceptual, banking portal SSO context) | ☐ | | |

### Module 20: Performance Tuning & JVM Diagnostics
| Topic | Status | Date | Notes |
|---|---|---|---|
| 20.1 GC Deep Dive | ☐ | | |
| 20.2 verboseGC Parsing | ☐ | | |
| 20.3 Thread Pool Architecture | ☐ | | |
| 20.4 Thread Dump Analysis | ☐ | | |
| 20.5 Heap Dump Analysis | ☐ | | Run on Node1, not RAM-constrained Node2 |
| 20.6 PMI | ☐ | | |
| 20.7 Pool Monitoring | ☐ | | |
| 20.8 CPU/Memory Baseline | ☐ | | |
| 20.9 Diagnosis Methodology | ☐ | | |
| 20.10 Web Container Tuning | ☐ | | |
| **Module 20 Overall Lab** | ☐ | | |
| **Module 20: Incidents (22-Sprint batch)** | ☐ | | Add GC-pause/heap-trend panel to DigiStack dashboard on incident batch 2 |

**Supplementary — Dynacache, Core Dumps & Monitoring** (Days 286–291)
| Item | Status | Date | Notes |
|---|---|---|---|
| Dynacache architecture (Distributed Map, Cache Instances, Servlet Cache) | ☐ | | |
| Lab: Cache replication (DRS) and Cache Monitor | ☐ | | |
| Core dump basics (native_stderr.log, crash signatures) | ☐ | | |
| Lab: Force a native crash, trace core dump to root cause | ☐ | | |
| Metrics/Logs/Traces concept, alert design | ☐ | | |
| Lab: Prometheus/Grafana dashboard tied to PMI/JMX | ☐ | | |

### Module 21: Logging & FFDC
| Topic | Status | Date | Notes |
|---|---|---|---|
| 21.1 Log Architecture | ☐ | | |
| 21.2 Trace String Config | ☐ | | |
| 21.3 Trace String Syntax | ☐ | | |
| 21.4 FFDC | ☐ | | |
| 21.5 Log Rotation/Retention | ☐ | | |
| 21.6 Cross-Component Correlation | ☐ | | |
| 21.7 IBM Message Decoder | ☐ | | |
| 21.8 Log-Based Incident Analysis | ☐ | | |
| 21.9 Daily & Weekly Health Check Runbook | ☐ | | Portfolio artifact: DigiStack health-check runbook |
| **Module 21 Overall Lab** | ☐ | | |
| **Module 21: Incidents (22-Sprint batch)** | ☐ | | Add FFDC/error-rate panel to DigiStack dashboard on incident batch 2 |

### Module 22: Patch & Vulnerability Management
| Topic | Status | Date | Notes |
|---|---|---|---|
| 22.1 CVE Monitoring | ☐ | | |
| 22.2 Emergency iFix vs Scheduled Fix Pack | ☐ | | |
| 22.3 Patch Testing Gate | ☐ | | |
| 22.4 Rollback Decision Criteria | ☐ | | |
| **Module 22 Overall Lab** | ☐ | | |
| **Module 22: Incidents (22-Sprint batch)** | ☐ | | |

### Module 23: Licensing & Modernization Framework
| Topic | Status | Date | Notes |
|---|---|---|---|
| 23.1 PVU/Core Licensing | ☐ | | |
| 23.2 WAS ND vs Liberty vs OpenLiberty | ☐ | | |
| 23.3 Migration Effort Estimation | ☐ | | |
| 23.4 Containerization Context | ☐ | | |
| 23.5 Building the Business Case | ☐ | | |
| 23.6 Liberty Migration Deep Dive | ☐ | | |
| **Module 23 Overall Lab** | ☐ | | |
| **Module 23: Incidents (22-Sprint batch)** | ☐ | | |

**Supplementary — Liberty Profile & Migration Tooling** (Days 313–317)
| Item | Status | Date | Notes |
|---|---|---|---|
| Liberty vs traditional WAS — server.xml, featureManager | ☐ | | |
| Lab: Run a Liberty server from dropins | ☐ | | |
| Liberty in Docker/Kubernetes (containerization awareness) | ☐ | | |
| WCT + WASPreUpgrade/WASPostUpgrade scripts | ☐ | | |
| Lab: Migrate a profile end-to-end using WCT | ☐ | | |

### Module 24: wsadmin / Automation Mastery
| Topic | Status | Date | Notes |
|---|---|---|---|
| 24.1 wsadmin Architecture | ☐ | | |
| 24.2 Jython in WAS | ☐ | | |
| 24.3 AdminConfig | ☐ | | |
| 24.4 AdminControl | ☐ | | |
| 24.5 AdminApp | ☐ | | |
| 24.6 AdminTask | ☐ | | |
| 24.7 MBean Querying | ☐ | | |
| 24.8 Script Library Patterns | ☐ | | |
| 24.9 Automation Framework | ☐ | | |
| 24.10 Interview Jython Scripts | ☐ | | |
| **Module 24 Overall Lab** | ☐ | | |
| **Module 24: Incidents (22-Sprint batch)** | ☐ | | |

**Supplementary — CI/CD Pipelines** (Days 331–332)
| Item | Status | Date | Notes |
|---|---|---|---|
| Ansible + Jenkins pipeline concepts for WAS automation | ☐ | | |
| Lab: Turn a wsadmin script into a Jenkins-triggered deployment pipeline | ☐ | | |

### Arc Milestones
| Milestone | Status | Date | Notes |
|---|---|---|---|
| **Arc 5 Capstone (Final)** — Full incident simulation from a log bundle: root cause, fix, post-incident report | ☐ | | |
| **Arc 5 Review Flash Drill** (Day 334) | ☐ | | |
| **Enterprise Incident Response & On-Call — Senior Refresh** (Day 334-A) | ☐ | | |
| **Incident Pattern Library** (Day 335) — Category 6 (IHS / Plugin) | ☐ | | |

---

## ARC 6 — Enterprise Maturity

### Module 25: Change Management & ITSM
| Topic | Status | Date | Notes |
|---|---|---|---|
| 25.1 Why Change Management Exists in Banking | ☐ | | |
| 25.2 ITSM Ticket Types | ☐ | | |
| 25.3 RFC Lifecycle | ☐ | | |
| 25.4 CAB Presentation | ☐ | | |
| 25.5 Deployment Window Management | ☐ | | |
| 25.6 Rollback Plan Writing | ☐ | | |
| 25.7 Post-Implementation Review (PIR) | ☐ | | |
| 25.8 Emergency Change Process | ☐ | | |
| 25.9 Change Freeze Periods | ☐ | | |
| 25.10 ServiceNow for WAS Admins | ☐ | | |
| **Module 25 Overall Lab** | ☐ | | |
| **Module 25: Incidents (22-Sprint batch)** | ☐ | | |

### Module 26: Enterprise Infrastructure Integration
| Topic | Status | Date | Notes |
|---|---|---|---|
| 26.1 F5/NetScaler Load Balancer Integration | ☐ | | |
| 26.2 F5 Health Monitor Tuning | ☐ | | |
| 26.3 APM Agent Integration | ☐ | | |
| 26.4 Tivoli/IBM Monitoring | ☐ | | |
| 26.5 CyberArk / PAM Vault Integration | ☐ | | |
| 26.6 Splunk / SIEM Log Forwarding | ☐ | | |
| 26.7 ServiceNow CMDB | ☐ | | |
| 26.8 Ansible / Infrastructure as Code | ☐ | | |
| 26.9 SSH Bastion / Jump Server | ☐ | | |
| 26.10 NFS / Shared Storage | ☐ | | |
| **Module 26 Overall Lab** | ☐ | | |
| **Module 26: Incidents (22-Sprint batch)** | ☐ | | |

### Module 27: Compliance & Audit Readiness
| Topic | Status | Date | Notes |
|---|---|---|---|
| 27.1 Regulatory Landscape for WAS in Indian Banking | ☐ | | |
| 27.2 SSL/TLS Compliance | ☐ | | |
| 27.3 Admin Console in Production | ☐ | | |
| 27.4 J2 Security Audit | ☐ | | |
| 27.5 Password & Credential Audit | ☐ | | |
| 27.6 Patch Compliance Evidence | ☐ | | |
| 27.7 Access Control Audit | ☐ | | |
| 27.8 Log Retention Compliance | ☐ | | Tie back to disk-layout gap decision |
| 27.9 Audit Trail for Config Changes | ☐ | | |
| 27.10 IS Audit Walkthrough Simulation | ☐ | | |
| 27.11 Remediation Documentation | ☐ | | |
| **Module 27 Overall Lab** | ☐ | | |
| **Module 27: Incidents (22-Sprint batch)** | ☐ | | |

**Supplementary — Compliance: Named Frameworks** (Days 376–377)
| Item | Status | Date | Notes |
|---|---|---|---|
| PCI DSS control mapping for WAS-fronted payment app | ☐ | | |
| SOX control mapping for change/config audit trails | ☐ | | |

### Module 28: SSL & Certificate Disaster Recovery
| Topic | Status | Date | Notes |
|---|---|---|---|
| 28.1 WAS SSL Architecture Deep Dive | ☐ | | |
| 28.2 Certificate Lifecycle in Banking | ☐ | | |
| 28.3 ikeycmd / gskcmd Mastery | ☐ | | |
| 28.4 Certificate Expiry — The Silent Killer | ☐ | | |
| 28.5 Zero-Downtime Certificate Rotation | ☐ | | |
| 28.6 LTPA Token & SSL Relationship | ☐ | | |
| 28.7 SSL Handshake Failure Diagnosis | ☐ | | |
| 28.8 SSL DR Scenario 1 — DMGR Keystore Lost | ☐ | | |
| 28.9 SSL DR Scenario 2 — Cert Expired at Midnight | ☐ | | |
| 28.10 SSL DR Scenario 3 — CA Root Cert Expired | ☐ | | |
| 28.11 SSL DR Scenario 4 — IHS kdb Corrupted | ☐ | | |
| 28.12 SSL Backup Strategy | ☐ | | |
| 28.13 SSL in DR Site | ☐ | | |
| **Module 28 Overall Lab** | ☐ | | |
| **Module 28: Incidents (22-Sprint batch)** | ☐ | | |

### Arc Milestones
| Milestone | Status | Date | Notes |
|---|---|---|---|
| **Incident Pattern Library** (Day 394) — Categories 7, 8 (SSL/Certificates, Change Management Failures) | ☐ | | |
| **Arc 6 Capstone** — Enterprise Readiness Audit | ☐ | | Dashboard becomes part of audit evidence pack, alongside 27.10 |
| **Arc 6 Review Flash Drill** (Day 396) | ☐ | | |

**Supplementary — Career Readiness** (Days 397–400)
| Item | Status | Date | Notes |
|---|---|---|---|
| Resume rewrite tailored to Senior WAS Admin roles, gap narrative | ☐ | | |
| Architecture whiteboard prep (redraw Cell/Node/Cluster/IHS from memory) | ☐ | | |
| Documentation portfolio assembly (SOPs, runbooks, incident reports) | ☐ | | |
| Mock interview day (scenario Q&A, STAR drill, salary negotiation) | ☐ | | |

---

## Incident Pattern Library
| Category | Status | Date | Notes |
|---|---|---|---|
| Category 1: JVM / Memory (10 patterns) | ☐ | | Study after Arc 3 |
| Category 2: ClassLoader (8 patterns) | ☐ | | Study after Arc 3 |
| Category 3: JDBC / Connection Pool (10 patterns) | ☐ | | Study after Arc 4 |
| Category 4: Clustering / HA (8 patterns) | ☐ | | Study after Arc 3 |
| Category 5: Federation / Sync (6 patterns) | ☐ | | Study after Arc 3 |
| Category 6: IHS / Plugin (6 patterns) | ☐ | | Study after Arc 5 M17 |
| Category 7: SSL / Certificates (8 patterns) | ☐ | | Study after Arc 6 M28 |
| Category 8: Change Management Failures (8 patterns, incl. 2 added post-review) | ☐ | | Study after Arc 6 M25 |

---

## Session Notes (running log)

| Date | What was covered | Weak areas to revisit |
|---|---|---|
| — | Day 0 setup: VM planning, WAS 9.0.5.28 ND installed at `/apps/IBM/WebSphere/AppServer`, wasadmin non-root fix applied, standalone profile AppSrv01 created | Awaiting final versionInfo.sh / manageprofiles.sh output to formally close Day 0. Disk-layout separation (`/wasdata`,`/logs`,`/tranlog`) deliberately left as default — documented gap, revisit in Module 13.9 and Module 27.8 |

---

**Current status:** Day 0 in progress (final verification pending). Next up: confirm Day 0 closeout, then **Day 1 — Module 0: Target Environment Declaration**.
