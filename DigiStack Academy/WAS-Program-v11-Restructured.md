# WAS PROGRAM v11 — 222 Days, Restructured for Token Efficiency

Supersedes `WAS-Program-v10-Full-222-Days.md` as the day-by-day source of
truth. **Content is identical** — every Day number, Day-Tag
(S=Standard/L=Lab/I=Incident/C=Closeout), and topic from v10 is preserved.
Only the format changed: narrative bullets → dense tables, grouped by the
15 phases already agreed, chapter-name headers kept as a column instead of
a full heading block per chapter. Day numbering/order is still fixed and
never renumbered, per program rules.

**Legend:** S = Standard Day (24 sprints) · L = Lab Day · I = Incident Day
· C = Closeout Day

---

## PHASE 1 — Foundation + OS + Installation (Days 1–17)

| Day | Tag | Chapter | Topic |
|---|---|---|---|
| 1 | S | Orientation | Enterprise IT overview, banking IT org structure, ITIL basics, ServiceNow/Jira/Confluence |
| 2 | S | Orientation | Java fundamentals — JDK/JRE/JVM (IBM Java 8), Heap, GC (conceptual) |
| 3 | S | Orientation | WAR/EAR/JAR packaging, full Browser→IHS→Plugin→WAS→DB diagram from memory |
| 4 | S | WAS Intro | What is WebSphere, market landscape, editions — WAS ND 9.0.5.28 |
| 5 | S | WAS Intro | Client-Server model, enterprise app architecture |
| 6 | S | WAS Intro | Cell/Node/Server terminology (pre-install) |
| 7 | S | WAS Intro | Admin Console tour, wsadmin concept intro |
| 8 | L | WAS Intro | Cumulative hands-on lab, all topics since program start |
| 9 | C | WAS Intro | Explain WAS architecture |
| 10 | S | Install | IBM Installation Manager 1.9.x concepts, repository setup |
| 11 | S | Install | Prerequisites — RHEL 8.x/Rocky 8.x tuning, ulimits, hostname/DNS |
| 12 | L | Install | Install IIM 1.9.x |
| 13 | L | Install | Install WebSphere ND 9.0.5.28 (Base) |
| 14 | S | Install | Silent installation (response files) |
| 15 | S | Install | Fix Packs — concept, versioning (target 9.0.5.28) |
| 16 | S | Install | Rollback strategy, Packaging Utility |
| 17 | C | Install | Verify install via versionInfo.sh, intentionally break/fix |

## PHASE 2 — Profiles + DMGR + Federation + Architecture (Days 18–31)

| Day | Tag | Chapter | Topic |
|---|---|---|---|
| 18 | S | Standalone Profile | Profile concepts — Standalone vs DMGR vs Custom |
| 19 | L | Standalone Profile | Create Standalone profile via manageprofiles |
| 20 | S | Basic Deployment | EAR/WAR deployment flow, Virtual Host, Context Root |
| 21 | L | Basic Deployment | Deploy WAR via Admin Console |
| 22 | L | Basic Deployment | Deploy via wsadmin/AdminApp |
| 23 | L | Custom Profile | Create Custom profile |
| 24 | L | DMGR | Create DMGR profile |
| 25 | S | DMGR | Profile directory structure, port assignment walkthrough |
| 26 | L | Federation | Federate Custom profile to DMGR |
| 27 | C | Federation | Delete/recreate a profile unaided, timed |
| 28 | S | Synchronization | syncNode, Node Federation deep dive — what happens on federation |
| 29 | S | Architecture Deep Dive | Cell, Node, Node Agent, DMGR — how the pieces relate |
| 30 | S | Architecture Deep Dive | Master Repository, repository.xml, Config Sync mechanics, SOAP Connector |
| 31 | S | Architecture Deep Dive | Deployment Flow, Server Lifecycle, Admin Console Architecture — closing review tying 18–30 together |

## PHASE 3 — Application Deployment + Classloading (Days 32–38)

| Day | Tag | Topic |
|---|---|---|
| 32 | S | Classloader Policy — PARENT_FIRST vs PARENT_LAST |
| 33 | L | Cumulative, applied to federated cell |
| 34 | S | WAR classloader scope, Shared Library classloader conflicts |
| 35 | L | Reproduce and fix ClassNotFoundException/NoClassDefFoundError |
| 36 | S | Classloader Viewer walkthrough |
| 37 | S | App update/uninstall, zero-downtime versioning concept |
| 38 | C | Deploy/break/fix a classloading issue live, on the federated cell |

## PHASE 4 — Clustering + WLM + Failover (Days 39–51)

| Day | Tag | Chapter | Topic |
|---|---|---|---|
| 39 | S | Clustering | Cluster concepts — why clustering (HA + scale) |
| 40 | L | Clustering | Lab day |
| 41 | L | Clustering | Create Cluster with 2 Cluster Members |
| 42 | S | Clustering | Horizontal vs Vertical clustering |
| 43 | S | Clustering | Core Groups, HAManager concepts |
| 44 | I | Clustering | Incident day |
| 45 | S | Clustering | Workload Management (WLM) — Web and EJB |
| 46 | S | Clustering | Work Manager, Asynchronous Beans, Resource Environment Providers |
| 47 | S | Clustering | On Demand Router (ODR), Intelligent Management concept |
| 48 | L | Clustering | Dynamic Clusters — ND auto-scaling |
| 49 | L | Cluster Failover | Stop one cluster member mid-request, observe failover |
| 50 | L | Deployment to Cluster | Deploy app to cluster, verify load distribution |
| 51 | C | Deployment to Cluster | Cluster + WLM + Dynamic Cluster interview-style Q&A |

## PHASE 5 — JDBC + JNDI + JMS/SIBus (Days 52–64)

| Day | Tag | Chapter | Topic |
|---|---|---|---|
| 52 | S | JDBC | JDBC driver concepts, Datasource architecture |
| 53 | L | JDBC | Lab day |
| 54 | L | JDBC | Configure PostgreSQL 13+ JDBC Provider + Datasource |
| 55 | S | JDBC | Connection Pool tuning basics (min/max, timeout) |
| 56 | S | JDBC | XA vs non-XA Datasource, two-phase commit concept |
| 57 | L | JDBC | Test connection, force pool exhaustion, read the exception |
| 58 | S | JDBC | Transactions, JTA basics |
| 59 | I | JDBC | Incident day |
| 60 | C | JDBC | Diagnose "Connection not available" end-to-end |
| 61 | S | JNDI | Naming/lookup concepts — Datasource → `jdbc/DigiStackDS`; JNDI names for jdbc/, jms/, mail/, url/, ejb/ |
| 62 | L | JNDI | Bind and look up JDBC Datasource + Connection Factory by JNDI name from a test app |
| 63 | S | SIBus | SIBus fundamentals — Bus Members, Messaging Engines, SIBus vs external IBM MQ |
| 64 | L | SIBus | Create SIBus, configure Queue Destination, wire JMS app via JNDI name; mini-closeout: 5 SIBus-vs-MQ interview Qs |

## PHASE 6 — IHS + Plugin + Session Management (Days 65–81)

| Day | Tag | Chapter | Topic |
|---|---|---|---|
| 65 | S | IHS | Web server architecture, Apache vs IHS 9.0.5.28 |
| 66 | L | IHS | Install IBM HTTP Server 9.0.5.28 |
| 67 | S | IHS | Config files, virtual hosts, HTTP/HTTPS request flow |
| 68 | L | IHS | Generate/propagate plugin-cfg.xml, connect IHS → WAS |
| 69 | S | IHS | URL mapping, reverse proxy behavior of the Plugin |
| 70 | S | IHS | mod_rewrite, mod_headers, mod_alias basics |
| 71 | S | IHS | Access logs, error logs, log rotation |
| 72 | L | IHS | SSL Virtual Hosts + Multiple Virtual Hosts |
| 73 | L | IHS | Plugin trace log analysis |
| 74 | S | IHS | mod_status, Plugin timeout tuning, KeepAlive, request routing under load |
| 75 | C | IHS | Break plugin config on purpose, troubleshoot from IHS error log back to WAS |
| 76 | S | Session Mgmt | HTTP session concepts, cookies, session affinity |
| 77 | L | Session Mgmt | Configure session timeout, tracking mechanisms |
| 78 | S | Session Mgmt | Session persistence — memory-to-memory replication |
| 79 | L | Session Mgmt | Enable session replication across your cluster |
| 80 | S | Session Mgmt | Sticky sessions at the LB layer |
| 81 | C | Session Mgmt | Kill a cluster member mid-session, verify session isn't lost |

**Current position: Day 1, Sprint 1** (program restarted from scratch;
this phase was previously reached under old numbering but is not complete).

## PHASE 7 — SSL + Security + LDAP + Hardening (Days 82–108)

| Day | Tag | Chapter | Topic |
|---|---|---|---|
| 82 | S | SSL Fund. | PKI fundamentals, Certificate Authority, CSR generation |
| 83 | S | SSL Fund. | Keystore vs Truststore, JKS vs PKCS12, GSKit |
| 84 | S | SSL Fund. | Cipher suites, TLS versions, Mutual SSL concepts |
| 85 | L | SSL Fund. | Generate self-signed cert and CSR using ikeyman/gskit |
| 86 | S | SSL Fund. | LDAP Security, Active Directory integration concepts |
| 87 | S | SSL Fund. | Kerberos, SPNEGO (conceptual) |
| 88 | S | SSL Fund. | OAuth/SAML basics (conceptual) |
| 89 | C | SSL Fund. | Full SSL handshake explanation with WAS/IHS placement |
| 90 | S | SSL Fund. | SSL configuration repertoire in WAS (Node/Cell/Server scope) |
| 91 | L | SSL Fund. | Import cert into WAS keystore, bind to SSL config |
| 92 | L | SSL Fund. | Configure SSL between IHS and WAS Plugin |
| 93 | S | SSL Fund. | CSIv2/IIOP security concepts |
| 94 | L | SSL Fund. | Enable Mutual SSL between two test endpoints |
| 95 | I | SSL E2E | Incident day |
| 96 | C | SSL E2E | Simulate an expired cert, fix end-to-end (Browser→IHS→Plugin→WAS→DB) |
| 97 | S | Security | WAS security domain concepts, User Registry types — Administrative Security |
| 98 | L | Security | Configure Federated Repository / local OS registry — LDAP/Federated Repository |
| 99 | S | Security | WAS Admin Roles — Monitor/Configurator/Operator/Administrator/Deployer |
| 100 | L | Security | Create console users, assign roles, test access limits — Users/Groups |
| 101 | S | Security | RunAs Roles, J2C Authentication Aliases |
| 102 | S | Security | LDAP concepts preview |
| 103 | I | Security | Incident day |
| 104 | C | Security | Role-based access lockdown test; admin console lockout/recovery |
| 105 | S | Security | Admin Console Lockdown & Hardening — Application Security |
| 106 | S | Security | Security Hardening Pack 1 — disable default apps, secure console access, least-privilege |
| 107 | S | Security | Security Hardening Pack 2 — TLS hardening, cipher selection, password policies, file permissions |
| 108 | L | Security | Apply full Security Hardening Pack 1+2 checklist against your cluster |

## PHASE 8 — Maintenance + Enterprise Operations (Days 109–122)

| Day | Tag | Chapter | Topic |
|---|---|---|---|
| 109 | L | Maintenance | Lab day |
| 110 | S | Maintenance | Fix Pack strategy, version roadmap, EOS tracking concept |
| 111 | L | Maintenance | Download and stage a Fix Pack |
| 112 | L | Maintenance | Apply Fix Pack to a test profile, verify versionInfo (target 9.0.5.28) |
| 113 | S | Maintenance | Rollback procedure after a failed patch |
| 114 | C | Maintenance | Write a Fix Pack SOP + rollback plan as a real document |
| 115 | S | Ent. Admin | Patch maintenance window planning — CAB process, Go/No-Go checklist |
| 116 | S | Ent. Admin | Production Change Management — change request lifecycle, rollback plan, comms templates |
| 117 | L | Ent. Admin | backupConfig/restoreConfig — real backup and restore |
| 118 | L | Ent. Admin | addNode/removeNode — remove and re-federate a node (Cell Recovery drill) |
| 119 | S | Ent. Admin | Capacity planning basics — sizing a cluster for expected load |
| 120 | S | Ent. Admin | DR/Failover tie-in — migration during a maintenance window, CAB approval flow |
| 121 | S | Ent. Admin | Daily Operations & Health Checks — what a shift looks like, morning health-check routine |
| 122 | C | Ent. Admin | Write a Shift Handover template + run a mock handover |

## PHASE 9 — Deep Troubleshooting (Days 123–148)

| Day | Tag | Sub-topic | Content |
|---|---|---|---|
| 123 | S | JVM startup | SystemOut/SystemErr/FFDC |
| 124 | I | JVM startup | Incident day |
| 125–126 | S | JVM startup | Log correlation, force startup failure |
| 127 | S | JVM startup | 3 induced-failure labs |
| 128 | L | Hung threads/CPU | Lab day |
| 129 | S | Hung threads/CPU | Thread dumps/javacore |
| 130 | L | Hung threads/CPU | Simulate hung thread, 3 dumps 10s apart |
| 131 | S | Heap/memory leaks | Heap dumps, MAT |
| 132 | S | Heap/memory leaks | OOM investigation |
| 133 | S | Heap/memory leaks | javacore deep dive — locks/deadlocks |
| 134 | S | Core dumps | Core dump basics, native_stderr, crash signatures |
| 135 | L | Core dumps | Lab day |
| 136 | S | Core dumps | Classloader memory leaks, MAT leak suspects |
| 137 | L | Core dumps | High CPU, correlate top + thread dump |
| 138 | L | Core dumps | Memory leak pattern, heap trend |
| 139 | I | Deploy/SSL/LDAP/Plugin | Incident day |
| 140 | S | Deploy/SSL/LDAP/Plugin | NodeAgent/DMGR down |
| 141 | S | Deploy/SSL/LDAP/Plugin | Deployment/SOAP connector failure |
| 142 | L | Deploy/SSL/LDAP/Plugin | Lab day |
| 143 | S | Deploy/SSL/LDAP/Plugin | SSL expired/LDAP failure |
| 144 | S | Deploy/SSL/LDAP/Plugin | DB down/disk full/DNS/firewall |
| 145 | S | Deploy/SSL/LDAP/Plugin | Core Group failure/session replication failure |
| 146 | S | Full workflow | 9-stage incident response workflow, timed |
| 147 | S | Full workflow | MustGather & IBM PMR process |
| 148 | C | Full workflow | Pick 10 strongest troubleshooting stories as STAR answers (15 interview Qs) |

## PHASE 10 — Performance Engineering (Days 149–162)

| Day | Tag | Topic |
|---|---|---|
| 149 | S | JVM Architecture — Class Loader, JIT, Native Memory, Metaspace |
| 150 | S | Garbage Collection internals, GC logs, GC Viewer/IBM Health Center |
| 151 | L | Enable verbose GC, capture and read a GC log |
| 152 | S | JVM Tuning — heap sizing (-Xms/-Xmx), GC policy choice |
| 153 | I | Incident day |
| 154 | S | Thread Pool tuning (Web Container, ORB), JDBC Pool tuning |
| 155 | L | Lab day |
| 156 | S | Session optimization, Dynacache tuning tie-in |
| 157 | L | Native Monitoring Deep Dive — PMI, TPV, Health Management, JMX |
| 158 | S | Throughput, Latency, TPS, Concurrent Users — defining a baseline |
| 159 | L | Basic load test (JMeter or ab) against your cluster |
| 160 | S | Bottleneck analysis method — CPU vs Heap vs Thread vs DB |
| 161 | L | Tune one deliberately-misconfigured JVM back to healthy |
| 162 | C | Present a tuning case study (problem→analysis→fix→result) |

## PHASE 11 — Migration (Days 163–170)

| Day | Tag | Topic |
|---|---|---|
| 163 | S | Migration types — version upgrade, profile migration, plugin migration, Java upgrade |
| 164 | S | Migration planning — inventory, risk assessment, rollback plan |
| 165 | L | Use WCT and WASPreUpgrade/WASPostUpgrade scripts to migrate a profile |
| 166 | I | Incident day |
| 167 | S | Plugin migration and compatibility checks |
| 168 | S | Java version upgrade considerations (JVM compatibility with app code) |
| 169 | S | Post-migration validation — smoke tests, health checks |
| 170 | C | Write a full migration runbook end to end |

## PHASE 12 — Advanced wsadmin + Automation (Days 171–191)

| Day | Tag | Topic |
|---|---|---|
| 171 | S | Admin Console deep dive — every menu |
| 172 | S | wsadmin scripting intro — Jython basics |
| 173 | L | AdminControl, AdminConfig — query running servers |
| 174 | L | AdminApp, AdminTask — install/list/uninstall apps via script |
| 175 | L | Node Agent Deep Dive |
| 176 | I | Incident day |
| 177 | S | Dynacache — Distributed Map, Cache Instances, Servlet Cache |
| 178 | L | Cache Replication (DRS), Cache Monitor |
| 179 | L | wsadmin Automation Pack 1 |
| 180 | L | wsadmin Automation Pack 2 |
| 181 | C | Full backup → simulate DMGR loss → restore, document as SOP |
| 182 | S | Jython refresher specific to wsadmin scripting |
| 183 | L | Script a full health check (cluster/node/JVM/datasource status) |
| 184 | L | Script an app deployment end-to-end (no manual console clicks) |
| 185 | L | Script backupConfig/restoreConfig on a schedule |
| 186 | L | Script user/role creation |
| 187 | S | SSL automation via wsadmin |
| 188 | S | Automated deployment pipeline scripting |
| 189 | S | Wrap wsadmin scripts into shell wrappers, add logging/error handling |
| 190 | S | Version control your scripts in Git |
| 191 | C | Build a Daily Health Check + Auto-Deploy script pack for a night-shift operator |

## PHASE 13 — MQ + LB + Monitoring + CI/CD (Days 192–210)

| Day | Tag | Chapter | Topic |
|---|---|---|---|
| 192 | S | IBM MQ | Queue Manager, Local/Remote/Alias/Dead Letter Queue concepts — IBM MQ 9.3.x/9.4.x |
| 193 | L | IBM MQ | Install a Queue Manager, create local queues, MQSC basics |
| 194 | S | IBM MQ | Channels, Listeners, Channel Authentication (CHLAUTH) |
| 195 | S | IBM MQ | MQ Clustering — cluster queue managers, workload balancing |
| 196 | L | IBM MQ | Multi-Instance Queue Manager — active/standby pair |
| 197 | S | IBM MQ | Dead Letter Queue handling procedure |
| 198 | S | IBM MQ | JMS Integration with WAS — Connection Factory, Activation Specification |
| 199 | L | IBM MQ | Wire a WAS app to send/receive an MQ message |
| 200 | S | Liberty | Liberty vs traditional WAS, server.xml, featureManager |
| 201 | L | Liberty | Install Liberty, run a server from dropins |
| 202 | S | Liberty | Liberty in Docker/Kubernetes concepts, tWAS-to-Liberty migration awareness |
| 203 | S | Load Balancers | Layer 4 vs Layer 7 LB, VIP, persistence, sticky sessions, SSL offloading |
| 204 | L | Load Balancers | Lab day |
| 205 | I | Load Balancers | Incident day |
| 206 | L | Load Balancers | Stand up NGINX or HAProxy in front of your cluster members |
| 207 | S | Monitoring | Metrics/Logs/Traces concept, Prometheus/Grafana basics, alert design |
| 208 | L | Monitoring | Wire basic Prometheus/Grafana dashboard, tie back to PMI/JMX |
| 209 | S | CI/CD | Python automation patterns, Ansible basics, Jenkins pipeline concept |
| 210 | L | CI/CD | Turn a wsadmin script into a Jenkins-triggered pipeline |

## PHASE 14 — DR + Compliance + Documentation (Days 211–214)

| Day | Tag | Topic |
|---|---|---|
| 211 | S | DR testing, cell/node recovery, backup validation, business continuity |
| 212 | S | PCI DSS/SOX/ITIL compliance awareness for banking environments |
| 213 | S | Build template set: Installation SOP, Deployment SOP, Restart SOP, Backup SOP |
| 214 | S | Build: DR Runbook, Health Check Checklist, Patch Runbook, Incident Runbook, 2 KB articles |

## PHASE 15 — Real Bank Production Simulation (Days 215–222)

| Day | Tag | Topic |
|---|---|---|
| 215 | S | Simulation Marathon: NodeAgent down, DMGR down, Cluster member crash, JDBC pool exhaustion, DB outage, MQ queue full |
| 216 | S | Simulation Marathon: SSL cert expired, Hung threads, High CPU, Memory leak, Slow response, Plugin timeout |
| 217 | S | Simulation Marathon: Session replication failure, Deployment failure, DNS issue, Disk full, FD exhaustion, Network latency |
| 218 | L | Inserted Lab Day |
| 219 | S | Capstone lab — full "Real Bank" stack, all integrated |
| 220 | I | Incident day |
| 221 | S | Resume rewrite, architecture whiteboard prep, gap narrative, documentation portfolio |
| 222 | S | Mock interview day — scenario Q&A, STAR delivery drill, salary/negotiation, whiteboard recap |

---

## Balance Check (unchanged from v10)

| Metric | Value |
|---|---|
| Total Days | 222 |
| Incident Days | 11 |
| Closeout Days | 17 |
| Lab Days (named + generic) | ~19 |
| New content days (v7→v10) | 7 (3 Architecture Deep Dive, 2 JNDI, 2 Enterprise Admin) |
| Days trimmed to fund them | 8 generic Lab Days removed |

Every named lab, every incident scenario, and nearly every closeout from
v7/v10 survives untouched — only the presentation format changed here.

## Retire
Delete `WAS-Program-v10-Full-222-Days.md` and `Phase-Map-v1.md` (if not
already removed per the reference-file consolidation) once this file is
confirmed to cover your needs — this file absorbs both.
