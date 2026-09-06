# WebSphere Session Management — Zero to Expert
## Traditional WAS 8.5.5 / 9.0 ND | Banking Production Focused

> **Teaching Ratio per Topic:** 80% Theory | 15% Real Banking Scenario | 5% Interview
> **Every topic includes:** Admin Console steps + wsadmin/Jython steps
> **Environment:** BankCell01 → DMGR → Node01/Node02 → PaymentCluster
>   (PaymentJVM01–04) → IHS01/IHS02 → PaymentApp.ear
> **Rule:** One topic at a time. Skip questions (save tokens). Move topic by topic.

---

# 🎯 LEVEL 1 — ABSOLUTE ZERO (What is a Session?)

## MODULE 1 — Session Basics
1.  What is an HTTP session? (in plain English)
2.  Why does the web need sessions at all? (HTTP is stateless)
3.  Banking example: Why a net-banking login MUST have a session
4.  What is a JSESSIONID? (the session's name tag)
5.  How a session is created (first request → Set-Cookie → next request carries cookie)
6.  Where sessions live: JVM memory (default)
7.  Session timeout — what it is, how it works, default 30 min
8.  Session invalidation — login vs logout
9.  What happens when a JVM restarts? (sessions are LOST — the core problem)

# 🎯 LEVEL 2 — SESSIONS IN A CLUSTER (Why It Gets Hard)

## MODULE 2 — Sessions + Clustering
10. Single server vs cluster — why sessions break in a cluster
11. Session affinity — what it is and why the plugin does it
12. How plugin-cfg.xml provides affinity (clone IDs, Partition/CloneID)
13. Sticky sessions — plugin behavior explained
14. What happens when an affine JVM dies? (failover problem)
15. Session persistence vs replication vs affinity — the 3 concepts people confuse

## MODULE 3 — Memory-to-Memory Replication (M-to-M)
16. What is memory-to-memory session replication?
17. Single replica mode vs multi-row replication topology
18. Replication domain — what it is, how members join it
19. Replica number / backup count explained
20. Replication modes: both client & server, server only, client only
21. Timeout, frequency (time/ based), disk offload settings
22. M-to-M: Admin Console configuration step-by-step (cluster → replica domain)
23. M-to-M: wsadmin/Jython steps (AdminConfig — replication domain, DRSSettings)
24. Internal working: DRS (Data Replication Service), core group involvement
25. Banking scenario: 4-member PaymentCluster with M-to-M — who holds whose backup?

## MODULE 4 — Database Session Persistence
26. What is DB session persistence?
27. When banks choose DB over M-to-M (audit, DR, large sessions)
28. Prerequisites: sessions database tables (WAS creates schema), DataSource, JNDI
29. Persistence schedule: end of service vs time-based vs manual
30. Session database overhead — performance reality
31. DB persistence: Admin Console step-by-step
32. DB persistence: wsadmin/Jython steps
33. M-to-M vs DB persistence — comparison table & decision guide

# 🎯 LEVEL 3 — PRODUCTION MASTERY

## MODULE 5 — Tuning & Sizing
34. Session size — why fat sessions kill clusters
35. Serialization cost — what gets replicated & when
36. Session tuning parameters (invalidation timeout, max in-memory count, overflow)
37. Tuning M-to-M: replica count, frequency, trigger vs time
38. Session cache vs session store behavior
39. Tuning: Admin Console steps (container settings → session management)
40. Banking capacity example: 50,000 concurrent users on PaymentCluster

## MODULE 6 — Security & Sessions
41. Security-integrated sessions (login session binding)
42. Session security: cookie security settings (Secure, HttpOnly, domain, path)
43. SSL-only cookies & session hijacking prevention (banking MUST)
44. Session fixation protection
45. Security + session failover — what survives, what forces re-login

## MODULE 7 — Session + Plugin / IHS Integration
46. Clone ID / clone separator (":") in JSESSIONID — decoded
47. How plugin reads JSESSIONID to route (affinity logic decoded)
48. What happens to affinity after plugin regeneration
49. Session affinity broken: symptoms & causes
50. Cookie path, URI group, and virtual host impact on sessions

## MODULE 8 — Failover Deep-Dive
51. Full failover walk-through: JVM1 dies mid-transfer → user lands on JVM2
52. M-to-M failover timeline (millisecond reconstruction of session)
53. DB persistence failover timeline
54. What is LOST even with replication (non-serializable objects, partial data)
55. Anti-patterns that break session failover (fat objects, non-serializable, static caching)

# 🎯 LEVEL 4 — TROUBLESHOOTING (The Job Itself)

## MODULE 9 — Session Troubleshooting Scenarios
*Each: Symptoms → Causes → Logs/Files → Console Steps → wsadmin Steps → Fix → Interview Answer*

56. S1:  Users randomly logged out during peak load
57. S2:  Session lost when one JVM restarted (rolling restart gone wrong)
58. S3:  Session works on JVM1, re-login loop on JVM2 (affinity broken / clone ID issue)
59. S4:  Failover works in UAT but sessions lost in PROD (config drift)
60. S5:  Users logged out after every 30 min despite activity (timeout misconfig)
61. S6:  OutOfMemoryError caused by too many sessions in memory
62. S7:  Sessions not replicating — DRS errors in SystemOut.log
63. S8:  "Duplicate session" / session collision after clone ID duplication (cloned VM without uniqueCloneID)
64. S9:  DB persistence: DataSource failure → all logins fail
65. S10: Payment transaction lost mid-way on failover (partial session data)

## MODULE 10 — Banking Production Procedures
66. Rolling restart with sessions in mind (drain members gracefully — plugin retry + timeout)
67. Session impact checklist before ANY production restart
68. Zero-session-loss deployment strategy (drain → stop → update → start → next member)
69. Session monitoring: PMI counters, Tivoli Performance Viewer, session count per JVM
70. UAT vs PROD session config comparison (prevent drift)
71. Bank audit requirement: session timeout policy compliance (e.g., 10-min lock for transactions)

# 🎯 LEVEL 5 — EXPERT / INTERVIEW

## MODULE 11 — Expert Design Thinking
72. Designing session strategy for a 3-tier bank app (M-to-M vs DB vs hybrid)
73. Disaster recovery: sessions across sites — what's realistic
74. Session sizing math: memory = concurrent users × avg session size × replica factor
75. Senior interview: "Design session management for our PaymentCluster" — full answer template

## MODULE 12 — Interview Marathon
76. Top 50 session management interview Q&As (one-liners + detailed answers)
77. Scenario-based STAR answers ("tell me about a session issue you fixed")
78. Whiteboard exercise: draw session flow User → IHS → Plugin → Cluster with M-to-M
79. Trick questions interviewers use (affinity ≠ replication; restart ≠ failover)

---

## 📅 SCHEDULE (~3 Weeks, 1.5–2 hrs/day)

| Days | Module |
|------|--------|
| –2   | Module 1 — Session Basics |
| 3–5   | Module 2 — Sessions + Clustering |
| 6–9   | Module 3 — Memory-to-Memory |
| 10–12 | Module 4 — DB Persistence |
| 13–15 | Module 5 — Tuning & Sizing |
| 16–17 | Module 6 — Security & Sessions |
| 18–20 | Module 7 — Plugin Integration |
| 21–23 | Module 8 — Failover Deep-Dive |
| 24–28 | Module 9 — Troubleshooting ⭐ |
| 29–30 | Module 10 — Production Procedures |
| 31–32 | Module 11 — Expert Design |
| 33–35 | Module 12 — Interview Marathon |

---
*Course by Ox Alpha — WebSphere Session Management Training*
