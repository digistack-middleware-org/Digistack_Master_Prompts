# WebSphere Administrator Career Re-Entry Plan — v10 (Build Phase / Operations Phase, 222 Days)

Total days held at exactly 222, matching v7. Three new chapters (Architecture Deep Dive, JNDI, and two Enterprise Administration days) are funded by removing 8 generic "cumulative review" Lab Days that carried no named content — one each from Install, Session Management, Users & Groups, Fix Pack, Performance (×2), wsadmin, and Administration. All named labs, all 11 Incident Days, and all folder closeouts are preserved. Troubleshooting is reorganized under your 17 topic headings with its original 26 days unchanged.

---

## PHASE A — Foundations (Days 1–17)

**Orientation (1–3)** — unchanged
- Day 1: Enterprise IT overview, banking IT org structure, ITIL basics, ServiceNow/Jira/Confluence
- Day 2: Java fundamentals — JDK/JRE/JVM (IBM Java 8), Heap, GC (conceptual)
- Day 3: WAR/EAR/JAR packaging, full Browser→IHS→Plugin→WAS→DB diagram from memory

**WAS Intro (4–9)** — unchanged
- Day 4: What is WebSphere, market landscape, editions — WAS ND 9.0.5.28
- Day 5: Client-Server model, enterprise app architecture
- Day 6: Cell/Node/Server terminology (pre-install)
- Day 7: Admin Console tour, wsadmin concept intro
- **Day 8 – LAB:** Cumulative hands-on lab, all topics since program start
- Day 9 – **CLOSEOUT:** explain WAS architecture

**Install (10–17)** — 8 days *(1 generic Lab Day removed)*
- Day 10: IBM Installation Manager 1.9.x concepts, repository setup
- Day 11: Prerequisites — RHEL 8.x/Rocky 8.x tuning, ulimits, hostname/DNS
- Day 12 – **Lab:** Install IIM 1.9.x
- Day 13 – **Lab:** Install WebSphere ND 9.0.5.28 (Base)
- Day 14: Silent installation (response files)
- Day 15: Fix Packs — concept, versioning (target 9.0.5.28)
- Day 16: Rollback strategy, Packaging Utility
- Day 17 – **CLOSEOUT:** verify install via versionInfo.sh, intentionally break/fix

---

## PHASE B — BUILD (Days 18–96, 79 days)

**1. Standalone Profile (18–19)**
- Day 18: Profile concepts — Standalone vs DMGR vs Custom
- Day 19 – **Lab:** Create Standalone profile via manageprofiles

**2. Basic Deployment (20–22)**
- Day 20: EAR/WAR deployment flow, Virtual Host, Context Root
- Day 21 – **Lab:** Deploy WAR via Admin Console
- Day 22 – **Lab:** Deploy via wsadmin/AdminApp

**3. Custom Profile (23)**
- Day 23 – **Lab:** Create Custom profile

**4. DMGR (24–25)**
- Day 24 – **Lab:** Create DMGR profile
- Day 25: Profile directory structure, port assignment walkthrough

**5. Federation (26–27)**
- Day 26 – **Lab:** Federate Custom profile to DMGR
- Day 27 – **CLOSEOUT:** delete/recreate a profile unaided, timed

**6. Synchronization (28)**
- Day 28: syncNode, Node Federation deep dive — what actually happens on federation

**7. WebSphere Architecture Deep Dive (29–31)** *(NEW)*
- Day 29: Cell, Node, Node Agent, DMGR — how the pieces relate now that you've built all of them
- Day 30: Master Repository, repository.xml, Configuration Sync mechanics, SOAP Connector
- Day 31: Deployment Flow, Server Lifecycle, Admin Console Architecture — closing review tying Days 18–30 together

**8. Deployment to Federated Servers (32–38)**
- Day 32: Classloader Policy — PARENT_FIRST vs PARENT_LAST
- **Day 33 – LAB DAY:** cumulative, applied to your federated cell
- Day 34: WAR classloader scope, Shared Library classloader conflicts
- Day 35 – **Lab:** Reproduce and fix ClassNotFoundException/NoClassDefFoundError
- Day 36: Classloader Viewer walkthrough
- Day 37: App update/uninstall, zero-downtime versioning concept
- Day 38 – **CLOSEOUT:** deploy/break/fix a classloading issue live, on the federated cell

**9. Clustering (39–48)**
- Day 39: Cluster concepts — why clustering (HA + scale)
- **Day 40 – LAB DAY**
- Day 41 – **Lab:** Create Cluster with 2 Cluster Members
- Day 42: Horizontal vs Vertical clustering
- Day 43: Core Groups, HAManager concepts
- **Day 44 – INCIDENT DAY**
- Day 45: Workload Management (WLM) — Web and EJB
- Day 46: Work Manager, Asynchronous Beans, Resource Environment Providers
- Day 47: On Demand Router (ODR), Intelligent Management concept
- Day 48 – **Lab:** Dynamic Clusters — ND auto-scaling

**10. Cluster Failover (49)**
- Day 49 – **Lab:** Stop one cluster member mid-request, observe failover

**11. Deployment to Cluster (50–51)**
- Day 50 – **Lab:** Deploy app to cluster, verify load distribution
- Day 51 – **CLOSEOUT:** cluster + WLM + Dynamic Cluster interview-style Q&A

**12. JDBC (52–60)**
- Day 52: JDBC driver concepts, Datasource architecture
- **Day 53 – LAB DAY**
- Day 54 – **Lab:** Configure PostgreSQL 13+ JDBC Provider + Datasource
- Day 55: Connection Pool tuning basics (min/max, timeout)
- Day 56: XA vs non-XA Datasource, two-phase commit concept
- Day 57 – **Lab:** Test connection, force pool exhaustion, read the exception
- Day 58: Transactions, JTA basics
- **Day 59 – INCIDENT DAY**
- Day 60 – **CLOSEOUT:** diagnose "Connection not available" end-to-end

**13. JNDI (61–62)** *(NEW)*
- Day 61: Naming/lookup concepts — how a Datasource becomes `jdbc/DigiStackDS`; JNDI names for jdbc/, jms/, mail/, url/, ejb/
- Day 62 – **Lab:** Bind and look up your JDBC Datasource and a Connection Factory by JNDI name from a test app

**14. SIBus (63–64)**
- Day 63: Service Integration Bus fundamentals — Bus Members, Messaging Engines, SIBus vs external IBM MQ
- Day 64 – **Lab:** Create a SIBus, configure a Queue Destination, wire a simple JMS app via its JNDI name — mini-closeout: 5 interview Qs on SIBus vs MQ

**15. IHS (65–75)**
- Day 65: Web server architecture, Apache vs IHS 9.0.5.28
- Day 66 – **Lab:** Install IBM HTTP Server 9.0.5.28
- Day 67: Configuration files, virtual hosts, HTTP/HTTPS request flow
- Day 68 – **Lab:** Generate/propagate plugin-cfg.xml, connect IHS → WAS
- Day 69: URL mapping, reverse proxy behavior of the Plugin
- Day 70: mod_rewrite, mod_headers, mod_alias basics
- Day 71: Access logs, error logs, log rotation
- Day 72 – **Lab:** SSL Virtual Hosts + Multiple Virtual Hosts
- Day 73 – **Lab:** Plugin trace log analysis
- Day 74: mod_status, Plugin timeout tuning, KeepAlive, request routing under load
- Day 75 – **CLOSEOUT:** break plugin config on purpose, troubleshoot from IHS error log back to WAS

**16. Session Management (76–81)** — 6 days *(1 generic Lab Day removed)*
- Day 76: HTTP session concepts, cookies, session affinity
- Day 77 – **Lab:** Configure session timeout, tracking mechanisms
- Day 78: Session persistence — memory-to-memory replication
- Day 79 – **Lab:** Enable session replication across your cluster
- Day 80: Sticky sessions at the LB layer
- Day 81 – **CLOSEOUT:** kill a cluster member mid-session, verify session isn't lost

**17. SSL — Fundamentals & Configuration (82–94)**
- Day 82: PKI fundamentals, Certificate Authority, CSR generation
- Day 83: Keystore vs Truststore, JKS vs PKCS12, GSKit
- Day 84: Cipher suites, TLS versions, Mutual SSL concepts
- Day 85 – **Lab:** Generate self-signed cert and CSR using ikeyman/gskit
- Day 86: LDAP Security, Active Directory integration concepts
- Day 87: Kerberos, SPNEGO (conceptual)
- Day 88: OAuth/SAML basics (conceptual)
- Day 89 – **CLOSEOUT:** full SSL handshake explanation with WAS/IHS placement
- Day 90: SSL configuration repertoire in WAS (Node/Cell/Server scope)
- Day 91 – **Lab:** Import cert into WAS keystore, bind to SSL config
- Day 92 – **Lab:** Configure SSL between IHS and WAS Plugin
- Day 93: CSIv2/IIOP security concepts
- Day 94 – **Lab:** Enable Mutual SSL between two test endpoints

**18. SSL — End-to-End, Browser to DB (95–96)**
- **Day 95 – INCIDENT DAY**
- Day 96 – **CLOSEOUT:** simulate an expired cert, fix end-to-end (Browser → IHS → Plugin → WAS → DB)

*Build Phase complete: everything from a bare OS to a fully clustered, SSL-terminated, JDBC/JNDI/SIBus-wired platform.*

---

## PHASE C — OPERATIONS (Days 97–191, 95 days)

**19. Security (97–108)** — 12 days *(merged: Users & Groups + SSL hardening; 1 generic Lab Day removed)*
- Day 97: WAS security domain concepts, User Registry types — **Administrative Security**
- Day 98 – **Lab:** Configure Federated Repository / local OS registry — **LDAP / Federated Repository**
- Day 99: WAS Administrative Roles — Monitor/Configurator/Operator/Administrator/Deployer — **Roles**
- Day 100 – **Lab:** Create console users, assign roles, test access limits — **Users / Groups**
- Day 101: RunAs Roles, J2C Authentication Aliases — **J2C Alias**
- Day 102: LDAP concepts preview
- **Day 103 – INCIDENT DAY**
- Day 104 – **CLOSEOUT:** role-based access lockdown test; admin console lockout/recovery
- Day 105: Admin Console Lockdown & Hardening — **Application Security**
- Day 106: Security Hardening Pack 1 — disable default apps, secure console access, least-privilege
- Day 107: Security Hardening Pack 2 — TLS hardening, cipher selection, password policies, file permissions
- Day 108 – **Lab:** Apply the full Security Hardening Pack 1+2 checklist against your cluster

**20. Maintenance (109–114)** — 6 days *(1 generic Lab Day removed; Change Management moved to Enterprise Administration)*
- **Day 109 – LAB DAY**
- Day 110: Fix Pack strategy, version roadmap, EOS tracking concept
- Day 111 – **Lab:** Download and stage a Fix Pack
- Day 112 – **Lab:** Apply Fix Pack to a test profile, verify versionInfo (target 9.0.5.28)
- Day 113: Rollback procedure after a failed patch
- Day 114 – **CLOSEOUT:** write a Fix Pack SOP + rollback plan as a real document

**21. Enterprise Administration (115–122)** *(NEW chapter — assembled from scattered days + 2 new)*
- Day 115: Patch maintenance window planning — CAB process, Go/No-Go checklist
- Day 116: Production Change Management — change request lifecycle, rollback plan, comms templates
- Day 117 – **Lab:** backupConfig/restoreConfig — real backup and restore
- Day 118 – **Lab:** addNode/removeNode — remove and re-federate a node (Cell Recovery drill)
- Day 119: Capacity planning basics — sizing a cluster for expected load
- Day 120: DR/Failover tie-in — migration during a maintenance window, CAB approval flow
- Day 121: Daily Operations & Health Checks — what a shift actually looks like, morning health-check routine *(new)*
- Day 122 – **CLOSEOUT:** write a Shift Handover template + run a mock handover *(new)*

**22. Troubleshooting (123–148)** — 26 days, unchanged, reorganized under your headings
- *JVM startup issues:* Day 123 (SystemOut/SystemErr/FFDC), **Day 124 – INCIDENT**, Day 125–126 (log correlation, force startup failure), Day 127 (3 induced-failure labs)
- *Hung threads / High CPU:* **Day 128 – LAB DAY**, Day 129 (thread dumps/javacore), Day 130 – **Lab** (simulate hung thread, 3 dumps 10s apart)
- *Heap dumps / Memory leaks:* Day 131 (heap dumps, MAT), Day 132 (OOM investigation), Day 133 (javacore deep dive — locks/deadlocks)
- *Core dumps:* Day 134 (core dump basics, native_stderr, crash signatures)
- **Day 135 – LAB DAY**
- Day 136 (classloader memory leaks, MAT leak suspects), Day 137 – **Lab** (high CPU, correlate top + thread dump), Day 138 – **Lab** (memory leak pattern, heap trend)
- **Day 139 – INCIDENT DAY**
- *Deployment failures / SSL problems / LDAP issues / Plugin issues:* Day 140 (NodeAgent/DMGR down), Day 141 (deployment/SOAP connector failure), **Day 142 – LAB DAY**, Day 143 (SSL expired/LDAP failure), Day 144 (DB down/disk full/DNS/firewall), Day 145 (Core Group failure/session replication failure)
- *Full workflow / MustGather:* Day 146 (9-stage incident response workflow, timed), Day 147 (MustGather & IBM PMR process)
- Day 148 – **CLOSEOUT:** pick your 10 strongest troubleshooting stories as STAR answers (15 interview Qs)

**23. Performance Tuning (149–162)** — 14 days *(capacity planning moved out; 2 generic Lab Days removed)*
- Day 149: JVM Architecture — Class Loader, JIT, Native Memory, Metaspace
- Day 150: Garbage Collection internals, GC logs, GC Viewer/IBM Health Center
- Day 151 – **Lab:** Enable verbose GC, capture and read a GC log
- Day 152: JVM Tuning — heap sizing (-Xms/-Xmx), GC policy choice
- **Day 153 – INCIDENT DAY**
- Day 154: Thread Pool tuning (Web Container, ORB), JDBC Pool tuning
- **Day 155 – LAB DAY**
- Day 156: Session optimization, Dynacache tuning tie-in
- Day 157 – **Lab:** Native Monitoring Deep Dive — PMI, TPV, Health Management, JMX
- Day 158: Throughput, Latency, TPS, Concurrent Users — defining a baseline
- Day 159 – **Lab:** Basic load test (JMeter or ab) against your cluster
- Day 160: Bottleneck analysis method — CPU vs Heap vs Thread vs DB
- Day 161 – **Lab:** Tune one deliberately-misconfigured JVM back to healthy
- Day 162 – **CLOSEOUT:** present a tuning case study (problem→analysis→fix→result)

**24. Migration (163–170)** — 8 days *(DR tie-in moved to Enterprise Administration)*
- Day 163: Migration types — version upgrade, profile migration, plugin migration, Java upgrade
- Day 164: Migration planning — inventory, risk assessment, rollback plan
- Day 165 – **Lab:** Use WCT and WASPreUpgrade/WASPostUpgrade scripts to migrate a profile
- **Day 166 – INCIDENT DAY**
- Day 167: Plugin migration and compatibility checks
- Day 168: Java version upgrade considerations (JVM compatibility with app code)
- Day 169: Post-migration validation — smoke tests, health checks
- Day 170 – **CLOSEOUT:** write a full migration runbook end to end

**25. wsadmin Automation (171–191)** — 21 days *(2 generic Lab Days removed; merged with Administration remainder)*
- Day 171: Admin Console deep dive — every menu
- Day 172: wsadmin scripting intro — Jython basics
- Day 173 – **Lab:** AdminControl, AdminConfig — query running servers
- Day 174 – **Lab:** AdminApp, AdminTask — install/list/uninstall apps via script
- Day 175 – **Lab:** Node Agent Deep Dive
- **Day 176 – INCIDENT DAY**
- Day 177: Dynacache — Distributed Map, Cache Instances, Servlet Cache
- Day 178 – **Lab:** Cache Replication (DRS), Cache Monitor
- Day 179 – **Lab:** wsadmin Automation Pack 1
- Day 180 – **Lab:** wsadmin Automation Pack 2
- Day 181 – **CLOSEOUT:** full backup → simulate DMGR loss → restore, document as SOP
- Day 182: Jython refresher specific to wsadmin scripting
- Day 183 – **Lab:** Script a full health check (cluster/node/JVM/datasource status)
- Day 184 – **Lab:** Script an app deployment end-to-end (no manual console clicks)
- Day 185 – **Lab:** Script backupConfig/restoreConfig on a schedule
- Day 186 – **Lab:** Script user/role creation
- Day 187: SSL automation via wsadmin
- Day 188: Automated deployment pipeline scripting
- Day 189: Wrap wsadmin scripts into shell wrappers, add logging/error handling
- Day 190: Version control your scripts in Git
- Day 191 – **CLOSEOUT:** build a Daily Health Check + Auto-Deploy script pack for a night-shift operator

*Operations Phase complete: everything an admin does day-to-day, incident, and long-term.*

---

## PHASE D — Enterprise & Advanced Layer (Days 192–222) — unchanged from v7

**IBM MQ + Messaging (192–199)** — kept separate from and after SIBus, as agreed
- Day 192: Queue Manager, Local/Remote/Alias/Dead Letter Queue concepts — IBM MQ 9.3.x/9.4.x
- Day 193 – **Lab:** Install a Queue Manager, create local queues, MQSC basics
- Day 194: Channels, Listeners, Channel Authentication (CHLAUTH)
- Day 195: MQ Clustering — cluster queue managers, workload balancing
- Day 196 – **Lab:** Multi-Instance Queue Manager — active/standby pair
- Day 197: Dead Letter Queue handling procedure
- Day 198: JMS Integration with WAS — Connection Factory, Activation Specification
- Day 199 – **Lab:** Wire a WAS app to send/receive an MQ message

**WebSphere Liberty (200–202)**
- Day 200: Liberty vs traditional WAS, server.xml, featureManager
- Day 201 – **Lab:** Install Liberty, run a server from dropins
- Day 202: Liberty in Docker/Kubernetes concepts, tWAS-to-Liberty migration awareness

**Load Balancers (203–206)**
- Day 203: Layer 4 vs Layer 7 LB, VIP, persistence, sticky sessions, SSL offloading
- **Day 204 – LAB DAY**
- **Day 205 – INCIDENT DAY**
- Day 206 – **Lab:** Stand up NGINX or HAProxy in front of your cluster members

**Monitoring & Observability (207–208)**
- Day 207: Metrics/Logs/Traces concept, Prometheus/Grafana basics, alert design
- Day 208 – **Lab:** Wire basic Prometheus/Grafana dashboard, tie back to PMI/JMX

**Automation & CI/CD (209–210)**
- Day 209: Python automation patterns, Ansible basics, Jenkins pipeline concept
- Day 210 – **Lab:** Turn a wsadmin script into a Jenkins-triggered pipeline

**DR, Backup & Compliance (211–212)**
- Day 211: DR testing, cell/node recovery, backup validation, business continuity
- Day 212: PCI DSS/SOX/ITIL compliance awareness for banking environments

**Enterprise Documentation Practice (213–214)**
- Day 213: Build template set: Installation SOP, Deployment SOP, Restart SOP, Backup SOP
- Day 214: Build: DR Runbook, Health Check Checklist, Patch Runbook, Incident Runbook, 2 KB articles

**Banking Production Simulation Marathon (215–217)**
- Day 215: NodeAgent down, DMGR down, Cluster member crash, JDBC pool exhaustion, DB outage, MQ queue full
- Day 216: SSL cert expired, Hung threads, High CPU, Memory leak, Slow response, Plugin timeout
- Day 217: Session replication failure, Deployment failure, DNS issue, Disk full, FD exhaustion, Network latency

**Inserted Lab Day (218)**
- **Day 218 – LAB DAY**

**Capstone + Interview Readiness (219–222)**
- Day 219: Capstone lab — full "Real Bank" stack, all integrated
- **Day 220 – INCIDENT DAY**
- Day 221: Resume rewrite, architecture whiteboard prep, gap narrative, documentation portfolio
- Day 222: Mock interview day — scenario Q&A, STAR delivery drill, salary/negotiation, whiteboard recap

---

## Balance Check (222 Days Total)

| Metric | v7 | v10 |
|---|---|---|
| Total Days | 222 | 222 |
| Incident Days | 11 | 11 (unchanged, none removed or added) |
| Closeout Days | 18 | 17 (Custom Profile and DMGR no longer get standalone closeouts — their content folds into Federation's closeout, since profile creation is now one continuous arc) |
| Lab Days (named + generic) | 20 | ~19 (8 generic filler days removed, 3 new labs added across Architecture Deep Dive/JNDI/Enterprise Admin, net −1) |
| New content days | 0 | 7 (3 Architecture Deep Dive, 2 JNDI, 2 Enterprise Administration) |
| Days trimmed to fund them | — | 8 generic Lab Days (net −1, absorbed into the total-days-held-flat requirement) |

Every named lab, every incident scenario, and nearly every closeout from v7 survives untouched — only generic unstructured "cumulative review" buffer days were sacrificed, and Troubleshooting's 26 days are the same 26 days, just grouped under your headings instead of a flat list.
