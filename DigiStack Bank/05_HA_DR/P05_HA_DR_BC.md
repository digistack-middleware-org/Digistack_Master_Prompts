ID: P05
Version: 1.1
Status: Active

Title: Enterprise High Availability (HA), Disaster Recovery (DR) & Business Continuity

Imports: IDX STD ARCH02 P04 STDGAP01 CAP01 RACI01

Exports: Versions 36-38
High Availability (WAS cluster/session/plugin failover)
Disaster Recovery (Hyderabad/Bangalore site pairing)
Business Continuity & Application Resilience
Failure Type Taxonomy
RPO/RTO Matrix

Used By: P06 P07

Next: P06

Purpose
Learn how enterprise organizations ensure that banking applications remain
available even during failures, maintenance, disasters, or data center
outages. This Part focuses on Business Continuity, High Availability,
Disaster Recovery, and Production Resilience — all critical skills for
senior WebSphere administrators.

Standing Rule (Phase-2 Application-Freeze Rule)
Per the Phase Boundary Rule (doc STD), P03 is the application
feature-completion point. No new banking modules are introduced in this
Part unless a later WebSphere topic makes one strictly unavoidable — and
if so, it must be explicitly called out and justified, not silently added.
This Part contains exactly one such exception (v38's idempotency-key
handling on Fund Transfer), documented explicitly at that version as a
resilience/safety property retrofitted onto an existing transaction, not
a new banking module.

Prerequisite
P04 Completion Checkpoint satisfied — full observability stack
(Prometheus/Grafana, OpenSearch/ELK, Jaeger, Alertmanager) operational
across all 9 P03 applications.

Deployment Model
No new banking features are added anywhere in this Part, with the one
narrow exception noted above (v38). Every version instruments, hardens,
or drills recovery procedures on the 9 applications already built.

Version Numbering Correction (resolved before this file is marked ready)
The source material this Part was drafted from numbered its versions 33
and 34. That collides with P04, which already owns and has frozen
Versions 31–35.5 (per Engineering Standards §7 — Version Numbering
Freeze: v33 is already "APM, Distributed Tracing & SRE Methodology" and
v34 is already "Alerting, Dashboard Engineering & Synthetic Monitoring").
Per the freeze rule, this Part is renumbered to start immediately after
P04 ends.

Renumbering table:

| Old # (draft) | Old Title | New # | New Title |
|---|---|---|---|
| 33 | High Availability (HA) | 36 | High Availability (HA) |
| 34 | Disaster Recovery (DR) | 37 | Disaster Recovery (DR) |
| — (recommended addition) | — | 38 | Business Continuity & Application Resilience |

No version has been implemented yet, so this is a clean
pre-implementation renumbering (same precedent as P03's and P04's own
renumbering passes) — not a mid-flight break.

Consolidation note. A gap-analysis review of the source material raised
12 distinct gaps (application continuity, database continuity, MQ
continuity, monitoring integration, planned maintenance, HA/DR split,
backup strategy, DR runbook, RPO/RTO examples, architecture consistency,
business continuity, production exercises). Rather than creating a
version per gap — which would fragment what is really one story ("what
happens to an in-flight banking transaction and the platform around it
when something breaks") — each gap is folded into whichever of the three
versions below it most naturally extends, with cross-cutting items
(monitoring verification) treated as a standing requirement rather than a
separate version.

A second gap-analysis pass (post-freeze) identified 8 additional gaps
specific to the two-site DR architecture itself — MQ's site placement,
the observability stack's own DR posture, the missing Secondary-site load
balancer, LDAP/security-registry replication, cell topology ambiguity,
inter-site network assumptions, the regulatory/compliance angle, and an
inconsistent RACI cross-reference. These are resolved below, primarily in
v37 (Architecture and DR Concepts) and v38 (RACI cross-reference), with
one addition to v36 (network/topology stated once, upfront, since v37 and
v38 both depend on it).

---

Version 36 — High Availability (HA)
Objective: Build a highly available WebSphere environment where no single
server failure causes an application outage.

Banking Features Added: None. This version is 100% infrastructure
resilience — every banking feature already built across P01–P04 is the
test subject, not the point.

Cell & Network Topology (stated once here; assumed by v37 and v38)
This Part treats Hyderabad and Bangalore as **two separate WAS cells**
(HydCell01, BlrCell01) kept in sync via config export/import
(backupConfig/restoreConfig, wsadmin scripts) rather than one WAS cell
stretched across both sites. Reasoning: DCS (Data Replication Service)
heartbeat and Core Group membership assume low, consistent inter-DC
latency; a real DigiStack-scale bank would not risk Core Group Bridge
instability over a WAN link between Hyderabad and Bangalore. This means:
DMgr, Node Sync, and Core Group Policy in this version operate strictly
*within* each site's own cell — Node Sync never crosses sites. Cross-site
consistency (v37) is achieved by keeping both cells' configuration
exports in lockstep, not by a shared DMgr.

Inter-site network assumption (concept-level, documented in
SetupDoc-v37.md, not built): a dedicated leased line or site-to-site VPN
between Hyderabad and Bangalore, sized and monitored specifically because
replication lag against that link is what the v37 RPO numbers are
actually measured against — a slower or saturated link directly degrades
RPO regardless of how well PostgreSQL streaming replication itself is
configured. For this lab, the two sites are simulated as two VM/network
segments with an artificial latency/bandwidth cap so replication lag is
observable rather than instant.

Infrastructure Features
WebSphere HA: Cluster Failover, Session Failover, Automatic Workload
Distribution, Cluster Member Recovery
IBM HTTP Server: Plugin Failover, Health Checks, Automatic Routing,
Plugin Generation/Propagation/Refresh — the everyday admin cycle that
keeps plugin-cfg.xml in sync whenever cluster membership changes
Load Balancer: Health Monitoring, Server Removal, Automatic Traffic
Routing, Sticky Session / Session Affinity / Cookie-based routing — how
the LB itself keeps a user on one member even though WebSphere's own
session replication (P01 v5/v9) is what actually protects the session
data

Architecture
```
                Load Balancer
                     │
        ┌────────────┴────────────┐
        ▼                         ▼
IBM HTTP Server-1         IBM HTTP Server-2
        │                         │
        └────────────┬────────────┘
                     ▼
              WebSphere Cluster
        ┌────────────┬────────────┐
        ▼             ▼            ▼
      JVM1          JVM2          JVM3
                     │
                     ▼
              DigiStack CBS
                     │
                     ▼
                PostgreSQL
```

Failure Type Taxonomy (introduced here, reused in v37/v38)
Every HA/DR drill from this version forward is classified against one of
these failure types, so "what kind of failure are we testing" is never
ambiguous:

- Server Failure (a JVM/cluster member dies)
- Application Failure (the app hangs/errors, server is fine)
- Database Failure (PostgreSQL unreachable)
- Storage Failure (disk full/unavailable)
- Network Failure (connectivity between tiers lost)
- DNS Failure (name resolution breaks)
- Load Balancer Failure (LB itself goes down)
- Site Failure (the whole DC is gone — this is v37's territory, not
  v36's)

v36 exercises the first two (Server, Application); v37 exercises Site;
v38's items exercise Database, Storage, and messaging-layer failure
specifically.

Failure Scenarios (drill-based test cases, not new features)

Scenario A — Customer transferring money, JVM crashes mid-transaction
```
Customer initiates Fund Transfer
        │
        ▼
   JVM1 crashes
        │
        ▼
Does the transfer complete, rollback, or hang?
Can the customer safely retry?
Can a duplicate transaction occur?
```
Expected: the in-flight transaction either completes via XA recovery or
rolls back cleanly — it must never leave the account in a half-updated
state, and a retry must not double-process. (Deep idempotency/retry
mechanics are v38's job; this version just proves the cluster-level
failover doesn't corrupt the transaction.)

Scenario B — Customer logged in, cluster member crashes
```
Customer is logged in
        │
        ▼
Cluster member crashes
        │
        ▼
Session replicated (reuses P01 v5/v9 session replication)
        │
        ▼
Customer continues without re-logging in
```

Scenario C — IHS/plugin failover
Kill one IHS instance mid-traffic; confirm the Load Balancer routes
around it with no dropped requests.

Post-Recovery Synchronization
Once a killed member/node comes back, failover alone doesn't guarantee it
rejoins cleanly:
- Node Sync — the recovered node pulls the latest configuration from the
  DMgr's master repository
- Full Resynchronization — used when a node's local config has drifted or
  is suspect, rather than trusting an incremental sync
- Repository Synchronization — confirming the DMgr's master repository
  and every node's local repository agree, cell-wide

Each drill in this version isn't considered closed until the recovered
member/node has gone through a confirmed Node Sync, not just "came back
up."

Monitoring Verification (standing requirement)
Not a separate feature. Every drill's pass/fail is confirmed through
P04's stack, not eyeballed: Grafana shows the cluster health dip and
recovery, Prometheus/Alertmanager fires (and clears) an alert for the
killed JVM, and Jaeger shows the in-flight trace either completing or
cleanly failing. If a drill "passes" but the observability stack didn't
see it, the drill isn't actually verified.

Topics Covered: Cluster Failover, Session Persistence, Plugin Routing,
Workload Management; Core Groups — Core Group Bridge, Core Group Policies
(which HAManager singleton services run where), DCS (Data Replication
Service — the underlying transport Core Groups use for heartbeat and
state replication); Messaging Engine HA — SIBus's Messaging Engine has
been load-bearing infrastructure since P02 v15 (Fund Transfer async
processing) but its own HA story was never covered separately from
generic cluster failover. Messaging Engines are singleton services with
their own Core Group Policy-driven failover binding — a genuinely
different concern from ordinary cluster member failover. Covered here:
Messaging Engine failover policy (which cluster member hosts the active
ME, and how Core Group Policy governs failover to another member), and
the ME data store — file store vs. DB store — with the consequence that a
file-store ME failing over to another cluster member can lose access to
in-flight/uncommitted messages, unlike a DB-backed store. Exercised
directly against this version's existing "kill a cluster member
mid-Fund-Transfer" drill (Scenario A) — confirm whether the ME failed
over cleanly and whether any in-flight JMS message was affected, given
the data store type actually configured; Node Synchronization — Node
Sync, Full Resynchronization, Repository Synchronization.

Enterprise Learning: High Availability, Zero-Downtime Services, Failover
Testing, Cluster Recovery.

Sprint Deliverable: JVM1, JVM2, and the Node Agent are each killed in turn
during active banking traffic; in every case, session state survives
(Scenario B), no in-flight Fund Transfer is corrupted or duplicated
(Scenario A), and Load Balancer/IHS failover is transparent to the user
(Scenario C) — each outcome confirmed via Grafana/Prometheus/Jaeger, not
manual inspection alone; each recovered member is confirmed to have
completed Node Sync against the DMgr's master repository before the
drill is marked closed.

---

Version 37 — Disaster Recovery (DR)
Objective: Implement a Disaster Recovery site for DigiStack Bank and
prove failover/failback between two data centers.

Banking Features Added: None. DR validates the same 9 applications from
P03 running at a second site.

DR Concepts
Primary Site: Hyderabad DC (HydCell01)
Secondary Site: Bangalore DC (BlrCell01) — a separate WAS cell, per v36's
Cell & Network Topology note, kept config-synchronized rather than
sharing a DMgr with Hyderabad.

Components per site (now includes MQ and observability — both were
missing from the original architecture and are filled in here):

| Component | Primary (Hyderabad) | Secondary (Bangalore) |
|---|---|---|
| Load Balancer | Active — fronts IHS-1/IHS-2 | **Standby** — a second LB (or the same physical LB re-pointed) fronting Bangalore's IHS pair, held idle/warm until a drill or real failover activates it. Previously missing from the architecture; without this, Bangalore has no traffic-entry point during a drill. |
| IBM HTTP Server | IHS-1, IHS-2 (active) | IHS-1, IHS-2 (standby/warm) |
| WebSphere ND | HydCell01 — Portal Cluster, CBS Cluster, satellite EARs (active) | BlrCell01 — Portal Cluster, CBS Cluster, satellite EARs (standby/warm) |
| CBS + other 8 P03 apps | Active | Deployed, standby/warm |
| **IBM MQ / SIBus** | **Active Messaging Engine, persistent (DB or file store per v36's ME coverage)** | **Standby Messaging Engine, same store type, kept warm; queue depth and DLQ state reconciled as part of the DR drill, not assumed to replicate automatically** — MQ was previously absent from this table entirely, despite the source Part itself calling SIBus "load-bearing infrastructure since P02 v15." |
| PostgreSQL | Primary | Standby (streaming replication target) |
| **Observability stack (Grafana/Prometheus/Jaeger/OpenSearch)** | **Centrally hosted — not site-local to Hyderabad.** Deployed on infrastructure independent of both DCs (e.g., a third, lightweight monitoring-only environment) specifically so a genuine Site Failure at Hyderabad does not blind the team to the very drill they're trying to observe. This is a correction from the original design, which implicitly assumed the stack lived in Hyderabad. | Same centrally-hosted stack scrapes/ingests from Bangalore once traffic switches there — no separate Bangalore-local monitoring stack is stood up. |
| **Security registry (LDAP / WAS federated repositories)** | **Active LDAP/registry** | **Replicated/standby registry**, synchronized on the same cadence as WAS cell config (below) — without this, a DR failover succeeds at the infrastructure layer but users and admins can't authenticate at Bangalore, which would surface as a false DR failure. |

Architecture (updated to include MQ, the Secondary LB, and the
centrally-hosted observability stack — all previously missing)

```
                    ┌───────────────────────────────┐
                    │   Observability (centralized)   │
                    │ Grafana / Prometheus / Jaeger /  │
                    │ OpenSearch — independent of both │
                    │           Hyd and Blr            │
                    └───────────────┬───────────────┘
                                    │ scrapes/ingests from both sites
                        ┌───────────┴───────────┐
                        ▼                       ▼
PRIMARY DC (Hyderabad)                  SECONDARY / DR DC (Bangalore)
        Users                                    (idle until failover)
          │                                          │
          ▼                                          ▼
   Load Balancer (active)                  Load Balancer (standby)
          │                                          │
          ▼                                          ▼
IBM HTTP Server (IHS-1, IHS-2)         IBM HTTP Server (IHS-1, IHS-2)
          │                                          │
          ▼                                          ▼
    Portal Cluster                          Portal Cluster (warm)
          │                                          │
   ┌──────┴──────┐                            ┌──────┴──────┐
   ▼             ▼                            ▼             ▼
CBS Cluster   Payment Hub / Notification   CBS Cluster    satellite EARs
   │          Service / Reporting             │           (warm/standby)
   ▼          (via IBM MQ event bus)          ▼
IBM MQ (active ME) ───────replication────► IBM MQ (standby ME)
   │                                          │
   ▼                                          ▼
PostgreSQL (digistack_cbs) — Primary ──repl─► PostgreSQL — Standby
   │
   ├── Branch Portal, Card Portal (WAS)
   └── Mobile, ATM (Tomcat)

LDAP / Security Registry (Hyd, active) ──sync─► LDAP / Security Registry (Blr, standby)
```

DR Activities: Backup/Restore, Database Replication (concepts —
streaming replication, standby promotion), MQ Replication/Reconciliation
(standby Messaging Engine queue-depth and DLQ state checked against
Primary as part of every drill — new, closes the previously-missing MQ
gap), Configuration Synchronization (WAS cell config — exported/imported
between the two separate cells per v36's topology note — IHS/plugin
config, security/LDAP registry sync, security config generally kept in
sync between sites), DR Drill, Planned Failover, Unplanned Failover,
Failback to Primary once it's confirmed healthy again.

Enterprise Storage (concept-level — not implemented, just understood):
Shared Storage, SAN/NAS, Snapshot Backup. These aren't built in this
project (no real SAN available), but knowing where they fit — e.g., a
storage-level snapshot as a faster restore path than a full pg_dump
restore — is expected enterprise DR knowledge, and belongs in
SetupDoc-v37.md as a documented concept even though the lab itself uses
simple VM/file-based backups.

DNS Failover (concept-level, alongside the DNS Failure type from v36's
taxonomy): DNS Failover, Virtual IP (VIP), Global Server Load Balancer
(GSLB) — concept only; the enterprise-grade way large banks route
traffic across sites without relying on DNS TTL expiry. For this lab, the
actual DR drill uses a manual traffic-routing step (updating the Load
Balancer or a hosts-file-level override, now that Bangalore has its own
standby LB to route to) rather than real DNS/GSLB infrastructure — call
out explicitly in SetupDoc-v37.md which stand-in is used.

RPO/RTO — Tied to Actual DigiStack Flows

| Flow | RPO | RTO | Rationale |
|---|---|---|---|
| Fund Transfer (P02 v15/v19, P03 v25) | 0 minutes | 15 minutes | Money movement — zero tolerated data loss, fast recovery |
| Login / Session | 0 minutes | 15 minutes | Tied to the same cluster as Fund Transfer; also gated on LDAP/registry replication (see Components table) — a fast cluster failover with a stale or unreachable registry still fails this RTO |
| Notification (P01 v13, P03 satellite) | 30 minutes | 60 minutes | Best-effort delivery is acceptable to delay |
| Reporting (P01 v14, P03 satellite) | 24 hours | 4 hours | Reports can be regenerated from replicated data after recovery |

These targets are what the DR drill below is actually measured
against — a drill that recovers Fund Transfer in 20 minutes has failed
its RTO even if everything else worked. Note also that the inter-site
network assumption from v36 directly bounds these RPO numbers: if the
simulated Hyderabad↔Bangalore link's replication lag exceeds a flow's RPO
window, the RPO is not actually achievable regardless of application-
level correctness.

Failure Type Coverage (from v36's taxonomy): This version specifically
exercises Site Failure — the case v36 deliberately left out.

Topics Covered: BackupConfig, RestoreConfig, Cell Export, Profile Backup,
DR Planning, LDAP/Registry Replication, MQ Replication Reconciliation,
Secondary-Site Load Balancing.

Enterprise Learning: Disaster Recovery, Recovery Point Objective (RPO),
Recovery Time Objective (RTO), Business Continuity (introductory pass —
formalized in v38).

Governance note (Site Failure approval): A Site Failure drill or real
event is a multi-team decision — who authorizes cutting traffic to
Bangalore is the same open question v38 resolves for Business
Continuity's "Management Approval" step. Per RACI01 §4, the Incident
Commander role holds this authority here too; this is a documentation
cross-reference only (closing the inconsistency where v38 named this
role and v37 previously didn't) — no technical scope in this version
changes as a result.

Monitoring Verification (standing requirement): DR drill success is
confirmed the same way as v36, using the centrally-hosted observability
stack described above (not a Hyderabad-local one): Grafana shows the
Secondary site's cluster coming to full health, Prometheus/Alertmanager
reflects the Primary site's alerts and their clearing on failback, and
Jaeger/ELK confirm a Fund Transfer trace completes correctly against the
Secondary site during the drill window.

Sprint Deliverable: A full planned failover from Hyderabad to Bangalore
is executed and measured against the RPO/RTO table above (Fund Transfer
recovers within 15 minutes with zero data loss); the standby Messaging
Engine's queue depth and DLQ state are reconciled against Primary as part
of the same drill; LDAP/registry failover is confirmed by a successful
admin and customer login against Bangalore; an unplanned-failure
simulation is also run; failback to Primary is performed once Primary is
confirmed healthy; all outcomes verified via the centrally-hosted P04
observability stack.

---

Version 38 — Business Continuity & Application Resilience
Objective: Close the remaining gaps between "the infrastructure fails
over" (v36/v37) and "the business — and the specific in-flight
transactions and messages — actually survives intact." This version is
where transaction-level integrity, database/MQ continuity specifics,
planned maintenance without downtime, a real backup inventory, a DR
runbook, and broader business-continuity process live.

Banking Features Added: Effectively none, with one narrow exception:
idempotency keys / duplicate-transaction prevention are added as a
resilience behavior on top of the existing Fund Transfer flow (P02 v15,
P03 v25) — not a new banking module, just a safety property retrofitted
onto an existing one.

Transaction Integrity (deepened from v36's Scenario A)
- In-flight Transaction Recovery — XA transaction recovery on JVM
  restart, so a Fund Transfer that was mid-commit when a JVM died is
  neither lost nor double-applied
- JMS Message Reliability — persistent messages on SIBus/MQ queues (P02
  v15/v19) survive a broker restart
- Idempotent Requests — Fund Transfer (and other write operations)
  accept an idempotency key so a client's retry after a timeout doesn't
  create a second transaction
- Retry Logic — standardized client-side and server-side retry policy
  (max attempts, backoff) reused from P03 v25's Payment Hub retry work
- Duplicate Transaction Prevention — enforced via the idempotency key,
  verified with a deliberate double-submit test

Failure scenario:
```
Message sent to MQ
        │
        ▼
   CBS unavailable
        │
        ▼
Message waits (persistent, not lost)
        │
        ▼
   CBS comes online
        │
        ▼
Processing resumes — exactly once, not twice
```

Database Continuity
Primary Database / Standby Database, Streaming Replication, Failover
concepts (manual promotion is fine for this project's scale), Read
Replica (concept), Automatic Promotion (concept — noted as a production
enhancement beyond this project's scope), Point-in-Time Recovery (PITR),
Connection Pool Recovery, JDBC Failover — confirming the WAS-managed
DataSource (P01 v7) reconnects cleanly once PostgreSQL is back.

Failure scenario:
```
Database temporarily unavailable
        │
        ▼
Application retries (connection pool)
        │
        ▼
   Pool recovers
        │
        ▼
Transaction resumes, or safely rolls back — never silently corrupts
```

IBM MQ Continuity
MQ HA (concepts), Queue Manager Backup, Persistent Messages, Dead Letter
Queue (reused from P02 v19), Message Replay, Queue Recovery, Channel
Recovery. This deepens — and is now consistent with — the MQ replication
reconciliation activity added to v37's DR Activities: v37 proves the
standby Messaging Engine exists and is reachable; v38 proves individual
messages within it survive and replay correctly.

Failure scenario:
```
CBS offline
        │
        ▼
MQ stores messages (persistent queue)
        │
        ▼
   CBS starts
        │
        ▼
Messages processed — none lost, none duplicated
```

Planned Maintenance / Zero-Downtime Operations
Rolling Restart, Rolling Deployment, Plugin Refresh/Regeneration, Node
Maintenance, JVM Maintenance, OS Patching (process, not deep OS admin),
WebSphere Fix Pack application, No-downtime maintenance — proven by
performing one real rolling restart of the cluster while banking traffic
continues, with zero customer-visible errors.

Backup Strategy — Expanded Inventory
The P01–P04 "Backup/Restore" line items were too thin for a real DR
posture. Full inventory:

| Item | Notes |
|---|---|
| WAS profile backup | backupConfig |
| IHS backup | Config + custom error pages (P01 v8) |
| Plugin backup | plugin-cfg.xml |
| SSL certificate / keystore backup | Certificate Inventory |
| **LDAP / security registry backup** | **Registry export/dump — added here to match the LDAP replication introduced in v37; without a registry backup, restoring Bangalore's registry after a failed sync has no fallback** |
| Database backup | pg_dump + PITR base backups |
| Application EAR/WAR backup | All 7 EARs + 2 WARs |
| **MQ / Queue Manager backup** | **Queue Manager configuration and persistent message store — added here to match the MQ component now explicit in v37's DR architecture** |
| Deployment scripts backup | wsadmin scripts, Ansible if used |
| Git repository backup | Mirrors of all project repos |
| Grafana dashboards backup | Exported JSON (P04 v34) |
| Prometheus configuration backup | Scrape configs, alert rules (P04 v31/v34) |

DR Runbook
A formally documented runbook, in the same shape as P04 v35's incident
runbooks:
```
Failure Detected
        │
        ▼
Incident Created
        │
        ▼
Management Approval (simulated)
        │
        ▼
   Activate DR
        │
        ▼
Restore Infrastructure
        │
        ▼
 Restore Database
        │
        ▼
Deploy Applications
        │
        ▼
  Health Checks
        │
        ▼
Business Validation
        │
        ▼
 Traffic Switch
        │
        ▼
  Monitoring
        │
        ▼
Close Incident
```
This runbook is the one actually exercised in this version's Sprint
Deliverable — v37 proved the technical failover works; this version
proves the process around it is real.

Cross-reference note (RACI — Incident Commander): "Management Approval
(simulated)" above and "Incident Response" in the Business Continuity
section below both name a step without naming who holds the authority to
approve/coordinate it during a multi-team Site Failure event. That answer
is RACI01 §4's Incident Commander role — added as a documentation
cross-reference only, per the Progress Log's Resolution path item 1; no
technical scope in this version changes. The same cross-reference is now
also stated in v37 (Governance note), so the two versions agree instead
of only v38 naming the role.

Business Continuity (broader than DR)
Alternate Workplace (concept), Communication Plan (who gets notified, in
what order), Incident Response (ties to P04 v35), Change Freeze (during
active incident/DR windows), Emergency CAB (concept), Business Owner
Approval, Customer Notification (ties to Notification Service, P03 v23),
**Regulatory & Audit Trigger** — for a Reserve Bank of India-regulated
banking entity, BCP/DR drills of this kind are typically tied to
regulatory expectations around periodic DR testing and auditable evidence
of recovery capability (concept-level here — no live regulatory filing is
produced by this lab, but the Recovery Record fields below are the same
category of evidence a real audit would request), Audit Compliance,
Post-Incident Review.

Production Exercises (this version's test cases)
1. Kill one JVM during active banking transactions and verify idempotency
   prevents duplication
2. Shut down an entire node while users remain logged in
3. Restart IBM HTTP Server during production traffic
4. Simulate a PostgreSQL outage and observe JDBC failover/recovery
5. Stop the IBM MQ Queue Manager and verify persistent messages process
   correctly on recovery
6. Perform a rolling cluster maintenance window with zero downtime
7. Execute one complete DR drill end-to-end using the runbook above,
   Primary → Secondary → back
8. Validate dashboards, alerts, and distributed traces (via the
   centrally-hosted observability stack from v37) after each exercise
9. Confirm traffic correctly switches back to Primary DC after recovery
10. **Simulate an LDAP/registry sync failure at Bangalore and confirm the
    backup registry restore path (new — exercises the backup item added
    above)**

Recovery Record (required for every exercise above):

| Field | Description |
|---|---|
| Failure Time | When the failure was injected |
| Detection Time | When monitoring/alerting first flagged it |
| Recovery Time | When the system returned to normal operation |
| Root Cause | What actually broke (even if deliberately induced) |
| Resolution | What action restored service |
| Lessons Learned | Anything that should change an alert threshold, runbook step, or timeout value |

These records are what feed the MTTD/MTTR figures referenced in P04 v35
and the RPO/RTO validation from v37 — without them, "we ran the drill"
isn't distinguishable from "we proved the numbers."

Monitoring Verification (standing requirement): Every production exercise
above is confirmed via Grafana (cluster/DB/MQ health), Prometheus/
Alertmanager (alert fired and cleared), Jaeger (trace behavior during the
failure), and OpenSearch/ELK (log evidence of retry/recovery behavior) —
consistent with v36 and v37, using the same centrally-hosted stack.

Topics Covered: XA Transaction Recovery, JDBC Failover, Rolling
Deployment, Plugin Regeneration, Fix Pack Management, backupConfig/
restoreConfig (full inventory, now including LDAP and MQ), Change
Management; Transaction Log & XA Recovery — Heuristic Completion —
"In-flight Transaction Recovery" above names the result (a transaction
isn't lost or duplicated); this names the actual mechanism behind it.
WebSphere's Transaction Log (tranlog) records in-doubt XA transactions so
that, on server restart after a crash mid-two-phase-commit, WebSphere can
automatically resolve most in-doubt transactions by replaying the log
against the resource managers involved. The harder, genuinely
differentiating case is heuristic completion: situations where a resource
manager (e.g., PostgreSQL) and the WebSphere Transaction Manager disagree
on the outcome of a transaction — one believes it committed, the other
believes it rolled back — and automatic recovery isn't possible; an
administrator must manually inspect the transaction log and force a
heuristic outcome (heuristic commit, heuristic rollback, or heuristic
mixed/hazard, if different resource managers within the same transaction
ended up in different states). This is exercised directly against v36's
Scenario A (JVM crashes mid-Fund-Transfer): after that drill, locate the
transaction log, confirm whether recovery was automatic or required
manual heuristic resolution, and document which occurred.

Enterprise Learning: Business Continuity Planning, Transaction Integrity,
Idempotency Design, Database HA, Messaging HA, Zero-Downtime Operations,
DR Runbook Execution, Post-Incident Review.

Sprint Deliverable: All ten production exercises listed above are run at
least once, each with its detection/recovery outcome recorded; a
deliberate double-submit of a Fund Transfer proves idempotency prevents a
duplicate; a full DR runbook execution (Primary → Secondary → failback)
is performed as a documented, approved (simulated) change rather than an
ad hoc technical test; the full backup inventory — including the two
items added in this pass (LDAP/registry, MQ) — is confirmed restorable,
not just present.

---

Completion Checklist
□ Cluster-level failover proven for Server and Application failure
  types — session survives, in-flight transactions don't corrupt (v36)
□ LB/IHS failover transparent to users under a killed-node drill (v36)
□ Cell & network topology (two separate cells, inter-site link
  assumptions) documented before DR implementation begins (v36)
□ Full DR site (Bangalore) stood up and kept in sync with Primary
  (Hyderabad) via replication and configuration sync — including MQ and
  LDAP/registry sync, not just app/DB (v37)
□ Secondary-site Load Balancer stood up and confirmed able to take
  traffic during a drill (v37)
□ Observability stack confirmed centrally hosted (not Hyderabad-local)
  and able to observe a genuine Site Failure (v37)
□ Planned failover, unplanned failover, and failback all executed at
  least once, measured against the RPO/RTO table (v37)
□ Idempotency/duplicate-prevention proven on Fund Transfer via a
  deliberate double-submit test (v38)
□ Database continuity (streaming replication, PITR, JDBC failover)
  demonstrated (v38)
□ MQ continuity (persistent messages, queue/channel recovery)
  demonstrated (v38)
□ LDAP/registry sync failure drilled and backup-restore path confirmed
  (v38)
□ At least one zero-downtime rolling maintenance window performed
  against live traffic (v38)
□ Full backup inventory (WAS, IHS, plugin, SSL/keystore, LDAP/registry,
  DB, EARs, MQ, scripts, Git, Grafana, Prometheus) confirmed restorable,
  not just backed up (v38)
□ DR runbook executed end-to-end as a documented, approved change (v38)
□ All production exercises in v38 run at least once with recorded
  outcomes
□ Every drill/exercise across v36–v38 verified through the P04
  observability stack (Grafana/Prometheus/Jaeger/ELK), not manual
  inspection alone
□ RACI Incident Commander authority confirmed consistent across v37's
  Site Failure governance note and v38's Management Approval step
□ All three versions' TestCases-v36.md–v38.md signed off per Test Case
  Standards
□ Promoted Dev → UAT → Prod per Environment Promotion Standards,
  part5-release tag applied

Application State After This Part
Application code: unchanged from P03/P04 (digistack-bank family of EARs +
Mobile/ATM Tomcat apps), with the narrow addition of idempotency-key
handling on Fund Transfer (v38). No new banking modules were added in
this Part.

New Infrastructure
- Secondary DR site (Bangalore DC) — full WAS ND (separate cell,
  BlrCell01), IHS, Load Balancer, CBS + satellite EARs, MQ standby
  Messaging Engine, LDAP/registry replica, PostgreSQL standby (v37)
- Centrally-hosted observability stack independent of both DCs (v37)
- Database streaming replication, PITR capability (v38)
- Formal DR runbook, expanded backup inventory (now including LDAP/MQ),
  business continuity process documentation (v38)

Carried Forward to P06
The HA/DR/continuity discipline built here (failure taxonomy, RPO/RTO
targets, DR runbook, backup inventory, two-cell topology, centrally-
hosted observability) becomes the template P06 extends from
single-DR-site to true multi-region operation.
