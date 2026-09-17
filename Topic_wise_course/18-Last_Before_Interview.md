# 📘 WAS Admin — Zero to Solo-Expert Study Plan (130 Days)

> **Goal:** WebSphere Application Server Administrator — Interview-Ready + Solo Production-Ready (Banking Environment)
>
> **Assumption:** 3–4 hours/day on weekdays, 5–6 hours on weekends
>
> **Total Duration:** 130 Days (~4.5 months) | 5 Phases | WAS Core + OS + DB + Network + Scripting + Bank Ecosystem Tools + Interview Packs

---

## 📅 Phase Overview

| Phase | Days | Focus |
|-------|------|-------|
| Phase 0 | 1–14 | Foundations (Java, Linux, AIX, Banking IT, Networks) |
| Phase 1 | 15–30 | Installation & Architecture |
| Phase 2 | 31– | Core Administration |
| Phase 3 | 63–90 | Advanced Administration |
| Phase 3.5 | 91–100 | Solo-Admin Ecosystem Skills |
| Phase 4 | 101–110 | Expert / Architect Level |
| Phase 5 | 111–130 | Interview Prep Packs |

---

# PHASE 0: Foundations (Days 1–14)

| Day | Topic | Banking Focus |
|-----|-------|---------------|
| 1 | Java: JVM, JRE, JDK, bytecode | Why banks run Java |
| 2 | JVM memory: heap, stack, metaspace | OOM foundation |
| 3 | JEE:, JSP | Internet banking request flow |
| 4 | EJB, JNDI, JDBC | Fund transfer app layers |
| 5 | JMS, JCA | messages |
| 6 | Linux: filesystem, permissions, vi | RHEL servers |
| 7 | Linux: grep/awk/sed, ps/top/netstat | Daily admin commands |
| 8 | Linux: logs, SSH, NFS, systemd | Log shares, remote access |
| 9 | AIX basics | LPARs |
| 10 | **Linux performance: iostat, vmstat, free, lsof, ulimit** ⭐ NEW | Prove "WAS slow" is actually OS |
| 11 | Banking systems: core banking, SWIFT/NEFT/RTGS/ACH | What you support |
| 12 | Network: DMZ, firewalls, F5, ports | Traffic flow |
| 13 | ITIL + SOX/PCI-DSS | Change windows, audits |
| 14 | **Revision: draw end-to-end request flow** | — |

---

# PHASE 1: Installation & Architecture (Days 15–30)

| Day | Topic |
|-----|-------|
| 15 | WAS family: Base vs ND vs Liberty |
| 16 | Install IIM + WAS (LAB) |
| 17 | Profiles, manageprofiles (LAB) |
| 18 | **Cell → Node → NodeAgent → Cluster** (deep dive) |
| 19 | Dmgr + federate node (LAB) |
| 20 | Admin console full tour |
| 21 | wsadmin: 5 objects (LAB) |
| 22 | Jython basics |
| 23 | Start/stop via scripts (LAB) |
| 24 | Node sync, nodeagent, syncNode |
| 25 | Directory structure, logs, ports |
| 26 | Fix, iFixes |
| 27 | Virtual hosts, context roots |
| 28–29 | **LAB PROJECT: Dmgr + 2 nodes + cluster + deploy EAR** |
| 30 | Revision + Q&A drill |

---

# PHASE 2: Core Administration (Days 31–62)

| Day | Topic | Banking Focus |
|-----|-------|---------------|
| 31 | Deployment: EAR/WAR/JAR, bindings | CardPinChange.ear |
| 32 | Deploy via console + wsadmin (LAB) | — |
| 33 | Rollout update, atomic deploy | Zero-downtime release |
| 34 | Classloading PARENT_FIRST/LAST, shared libs | Jackson conflict |
| 35 | Heap config -Xms/-Xmx, verbose GC | — |
| 36 | GC policies: gencon, balanced | — |
| 37 | OOM + heapdump analysis | Sunday batch OOM |
| 38 | PMI + Tivoli Performance Viewer | Monitoring |
| 39 | Thread pools: WebContainer, ORB | Slow statements |
| 40 | IHS install + httpd.conf | DMZ web tier |
| 41 | plugin-cfg.xml generation/propagation | 404 vs 503 |
| 42 | Session affinity, JSESSIONID | User kicked out |
| 43 | HTTP session + replication | Active-active DCs |
| 44 | JDBC + data sources (LAB) | DB connectivity |
| 45 | Connection pool tuning | Pool exhausted 10 AM |
 46 | J2C aliases | DB credentials |
| 47 | **DB basics: SQL select, locks, top SQL, reading evidence** ⭐ NEW | Prove "WAS fine, DB slow" |
| 48 |IBus vs MQ | — |
| 49 | MQ queues, listeners, activation specs | TRANS.Q payments |
| 50 | MQ troubleshooting: queue depth, channels | Payment delays |
| 51 | Global security (LAB) | Secure console |
| 52 | LDAP/AD + federated repositories | AD group access |
| 53 | LTPA, SSO | Cross-app tokens |
| 54 | SSL: keystores, certs, plugin-key.kdb | Cert expiry outage |
| 55 | Admin roles, SAS/CSIv2 | Segregation of duties |
| 56 | Dynacache + servlet caching | 8s → 1.2s statements |
| 57 | Request metrics, tracing | Slow request analysis |
| 58 | HPEL + log rotation + Splunk | 7-yr audit logs |
| 59–61 | **LAB PROJECT: secure env (SSL + LDAP + DS + MQ)** | — |
| 62 | Revision + Q&A drill | — |

---

# PHASE 3: Advanced Administration (Days 63–90)

| Day | Topic | Banking Focus |
|-----|-------|---------------|
| 63 | HA: vertical vs horizontal | 99.99% availability |
| 64 | Clustering: routing, failover (LAB) | Mid-session JVM fail |
| 65 | Core groups + HA manager | Cross-DC HA |
| 66 | Memory-to-memory replication | CardAuth active-active |
| 67 | Dynamic clusters + ODR | Elastic workloads |
| 68 | DR: RTO/RPO, backupConfig | Regulator drills |
| 69 | DR restore (LAB) | 4-hr RTO runbook |
| 70 | DR drill simulation | Audit docs |
| 71 | Rolling upgrades, patch strategy | Patch nights |
| 72 | WAS 8.5 → 9 migration planning | EOS deadline |
| 73 | Migration LAB + rollback | Parallel run |
| 74 | Performance methodology (any slowness) | Month-end |
| 75 | JMeter load testing | Pre-festival capacity |
| 76 | Full JVM tuning exercise | 2000 TPS New Year |
| 77 | Thread dumps/javacore (LAB) | WSVR0605W |
| 78 | Heapdump, Snap, MustGather, PMR | IBM support |
| 79 | Troubleshooting: nodeagent sync, wsadmin fails | Daily ops |
| 80 | Troubleshooting: classloader, cert errors | Release failures |
| 81 | Jython bulk automation (LAB) | 200-server audit |
| 82 | **Bash scripting: health checks, disk/log monitors, auto-restart + cron** ⭐ NEW | Your own safety nets |
| 83 | Ansible for WAS | Provisioning |
| 84 | Jenkins/Git CI/CD | Release pipeline |
| 85 | Liberty + OpenShift/containers | Hybrid bank estate |
| 86 | Change management + CAB | Change records |
| 87 | Incident mgmt: bridge calls, P1 | War room |
| 88 | RCA writing + templates | Post-incident reports |
| 89 | On-call, SLAs, escalation | Bank ops reality |
| 90 | Full revision + mock drill | — |

---

# PHASE 3.5: Solo-Admin Ecosystem Skills (Days 91–100) ⭐ NEW PHASE

| Day | Topic | Banking Focus |
|-----|-------|---------------|
| 91 | **Networking deep dive:** TCP handshake, netstat, telnet port checks | "Connection refused" tickets |
| 92 | DNS troubleshooting + firewall request process | Timeout isolation |
| 93 | tcpdump basics + packet-level proof | Prove where traffic dies |
| 94 | **F5 basics:** health checks, pool member status, VIP | Why F5 team calls you |
| 95 | Nginx/Apache overview | Alternate web tiers |
| 96 | **APM tools:** AppDynamics/Dynatrace/Instana navigation | Find slow transactions |
| 97 | Splunk queries: search, filter, correlation ID tracing | Trace one customer payment |
| 98 | **ServiceNow/ITSM:** change records, incident workflow, RCA reports | Full ITIL cycle alone |
| 99 | **Solo isolation drill:** 503 error → F5? IHS? Plugin? JVM? DB? in 10 min | Core solo skill |
| 100 | **Solo drill:** OOM at midnight — dump, analyze, safe restart decision | 2 AM confidence |

---

# PHASE 4: Expert / Architect (Days 101–110)

| Day | Topic |
|-----|-------|
| 101 | Sizing & capacity planning |
| 102 | Design exercise: internet banking platform from scratch |
| 103 | Security hardening: CIS, port lockdown, console lockdown |
| 104 | PCI-DSS audit closure exercise |
| 105 | PVU license optimization, consolidation |
| 106 | Compile Issue Log (30 issues: symptom → RCA → fix) |
| 107 | Advanced RCA: cross-domain war stories (WAS + DB + network) |
| 108 | Runbook authoring + mentoring (Lead skill) |
| 109 | Vendor management: IBM PMRs, escalations |
| 110 | Full revision + mock drill |

---

# PHASE 5: Interview Prep Packs (Days 111–130)

| Day | Activity |
|-----|----------|
| 111–113 | **Pack 1:** 20 Interview Q&A per major topic (10-yr level) |
| 114–115 | **Pack 2:** "Recent issues faced" — craft your 10 stories |
| 116–118 | **Pack 3:** 20 banking scenario questions drill |
| 119–121 | **Pack 4:** 20 troubleshooting scenario drill |
| 122–123 | **Pack 5:** 5 outage war stories — write & rehearse |
| 124–125 | **Pack 6:** 5 Behavioral/Leadership answers |
| 126–127 | **Pack 7:** 5 Architecture/Design questions (Lead level) |
| 128 | **Solo-readiness test:** the 8 scenarios — no Google |
| 129 | Full mock interview (2 hrs, self-timed) |
| 130 | Gap fixing + final review |

---

# ✅ Final Solo-Readiness Checklist (Day 128 Gate)

> Handle each in **< 30 minutes**, without help:

- [ ] 503 on internet banking → isolate F5/IHS/Plugin/JVM
- [ ] Midnight OOM → dump, analyze, safe restart
- [ ] Expired plugin cert → replace + propagate, no outage
- [ ] MQ queue depth rising → listener/channel/consumer fix
- [ ] Disk full → safe cleanup + prevention
- [ ] Month-end slowness → prove WAS vs DB vs code with evidence
- [ ] Node agent down → resync + verify
- [ ] Full ITIL cycle alone: change record → execute → rollback plan → RCA

---

# 📏 Rules for Success

1. **No topic moves forward without a LAB** — install WAS ND trial on VirtualBox/VMware VMs
2. **Maintain 3 notebooks:** Theory / Lab steps / Issue log
3. Missed a day? Don't skip topics — extend the plan
4. Write every lab exercise **as if it happened in production** — these become your interview war stories
5. Interview packs are delivered topic by topic — request which topic you need

---

# 📊 Summary

| Metric | Value |
|--------|-------|
| Total Days | 130 |
| Phases | 6 (including Phase 3.5) |
| LAB Projects | 3 major + daily labs |
| Interview Packs | 7 |
| Final Gate | Solo-readiness: 8 scenarios, < 30 min each, no Google |
