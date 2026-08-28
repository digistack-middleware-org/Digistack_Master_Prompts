ID: P04
Version: 1.0
Status: Active

Title: Enterprise Observability, SRE & Production Operations

Imports:
IDX
STD
ARCH02
STDGAP01
SOE01
CAP01
RACI01

Content Prerequisites (not formal Import Rule dependencies — listed here
for traceability; these Parts' applications are what this Part instruments):
P01 (v14 JVM tuning, v7 connection pool sizing)
P02 (v18 Operations Dashboard superseded here at v31)
P03 (9 deployables this Part instruments — CBS, Portal, PaymentHub,
     NotificationService, ReportingService, BranchPortal, CardPortal,
     MobileBanking, ATMSimulator)

Exports:
Versions 31-35.5
Application & Infrastructure Observability Foundations
Centralized Logging & Log Analytics
APM, Distributed Tracing & SRE Methodology
Alerting, Dashboard Engineering & Synthetic Monitoring
Production Operations, Capacity Planning & Reporting
Enterprise Incident Management & ServiceNow Integration —
Unified Monitoring Portal (monitoring.digistack.cloud)

Used By:
P03.1 (Interview-3/4 cite P04 v31/v33/v34/v35 as forward references — now resolved)
P03.2 (Chapter 2 production scenarios reference P04 components)
P05
P06

Next:
P05

---

Purpose
-------
Learn to operate, monitor, troubleshoot, tune, and support a production
WebSphere environment — moving from "the app exists" (P01–P03) to "the app
is operated like a real bank runs it."

Prerequisite
------------------
P03 Completion Checkpoint satisfied — 9 distinct deployables (7 WAS EARs +
2 Tomcat apps) live, CBS is sole writer of digistack_cbs. Named
explicitly, per P03's Carried Forward to P04: digistack-cbs.ear (CBS),
Internet Banking Portal EAR, Payment Hub EAR, Notification Service EAR,
Reporting Service EAR, Branch Portal EAR, Card Portal EAR (7 WAS EARs) +
Mobile Banking Simulator WAR, ATM Simulator WAR (2 Tomcat apps).

P03.1 and P03.2 (Interview Preparation) are recommended complete before
starting this Part — P03.1's "Carried Forward to P04" note states the same.
P04 is not a technical dependency of P03.1/P03.2, but the observability
work here assumes the same P01–P03 platform fluency those Parts are
designed to solidify. Reading order: P03 → P03.2 → P03.1 → P04.

Glossary note (added 2026-08-26): SOE01 and CAP01, imported above (and by
P01), are the project's Standard Operating Environment and Capacity/
Sizing reference documents respectively — SOE01 defines the baseline OS/
JVM/middleware versions and patching conventions every VM in this roadmap
follows; CAP01 defines the lab-scale VM sizing conventions this Part
refers to elsewhere as "doc 01" (see Version 33's Load-Test Scale
Disclaimer). Neither has its own authored file yet in this project — until
one exists, treat both as external reference documents whose conventions
this roadmap follows rather than restates.

Deployment Model
------------------
No new banking features are added anywhere in this Part. Every version
instruments, monitors, or operationally hardens the 9 applications already
built. This Part is 100% infrastructure/tooling — the strictest "zero new
banking functionality" run of the whole roadmap.

---
Part-Start Architecture Diagram (generate first, before Version 31 work begins)
----------------------------------------------------------------------------------
P04 is the first Part where the 01_Architecture/ file set (9 diagrams,
all scoped to network/VM/cluster/request/DB/security/deployment/DR) has
no dedicated file for what this Part actually builds — observability
tooling (Prometheus/Grafana/OpenSearch/Jaeger/Alertmanager) isn't covered
by any of the 9 filenames in that folder's index. This Part's tree is
therefore mostly "no diagram file to touch," which is worth stating
explicitly rather than silently skipping the check.

                 DIGISTACK BANK — P04 (v31-v35.5)
                       |
              (inherits P01-P03's full tree unchanged —
               zero new banking functionality, zero new
               network/VM/cluster/deployment topology)
                       |
                       v
                 SERVERS
                       |
                 02_VM_Layout.md
                 (extended: v31 dsb-monitor, v32 dsb-elk, v33 dsb-tracing all power on)

No dedicated 01_Architecture/ file exists for the observability stack
itself (Prometheus/Grafana/OpenSearch/Jaeger/Alertmanager topology) —
this is a gap worth flagging back to IDX/01_Architecture/README.md if a
10th diagram file is ever wanted for this Part's own request/data flow
(Version 31's own "Request Flow" ASCII diagrams inside P04_Observability.md
itself currently serve that purpose informally). Not in scope: 09_DR_
Architecture.md (P05, next Part).

End-to-End Enterprise Request Flow (Observability View)
------------------------------------------------------------
Every component named throughout this Part fits into one lifecycle. This
is the picture to hold in mind before reading the individual versions —
each version below builds one layer of it.

Customer
│
▼
IBM HTTP Server
│
▼
WebSphere Plugin
│
▼
WebSphere Cluster
│
┌────┼────┐
│ │ │
CBS MQ Notification
│
▼
Oracle 21c XE (digistack_cbs)

============================

Logs
↓
OpenSearch

Metrics
↓
Prometheus

Dashboards
↓
Grafana

Tracing
↓
Jaeger

Alerts
↓
Alertmanager

Incident
↓
Operations Team


The top half is the request path already built in P01–P03; the bottom
half is the observability platform this Part adds on top of it. Reading
top to bottom: a request flows through the application stack, and every
layer of that stack simultaneously feeds logs, metrics, and traces into
the observability platform, which in turn drives dashboards, alerts, and —
when something breaks — an incident.

---

Version Numbering Correction (resolved before this file is marked ready)
------------------------------------------------------------------------------
The source material this Part was drafted from numbered its versions
28–32. That collides with P03, which already owns and has frozen Versions
23–30 (per Engineering Standards §7 — Version Numbering Freeze). Per the
freeze rule, this Part is renumbered to start immediately after P03 ends.

Renumbering table (required by Engineering Standards §7 whenever a
renumbering happens):

| Old # (draft) | Old Title | New # | New Title |
|---|---|---|---|
| 28 | Enterprise Monitoring & Observability | 31 | Application & Infrastructure Observability Foundations |
| 29 | Enterprise Logging & Observability | 32 | Centralized Logging & Log Analytics |
| 30 | Performance Monitoring & APM | 33 | APM, Distributed Tracing & SRE Methodology |
| 31 | Distributed Tracing & SRE | 34 | Alerting, Dashboard Engineering & Synthetic Monitoring |
| 32 | Production Support & Incident Management | 35 | Production Operations, Capacity Planning & Reporting |

No version has been implemented yet, so this is a clean renumbering (same
precedent as P03's own pre-implementation renumbering pass) — not a
mid-flight break.

Second correction — the "gap analysis" document (source doc 14) was never
actually merged into any version. It proposed: Application Observability,
Business Monitoring, SRE Golden Signals/RED/USE, Alert Engineering,
Dashboard Engineering, Synthetic Monitoring, Runbooks, Capacity Management,
SLA/SLO/SLI/Error Budgets, Production Reporting, and deeper WebSphere
diagnostics. All of these are now folded into the 5 versions below rather
than left as a floating wishlist.

Third correction — P03's own forward reference. P03's Completion
Checkpoint states this Part will cover "APM, distributed tracing, and
chaos testing" for all 9 applications. The first two are covered (v33);
chaos testing was missing from the original draft entirely — it's now
added in Version 35.

---

Version 31 — Application & Infrastructure Observability Foundations
------------------------------------------------------------------------------
Objective: Establish the base observability layer: the application itself
must emit logs, metrics, traces, and a health endpoint (doc 14's gap #1 —
"observability starts inside the app, not outside it") — and
infrastructure/JVM-level monitoring is stood up alongside it.

Relationship to P02 v18's Operations Dashboard (resolved gap)
P02 v18 already built a minimal, custom PMI/JMX-based "Operations
Dashboard" covering JVM health, active session count, JMS queue depth, and
DB pool usage — and this version's scope covers almost identical ground
(JVM heap/GC/thread pools, JDBC pool usage, session count, cluster health)
via industry-standard tooling instead. Neither file previously stated how
the two relate, which could read as accidental duplication to a fresh
reader.

Resolution: Version 31 formally supersedes and retires P02 v18's custom
Operations Dashboard. v18's dashboard served its purpose at that point in
the roadmap — proving the underlying PMI/JMX data existed and was
reachable, and giving genuine WAS-admin practice building a simple viewer
over it. It was never intended as the project's permanent monitoring
solution. Here in v31, the same underlying PMI/JMX data sources are
re-pointed into the Prometheus/Grafana stack; v18's standalone custom
dashboard code is decommissioned once this version's Grafana instance
reaches equivalent coverage. SetupDoc-v31.md should note this retirement
explicitly (what URL/app v18's dashboard lived at, and confirmation it's
been taken down) so the decommission isn't silently assumed, consistent
with how this project already treats every other retirement (e.g., the
legacy Portal database after v23).

Forward reference flag (added 2026-08-26): an earlier draft of this note
also cited "the old WAS platform after P07 v48" as a second precedent.
P07 is not yet authored, so that citation was a forward reference only,
not an already-real precedent — removed above. The v23 Portal-database
retirement is sufficient precedent on its own. If P07 v48 does retire a
WAS platform version once authored, it should follow this same
documented-retirement convention, not the other way around.

Minimum App Needed
Zero new banking functionality. Every one of P03's 9 applications gets:
- A /health endpoint (liveness/readiness) — CBS, Internet Banking Portal,
  Payment Hub, Notification Service, Reporting Service, Branch Portal, Card
  Portal, Mobile, ATM
- Structured log output (consistent field format, so Version 32's log
  pipeline has something uniform to ingest) — field format must
  mask/tokenize sensitive values at the point of emission (see the Log PII
  Masking Requirement below); this is a property of the structured-logging
  work done here, not something bolted on after ingestion in v32
- A metrics-exposition endpoint per app (JMX-backed for WAS apps, a
  lightweight exporter for the two Tomcat apps)
- Monitoring data retention is set explicitly (Prometheus 30 days,
  OpenSearch 90 days, Jaeger 7 days) rather than left at each tool's
  default

/health Endpoint Authentication Note (added 2026-08-25)
----------------------------------------------------------
Several P03 applications enforce role-based authentication that could
block unauthenticated Prometheus scrapes of the /health endpoint:
- Internet Banking Portal: Customer / Administrator roles (P01 v10)
- Branch Portal: Branch Teller role (P03 v29)
- Card Portal: own auth (P03 v28)
Resolution: /health and /metrics endpoints are placed on a sub-context
that bypasses the application's role-mapping authorization filter. This
is a standard pattern (health/readiness probes must be reachable by the
monitoring system without end-user credentials). The bypass is
implemented as a Servlet filter exclusion list in each EAR's web.xml and
documented in SetupDoc-v31.md. The bypass does NOT expose any banking
data — /health returns only {status: UP/DOWN} and /metrics returns only
numeric counters/gauges. Prometheus scraping uses a dedicated monitoring
service account (not a customer or teller credential) configured in each
application's security descriptor.

Log PII Masking Requirement (resolved gap)
P03 v24 introduces CIF data including Aadhaar and PAN verification, and
every version since v2 has logged login/transaction activity. Prior to
this revision, no version anywhere defined a masking/redaction policy
before that data landed in a centralized, searchable log store — a real
compliance gap for anything resembling a banking platform, especially once
P09 v65 replicates logs into S3 across regions.

Rule, effective at this version (the structured-log format is being
defined here for the first time): account numbers, Aadhaar/PAN numbers,
and full card numbers (once Card Portal exists, P03 v28) are masked or
tokenized in the structured log format itself — e.g., last-4-digits-only
for account/card numbers, full redaction for Aadhaar/PAN — before the log
line is ever written, not filtered or redacted after ingestion into
OpenSearch (v32). Amount fields and transaction IDs are not considered
sensitive in the same way and may remain unmasked, since they're needed
for troubleshooting and reconciliation. SetupDoc-v31.md must show the
actual masking implementation (regex/field-level) used, and this
requirement is checked again explicitly at P09 v72's security/compliance
audit.

PII Masking — P02 v16.5 Pagination Fetch Path (added 2026-08-25)
------------------------------------------------------------------
P02 v16.5 fetches the full SOAP Account Statement result set into servlet
memory before slicing it into pages. If any log line emitted during that
fetch contains unmasked account numbers (e.g., a debug log printing the
raw SOAP response body), the masking rule above applies there too. The
structured-log format defined here must cover the Portal's servlet layer,
not just the CBS/Payment Hub/Notification Service application logs.
SetupDoc-v31.md must confirm the masking implementation is applied to the
Portal EAR's structured logger, and a negative test (deliberately log the
raw SOAP fetch, confirm the account number is masked in the output) is
required in TestCases-v31.md.

Enterprise Tools: Prometheus, Node Exporter, JMX Exporter, Oracle DB Exporter (for digistack_cbs on Oracle 21c XE), Grafana (dashboard shell only — dashboard design is Version 34).

WebSphere Native Monitoring: PMI, JMX, Tivoli Performance Viewer (TPV).

Monitoring Coverage
- Linux: CPU, memory, disk, filesystem, network, process monitoring
- WebSphere: JVM heap, GC, thread pools, JDBC connection pools, session
  count, cluster health, Node Agent health, DMgr health
- Application: Internet Banking health, CBS health, ATM Service health,
  Card Service health, Payment Hub health, Database health (Oracle 21c XE — digistack_cbs)

Request Flow

Linux Servers  ──► Node Exporter         ─┐
WebSphere JVM  ──► JMX Exporter         ──┼──► Prometheus ──► Grafana ──► Ops Dashboard
Oracle 21c XE  ──► Oracle DB Exporter ───┘
▲
IBM WebSphere ND Cell (DMgr / Node Agent / Cluster Members)
+ Mobile/ATM Tomcat instances (lightweight exporter)


VM Setup Note: New VM introduced: dsb-monitor (Prometheus, Grafana,
Alertmanager, exporters — per STD §VM Hostnames).

Topics Covered: PMI, JMX, Health Policies, JVM Monitoring, Cluster
Monitoring, JDBC Pool Monitoring, Session Monitoring.

Enterprise Learning: Capacity Planning (intro), Health Monitoring, SLA
Monitoring (intro — formalized in v35), Middleware Operations.

Sprint Deliverable: Every one of the 9 P03 applications exposes a working
/health endpoint and metrics endpoint (with auth bypass for monitoring
service account, per the /health Endpoint Authentication Note above);
Prometheus scrapes Node/JMX/Oracle exporters across the full WAS +
Tomcat topology; a bare Grafana instance renders live JVM heap, session
count, and cluster health with no manual polling; P02 v18's custom
Operations Dashboard is confirmed retired, with its former data sources
now flowing into this Grafana instance instead; a negative test confirms
account numbers in servlet log lines from the Portal's SOAP fetch path
(P02 v16.5) are masked per the PII Masking rule above.

---

Version 32 — Centralized Logging & Log Analytics
--------------------------------------------------------
Objective: Centralize logs across all 9 applications, IHS, MQ, and Linux
for troubleshooting and production support — and give Version 31's
app-level structured logs somewhere to land.

Minimum App Needed: Zero new banking functionality.

Correlation ID Standard (added 2026-08-25)
-------------------------------------------
The Sprint Deliverable for this version requires searching a failed Fund
Transfer "by correlation ID" across CBS, Payment Hub, and Notification
Service logs — but no prior version defined what a correlation ID is, who
sets it, or what format it takes.

Definition, effective retroactively from P02 v15 (Fund Transfer introduced):
- Format: FT-{YYYYMMDD}-{5-digit-zero-padded-sequence}, e.g.
  FT-20260825-00192 (matches the example already used in v35.5).
- Set by: the Internet Banking Portal's Fund Transfer Servlet at the moment
  the transfer request is accepted (before the JMS message is enqueued).
- Propagated via: JMS message header (JMSCorrelationID property) from
  Portal → SIBus/MDB → CBS; HTTP header (X-Correlation-ID) on any REST
  call leg (Portal → CBS REST, Payment Hub → CBS REST); SOAP header on
  the Account Statement SOAP call (P02 v16).
- Logged by: every service that touches the transfer (Portal, CBS, Payment
  Hub, Notification Service, MQ) must include the correlation ID in every
  structured log line relating to that transfer.
- Scope: Fund Transfer only in P02–P03. Extended to IMPS/NEFT at P03 v25
  (Payment Hub) using the same format with prefix PT- for payment
  transactions.
This standard is documented once here (v32, where it first becomes
operationally necessary) and applied retroactively as a code fix to P02
v15 onward. SetupDoc-v32.md must include a "Correlation ID Retrofit"
section confirming the header propagation is in place across all affected
EARs before the log pipeline is stood up.

Enterprise Tools: Filebeat, Logstash, OpenSearch (Elasticsearch-compatible),
OpenSearch Dashboards (Kibana-compatible).

WebSphere Native: SystemOut.log, SystemErr.log, HPEL, FFDC, Trace Logs.

Log Sources Covered: Middleware (deployment/startup/security/plugin logs),
Application (login transactions, Fund Transfer logs, ATM logs, Card logs,
CBS logs), IHS logs, MQ logs, Linux logs, Database logs.

Search & Analysis Capability: Exception search, user activity, failed
transactions, JVM errors, database errors.

Request Flow

WebSphere Logs / IHS Logs / MQ Logs / Linux Logs / DB Logs
│
Filebeat Agents
│
Logstash
│
OpenSearch Cluster
│
OpenSearch Dashboards
│
Operations & Support Team


VM Setup Note: New VM introduced: dsb-elk (Filebeat targets ship to
Logstash → OpenSearch → OpenSearch Dashboards — per STD §VM Hostnames).

Topics Covered: HPEL, FFDC, Trace Specification, Log Rotation, Heap Dumps,
Thread Dumps, GC Logs.

Enterprise Learning: Log Analysis, Root Cause Analysis (RCA) —
introductory pass, deepened in v35; Production Troubleshooting, Audit
Investigation.

Sprint Deliverable: A single OpenSearch Dashboards view lets you search a
failed Fund Transfer across CBS, Payment Hub, and Notification Service
logs by correlation ID (per the Correlation ID Standard defined above),
without touching any individual VM's log files directly; a manually
triggered thread dump and heap dump are captured, shipped through the
pipeline, and located in OpenSearch.

---

Version 33 — APM, Distributed Tracing & SRE Methodology
------------------------------------------------------------
Objective: Move from "metrics + logs" to true request-level tracing
across the 9-service topology, and formally introduce SRE measurement
methodology — the concepts doc 14 flagged as missing entirely from the
original draft (Golden Signals, RED, USE, SLO/SLI/SLA/Error Budgets).

Minimum App Needed: Zero new banking functionality. A single Fund Transfer
request is traced end-to-end: Internet Banking Portal → CBS → IBM MQ →
Notification Service → PostgreSQL.

Enterprise Tools: OpenTelemetry SDK (instrumentation), Jaeger (trace
storage/UI); Prometheus/Grafana (reused from v31) for the
performance-metrics half of APM.
Concepts-only (no install required — commercial licensing): IBM Instana,
Dynatrace, AppDynamics, Datadog — covered as enterprise APM concepts a
resume-holder should be able to discuss, not deployed.

SRE Measurement Methodology (doc 14 gap — now a first-class topic)
- Four Golden Signals: Latency, Traffic, Errors, Saturation
- RED Method: Rate, Errors, Duration (applied to CBS/Payment Hub REST+SOAP
  endpoints)
- USE Method: Utilization, Saturation, Errors (applied to JVM/DB/MQ
  resources)
- SLI / SLO / SLA / Error Budgets — defined concretely for at least one
  business flow (recommend: Fund Transfer availability + latency)

Performance Coverage: JVM heap/GC/thread utilization (see the thread-pool
note below), DB connection pool usage/slow queries/wait time (worked
sizing example: P01 v7's Connection Pool Sizing), application response
times (Login, Fund Transfer, CBS, ATM Service), load/stress/concurrent-user
testing via Apache JMeter (standing tool choice).

WebSphere Thread Pools — Named Explicitly
"Thread Pool Tuning" appeared as a generic topic since P01 v14 without
ever naming WebSphere's actual distinct thread pools — genuinely
differentiating knowledge for a WAS admin, and directly relevant given how
central MDBs have been to this project's Fund Transfer flow since P02 v15.
Named here, once, for reuse: the Web Container thread pool (handles
incoming HTTP/HTTPS requests), the ORB/EJB thread pool (remote EJB and
some internal WAS calls), the Default thread pool (fallback for work not
bound to a specific pool), and the MDB listener port's own thread pool
(specifically relevant to SIBus/MQ message consumption — MDB listener
thread starvation is a distinct failure mode from Web Container
exhaustion, and worth distinguishing during this version's load testing).

Load-Test Scale Disclaimer
This project's infrastructure is sized per doc 01's lab baseline (2–4
vCPU, 4–8 GB RAM per VM). A JMeter run against this topology validates the
mechanism — that SLO measurement, RED/USE instrumentation, and saturation
detection actually work end-to-end — not a production-representative
capacity number. Treat any concurrency figure produced in this version's
load test as illustrative of the method, not as a claim about what the
platform could handle in real production; SetupDoc-v33.md should record
the actual achieved concurrency honestly, without implying it validates
production-scale capacity.

SLO Placeholder Note (added 2026-08-25)
----------------------------------------
The Sprint Deliverable states the SLO as "99% of Fund Transfers complete
under Xms, 99.9% monthly availability." The X and N (concurrent users) are
intentional TBDs — they cannot be filled in before the load test runs,
because this lab environment's capacity varies by hardware. SetupDoc-v33.md
must fill these in from actual load-test results before this version is
signed off. The SLO template is:
  - Latency SLO:      99% of Fund Transfers complete under [X]ms
                      (fill from p99 latency at load-test steady state)
  - Availability SLO: 99.9% monthly availability
                      (fixed target — not hardware-dependent)
  - Saturation point: first component to saturate at [N] concurrent users
                      (fill from JMeter + RED/USE data)
TestCases-v33.md must include a test case that reads the SLO values from
SetupDoc-v33.md and verifies the running system meets them under the
recorded load.

Request Flow

Customer Request → IHS → WAS Cluster → {Banking Portal, CBS, IBM MQ} → Oracle 21c XE (digistack_cbs)
│
OpenTelemetry SDK
│
Jaeger (distributed tracing)
│
JMX Exporter → Prometheus → Grafana (perf metrics)


VM Setup Note: New VM introduced: dsb-tracing (Jaeger backend/UI — may
co-locate on dsb-monitor per SOE01 §1a RAM budget; per STD §VM Hostnames).

Topics Covered: JVM Tuning, Thread Pool Tuning, JDBC Pool Tuning, Dynamic
Cache, Performance Advisor, Garbage Collection Analysis.

Enterprise Learning: Performance Tuning, Capacity Planning, Application
Performance Monitoring, JVM Optimization, Request Tracing, SLO/SLA/SLI,
Error Budgets.

Sprint Deliverable: A single Fund Transfer trace is visible end-to-end in
Jaeger across Portal → CBS → MQ → Notification Service, with per-hop
latency; an SLO is defined (with Xms and N filled in from actual load-test
results per the SLO Placeholder Note above) and its current error budget is
calculated from real traced data; a load test proves the SLO holds under N
concurrent users and identifies the first component to saturate (RED/USE
data pinpoints it).

---

Version 34 — Alerting, Dashboard Engineering & Synthetic Monitoring
------------------------------------------------------------------------------
Objective: Doc 14 flagged that the original draft stopped at "install
Alertmanager" and "build a Grafana dashboard" without teaching how to
design either — this version corrects that, and adds synthetic
(always-on, no-real-user-required) monitoring and business-level KPIs,
which were entirely missing from the original scope.

Minimum App Needed: Zero new banking functionality. Scheduled synthetic
transactions run against existing endpoints (login, balance check, Fund
Transfer, ATM request, card payment) every 5 minutes.

Synthetic Monitoring Service Account (added 2026-08-25)
---------------------------------------------------------
Synthetic Login, Fund Transfer, Balance Check, ATM Request, and Card
Payment checks require real credentials and a real test account in the
system — they cannot run against production customer accounts, and they
must not generate real financial transactions visible to customers or
reconciliation. Resolution:
- A dedicated synthetic monitoring user (username: digistack-synthetic,
  role: Customer) is created in the WAS user registry (file-based or LDAP,
  per P01 v10) and in digistack_cbs (a real account with a synthetic test
  balance, not deducted from any real customer's funds).
- A dedicated test Beneficiary (for Fund Transfer synthetic check) is
  registered under this account, pointing to a second synthetic account
  (digistack-synthetic-dest) so the transfer completes end-to-end without
  touching any real account.
- Synthetic transactions are tagged with a synthetic=true header/flag so
  they are: (a) excluded from Business Dashboard KPIs (real customer
  metrics only), (b) included in availability/SLO tracking (that's their
  purpose), and (c) excluded from the Notification Service's email triggers
  (no alert emails for synthetic test transfers).
- SetupDoc-v34.md must include setup of the synthetic service account as
  a prerequisite step before synthetic monitoring is enabled.

Alert Engineering (doc 14 gap): Alertmanager routing rules covering:
priority tiers (P1–P4), threshold design, escalation matrix, alert
routing, deduplication, maintenance windows/suppression, and the "alert
fatigue / false positive" tradeoff — applied to at least one real alert
(e.g., JDBC pool exhaustion) end-to-end from threshold to email
notification.

Dashboard Engineering (doc 14 gap): Purpose-built dashboards, not default
exports: Linux, WebSphere/JVM, Cluster, Database, MQ, Business, Executive,
NOC, Production, SLA. At minimum, build one Operational dashboard
(JVM/cluster/DB health) and one Business dashboard.

Business Monitoring (doc 14 gap — new KPI category, distinct from infra
metrics): Login success/failure rate, account opening rate, Fund Transfer
success/failure rate, average transfer time, ATM transaction count, CBS
transaction count, daily active users, transactions per minute.

Synthetic Monitoring (doc 14 gap): Scheduled synthetic Login, Fund
Transfer, Balance Check, ATM Request, Card Payment, and Health Endpoint
checks every 5 minutes using the synthetic service account above, alerting
Alertmanager on failure — proves the platform is monitored even with zero
real user traffic.

Request Flow

Synthetic Job (every 5 min) ──► Login / Fund Transfer / Balance Check / ATM / Card
│
Success/Failure metric ──► Prometheus ──► Alertmanager ──► Email/On-call
│
Grafana
(Business Dashboard + SLA Dashboard)


Topics Covered: Health Policies (extended from v31), Performance Advisor
cross-reference, Alert-driving PMI thresholds.

Enterprise Learning: Alert Engineering, Dashboard Engineering, Synthetic
Monitoring, Business/Executive reporting design.

Sprint Deliverable: A deliberately induced failure (kill a cluster member)
fires a P2 alert through Alertmanager to email within the defined
threshold window, with no duplicate alerts and no false positive during a
defined maintenance window; a Business Dashboard shows live Fund Transfer
success rate and daily active users (synthetic transactions excluded from
KPI counts per the Synthetic Monitoring Service Account note above); a
synthetic Fund Transfer check catches a deliberately broken endpoint
within 5 minutes, before any real customer would.

---

Version 35 — Production Operations, Capacity Planning & Reporting
------------------------------------------------------------------------
Objective: Simulate real production support end-to-end — incident
lifecycle, runbooks, chaos/resilience testing, capacity forecasting, and
operational reporting — closing every remaining doc 14 gap and fulfilling
P03's forward reference to chaos testing.

Minimum App Needed: Zero new banking functionality.

Enterprise Tools: ServiceNow (concepts only), Grafana Alerts, Alertmanager,
Email Notifications.

Incident Management Scope: JVM crash, OutOfMemoryError, hung threads,
database connection failure, Node Agent failure, cluster member failure,
IHS failure.

Support Process: Incident creation → assignment → investigation →
resolution → RCA → closure. Change Management covers EAR deployment,
config changes, JVM restart, rollback — all tying back to P01's rollback
discipline (v4) and P01 doc 01's snapshot conventions.

Cross-reference note (added post-freeze, per Progress Log's Open
Questions entry, resolved 2026-07-23): this version's Incident Management
Scope names the process steps (creation → assignment → investigation →
resolution → RCA → closure) but does not name who holds cross-team
authority when an incident spans more than one team. That answer is
RACI01 §4's Incident Commander role — added as a documentation
cross-reference only, per the Progress Log's Resolution path item 1; no
technical scope in this version changes.

Production Runbooks (doc 14 gap): For each incident type in scope above, a
runbook is written in the standard shape:

Alert → Symptoms → Investigation Commands → Diagnosis → Resolution → Verification → Closure

Minimum: one fully worked runbook for a hung-thread incident, one for a
JDBC connection-pool exhaustion incident.

Chaos Engineering / Resilience Testing (fulfills P03's forward reference)
P03's Completion Checkpoint explicitly carries forward "APM, distributed
tracing, and chaos testing" as this Part's job — the first two are
covered in v33; this is where chaos testing is fulfilled. Deliberately
inject failure into the now-fully-observable platform and confirm the
observability stack (v31–v34) actually detects, alerts on, and traces each
one:
- Kill a WAS cluster member mid-transaction → confirm failover (reuses
  P01 v5's session replication) is visible in Grafana within seconds and
  does not breach the v33 SLO
- Kill the MQ Queue Manager → confirm a Fund Transfer's external leg (P02
  v19) lands in the DLQ and fires an alert (v34), not a silent failure
- Saturate a JDBC connection pool deliberately → confirm the v34 alert
  fires before the incident is manually noticed, and the resulting
  runbook-driven response is the one written above
- Kill one monitoring VM process (Prometheus on dsb-monitor, or OpenSearch on dsb-elk) → confirm the observability
  platform's own resilience (monitoring the monitors)

Chaos Scenario 4 — Single-VM Monitoring Resilience Note (added 2026-08-25)
---------------------------------------------------------------------------
The original draft described chaos scenario 4 as "Kill one
OpenSearch/Prometheus/Jaeger node" — implying a multi-node cluster where
killing one node tests failover. This project's VM setup (v31/v32) uses
single-instance deployments: one dsb-monitor for Prometheus, one dsb-elk for OpenSearch. There is no second node to fail over
to. Scenario 4 is therefore restated as: kill the Prometheus process
(not the VM) on dsb-monitor, confirm Alertmanager fires a
"monitoring system degraded" alert and Grafana shows data gaps, then
restart Prometheus and confirm it re-scrapes and back-fills within its
retention window. Same for the OpenSearch process on dsb-elk.
This tests process-level resilience and recovery (a real operational
concern) without requiring a multi-node cluster that isn't in this
project's VM inventory. If a multi-node observability cluster is desired
for a more realistic test, that is new infrastructure scope requiring a
doc 01 VM inventory update before this scenario can be run as originally
described.

Each chaos scenario is run once, its detection time recorded (feeds the
MTTD figure below), and the outcome — caught vs. missed — becomes an
input into the next iteration of alert thresholds (v34) and runbooks (this
version).

Capacity Management (doc 14 gap): Trend analysis, peak load analysis,
capacity forecasting, growth planning, JVM sizing, database growth
projection, storage/CPU forecasting — built from Version 31–34's real
collected metrics, not estimated from scratch.

Availability & SLA Formalization (doc 14 gap, builds on v33's SLO work):
Service availability, uptime/downtime tracking, MTTR, MTTD, MTBF, and a
finalized SLA document for at least the Fund Transfer and Login flows,
tying directly to v33's error budget.

Production Reporting (doc 14 gap): Daily Health Report, Weekly Operations
Report, Monthly Capacity Report, Incident Trend Report, Availability
Report, SLA Report, Executive Summary — at minimum one of each generated
once, from real data, not a template with placeholders.

Deeper WebSphere-Specific Diagnostics (doc 14 gap, extends v31/v32):
javacore analysis, native memory monitoring (concepts), PMI counter
interpretation, hung thread investigation, session leak investigation,
JDBC leak investigation.

Request Flow

Monitoring Alert (v34) ──► Alertmanager ──► Email ──► ServiceNow Incident (concept)
│
Middleware Team ──► Runbook ──► Investigation ──► Resolution ──► RCA ──► Closure
│
Capacity data / Availability data ──► Monthly Capacity Report / SLA Report


Topics Covered: Heap Analysis, Thread Analysis, FFDC Analysis, Server
Recovery, Node Synchronization, Cluster Recovery, javacore Analysis.

Enterprise Learning: Incident Management, Problem Management, Change
Management, Production Support, Root Cause Analysis, Chaos Engineering,
Capacity Planning, SLA/MTTR/MTTD/MTBF, Production Reporting.

Sprint Deliverable: A simulated hung-thread incident is worked
start-to-finish using its written runbook — detected via v34's alerting,
diagnosed via v32's log pipeline and v31's JMX data, resolved, and closed
with a documented RCA; the four chaos scenarios above are each run once
with detection outcomes recorded (scenario 4 per the single-VM resilience
restatement above); a Monthly Capacity Report is generated from real
Version 31–34 metrics and includes a JVM heap growth forecast; an SLA
Report for Fund Transfer shows actual measured availability against the
Version 33 SLO (with Xms and N filled from SetupDoc-v33.md), with
MTTR/MTTD calculated from the incident and chaos runs above.

---

Version 35.5 — Enterprise Incident Management & ServiceNow Integration
------------------------------------------------------------------------
Core promise: Alert → Automatic Ticket → Assignment → Investigation →
Resolution → Verification → RCA → Closure.

Objective: Give Versions 31–35 a single-pane-of-glass entry point
(monitoring.digistack.cloud), and make the final leg — turning an alert
into a formally managed, assigned, deduplicated, and closed incident —
fully practical, not just conceptual. This is a capstone integration
version: the portal surfaces and links into data/tools already built
(Prometheus/Grafana, OpenSearch, Jaeger, Alertmanager, ServiceNow) and
adds the ServiceNow-facing incident workflow on top. None of those
underlying tools are replaced or retired by the portal's existence — see
"Portal Is an Entry Point, Not a Replacement" below.

Important clarification — the portal is an entry point, not a
replacement: Grafana, OpenSearch, Jaeger, Alertmanager, and ServiceNow are
NOT retired or hidden by this version. The portal is a single-pane-of-
glass front door that surfaces and links to those tools; each specialized
tool still exists, is still directly reachable, and still owns its own
data:

```
Portal
  │
  ├── Prometheus / Grafana → Metrics
  ├── OpenSearch            → Logs
  ├── Jaeger                → Traces
  ├── Alertmanager          → Alerts
  ├── Runbooks              → Operations
  └── ServiceNow            → Incidents
```

This is a different relationship than v31's supersession of P02 v18's
custom dashboard: v18's dashboard was a bespoke one-off tool being
replaced by industry-standard tooling, so it was correctly retired.
Grafana/OpenSearch/Jaeger/Alertmanager/ServiceNow are the industry-
standard tools themselves — the portal composes them, it doesn't
duplicate or supersede them.

Minimum App Needed: Zero new banking functionality. The portal itself is
a new small internal WAS application (digistack-monitoring-portal.ear)
deployed to dsb-monitor, co-located with the monitoring stack (not a member of the banking cluster, not a new VM — it must remain reachable even if the banking cluster is fully
down). This is the 10th deployable in the project (the 8th WAS EAR) but
is NOT counted among the 9 banking deployables — it is infrastructure,
not a banking application, the same way dsb-monitor is a VM
in the topology but not a banking server. SetupDoc-v35.5.md must document
its deployment target server (dsb-monitor, co-located — not a new VM),
context root (/monitoring), and that it is excluded from the 9-app banking
count.

ServiceNow Integration — Concepts vs. Simulation Clarification
(added 2026-08-25)
---------------------------------------------------------------
v35 lists "ServiceNow (concepts only)" under Enterprise Tools. v35.5
describes "Automatic Ticket Creation" with a specific API call timeline.
These are reconciled as follows:
- No live ServiceNow instance is required. The Alertmanager webhook calls
  a local ServiceNow simulator — a lightweight stub server
  (digistack-servicenow-stub, running on dsb-monitor) that
  accepts the same REST API calls as a real ServiceNow instance and
  returns realistic INC-number responses. It does not require a
  ServiceNow licence or cloud account.
- The stub is documented in SetupDoc-v35.5.md with its API contract so
  it can be replaced by a real ServiceNow developer instance (free tier)
  or a mock if preferred — the Alertmanager webhook configuration is
  identical either way.
- "Concepts only" means the candidate understands ServiceNow's ITSM
  workflow (incident lifecycle, priority routing, assignment groups) and
  can discuss it in an interview — not that the integration is entirely
  theoretical. The stub makes the auto-ticket timeline real and
  demonstrable, without requiring enterprise licensing.

V35.5 Architecture

```
                    WebSphere
                       │
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
    Prometheus      OpenSearch       Jaeger
     Metrics          Logs           Traces
        │              │              │
        └──────────────┼──────────────┘
                       ▼
                  Alertmanager
                       │
                  P2 Alert
                       │
                       ▼
          monitoring.digistack.cloud
                       │
                       ▼
              Incident Management
                       │
                       ▼
            ServiceNow Stub / Instance
                       │
                       ▼
                WAS Support Team
                       │
                       ▼
                 Investigation
                       │
                       ▼
                    Runbook
                       │
                       ▼
                   Resolution
                       │
                       ▼
                  Verification
                       │
                       ▼
                      RCA
                       │
                       ▼
               ServiceNow Closure
```

Portal V1 — Monitor (built on v31)
Purpose: WebSphere/infrastructure health at a glance.
Sources: WAS JMX/PMI → JMX Exporter → Prometheus → Grafana; Linux → Node
Exporter → Prometheus; Oracle 21c XE (digistack_cbs) → Oracle DB Exporter → Prometheus.
Screens: WAS Cell / Cluster / Database summary tiles, Node Agent status,
per-application status table (all 9 deployables), JVM health (heap/GC/
threads), JDBC connection pool gauges per server, recent events feed.

Portal V2 — Observe (built on v31–v34)
Purpose: Full observability — metrics, logs, traces, and alerts in one
entry point.
Sources: Metrics (Prometheus/Grafana), Logs (Filebeat → Logstash →
OpenSearch), Traces (OpenTelemetry → Jaeger), Alerts (Prometheus →
Alertmanager), Synthetic monitoring (v34).
Screens: System health tiles (WebSphere/Database/MQ/Monitoring), active
alerts by priority (P1–P3), WebSphere health bars (heap/threads/JDBC/GC),
business health (Login success, Fund Transfer success, avg transfer time,
transactions/min), recent events, observability-service status strip
(Prometheus/Grafana/OpenSearch/Jaeger/Alertmanager/Synthetic).

Portal V3 — Operate (built on v35)
Purpose: Turn V2's data into actual production operations.
Screens: Production overview tiles, open incidents by priority,
Operations KPIs (MTTD/MTTR/MTBF), SLO/SLA panel (availability, error
budget, response SLO), capacity trend (JVM heap/JDBC pool/CPU
progression), and action shortcuts into the Runbook Library, Chaos Tests,
Capacity Report, SLA Report, and RCA Reports produced in v35.

Portal V4 — Manage Incident (new in this version)
Purpose: Close the loop from alert to resolved incident, wired to
ServiceNow stub (per the ServiceNow Integration clarification above).

New menu item added to the portal: 🚑 Incident Management. Selecting it
opens the Incident Management screen:

```
╔══════════════════════════════════════════════════════════════╗
║ INCIDENT MANAGEMENT                                          ║
╠══════════════════════════════════════════════════════════════╣
║                                                                ║
║ ACTIVE INCIDENTS                                              ║
║                                                                ║
║ 🔴 P1    0                                                    ║
║ 🟠 P2    2                                                    ║
║ 🟡 P3    4                                                    ║
║ 🔵 P4    7                                                    ║
║                                                                ║
║ ────────────────────────────────────────────────────────────  ║
║                                                                ║
║ INC0010042                                                    ║
║ P2 — JDBC Connection Pool Exhaustion                          ║
║ CBS / WAS02                                                   ║
║ Status: INVESTIGATING                                        ║
║ Assigned: WAS-L3                                              ║
║                                                                ║
║ Created: 14:32                                                ║
║ MTTD: 8 seconds                                               ║
║                                                                ║
║ [Open] [Investigate] [ServiceNow]                             ║
║                                                                ║
╚══════════════════════════════════════════════════════════════╝
```

Automatic Ticket Creation (the most important V35.5 feature): the WAS
administrator never manually opens the ticket. Example timeline:

```
14:32:00  JDBC Pool = 96%
14:32:05  Prometheus Alert Rule fires
14:32:06  Alertmanager receives alert
14:32:08  ServiceNow stub API called
14:32:09  INC0010042 CREATED
14:32:10  WAS-L3 assigned
```

ServiceNow ticket auto-populated fields: Incident Number, Priority,
Application, WebSphere Server, Cluster, Alert Name, Current Metric,
Threshold, Detected Time, Environment, Correlation ID, Description,
Monitoring Portal URL, Grafana URL, OpenSearch URL, Jaeger URL, Runbook
URL. Example:

```
INC0010042
Priority: P2
Service: DigiStack Banking
Application: CBS
Server: WAS02
Alert: JDBC Pool Exhaustion
Current: 96%
Threshold: 90%
Correlation ID: FT-20260825-00192
Source: Monitoring.digistack.cloud
```

Incident lifecycle (official workflow, expands the shorthand already
used in v35's Sprint Deliverable into full named states):
ALERT → TICKET CREATED → ASSIGNED → ACKNOWLEDGED → INVESTIGATING →
ROOT CAUSE FOUND → FIX IN PROGRESS → RESOLVED → VERIFICATION → RCA →
CLOSED.

Deduplication requirement (critical — prevents alert storms from
becoming ticket storms): if JDBC stays at 96% across multiple alert
evaluation cycles, this must NOT create INC001, INC002, INC003... in
sequence. Instead:

```
Alert starts    → INC001 created
Alert continues → INC001 updated
Alert continues → INC001 updated
Alert clears    → INC001 → RESOLVED
```

This works together with (does not duplicate) the Alertmanager
deduplication/grouping already required in Version 34 — V34 dedupes at
the alerting layer, V35.5 dedupes at the ticketing layer using the same
correlation ID so one real-world condition never produces more than one
open incident.

What a closed ticket contains (the glue this version provides): each
ServiceNow incident links out to every other version's artifacts rather
than duplicating their data —

```
INC0010042
     │
     ├── 📊 Metrics   → Grafana / Prometheus (v31, v34)
     ├── 📜 Logs      → OpenSearch (v32)
     ├── 🔍 Trace     → Jaeger (v33)
     ├── 📖 Runbook   → (v35)
     ├── 🖥 WebSphere → native PMI/JMX (v31)
     └── 📝 RCA       → (v35)
```

Flow: WAS → Prometheus → Alert Rule → Alertmanager → P2/P1 alert →
monitoring.digistack.cloud → Incident Management → ServiceNow stub API →
ticket (e.g., INC0010042) → team assignment (e.g., WAS-L3) →
Acknowledged → Investigation (pulls Metrics/Logs/Trace inline from Portal
V2) → Root Cause Found → Runbook (v35) → Fix in Progress → Resolved →
Verification → RCA (v35) → ServiceNow Closure.

Screens: Incident counts by priority (P1–P4), active incident cards with
inline links to Metrics/Logs/Trace/Runbook/ServiceNow, incident lifecycle
strip (Alert → Ticket → Assigned → Acknowledged → Investigating → Root
Cause → Fix → Resolved → Verify → RCA → Closed), MTTD/MTTR/SLA compliance
KPIs, ServiceNow connection status panel.

Portal Is an Entry Point, Not a Replacement (important clarification)
The portal is a single-pane-of-glass front end — it does not retire,
replace, or disappear the specialized tools underneath it. Grafana still
exists for deep-dive metric exploration, OpenSearch still exists for raw
log search, Jaeger still exists for full trace inspection, Alertmanager
still exists as the actual alerting engine, and ServiceNow still exists
as the actual system of record for incidents. The portal embeds/links
into each of these; it does not reimplement their functionality.

This is the same relationship v31 already established between the
Operations Dashboard and the underlying PMI/JMX data — except here it's
formalized across all five tools instead of just one, and made explicit
so a fresh reader doesn't assume v35.5 decommissions Grafana/OpenSearch/
Jaeger/Alertmanager/ServiceNow the way v31 decommissioned P02 v18's
custom dashboard.

Portal Family Summary

```
DIGISTACK BANK — monitoring.digistack.cloud
        │
   ┌────┼────┬─────────┐
   ▼    ▼    ▼         ▼
  V1   V2   V3        V4
Monitor Observe Operate Manage Incident
 (v31) (v31-34) (v35)   (this version)
```

How this Part reads end-to-end (V31→V35.5 narrative):
V31 Monitor — "Is it healthy?" (Prometheus + Grafana)
V32 Log — "What happened?" (Filebeat + Logstash + OpenSearch)
V33 Trace — "Where did it fail/slow?" (OpenTelemetry + Jaeger)
V34 Alert — "Tell me automatically." (Prometheus + Alertmanager)
V35 Operate — "How do I fix it?" (Runbooks + Chaos + RCA + SRE)
V35.5 Incident — "How do we formally manage it?" (ServiceNow + Incident
Management)

Cross-reference note: Portal V4's "Assignment" concept ties back to
RACI01 §4's Incident Commander role (already referenced in v35) for any
incident spanning more than one team — the portal surfaces the assignment,
it does not redefine who holds authority.

Topics Covered: Dashboard Engineering (consolidation), Portal
Architecture, ServiceNow Integration (concepts + stub simulation),
Single-Pane-of-Glass Design, Incident Workflow Automation,
Alert-to-Ticket Deduplication.

Enterprise Learning: Observability Portal Design, NOC/SOC-style unified
monitoring, Incident Management tooling integration, Executive/Operations
reporting surfaces.

Sprint Deliverable: monitoring.digistack.cloud is reachable and shows all
four portal views (V1–V4); a live P2 alert (e.g., JDBC pool exhaustion
from v35's chaos testing) is visible flowing through Portal V2 (alert
raised) → Portal V4 (ticket auto-created within seconds via ServiceNow
stub, assigned, acknowledged, investigated using inline Metrics/Logs/
Trace, root-caused, fixed, resolved, verified, RCA'd, closed) — with the
sustained-alert scenario proven to update the single existing incident
rather than spawning duplicates — demonstrating the full v31–v35 stack
operating through one interface rather than six separate tools.

---

Completion Checklist
------------------------
□ Unified portal (monitoring.digistack.cloud) live with all four views —
  Monitor, Observe, Operate, Manage Incident (v35.5)
□ At least one real alert traced end-to-end through the portal: raised
  (V2) → ticket auto-created and assigned within seconds (V4) →
  acknowledged → investigated inline → root-caused → fixed → resolved →
  verified → RCA'd → closed (v35.5)
□ ServiceNow ticket auto-populated with all required fields (Incident
  Number, Priority, Application, Server, Cluster, Alert Name, Current
  Metric, Threshold, Detected Time, Environment, Correlation ID,
  Description, Portal/Grafana/OpenSearch/Jaeger/Runbook URLs) (v35.5)
□ Deduplication proven: a sustained alert condition updates a single
  existing incident rather than creating duplicate tickets, using the
  same correlation ID discipline as V34's Alertmanager grouping (v35.5)
□ All 9 P03 applications expose working /health and metrics endpoints,
  with auth bypass for monitoring service account confirmed per
  /health Endpoint Authentication Note (v31)
□ PII masking confirmed on Portal's SOAP fetch path (P02 v16.5),
  negative test in TestCases-v31.md (v31)
□ Prometheus/Grafana operational across Linux, WAS/JVM, and Oracle 21c XE (digistack_cbs) layers (v31)

□ P02 v18's custom Operations Dashboard confirmed retired, with its data
  sources migrated into this Part's Prometheus/Grafana stack (v31)
□ Correlation ID standard documented in SetupDoc-v32.md, retrofit
  confirmed across all affected EARs before log pipeline stood up (v32)
□ Centralized logging live via Filebeat → Logstash → OpenSearch →
  OpenSearch Dashboards, covering all 9 apps + IHS + MQ + Linux + DB (v32)
□ End-to-end distributed trace of a Fund Transfer visible in Jaeger across
  Portal → CBS → MQ → Notification Service (v33)
□ SLO latency (Xms) and saturation point (N users) filled from load-test
  results in SetupDoc-v33.md; TestCases-v33.md verifies system meets SLO
  under recorded load (v33)
□ At least one SLO/SLI/Error Budget formally defined and measured from
  real data (v33)
□ Golden Signals / RED / USE applied to at least one service each (v33)
□ Synthetic monitoring service account (digistack-synthetic) created in
  WAS registry and digistack_cbs; synthetic transactions tagged and
  excluded from Business Dashboard KPIs (v34)
□ Alerting proven end-to-end (threshold → Alertmanager → email) with
  maintenance-window suppression tested (v34)
□ Business Dashboard and at least one other purpose-built dashboard (Ops,
  SLA, or NOC) live in Grafana (v34)
□ Synthetic monitoring running every 5 minutes across Login/Fund
  Transfer/Balance/ATM/Card, alerting on failure (v34)
□ At least two fully worked production runbooks exist and were exercised
  against a real simulated incident (v35)
□ All four chaos/resilience scenarios run at least once, with detection
  outcomes recorded; scenario 4 per single-VM restatement (v35)
□ Availability/SLA formalized (MTTR/MTTD/MTBF) for Fund Transfer and Login
  (v35)
□ At least one of each Production Reporting artifact generated from real
  data (v35)
□ digistack-monitoring-portal.ear deployed to dsb-monitor (co-located), context root /monitoring, deployment documented in SetupDoc-v35.5.md (v35.5)
□ ServiceNow stub (digistack-servicenow-stub) set up and documented with
  API contract in SetupDoc-v35.5.md (v35.5)
□ All six versions' TestCases-v31.md–v35.5.md signed off per Test Case
  Standards
□ VM inventory (STD §VM Hostnames / SOE01 §1a) updated with dsb-monitor,
  dsb-elk, dsb-tracing (per STD §VM Hostnames naming convention); monitoring portal server noted as WAS application deployment
  target on dsb-monitor (co-located, not a new VM)
□ Promoted Dev → UAT → Prod per Environment Promotion Standards,
  part4-release tag applied

Application State After This Part
--------------------------------------
Application code: unchanged from P03 (digistack-bank family of EARs +
Mobile/ATM Tomcat apps) — zero new banking functionality was added in this
Part.

New WAS Application (infrastructure, not counted in the 9 banking deployables)
- digistack-monitoring-portal.ear — Unified Monitoring Portal at
  monitoring.digistack.cloud, deployed to a dedicated monitoring server
  outside the banking cluster (v35.5)

New VMs
- dsb-monitor — Prometheus, Grafana, Alertmanager, exporters (v31)
- dsb-elk — Filebeat/Logstash/OpenSearch/OpenSearch Dashboards
  (v32) — per STD §VM Hostnames
- dsb-tracing — Jaeger (v33, may co-locate on dsb-monitor per SOE01 §1a
  RAM budget) — per STD §VM Hostnames

Observability Infrastructure Added
- Prometheus + Node/JMX/Oracle exporters (v31) — supersedes and
  retires P02 v18's custom PMI/JMX Operations Dashboard
- /health and /metrics endpoints on all 9 apps, with monitoring-service-
  account auth bypass (v31)
- Correlation ID standard (FT-/PT- format), propagated across Portal,
  CBS, Payment Hub, Notification Service via JMS/HTTP/SOAP headers (v32
  retrofit of P02 v15+)
- Grafana (base instance v31; purpose-built dashboards v34)
- WebSphere native: PMI, JMX, TPV (v31)
- Filebeat → Logstash → OpenSearch → OpenSearch Dashboards (v32)
- OpenTelemetry SDK + Jaeger distributed tracing (v33)
- Formal SRE methodology: Golden Signals, RED, USE, SLO/SLI/SLA, Error
  Budgets (v33); SLO values filled from SetupDoc-v33.md load-test results
- Alertmanager with tiered alert routing and maintenance windows (v34)
- Synthetic monitoring service account + tagged synthetic transactions (v34)
- Business/Executive/SLA/NOC dashboards (v34)
- Synthetic monitoring jobs (v34)
- Production runbooks, chaos/resilience testing (single-VM restatement for
  scenario 4), capacity forecasting, availability/SLA reporting (v35)
- ServiceNow stub (digistack-servicenow-stub) on dsb-monitor
  (v35.5)
- Unified Monitoring & Incident Management Portal at
  monitoring.digistack.cloud (digistack-monitoring-portal.ear), four views:
  Monitor / Observe / Operate / Manage Incident, with automatic
  alert-to-ServiceNow-stub ticket creation, assignment, deduplication, and
  full incident lifecycle tracking (v35.5)

Carried Forward to P05
---------------------------
The full observability stack (Prometheus/Grafana, OpenSearch, Jaeger,
Alertmanager) becomes the tooling used to validate disaster-recovery
drills — DR failover success/failure will be measured through the same
metrics, logs, and traces built here, not a separate ad hoc check. The
chaos-testing discipline from v35 is also the direct precedent for P05's
DR failover drills. The v35.5 Unified Portal becomes the single screen used
to observe and manage those DR drills, rather than a separate DR-specific
dashboard being built from scratch in P05. The Correlation ID standard
(v32) carries forward to P05's DR scenarios — correlation IDs must survive
a failover event and remain traceable in the DR site's log pipeline.
