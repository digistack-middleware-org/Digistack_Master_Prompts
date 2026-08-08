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

Exports:
Versions 31-35
Application & Infrastructure Observability Foundations
Centralized Logging & Log Analytics
APM, Distributed Tracing & SRE Methodology
Alerting, Dashboard Engineering & Synthetic Monitoring
Production Operations, Capacity Planning & Reporting

Used By:
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
2 Tomcat apps) live, CBS is sole writer of digistack_cbs.

Deployment Model
------------------
No new banking features are added anywhere in this Part. Every version
instruments, monitors, or operationally hardens the 9 applications already
built. This Part is 100% infrastructure/tooling — the strictest "zero new
banking functionality" run of the whole roadmap.

---

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
PostgreSQL

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
legacy Portal database after v23, the old WAS platform after P07 v48).

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

Enterprise Tools: Prometheus, Node Exporter, JMX Exporter, PostgreSQL
Exporter, Grafana (dashboard shell only — dashboard design is Version 34).

WebSphere Native Monitoring: PMI, JMX, Tivoli Performance Viewer (TPV).

Monitoring Coverage
- Linux: CPU, memory, disk, filesystem, network, process monitoring
- WebSphere: JVM heap, GC, thread pools, JDBC connection pools, session
  count, cluster health, Node Agent health, DMgr health
- Application: Internet Banking health, CBS health, ATM Service health,
  Card Service health, Payment Hub health, Database health

Request Flow

Linux Servers ──► Node Exporter ─┐
WebSphere JVM ──► JMX Exporter ──┼──► Prometheus ──► Grafana ──► Ops Dashboard
PostgreSQL ──► PG Exporter ───┘
▲
IBM WebSphere ND Cell (DMgr / Node Agent / Cluster Members)
+ Mobile/ATM Tomcat instances (lightweight exporter)


VM Setup Note: New VM introduced: digistack-monitoring-01 (Prometheus,
Grafana, Alertmanager, exporters).

Topics Covered: PMI, JMX, Health Policies, JVM Monitoring, Cluster
Monitoring, JDBC Pool Monitoring, Session Monitoring.

Enterprise Learning: Capacity Planning (intro), Health Monitoring, SLA
Monitoring (intro — formalized in v35), Middleware Operations.

Sprint Deliverable: Every one of the 9 P03 applications exposes a working
/health endpoint and metrics endpoint; Prometheus scrapes Node/JMX/
PostgreSQL exporters across the full WAS + Tomcat topology; a bare Grafana
instance renders live JVM heap, session count, and cluster health with no
manual polling; P02 v18's custom Operations Dashboard is confirmed
retired, with its former data sources now flowing into this Grafana
instance instead.

---

Version 32 — Centralized Logging & Log Analytics
--------------------------------------------------------
Objective: Centralize logs across all 9 applications, IHS, MQ, and Linux
for troubleshooting and production support — and give Version 31's
app-level structured logs somewhere to land.

Minimum App Needed: Zero new banking functionality.

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


VM Setup Note: New VM introduced: digistack-elk-01 (Filebeat targets ship
to Logstash → OpenSearch → OpenSearch Dashboards).

Topics Covered: HPEL, FFDC, Trace Specification, Log Rotation, Heap Dumps,
Thread Dumps, GC Logs.

Enterprise Learning: Log Analysis, Root Cause Analysis (RCA) —
introductory pass, deepened in v35; Production Troubleshooting, Audit
Investigation.

Sprint Deliverable: A single OpenSearch Dashboards view lets you search a
failed Fund Transfer across CBS, Payment Hub, and Notification Service
logs by correlation ID, without touching any individual VM's log files
directly; a manually triggered thread dump and heap dump are captured,
shipped through the pipeline, and located in OpenSearch.

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

Request Flow

Customer Request → IHS → WAS Cluster → {Banking Portal, CBS, IBM MQ} → PostgreSQL
│
OpenTelemetry SDK
│
Jaeger (distributed tracing)
│
JMX Exporter → Prometheus → Grafana (perf metrics)


VM Setup Note: New VM introduced: digistack-tracing-01 (Jaeger backend/UI
— may co-locate on digistack-monitoring-01 if capacity allows).

Topics Covered: JVM Tuning, Thread Pool Tuning, JDBC Pool Tuning, Dynamic
Cache, Performance Advisor, Garbage Collection Analysis.

Enterprise Learning: Performance Tuning, Capacity Planning, Application
Performance Monitoring, JVM Optimization, Request Tracing, SLO/SLA/SLI,
Error Budgets.

Sprint Deliverable: A single Fund Transfer trace is visible end-to-end in
Jaeger across Portal → CBS → MQ → Notification Service, with per-hop
latency; an SLO ("99% of Fund Transfers complete under Xms, 99.9% monthly
availability") is defined and its current error budget is calculated from
real traced data; a load test proves the SLO holds under N concurrent
users and identifies the first component to saturate (RED/USE data
pinpoints it).

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
checks every 5 minutes, alerting Alertmanager on failure — proves the
platform is monitored even with zero real user traffic.

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
success rate and daily active users; a synthetic Fund Transfer check
catches a deliberately broken endpoint within 5 minutes, before any real
customer would.

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
- Kill one OpenSearch/Prometheus/Jaeger node itself → confirm the
  observability platform's own resilience (monitoring the monitors), not
  just the banking app's

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
with detection outcomes recorded; a Monthly Capacity Report is generated
from real Version 31–34 metrics and includes a JVM heap growth forecast;
an SLA Report for Fund Transfer shows actual measured availability against
the Version 33 SLO, with MTTR/MTTD calculated from the incident and chaos
runs above.

---

Completion Checklist
------------------------
□ All 9 P03 applications expose working /health and metrics endpoints
  (v31)
□ Prometheus/Grafana operational across Linux, WAS/JVM, and PostgreSQL
  layers (v31)
□ P02 v18's custom Operations Dashboard confirmed retired, with its data
  sources migrated into this Part's Prometheus/Grafana stack (v31)
□ Centralized logging live via Filebeat → Logstash → OpenSearch →
  OpenSearch Dashboards, covering all 9 apps + IHS + MQ + Linux + DB (v32)
□ End-to-end distributed trace of a Fund Transfer visible in Jaeger across
  Portal → CBS → MQ → Notification Service (v33)
□ At least one SLO/SLI/Error Budget formally defined and measured from
  real data (v33)
□ Golden Signals / RED / USE applied to at least one service each (v33)
□ Alerting proven end-to-end (threshold → Alertmanager → email) with
  maintenance-window suppression tested (v34)
□ Business Dashboard and at least one other purpose-built dashboard (Ops,
  SLA, or NOC) live in Grafana (v34)
□ Synthetic monitoring running every 5 minutes across Login/Fund
  Transfer/Balance/ATM/Card, alerting on failure (v34)
□ At least two fully worked production runbooks exist and were exercised
  against a real simulated incident (v35)
□ All four chaos/resilience scenarios run at least once, with detection
  outcomes recorded (v35)
□ Availability/SLA formalized (MTTR/MTTD/MTBF) for Fund Transfer and Login
  (v35)
□ At least one of each Production Reporting artifact generated from real
  data (v35)
□ All five versions' TestCases-v31.md–v35.md signed off per Test Case
  Standards
□ VM inventory (doc 01) updated with digistack-monitoring-01,
  digistack-elk-01, digistack-tracing-01
□ Promoted Dev → UAT → Prod per Environment Promotion Standards,
  part4-release tag applied

Application State After This Part
--------------------------------------
Application code: unchanged from P03 (digistack-bank family of EARs +
Mobile/ATM Tomcat apps) — zero new banking functionality was added in this
Part.

New VMs
- digistack-monitoring-01 — Prometheus, Grafana, Alertmanager, exporters
  (v31)
- digistack-elk-01 — Filebeat/Logstash/OpenSearch/OpenSearch Dashboards
  (v32)
- digistack-tracing-01 — Jaeger (v33, may co-locate with monitoring VM)

Observability Infrastructure Added
- Prometheus + Node/JMX/PostgreSQL exporters (v31) — supersedes and
  retires P02 v18's custom PMI/JMX Operations Dashboard
- Grafana (base instance v31; purpose-built dashboards v34)
- WebSphere native: PMI, JMX, TPV (v31)
- Filebeat → Logstash → OpenSearch → OpenSearch Dashboards (v32)
- OpenTelemetry SDK + Jaeger distributed tracing (v33)
- Formal SRE methodology: Golden Signals, RED, USE, SLO/SLI/SLA, Error
  Budgets (v33)
- Alertmanager with tiered alert routing and maintenance windows (v34)
- Business/Executive/SLA/NOC dashboards (v34)
- Synthetic monitoring jobs (v34)
- Production runbooks, chaos/resilience testing, capacity forecasting,
  availability/SLA reporting (v35)

Carried Forward to P05
---------------------------
The full observability stack (Prometheus/Grafana, OpenSearch, Jaeger,
Alertmanager) becomes the tooling used to validate disaster-recovery
drills — DR failover success/failure will be measured through the same
metrics, logs, and traces built here, not a separate ad hoc check. The
chaos-testing discipline from v35 is also the direct precedent for P05's
DR failover drills.