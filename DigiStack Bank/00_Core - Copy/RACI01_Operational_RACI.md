ID: RACI01
Version: 1.1
Status: Active

Title: Operational RACI — Production Support Ownership Matrix

Imports:
IDX
STD

Exports:
Role definitions (WAS Admin, DBA, MQ Admin, Network, Security, App Team, NOC)
RACI matrix by failure/incident category
Escalation path
On-call rotation model (concept-level)

Used By:
P02
P03
P03.1 
P03.2
P04 
P05 
P06
P07 
P07.1
P08
P08.1
P09
P09.1
P10
P10.1

Next:
(none — reference document, cited by version, not sequenced)

---

Purpose
-------
Defines *who* is responsible, accountable, consulted, and informed when
something breaks — the operational counterpart to ARCH01's component
ownership matrix (which defines who writes what data) and P04 v35's
incident lifecycle (which defines the process steps, not the people
executing them). Every incident scenario in P03.1 Interview-3/4 and every
drill in P05 v36-v38 assumes this matrix exists; this document is that
assumption made explicit.

RACI Definitions
---------------------
| Letter | Meaning |
|---|---|
| R | Responsible — does the actual work |
| A | Accountable — owns the outcome, signs off, single person per row |
| C | Consulted — provides input before/during resolution |
| I | Informed — notified of status, not actively engaged |

---

1. Roles (Reference Definitions)
--------------------------------------
| Role | Scope |
|---|---|
| WAS Admin | JVM, Cluster, Deployment, Plugin, WAS Security config, wsadmin |
| DBA | PostgreSQL (schema, replication, backup/restore, performance) |
| MQ Admin | Queue Manager, channels, queue depth, DLQ, CHLAUTH |
| Network Team | Load Balancer, IHS reverse-proxy config, firewall, DNS, VPN/hybrid link (P09) |
| Security Team | SSL/TLS certs, LDAP/LTPA, IAM (cloud phase), security audit findings |
| Application Team | Code-level defects, business logic bugs, application-level config |
| NOC / Monitoring | First-line alert triage (Grafana/Alertmanager, P04 v34), initial incident creation |
| Cloud/Platform Team (P09 onward) | AWS account, IAM, RDS/S3/SQS-SNS managed-service issues |

2. RACI Matrix — By Failure Category
-------------------------------------------
(Cross-referenced against P05 v36's Failure Type Taxonomy and P03.1
Interview-4's Troubleshooting Scenarios — same categories, now with
ownership attached.)

| Failure Type | WAS Admin | DBA | MQ Admin | Network | Security | App Team | NOC |
|---|---|---|---|---|---|---|---|
| Server Failure (JVM/cluster member down) | **A/R** | I | I | C | I | I | R (detect/page) |
| Application Failure (hangs/errors, server fine) | **A/R** | C | I | I | I | **C/R** | R (detect/page) |
| Database Failure (PostgreSQL unreachable) | C | **A/R** | I | I | I | I | R (detect/page) |
| Storage Failure (disk full/unavailable) | C | C | I | I | I | I | R (detect/page) |
| Network Failure (connectivity between tiers) | I | I | I | **A/R** | I | I | R (detect/page) |
| DNS Failure | I | I | I | **A/R** | I | I | R (detect/page) |
| Load Balancer Failure | I | I | I | **A/R** | I | I | R (detect/page) |
| Site Failure (DR event, P05 v37) | **C/R** | **C/R** | **C/R** | **C/R** | C | I | A (Incident Commander role — see §4) |
| Node Agent Down | **A/R** | I | I | I | I | I | R (detect/page) |
| DMgr Down | **A/R** | I | I | I | I | I | R (detect/page) |
| JDBC Pool Exhausted | **A/R** | **C/R** | I | I | I | C | R (detect/page) |
| Hung Threads | **A/R** | I | I | I | I | C | R (detect/page) |
| OutOfMemoryError | **A/R** | I | I | I | I | **C/R** | R (detect/page) |
| Session Loss | **A/R** | I | I | C | I | I | R (detect/page) |
| MQ Queue Full / DLQ growth | C | I | **A/R** | I | I | C | R (detect/page) |
| SSL/Certificate Failure | C | I | I | I | **A/R** | I | R (detect/page) |
| Plugin Failure (plugin-cfg.xml stale) | **A/R** | I | I | **C/R** | I | I | R (detect/page) |
| Deployment Failure (EAR won't start) | **A/R** | I | I | I | I | **C/R** | R (detect/page) |
| LTPA/SSO Cross-Cell Failure (P06 v42) | **C/R** | I | I | I | **A/R** | I | R (detect/page) |
| Cross-Region mTLS Failure (P06 v41) | C | I | I | **C/R** | **A/R** | I | R (detect/page) |
| Cloud/AWS Managed-Service Failure (RDS/S3/SQS, P09) | C | **C/R** (if DB) | I | I | I | I | R (detect/page) — **A: Cloud/Platform Team** |

Note: rows marked with two roles in **A/R** (e.g., Site Failure) reflect
genuine multi-team ownership during a DR event — this is why §4 below
introduces a single Incident Commander role for exactly those
cross-team scenarios, rather than leaving "who's accountable" ambiguous
when five teams are all technically "R."

3. Escalation Path
------------------------

NOC/Monitoring detects (Alertmanager, P04 v34)
│
▼
Tier 1 — WAS Admin on-call (or relevant team per matrix above)
│ (not resolved within defined SLA window)
▼
Tier 2 — Senior WAS Admin / Team Lead + relevant specialist team engaged
│ (Site Failure, multi-team, or SLA breach)
▼
Tier 3 — Incident Commander declared (see §4) + Management notified
│ (regulatory/customer-impacting)
▼
Business Owner Approval + Customer Notification (per P05 v38 §7)


SLA windows (tie to P04 v35's MTTD/MTTR figures and P05 v37's RPO/RTO
table): Tier 1 response within 15 minutes of alert (matches Fund
Transfer's RTO target); escalate to Tier 2 if unresolved at the 30-minute
mark.

4. Incident Commander Role (introduced here — closes a gap)
------------------------------------------------------------------
P05 v38's DR Runbook references "Management Approval (simulated)" and
"Incident Response," and P04 v35 references incident lifecycle steps, but
neither names *who* actually holds cross-team authority during a
multi-team incident (Site Failure, cross-region mTLS failure, etc.). This
role is defined here to close that gap:

- **Incident Commander (IC)**: a designated on-call rotation (WAS Admin
  Lead by default, per this project's WebSphere-practice-first scope) who
  is Accountable (not necessarily Responsible for the technical fix)
  during any incident that spans more than one team's row in §2's matrix.
  The IC coordinates the teams already marked R/C in the relevant row —
  it does not add a new technical responsibility, it resolves the "who's
  accountable when five teams are all R" ambiguity.
- IC authority: can declare a Change Freeze (per P05 v38 §4, EPS01 §3.8),
  can invoke Emergency CAB, and is the single point of truth for status
  updates up the escalation path.

5. On-Call Rotation (Concept-Level)
------------------------------------------
Consistent with this project's "commercial/heavier tooling = concept"
pattern (PagerDuty/Opsgenie not stood up, ServiceNow already
concept-level per P04 v35): on-call rotation is documented as a weekly
rotation table per role (WAS Admin, DBA, MQ Admin) in SetupDoc format,
using simple email/SMS escalation (reusing P01 v13's JNDI Mail Session
plumbing) rather than a dedicated paging product. A working example
rotation table is populated once P03.1 Interview-3's "typical week"
runbook is produced, since that's the natural place a real on-call
schedule would be exercised.

6. Cross-Reference to Existing Documents
-----------------------------------------------
- **ARCH01's Component Boundary Rule** — defines who writes what *data*;
  this document defines who responds when that boundary or its
  surrounding infrastructure breaks.
- **P04 v35's Incident Management Scope** — the process steps (creation →
  assignment → investigation → resolution → RCA → closure); this document
  is the answer to "assignment to whom."
- **P05 v38's Business Continuity §7** — Communication Plan, Business
  Owner Approval, Emergency CAB — this document's Incident Commander role
  is who actually triggers those steps.
- **P03.1 Interview-3/Interview-4** — every scenario's "who gets paged"
  question now has a documented answer instead of an implied one.

---

*This is RACI01. Companion standards: STD (role names should stay
consistent with any future org-chart-style document), STDGAP01 (EPS01
§3.8's CAB/Change Freeze triggers, which the Incident Commander role in
§4 above is authorized to invoke).*