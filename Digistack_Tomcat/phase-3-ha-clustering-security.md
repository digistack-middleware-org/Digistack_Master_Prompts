# PHASE 3: HA, Clustering & Security (Day 49–70)
Goal: Design HA/DR on a whiteboard; pass a banking security audit.
Milestone Day 70: Whiteboard HA platform + name 10 security findings from memory.

## Module 6: Clustering, LB & Session Management (Day 49–54)
| Day | Topic | Lab |
|---|---|---|
| 49 | Scaling: horizontal vs vertical; LB concepts (round robin, least conn, health checks) | Install Nginx; LB across 2 Tomcats |
| 50 | Sticky sessions theory + banking session-expired scenario | Test sticky vs non-sticky with session app |
| 51 | Session replication: DeltaManager vs BackupManager | Configure 2-node cluster; test failover |
| 52 | Session serialization issues (non-serializable = classic bug) | Put non-serializable object in session; break; fix |
| 53 | Stateless design: Redis/Spring Session (modern approach) | Concept + simple Redis session demo |
| 54 | 🏦 Banking: fund-transfer "session expired" — design the fix | Sticky sessions behind Apache; test transfer flow |

## Module 7: HA & DR (Day 55–60)
| Day | Topic | Lab |
|---|---|---|
| 55 | Active-Active vs Active-Passive; DC-DR architecture (RBI mandate) | Draw full DC-DR diagram from memory |
| 56 | RTO/RPO; Tomcat's role in DR; backup strategy | Write backup script: configs + apps + certs |
| 57 | DR drill process: failover, DNS/LB switch, smoke tests | Simulate DR: kill DC instance; route traffic; verify |
| 58 | 🏦 Banking: full DR drill role-play — drill checklist | Execute mini-drill end-to-end; document |
| 59 | F5/LB concepts with Tomcat: drain, monitors, persistence | Simulate F5-style health monitor via Nginx checks |
| 60 | ✅ Checkpoint | Whiteboard: HA Net Banking platform (F5→Apache→4 Tomcats→DB), explain aloud 15 min |

## Module 8: Security & Compliance (Day 61–70)
| Day | Topic | Lab |
|---|---|---|
| 61 | Run as non-root, permissions, SHUTDOWN port risks | Create tomcat user; fix ownership; disable/change shutdown |
| 62 | Remove defaults, restrict Manager by IP, disable listings | Full hardening pass on instance |
| 63 | Security realms: LDAP/AD auth, basic/form auth | Configure JAAS/memory realm on Manager |
| 64 | TLS 1.2/1.3 only, cipher suites, testing with openssl | Restrict protocols; scan with testssl/nmap |
| 65 | Cookie security: HttpOnly, Secure, SameSite; HSTS, clickjacking | Add security headers; verify in browser dev tools |
| 66 | CVEs: Ghostcat (AJP), patch cycles; upgrade path 8→9→10 | Download latest Tomcat; diff config; simulate patch |
| 67 | Cert expiry monitoring + renewal (90-day B2B certs) | Write cert-expiry check script; schedule via cron |
| 68 | 🏦 Banking: security audit simulation — 10 findings + fixes | Run own scan; remediate; write audit response |
| 69 | Secrets management: Vault/CyberArk; no plaintext passwords | Move DB password out of context.xml (env/JVM approach) |
| 70 | ✅ Checkpoint | Mock security interview: "Name 10 findings" — from memory, timed |

## ✅ Phase 3 War Stories (minimum 3)
- Fund-transfer session-expired fix
- DR drill execution
- Security audit remediation
