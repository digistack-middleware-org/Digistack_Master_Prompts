# DAY-TO-VERSION MAPPING — DigiStack Bank App Project Overlay

**Purpose:** Maps each course Day (per `WAS-Program-v10-Full-222-Days.md`)
to the matching DigiStack Bank app-development project Version (P01 v1–14,
P02 v15–22, P03 v23+), so Sprint 4 (Real Banking Example), Sprint 7 (Lab),
and Sprint 8 (Admin Console + wsadmin) can use real artifacts — table
names, JNDI names, EAR names, architecture diagrams, sprint deliverables —
instead of generic invented ones.

**Ground rules:**
- The 222-Day numbering/order is NEVER changed by this mapping.
- This course Project does not perform app coding/deployment — the app is
  built separately; this file is a reference/example source only.
- Where no Version matches, continue with realistic generic banking
  examples (see Session Rules v2, Rule 7 / Section 10).
- P02 v16 (Web Services) and most of P03 (v23+, CBS/CIF/Payments/Channel
  Simulators/Loans) don't have a clean 1:1 course Day — see Gaps section.

---

## Phase A — Foundations (Days 1–17)

| Days | Topic | Version | Notes |
|---|---|---|---|
| 1–3 | Orientation | — | generic (ITIL, ServiceNow/Jira/Confluence) |
| 4–9 | WAS Intro | — | generic (pre-app-project conceptual) |
| 10–17 | Install | — | generic (IIM, WAS ND install) |

## Phase B — Build (Days 18–96)

| Days | Topic | Version | Real artifacts |
|---|---|---|---|
| 18–19 | Standalone Profile | P01 v1 | `digistack-bank-v1.ear`, `app_config` table |
| 20–22 | Basic Deployment | P01 v1–v2 | context root `/digistack-bank`, `users` table, Login/Logout |
| 23 | Custom Profile | — | no match, generic |
| 24–25 | DMGR | P01 v5–v6 | `dsb-dmgr`, DMgr profile creation |
| 26–27 | Federation | P01 v5 | `dsb-node01` federation, cell name |
| 28 | Synchronization | P01 v6 | Node Sync vs Full Resync drill |
| 29–31 | Architecture Deep Dive | — | synthesis across v1–v6, no single Version |
| 32–38 | Deploy to Federated Servers / Classloader | P01 v3–v4 | `AccountDao`, `AccountService`, ClassLoader policy, Update Application vs rollback (v3→v4) |
| 39–48 | Clustering | P01 v5 | 2-member `AppCluster`, `dsb-node02`, WLM |
| 49 | Cluster Failover | P01 v5 | Sprint 5 kill-member-mid-session test |
| 50–51 | Deploy to Cluster | P01 v5 | deploy `digistack-bank-v5.ear` to both members |
| 52–60 | JDBC | P01 v7 | `jdbc/BankDS`, JAAS Auth Alias, CAP01 §4 pool sizing (2×20=40 conns) |
| 61–62 | JNDI | P01 v7 | JNDI lookup of `jdbc/BankDS`; fold in P02 v16 REST contract awareness here (Gap item) |
| 63–64 | SIBus | **P02 v15** | `BANK.FUNDTRANSFER.Q`, `FundTransferMDB`, DLQ — flagged out-of-numeric-order vs P01 v8-14, used anyway per topic match |
| **65–75** | **IHS** ← current position | **P01 v8** | plugin-cfg.xml gen/propagation, custom 404/500 pages, `digistack-bank-v8.ear` |
| 76–81 | Session Management | P01 v9 | session timeout, memory-to-memory replication |
| 82–94 | SSL Fundamentals & Config | P01 v11 | self-signed cert, HTTPS enforced on IHS, HTTP→HTTPS redirect |
| 95–96 | SSL End-to-End | P01 v12 | mTLS on internal hop, SSL repertoires, NodeDefaultSSLSettings/CellDefaultSSLSettings |

## Phase C — Operations (Days 97–191)

| Days | Topic | Version | Real artifacts |
|---|---|---|---|
| 97–108 | Security | P01 v10 + P02 v17 | Customer/Administrator roles, MFA/OTP, account lockout, LTPA/SSO |
| 109–114 | Maintenance | — | no match, generic (Fix Pack SOP) |
| 115–122 | Enterprise Administration | P01 v6 (wsadmin) | backupConfig/restoreConfig, addNode/removeNode |
| 123–148 | Troubleshooting | draws incidents from all Versions built so far | e.g. JDBC pool exhaustion (v7), MDB/DLQ failure (P02 v15) |
| 149–162 | Performance Tuning | P01 v14 | Transaction Report large-dataset heap-stress test, PMI/GC before/after |
| 163–170 | Migration | — | no match, generic |
| 171–191 | wsadmin Automation | P01 v6 (wsadmin base) | script Freeze/Unfreeze, health checks, deployment scripts |

## Phase D — Enterprise & Advanced Layer (Days 192–222)

| Days | Topic | Version | Real artifacts |
|---|---|---|---|
| 192–199 | IBM MQ + Messaging | P02 v19 | external Fund Transfer leg via MQ, CHLAUTH, Payment Request/Response queues |
| 200–202 | WebSphere Liberty | — | no match, generic |
| 203–206 | Load Balancers | P02 v21 | NGINX fronting 2 IHS instances, blue-green deployment, health checks |
| 207–208 | Monitoring & Observability | P02 v18 | Operations Dashboard, PMI/JMX, JMS queue depth, DB pool usage |
| 209–210 | Automation & CI/CD | — | no match, generic |
| 211–212 | DR, Backup & Compliance | — | no match, generic |
| 213–214 | Enterprise Documentation Practice | — | no match, generic |
| 215–217 | Banking Production Simulation Marathon | P02 v22 (Capstone) | full stack incident simulation |
| 218 | Inserted Lab Day | — | cumulative, draws from all above |
| 219–222 | Capstone + Interview Readiness | P02 v22 / P03 v23+ | full CBS cutover flow, multi-EAR topology (7 WAS EARs + 2 Tomcat apps) |

---

## Gaps (no clean 1:1 course Day — fold in or treat as open items)

- **P02 v16 (Web Services REST/SOAP)** — no dedicated course chapter.
  Fold into Days 61–62 (JNDI) as API/JNDI-adjacent context, or treat as an
  open item for later chapter insertion if the user later allows
  non-strict day-count changes.
- **P03 v23–v30 (CBS, CIF, Payment Hub, Mobile/ATM Tomcat simulators, Card
  Portal, Branch Portal, Loan Management)** — the entire multi-EAR,
  multi-application architecture (Governing Rule: only CBS writes to
  `digistack_cbs`) has no dedicated course Days. Fold into:
  - Days 123–148 (Troubleshooting) — cross-application/cross-EAR
    incident scenarios
  - Days 115–122 (Enterprise Administration) — multi-EAR
    deployment/lifecycle management practice
  - Days 215–222 (Capstone) — end-to-end CBS/Portal/Payment Hub flow
- **P04+ (if/when provided)** — not yet mapped; Days 192+ currently use
  only P02/P03 content plus generic material.

---

## How to use this file each session

1. Check current Day from `Progress-Log-v12.md`.
2. Look up that Day in this table.
3. If a Version is mapped, pull real table/JNDI/EAR names and architecture
   from that Version's entry in the DigiStack project files (P01/P02/P03
   documents) for Sprints 3, 4, 5, 7, 8 especially.
4. If no Version is mapped, continue with realistic generic banking
   examples per Session Rules v2, Section 10.
5. Never let this overlay cause coding/deployment work inside the
   mentoring course Project — it's reference material only.
