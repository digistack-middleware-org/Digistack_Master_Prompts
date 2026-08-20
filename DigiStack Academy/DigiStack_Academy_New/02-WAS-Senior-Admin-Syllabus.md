# Senior WebSphere Administrator Syllabus — Banking Context

Upload this file to the Claude Project's **Project Knowledge** section.

Target: Senior WAS Administrator, 10-year depth, banking enterprise context.
Every topic includes: Concept Explanation → Real Banking Scenario → Hands-On Lab → Production Incident → Closeout.
Every module close: 1 overall module lab + 6+ production incidents with resolution.
Every arc close: Arc Capstone.

---

## ARC 1 — Foundation & Architecture

**Module 1: WAS Strategic Context**
1.1 Product Family · 1.2 Fix Pack Strategy · 1.3 IIM Internals · 1.4 vs Competitors · 1.5 Admin's Real Role · 1.6 IBM Support Engagement · 1.7 Interview Trap Questions

**Module 2: Architecture Internals**
2.1 Cell/Node/Server Internals · 2.2 DMGR Internals · 2.3 Node Agent Internals · 2.4 Config Repository · 2.5 Scope Hierarchy · 2.6 Port Architecture · 2.7 ORB & IIOP · 2.8 Admin Console Internals · 2.9 Process Communication · 2.10 Config Locking · 2.11 MBean Architecture · 2.12 Architecture Design Decisions

*Arc 1 Capstone:* Design a cell topology for a stated banking workload; defend every choice.

---

## ARC 2 — Build & Deploy

**Module 3: Installation**
3.1 Pre-Install Decisions · 3.2 OS Hardening · 3.3 IIM in Depth · 3.4 Silent Install · 3.5 Multi-Component Install · 3.6 Fix Pack Upgrade · 3.7 versionInfo.sh · 3.8 Install Rollback · 3.9 Install Documentation · 3.10 Common Install Failures

**Module 4: Standalone Profile**
4.1 Profile Architecture · 4.2 Profile Directory Files · 4.3 JVM Architecture · 4.4 JVM Sizing · 4.5 Process Management · 4.6 Environment Variables · 4.7 Logging Config · 4.8 Admin Security Day 1

**Module 5: Deployment Concepts / ClassLoader**
5.1 EAR/WAR/JAR Packaging · 5.2 ClassLoader Deep Dive · 5.3 ClassLoader Diagnosis · 5.4 Virtual Host Architecture · 5.5 ibm-web-bnd.xml vs web.xml · 5.6 Shared Libraries · 5.7 Edition Control · 5.8 Descriptor Hierarchy

**Module 6: Deploy to Standalone**
6.1 Deployment Strategy Types · 6.2 wsadmin Deployment · 6.3 Deployment Scripts · 6.4 App Validation · 6.5 Rollback Procedure · 6.6 Deployment Logging · 6.7 Zero-Downtime Concepts · 6.8 Config via wsadmin

**Module 7: DMGR & Custom Profile**
7.1 DMGR Internals · 7.2 Custom Profile Internals · 7.3 Profile Strategy · 7.4 DMGR HA · 7.5 Creating Profiles Production Way · 7.6 Naming Conventions · 7.7 Post-Creation Baseline

**Module 8: Federation**
8.1 Federation Internals · 8.2 Pre-Federation Checklist · 8.3 Trust/Certificates · 8.4 addNode Parameters · 8.5 Post-Federation Verification · 8.6 removeNode · 8.7 Federation in Pipelines · 8.8 Federation Troubleshooting · 8.9 Multi-Node Architecture

**Module 9: Synchronization**
9.1 Sync Architecture · 9.2 Sync Protocol · 9.3 Auto Sync Config · 9.4 Manual Sync Scenarios · 9.5 syncNode.sh · 9.6 Sync Failure Diagnosis · 9.7 Partial Sync/Split-Brain · 9.8 Configuration Drift · 9.9 Sync/Deploy Timing

**Module 10: Deploy to Federated Server**
10.1 Managed App Server · 10.2 Deployment Targeting · 10.3 Deploy to Managed Server · 10.4 Sync After Deploy · 10.5 App State Management · 10.6 Multi-Server Non-Cluster · 10.7 Troubleshooting Managed Server

*Arc 2 Capstone:* Federate 2 nodes, deploy an app, diagnose an injected sync failure.

---

## ARC 3 — Scale & Availability

**Module 11: Clustering**
11.1 Cluster Design Architecture · 11.2 Cluster Internals · 11.3 Port Management · 11.4 rippleStart vs Full Start · 11.5 Config Propagation · 11.6 Cluster SPOF · 11.7 Cross-Node Clustering · 11.8 Weights/Load Distribution · 11.9 Health Monitoring · 11.10 Anti-Patterns

**Module 12: Deploy to Cluster**
12.1 Cluster Deploy Internals · 12.2 Deployment Options · 12.3 Rolling Deployment · 12.4 Maintenance Mode · 12.5 Verification Checklist · 12.6 Update Strategies · 12.7 Cluster Deploy via wsadmin · 12.8 Failure Isolation · 12.9 Change Management

**Module 13: HA & DR**
13.1 HA Architecture Patterns · 13.2 DMGR SPOF Mitigation · 13.3 DMGR Backup Strategy · 13.4 Node Agent Resilience · 13.5 Cluster Member Failover · 13.6 DR Runbook · 13.7 Backup Automation · 13.8 Recovery Scenarios

**Module 14: Capacity Planning & Sizing** *(new)*
14.1 Load-to-Node Translation · 14.2 Heap Sizing Formula · 14.3 Thread Pool Sizing vs Downstream Limits · 14.4 Headroom Planning · 14.5 Documenting Sizing Decisions

*Arc 3 Capstone:* Size and design HA for a stated load profile; justify every number.

---

## ARC 4 — Data & Integration

**Module 15: JDBC**
15.1 Pool Architecture · 15.2 JDBC Providers · 15.3 XA vs Non-XA · 15.4 J2C Auth Alias · 15.5 Pool Sizing · 15.6 Pool Timeout Params · 15.7 Connection Leak Detection · 15.8 Stale Connection Handling · 15.9 Cell vs Server Scope · 15.10 DB Failover · 15.11 JDBC Diagnosis · 15.12 wsadmin JDBC Automation

**Module 16: Messaging — MQ/JMS/SIBus** *(new)*
16.1 SIBus vs External MQ · 16.2 JMS Connection Factories/Activation Specs · 16.3 MQ Queue Manager Integration · 16.4 MDB Deployment/Sizing · 16.5 Poison Message/DLQ Handling · 16.6 Diagnosing Stuck Queues

*Arc 4 Capstone:* Trace a transaction across JDBC (XA) + MQ; identify where a 2PC failure surfaces.

---

## ARC 5 — Operate & Defend

**Module 17: IHS**
17.1 Role in Architecture · 17.2 Production Install · 17.3 httpd.conf Mastery · 17.4 plugin-cfg.xml · 17.5 Plugin Generation · 17.6 Plugin Error Interval · 17.7 Plugin Retry Interval · 17.8 SSL/TLS Architecture · 17.9 Virtual Hosting · 17.10 Tuning · 17.11 Log Analysis · 17.12 IHS HA · 17.13 Troubleshooting

**Module 18: Session Management** *(trimmed)*
18.1 Session Lifecycle/JSESSIONID · 18.2 Affinity via Plugin · 18.3 Failover Without Replication · 18.4 Replication vs DB Persistence · 18.5 Timeout Precedence · 18.6 When Session Design Is Wrong

**Module 19: User Management & Security**
19.1 Security Model · 19.2 User Registry Architecture · 19.3 LDAP Integration · 19.4 Federated Repository · 19.5 Admin Roles · 19.6 Role-to-Group Mapping · 19.7 Java 2 Security · 19.8 SSL Config Architecture · 19.9 Admin Security Recovery · 19.10 wsadmin with Security On · 19.11 Security Audit Checklist

**Module 20: Performance Tuning & JVM Diagnostics**
20.1 GC Deep Dive · 20.2 verboseGC Parsing · 20.3 Thread Pool Architecture · 20.4 Thread Dump Analysis · 20.5 Heap Dump Analysis · 20.6 PMI · 20.7 Pool Monitoring · 20.8 CPU/Memory Baseline · 20.9 Diagnosis Methodology · 20.10 Web Container Tuning

**Module 21: Logging & FFDC**
21.1 Log Architecture · 21.2 Trace String Config · 21.3 Trace String Syntax · 21.4 FFDC · 21.5 Log Rotation/Retention · 21.6 Cross-Component Correlation · 21.7 IBM Message Decoder · 21.8 Log-Based Incident Analysis

**Module 22: Patch & Vulnerability Management** *(new)*
22.1 CVE Monitoring · 22.2 Emergency iFix vs Scheduled Fix Pack · 22.3 Patch Testing Gate · 22.4 Rollback Decision Criteria

**Module 23: Licensing & Modernization Framework** *(new)*
23.1 PVU/Core Licensing · 23.2 WAS ND vs Liberty vs OpenLiberty · 23.3 Migration Effort Estimation · 23.4 Containerization Context · 23.5 Building the Business Case

**Module 24: wsadmin / Automation Mastery**
24.1 wsadmin Architecture · 24.2 Jython in WAS · 24.3 AdminConfig · 24.4 AdminControl · 24.5 AdminApp · 24.6 AdminTask · 24.7 MBean Querying · 24.8 Script Library Patterns · 24.9 Automation Framework · 24.10 Interview Jython Scripts

*Arc 5 Capstone:* Full incident simulation from a log bundle — root cause, fix, post-incident report.

---

## ARC 6 — Enterprise Maturity (10-Year Closer)

**Module 25: Change Management & ITSM**
25.1 Why Change Management Exists in Banking — Regulatory driver (RBI, SOX), what happens without it (real bank outage stories)
25.2 ITSM Ticket Types — RFC (Request for Change), SCTASK, TASK, Incident, Problem — when each is raised for WAS work
25.3 RFC Lifecycle — Draft → Review → CAB approval → Scheduled → Implemented → PIR — full WAS deployment RFC walkthrough
25.4 CAB (Change Advisory Board) — What they ask, how a WAS admin presents a deployment change, risk/impact/rollback template
25.5 Deployment Window Management — Banking blackout periods (month-end, NEFT cutover window, RBI reporting night), how to work within them
25.6 Rollback Plan Writing — What a real rollback plan looks like for a WAS EAR deployment — not just "redeploy old version"
25.7 Post-Implementation Review (PIR) — What you write after every production change — evidence, outcome, lessons learned
25.8 Emergency Change Process — P1 incident → emergency RFC → fast-track CAB approval — the 2 AM process
25.9 Change Freeze Periods — Year-end freeze, audit freeze, how to handle urgent fixes during freeze
25.10 ServiceNow for WAS Admins — Creating, updating, closing ITSM tickets, linking RFC to deployment evidence

*Module 25 Overall Lab:* Write a complete RFC package for a WAS fix pack upgrade on a banking NEFT gateway — CAB presentation, rollback plan, deployment window, PIR template.
*Module 25: 6+ Incidents:* Change-management-related production failures — unauthorized change causing outage, rollback failure, missed deployment window, PIR revealing repeat failure, emergency RFC under pressure, freeze violation consequences.

---

**Module 26: Enterprise Infrastructure Integration**
26.1 F5/NetScaler Load Balancer Integration — iRules, persistence profiles, health monitors pointed at WAS cluster members, SSL offload at F5 vs end-to-end SSL
26.2 F5 Health Monitor Tuning — URI-based health check, what WAS returns that F5 reads, false positives/negatives in banking
26.3 APM Agent Integration — Dynatrace/AppDynamics agent inside WAS JVM — startup arguments, heap overhead, what breaks when APM agent corrupts
26.4 Tivoli/IBM Monitoring — ITM agent for WAS, ITCAM, how monitoring agents interact with WAS MBeans
26.5 CyberArk / PAM Vault Integration — WAS admin credentials stored in vault, wsadmin scripts that call vault API, what happens when password rotation breaks overnight batch
26.6 Splunk / SIEM Log Forwarding — Forwarding WAS SystemOut.log to Splunk, field extraction, alert rules for WSVR0605W and OOM patterns
26.7 ServiceNow CMDB — WAS servers registered as CIs, relationship mapping (WAS server → application → business service), keeping CMDB accurate
26.8 Ansible / Infrastructure as Code — Ansible playbooks for WAS config, idempotency in WAS context, what Ansible cannot automate in WAS
26.9 SSH Bastion / Jump Server — How WAS admins access production in a bank — no direct SSH, bastion host, session recording, audit trail
26.10 NFS / Shared Storage — WAS shared config on NFS, what NFS mount failure does to a WAS cell, stale file handle recovery

*Module 26 Overall Lab:* Simulate an F5 health monitor misconfiguration that takes one cluster member out of rotation — diagnose from IHS plugin.log + F5 logs, fix without a full outage.
*Module 26: 6+ Incidents:* APM agent causing JVM OOM, CyberArk rotation breaking wsadmin batch job, Splunk forwarder consuming WAS file handles, NFS stale mount corrupting node sync, Ansible playbook applying wrong scope, F5 iRule blocking WAS health check URI.

---

**Module 27: Compliance & Audit Readiness**
27.1 Regulatory Landscape for WAS in Indian Banking — RBI IT Framework, IDRBT guidelines, SEBI cybersecurity circular, PCI-DSS scope for payment apps on WAS
27.2 SSL/TLS Compliance — Minimum TLS version (TLS 1.2 mandatory, 1.3 preferred), banned cipher suites, how to audit and enforce in WAS
27.3 Admin Console in Production — Why admin console must be disabled or access-restricted in production — audit finding walkthrough
27.4 J2 Security Audit — What auditors look for in Java 2 security policy files, common violations, how to document exceptions
27.5 Password & Credential Audit — No hardcoded passwords in wsadmin scripts, J2C alias review, PropFilePasswordEncoder evidence
27.6 Patch Compliance Evidence — How to produce versionInfo.sh output as audit evidence, fix pack currency SLA (typically 90 days for critical CVEs in banking)
27.7 Access Control Audit — WAS admin role assignments, who has admin vs operator vs monitor — LDAP group review
27.8 Log Retention Compliance — 90-day / 180-day log retention mandates, what WAS logs count as audit evidence
27.9 Audit Trail for Config Changes — wsadmin change logging, admin console audit log, how to prove who changed what and when
27.10 IS Audit Walkthrough Simulation — Full mock IS audit of a WAS environment — 20 questions an auditor asks, correct admin responses
27.11 Remediation Documentation — How to write an audit finding remediation report — finding, root cause, fix applied, evidence attached

*Module 27 Overall Lab:* Run a full IS audit simulation on your WAS environment — produce audit evidence pack (versionInfo output, cipher suite report, admin role list, log retention proof, RFC evidence).
*Module 27: 6+ Incidents:* TLS 1.0 found enabled on payment gateway (audit critical), hardcoded password found in wsadmin script, admin console accessible from internet, J2 security violation blocking NEFT app, log retention gap during RBI audit, unauthorized admin role found on production node.

---

**Module 28: SSL & Certificate Disaster Recovery**
28.1 WAS SSL Architecture Deep Dive — NodeDefaultSSLSettings, CellDefaultSSLSettings, repertoire, keystore types (JKS vs PKCS12 vs CMS/kdb), trust chain
28.2 Certificate Lifecycle in Banking — Internal CA vs external CA (DigiCert, GlobalSign), certificate request → approval → install → verify → monitor expiry workflow
28.3 ikeycmd / gskcmd Mastery — Full command set for kdb management — create, import, export, list, delete, self-signed vs CA-signed
28.4 Certificate Expiry — The silent killer — how WAS behaves as cert approaches expiry, SSL handshake failure symptoms, monitoring expiry proactively
28.5 Zero-Downtime Certificate Rotation — Replace cert on DMGR + all nodes + IHS without dropping active connections — exact sequence
28.6 LTPA Token & SSL Relationship — What LTPA tokens are, how they tie to SSL config, what breaking LTPA does to SSO in a banking portal
28.7 SSL Handshake Failure Diagnosis — Reading SSL handshake errors in SystemOut.log + IHS error_log, common root causes (expired cert, missing CA in trust, cipher mismatch)
28.8 SSL DR Scenario 1 — DMGR keystore lost/corrupted — full rebuild procedure, re-establishing node trust without losing running apps
28.9 SSL DR Scenario 2 — Certificate expired in production at midnight — emergency rotation procedure, who to call, what to do first
28.10 SSL DR Scenario 3 — CA root certificate expired — cascading trust failure across entire cell — diagnosis and cell-wide trust rebuild
28.11 SSL DR Scenario 4 — IHS kdb corrupted after server crash — IHS SSL restore from backup, verify before re-enabling production traffic
28.12 SSL Backup Strategy — What to back up (kdb, stash, sth files), where, how often, how to verify backups are valid without restoring to production
28.13 SSL in DR Site — Certificate replication to DR site, ensuring DR WAS cell has valid, current keystores before failover is needed

*Module 28 Overall Lab:* Simulate certificate expiry on a test WAS cell — observe the failure, perform zero-downtime rotation on DMGR + node + IHS, verify SSL handshake restored, write post-incident report.
*Module 28: 6+ Incidents:* Midnight cert expiry on internet banking portal, LTPA token mismatch breaking bank staff SSO after cert rotation, IHS kdb lost during disk failure, CA root cert expiry cascading across 3 WAS cells, DR site cert 6 months out of date discovered during DR drill, cipher suite downgrade attack detected in audit.

*Arc 6 Capstone:* Full enterprise readiness audit — given a WAS banking environment, produce: RFC for a fix pack upgrade (Module 25), verify all enterprise integrations are working (Module 26), produce the IS audit evidence pack (Module 27), perform certificate health check and rotate one expiring cert under change control (Module 28). Present findings as a senior admin would to a bank's IT risk committee.

---

## Incident Pattern Library — Standalone Reference

*Not tied to any one module. Study this after completing Arc 3. Add to it as you complete later modules.*

### How to Use
Each pattern has: Pattern Name → Trigger Symptoms → Most Likely Causes (ranked) → First 3 Diagnostic Steps → Distinguishing Factor (what separates this from a similar-looking incident)

### Category 1: JVM / Memory (10 patterns)
1. Heap exhaustion — OutOfMemoryError: Java heap space
2. Metaspace exhaustion — OutOfMemoryError: Metaspace (WAS 9 with IBM J9)
3. GC overhead limit exceeded — application alive but response time > 30s
4. Memory leak — heap grows session by session, never releases
5. ClassLoader leak — permgen/metaspace grows after repeated deployments
6. Native memory exhaustion — JVM crash without OOM in heap, native_stderr.log shows signal 11
7. Thread stack overflow — StackOverflowError in recursive call
8. Heap dump trigger loop — JVM generating heapdump every 5 minutes
9. JVM crash on startup — javacore generated before server reaches STARTED state
10. GC pause > 10 seconds — application appears hung, thread dump shows GC thread blocking all others

### Category 2: ClassLoader (8 patterns)
1. ClassCastException — same class loaded by two different classloaders
2. ClassNotFoundException — JAR present in EAR but not visible to app
3. NoSuchMethodError — old version of class taking precedence over new version
4. LinkageError — class loaded twice in same classloader hierarchy
5. Shared library version conflict — two apps need different versions of same JAR
6. ClassLoader leak after undeploy — old app classloader not GC'd, permgen grows
7. ibm-web-bnd.xml missing — app deploys but binding fails silently
8. Parent-last misconfiguration — app uses WAS internal class instead of its own

### Category 3: JDBC / Connection Pool (10 patterns)
1. Pool exhaustion — ConnectionWaitTimeoutException after connectionTimeout
2. Connection leak — pool slowly exhausts, app never returns connections
3. Stale connection — DB restarted, WAS pool holds dead connections, EntirePool purge needed
4. XA transaction orphan — in-doubt transaction left after DB crash, must be manually resolved
5. J2C auth alias wrong — authentication failure on datasource, not connection refusal
6. Pool min/max same — no headroom, burst traffic causes immediate wait
7. DB failover pool recovery — primary DB failed over, pool must reconnect to secondary
8. Connection validation failure — testConnection returns error but DB is up (driver bug)
9. Prepared statement cache corruption — after schema change, cached statements invalid
10. Multi-datasource XA deadlock — two datasources in same XA transaction deadlock each other

### Category 4: Clustering / HA (8 patterns)
1. Split-brain — cluster members running but not seeing each other
2. Plugin not updated after cluster change — traffic still going to removed member
3. rippleStart rolling restart skipped — all members restarted simultaneously, full outage
4. Cluster member weight zero — member running but receiving no traffic
5. Port conflict on new cluster member — member fails to start, port already bound
6. Session affinity broken — JSESSIONID clone ID not matching any live member
7. Cluster config not propagated — new app deployed on DMGR, one member still running old version
8. Cross-node cluster latency — members on Node1 and Node2, network between nodes degraded

### Category 5: Federation / Sync (6 patterns)
1. Node agent cannot connect to DMGR — SOAP port blocked by firewall change
2. Config drift — node running config from 3 deployments ago, sync was silently failing
3. Partial sync — one file syncs, another doesn't — exclusion pattern misconfigured
4. Clock skew — node and DMGR time difference > 5 minutes, SSL handshake fails
5. addNode fails — hostname resolution fails, DMGR SOAP port not reachable
6. removeNode leaves orphan config — node removed but DMGR still shows it, causes sync errors

### Category 6: IHS / Plugin (6 patterns)
1. 503 Service Unavailable — all cluster members marked down in plugin
2. Plugin not regenerated — new app deployed, plugin still routing to old server list
3. Plugin error interval too long — dead member getting traffic for 60 seconds after failure
4. IHS worker thread exhaustion — MaxRequestWorkers reached, new connections refused
5. SSL handshake failure at IHS — cert expired on IHS kdb, not on WAS
6. Virtual host mismatch — request coming to correct port but wrong host alias, 404

### Category 7: SSL / Certificates (8 patterns)
1. Certificate expired — SSL handshake fails, clear error in SystemOut
2. CA root expired — entire trust chain broken, harder to diagnose than leaf cert expiry
3. Missing intermediate CA — chain incomplete, some clients connect, others reject
4. LTPA token invalidated — all logged-in users kicked out after cert rotation
5. Cipher suite mismatch — client and WAS cannot agree on cipher, connection refused
6. Keystore password wrong after migration — node cannot read its own keystore after restore
7. Self-signed cert in production — works but auditors flag it, breaks some strict clients
8. Certificate CN mismatch — cert issued for wrong hostname, SNI-aware clients reject

### Category 8: Change Management Failures (6 patterns)
1. Unauthorized change — someone deployed directly without RFC, caused outage
2. Rollback failure — rollback plan written but not tested, old EAR incompatible with current DB schema
3. Change during freeze — emergency fix applied, violated freeze policy, audit finding raised
4. PIR not written — change considered closed, same failure recurs 3 months later
5. Wrong deployment window — deployed during peak NEFT hours, transaction failures
6. CAB approval bypassed under pressure — senior manager pushed deployment, RFC not approved

---

## Progress Tracker (update as you complete topics)

- [ ] Arc 1 — Module 1 (7 topics)
- [ ] Arc 1 — Module 2 (12 topics)
- [ ] Arc 2 — Modules 3-10
- [ ] Arc 3 — Modules 11-14
- [ ] Arc 4 — Modules 15-16
- [ ] Arc 5 — Modules 17-24
- [ ] Arc 6 — Module 25: Change Management & ITSM (10 topics)
- [ ] Arc 6 — Module 26: Enterprise Integration (10 topics)
- [ ] Arc 6 — Module 27: Compliance & Audit Readiness (11 topics)
- [ ] Arc 6 — Module 28: SSL & Certificate DR (13 topics)
- [ ] Incident Pattern Library — studied after Arc 3 completion
