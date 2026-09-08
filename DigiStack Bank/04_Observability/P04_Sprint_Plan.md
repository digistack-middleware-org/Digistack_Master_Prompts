# P04 — Observability: Consolidated Sprint Plan

**Part:** P04 — Enterprise Observability, SRE & Production Operations
**Status:** ⏳ Not Started
**Deployment Model:** Versions 31–35 add infrastructure VMs and tooling (no EAR changes except instrumented versions of the existing 9 P03 deployables). Version 35.5 adds one new infrastructure EAR — `digistack-monitoring-portal.ear` — to a standalone (non-federated) profile on `dsb-monitor`, NOT the banking cluster.
**Sprint Structure:** 8 sprints per version — Sprint 1–4 Build/Configure, Sprint 5 Integration & Deployment, Sprint 6 Test Cases, Sprint 7 Sign-off, Sprint 8 Fault Injection + Incident.
**Prerequisites:** P03 Completion Checkpoint satisfied (9 deployables live; CBS sole writer of `digistack_cbs`). P03.1/P03.2 recommended complete.

---

# Version 31 — Application & Infrastructure Observability Foundations

## Version Overview
**Objective:** Establish the base observability layer — every one of the 9 P03 applications emits structured logs, metrics, and a `/health` endpoint; Prometheus/Grafana monitoring is stood up across the full Linux + WAS + Oracle + Tomcat topology.
**Business Scope:** Zero new banking functionality.
**Focus:** PMI, JMX, TPV, exporters, health endpoints, PII masking at emission, monitoring auth bypass, data retention policy.
**Expected Outcome:** Prometheus scrapes Node/JMX/ exporters across the full topology; bare Grafana renders live JVM heap, session count, and cluster health; P02 v18's custom Operations Dashboard formally retired.
**Prerequisites:** P03 signed off. New VM: `dsb-monitor` (Prometheus, Grafana, Alertmanager, exporters — per STD §VM Hostnames).

### Sprint 1
**Goal:** Provision `dsb-monitor` and install Prometheus + Node Exporter.
**Learning Objective:** Prometheus scrape model (pull-based metrics collection).
**Infrastructure:** Build `dsb-monitor` per SOE01 §1a/CAP01 sizing; install Prometheus; install Node Exporter on all existing VMs (dsb-dmgr, node VMs, dsb-ihs, dsb-db/Oracle VM, Tomcat VM).
**Acceptance Criteria:** Prometheus UI shows all Node Exporter targets UP; Linux CPU/memory/disk metrics visible.
**Enterprise Outcome:** Infrastructure metrics layer live.

### Sprint 2
**Goal:** Install JMX Exporter and Oracle DB Exporter; wire WAS + DB into Prometheus.
**Learning Objective:** PMI/JMX as WebSphere's native metric source; exporter wiring.
**WebSphere Admin:** Deploy JMX Exporter agent to all cluster members + DMgr + Node Agents; enable PMI counters as needed. Install Oracle DB Exporter against `digistack_cbs` (Oracle 21c XE).
**Acceptance Criteria:** JVM heap/GC/thread pool, JDBC pool, session count metrics visible in Prometheus; Oracle metrics visible.
**Enterprise Outcome:** Middleware + DB metrics layers live.

### Sprint 3
**Goal:** Add `/health` and `/metrics` endpoints to all 9 P03 applications with monitoring auth bypass.
**Learning Objective:** Health/readiness probes; why monitoring must bypass role-based auth without exposing banking data.
**App Dev:** Add `/health` ({status: UP/DOWN} only) and `/metrics` (numeric counters/gauges only) on a sub-context excluded from each EAR's role-mapping authorization filter (Servlet filter exclusion list in web.xml); lightweight exporter for the 2 Tomcat apps.
**App Dev (PII masking):** Implement the structured log format with masking at emission — last-4-only for account/card numbers, full redaction for Aadhaar/PAN — covering the Portal's SOAP fetch path (P02 v16.5) explicitly.
**Acceptance Criteria:** All 9 apps report `/health` UP, scrapeable by a dedicated monitoring service account with no customer/teller credentials; no unmasked PII in emitted log lines.
**Enterprise Outcome:** Observability starts inside the apps.

### Sprint 4
**Goal:** Install Grafana; set explicit data retention; retire P02 v18's Operations Dashboard.
**Infrastructure:** Install Grafana on `dsb-monitor`; create a bare dashboard rendering live JVM heap, session count, cluster health. Set retention: Prometheus 30 days, OpenSearch 90 days, Jaeger 7 days (recorded, not defaulted).
**WebSphere Admin:** Confirm P02 v18's custom Operations Dashboard is decommissioned (URL/app removed; former PMI/JMX sources now flowing to Grafana).
**Acceptance Criteria:** Grafana renders live data with no manual polling; v18 retirement confirmed and documented; retention values documented.
**Enterprise Outcome:** Unified metrics dashboard replaces the bespoke one.

### Sprint 5
**Goal:** Integration pass — full-topology monitoring verification.
**Infrastructure/WebSphere Admin:** Verify every exporter target, all 9 `/health` endpoints, Grafana panels, and retention settings together as one system; redeploy instrumented EARs to the cluster as needed and confirm clean restart.
**Acceptance Criteria:** No gaps in the coverage matrix (Linux/WAS/DB/Tomcat/apps); all instrumented EARs serving normally post-redeploy.

### Sprint 6
**Goal:** Write and execute test cases for Version 31.
**Learning Objective:** Test Case discipline (TCS01/TCS02).
**Deliverables:** TestCases-v31.md — must include the negative PII test (deliberately log the raw SOAP fetch from P02 v16.5's path; confirm account numbers are masked in output).
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7; negative PII test passes.

### Sprint 7
**Goal:** Sign off Version 31.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Admin:** Capture backupConfig baseline (including `dsb-monitor` config); final smoke test.
**Deliverables:** SetupDoc-v31.md — must document: auth bypass implementation (filter exclusion lists + monitoring service account), actual masking implementation (regex/field-level), P02 v18 retirement (former URL, confirmation taken down), retention settings.
**Acceptance Criteria:** SetupDoc complete; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 31 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 31.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01) — e.g., kill a scrape target or exporter, diagnose via Prometheus/Grafana symptoms.
**Deliverables:** FaultDrill-v31.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 31 fault drill complete. Non-gating.

**Version 31 Deliverables:** `dsb-monitor` VM, Prometheus + Node/JMX/Oracle exporters + Grafana config, `/health` + `/metrics` endpoints across all 9 apps (with auth bypass), structured-logging + PII masking format, SetupDoc-v31.md, TestCases-v31.md, FaultDrill-v31.md.
**Exit Criteria (target, not yet verified):** Full-topology Prometheus coverage; Grafana live; all 9 health endpoints working; PII masking negative test passed; v18 retirement confirmed; Fault drill complete (non-gating).
**Lessons Learned:** Observability starts inside the app; pull-based metrics; health endpoints must be auth-bypassed but data-free.
**Technical Debt:** Grafana is a bare dashboard only — purpose-built dashboard engineering deferred to v34.

---

# Version 32 — Centralized Logging & Log Analytics

## Version Overview
**Objective:** Centralize logs from all 9 apps, IHS, MQ, WAS, Linux, and DB into OpenSearch; establish the Correlation ID Standard (retrofit to P02 v15+).
**Business Scope:** Zero new banking functionality (Correlation ID propagation is a code retrofit, not a feature).
**Focus:** Filebeat → Logstash → OpenSearch → OpenSearch Dashboards; HPEL, FFDC, trace logs, dumps.
**Expected Outcome:** A single OpenSearch Dashboards view searches a failed Fund Transfer by correlation ID across Portal, CBS, Payment Hub, and Notification Service without touching any VM's log files.
**Prerequisites:** P04 v31 signed off. New VM: `dsb-elk` (per STD §VM Hostnames).

### Sprint 1
**Goal:** Provision `dsb-elk`; install OpenSearch + OpenSearch Dashboards.
**Learning Objective:** Index-based log storage; 90-day retention (set explicitly, per v31's retention policy).
**Acceptance Criteria:** OpenSearch cluster (single-node) healthy; Dashboards UI reachable.

### Sprint 2
**Goal:** Install Logstash and Filebeat agents across all log sources.
**Learning Objective:** Log shipping pipeline — shipping (Filebeat) vs. processing (Logstash) vs. storage (OpenSearch).
**Infrastructure:** Filebeat on all WAS VMs (SystemOut/SystemErr/HPEL/FFDC), `dsb-ihs` (access/error logs), MQ VM, Tomcat VMs, Linux system logs, DB logs; Logstash pipelines parsing each source type.
**Acceptance Criteria:** All source types landing in OpenSearch indices with parsed fields.

### Sprint 3
**Goal:** Implement the Correlation ID Standard — format + generation.
**Learning Objective:** Correlation IDs as the join key across distributed logs.
**Definition (per P04_Observability.md):** Format `FT-{YYYYMMDD}-{5-digit-sequence}` (e.g., `FT-20260825-00192`; `PT-` prefix for Payment Hub legs from P03 v25).
**App Dev:** Portal's Fund Transfer Servlet sets the ID at request acceptance and hands it to CBS on the service call; CBS stamps it on all internal messages.
**Acceptance Criteria:** ID generated for every Fund Transfer; visible in Portal + CBS log lines.

### Sprint 4
**Goal:** Retrofit correlation ID propagation across all legs.
**Learning Objective:** Context propagation across HTTP, SOAP, and JMS transports.
**App Dev:** HTTP header (`X-Correlation-ID`) on REST legs (Portal→CBS, PaymentHub→CBS); SOAP header on Account Statement; JMS `JMSCorrelationID` from CBS → SIBus/MDB and CBS → MQ (Notification Service, Payment Hub consumers). Every touching service logs the ID in every related line.
**Acceptance Criteria:** One transfer's ID traceable across Portal → CBS → MQ → Notification Service → Payment Hub log lines end-to-end.

### Sprint 5
**Goal:** Integration pass — build the unified search view; capture thread/heap dumps through the pipeline.
**WebSphere Admin:** Manually trigger a thread dump and heap dump; ship them through the pipeline; confirm located in OpenSearch. Build/verify the cross-service correlation-ID search view.
**Acceptance Criteria:** Failed Fund Transfer searchable by correlation ID across CBS, Notification Service, Payment Hub without touching VM log files; dumps findable in OpenSearch.

### Sprint 6
**Goal:** Write and execute test cases for Version 32.
**Deliverables:** TestCases-v32.md — must include: failed-transfer cross-service search by correlation ID; dump capture/location.
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7.

### Sprint 7
**Goal:** Sign off Version 32.
**WebSphere Admin:** Capture backupConfig baseline (including `dsb-elk` config); final smoke test.
**Deliverables:** SetupDoc-v32.md — must include the "Correlation ID Retrofit" section confirming header propagation across all affected EARs (retrofit to P02 v15+), plus pipeline and index/retention config.
**Acceptance Criteria:** SetupDoc complete; retrofit section confirms propagation; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 32 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 32.
**Learning Objective:** Diagnose a live injected fault (e.g., a Fund Transfer failure) purely through the centralized log pipeline — no VM log file access.
**Deliverables:** FaultDrill-v32.md.
**Acceptance Criteria:** Fault injected, incident raised from log evidence, RCA completed via correlation-ID search, environment restored. Non-gating.

**Version 32 Deliverables:** `dsb-elk` VM, Filebeat/Logstash/OpenSearch pipeline, Correlation ID Standard + retrofit across affected EARs, SetupDoc-v32.md, TestCases-v32.md, FaultDrill-v32.md.
**Exit Criteria (target, not yet verified):** Cross-service correlation-ID search works; dumps shipped and searchable; retrofit confirmed pre-pipeline; Fault drill complete (non-gating).
**Lessons Learned:** Correlation IDs are the join key of distributed troubleshooting; centralize before you need it, not during an outage.
**Technical Debt:** Pre-P02-v15-era logs lack correlation IDs (accepted — not retrofitted beyond v15).

---

# Version 33 — APM, Distributed Tracing & SRE Methodology

## Version Overview
**Objective:** Move from metrics + logs to request-level tracing across the 9-service topology, and formally introduce SRE methodology — Golden Signals, RED, USE, SLO/SLI/SLA/Error Budgets.
**Business Scope:** Zero new banking functionality. A single Fund Transfer traced end-to-end: Portal → CBS → IBM MQ → Notification Service → Oracle 21c XE.
**Focus:** OpenTelemetry, Jaeger, JVM/thread-pool/JDBC tuning topics, JMeter load testing, load-test scale disclaimer.
**Expected Outcome:** One Fund Transfer trace visible in Jaeger with per-hop latency; an SLO with real measured values; load test identifies the first component to saturate.
**Prerequisites:** P04 v32 signed off. New VM: `dsb-tracing` (Jaeger; may co-locate on `dsb-monitor` per SOE01 §1a RAM budget).

### Sprint 1
**Goal:** Install Jaeger; provision `dsb-tracing` (or co-locate per RAM budget).
**Learning Objective:** Trace/span model; 7-day retention (set explicitly per v31 policy).
**Acceptance Criteria:** Jaeger UI reachable; retention set.

### Sprint 2
**Goal:** Instrument the Fund Transfer path with OpenTelemetry.
**App Dev:** OpenTelemetry SDK instrumentation across Portal → CBS → MQ → Notification Service; context propagated over HTTP/SOAP/JMS legs (building on v32's correlation ID).
**Acceptance Criteria:** Spans emitted from each hop; context not broken at any transport boundary.

### Sprint 3
**Goal:** Define the SRE measurement framework — Golden Signals, RED, USE.
**Learning Objective:** Applied SRE methodology, not definitions-by-rote.
**WebSphere Admin/App Dev:** Golden Signals (Latency/Traffic/Errors/Saturation) on the platform; RED applied to CBS + Payment Hub REST/SOAP endpoints; USE applied to JVM/DB/MQ resources; SLI/SLO/SLA/Error Budget defined for Fund Transfer (latency SLO per template — X and N are TBDs until Sprint 4's load test; availability SLO fixed at 99.9% monthly).
**Acceptance Criteria:** RED/USE instrumentation visible in Prometheus; SLO template recorded with TBDs marked.

### Sprint 4
**Goal:** JMeter load test; fill in SLO values from real results.
**Learning Objective:** Load testing validates the *method* (saturation detection works), not production capacity — record actual achieved concurrency honestly per the Load-Test Scale Disclaimer.
**WebSphere Admin:** Run JMeter at increasing concurrency; observe RED/USE data; identify first component to saturate (distinguish Web Container exhaustion from MDB listener thread starvation — per the named thread pools: Web Container, ORB/EJB, Default, MDB listener port). Fill Xms (p99 latency at steady state) and N (saturation point) into the SLO.
**Acceptance Criteria:** SLO values filled from measured results in SetupDoc-v33.md; saturating component identified with RED/USE evidence.

### Sprint 5
**Goal:** Integration pass — end-to-end trace verification.
**WebSphere Admin:** Verify a single Fund Transfer trace renders in Jaeger across all four hops with per-hop latency; confirm trace + metrics + logs all reference the same correlation ID.
**Acceptance Criteria:** Trace visible end-to-end; error budget calculable from real traced data.

### Sprint 6
**Goal:** Write and execute test cases for Version 33.
**Deliverables:** TestCases-v33.md — must include a test reading SLO values from SetupDoc-v33.md and verifying the running system meets them under the recorded load.
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7; SLO verification test passes.

### Sprint 7
**Goal:** Sign off Version 33.
**WebSphere Admin:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v33.md — SLO values (Xms, N) filled from load-test results; achieved concurrency recorded honestly; saturation analysis.
**Acceptance Criteria:** SetupDoc complete; SLO TBDs resolved; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 33 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 33.
**Learning Objective:** Use Jaeger traces to diagnose an injected latency/failure in one hop of the Fund Transfer path.
**Deliverables:** FaultDrill-v33.md.
**Acceptance Criteria:** Fault injected, located via trace, RCA completed, environment restored. Non-gating.

**Version 33 Deliverables:** `dsb-tracing` VM, OpenTelemetry instrumentation + Jaeger traces, RED/USE instrumentation, SLO/Error Budget definition with measured values, JMeter load-test artifacts, SetupDoc-v33.md, TestCases-v33.md, FaultDrill-v33.md.
**Exit Criteria (target, not yet verified):** End-to-end trace visible; SLO filled from real data and verified under load; first saturating component identified; Fault drill complete (non-gating).
**Lessons Learned:** Traces answer "where did it slow/fail"; SLOs must come from measured data, not aspirations.
**Technical Debt:** Commercial APM tools (Instana/Dynatrace/AppDynamics/Datadog) covered as concepts only — accepted scope.

---

# Version 34 — Alerting, Dashboard Engineering & Synthetic Monitoring

## Version Overview
**Objective:** Design (not just install) alerting and dashboards; add business KPIs and 5-minute synthetic monitoring using a dedicated synthetic service account.
**Business Scope:** Zero new banking functionality. Synthetic transactions run every 5 minutes against existing endpoints.
**Focus:** Alertmanager routing/thresholds/dedup/maintenance windows; purpose-built Grafana dashboards; tagged synthetic transactions.
**Expected Outcome:** Induced failure fires a P2 alert to email within threshold; synthetic checks catch a broken endpoint within 5 minutes, before any real customer.
**Prerequisites:** P04 v33 signed off.

### Sprint 1
**Goal:** Create the synthetic monitoring service account and test data.
**Prerequisite setup (per the Synthetic Monitoring Service Account note):** Create `digistack-synthetic` (role: Customer) in the WAS registry and `digistack_cbs` (real synthetic account + balance); register `digistack-synthetic-dest` test Beneficiary so Fund Transfer completes without touching real funds.
**Acceptance Criteria:** Synthetic login/transfer works end-to-end under the dedicated account.
**Enterprise Outcome:** Synthetic monitoring prerequisite satisfied.

### Sprint 2
**Goal:** Engineer Alertmanager routing rules.
**Learning Objective:** Alert design — priority tiers (P1–P4), thresholds, escalation matrix, deduplication, maintenance windows, alert-fatigue tradeoff.
**Infrastructure:** Build Alertmanager rules on dsb-monitor; apply at least one real alert (JDBC pool exhaustion at 90%) end-to-end from threshold to email.
**Acceptance Criteria:** Alert fires within threshold window; deduplication and maintenance-window suppression configured and demonstrated.

### Sprint 3
**Goal:** Build purpose-built dashboards.
**Learning Objective:** Dashboard engineering — designed for an audience, not default exports.
**Infrastructure:** Operational dashboard (JVM/cluster/DB health) + Business dashboard (Fund Transfer success rate, daily active users, logins, transactions/min) — synthetic transactions excluded from Business KPI counts via the `synthetic=true` tag.
**Acceptance Criteria:** Both dashboards live; synthetic exclusion verified in KPI counts; no synthetic alert emails from Notification Service.

### Sprint 4
**Goal:** Deploy synthetic monitoring jobs.
**Infrastructure:** Scheduled synthetic Login, Fund Transfer, Balance Check, ATM Request, Card Payment, and Health Endpoint checks every 5 minutes; failures alert Alertmanager; synthetic results included in availability/SLO tracking, excluded from business KPIs.
**Acceptance Criteria:** All six checks running on schedule; failure alerts observed on a deliberate break.

### Sprint 5
**Goal:** Integration pass — full alert-to-notification verification.
**WebSphere Admin:** Kill a cluster member deliberately; confirm P2 alert fires through Alertmanager to email within the defined threshold window — no duplicates, no false positive during a defined maintenance window; deliberately break one endpoint; confirm a synthetic check catches it within 5 minutes.
**Acceptance Criteria:** Both scenarios pass as specified.

### Sprint 6
**Goal:** Write and execute test cases for Version 34.
**Deliverables:** TestCases-v34.md — must include: alert threshold/dedup/suppression tests; synthetic catch-before-customers test; synthetic KPI exclusion test.
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7.

### Sprint 7
**Goal:** Sign off Version 34.
**WebSphere Admin:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v34.md — must include synthetic service account setup as a documented prerequisite step; alert rule and dashboard design rationale.
**Acceptance Criteria:** SetupDoc complete; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 34 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 34.
**Learning Objective:** The whole version is fault-driven — inject a non-covered failure mode and evaluate whether alerting catches it (feeds v35's runbook/threshold iteration).
**Deliverables:** FaultDrill-v34.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored. Non-gating.

**Version 34 Deliverables:** Synthetic service account + tagged transactions, Alertmanager rules, Operational + Business dashboards, synthetic monitoring jobs, SetupDoc-v34.md, TestCases-v34.md, FaultDrill-v34.md.
**Exit Criteria (target, not yet verified):** P2 alert end-to-end with dedup + suppression; synthetic checks running every 5 min; Business Dashboard live with synthetic exclusion; Fault drill complete (non-gating).
**Lessons Learned:** Alert design is a tradeoff against fatigue; synthetic monitoring proves health with zero real traffic.
**Technical Debt:** NOC/SLA/Executive dashboards partially deferred to v35's reporting outputs — Ops + Business dashboards are the required minimum here.

---

# Version 35 — Production Operations, Capacity Planning & Reporting

## Version Overview
**Objective:** Simulate real production support end-to-end — runbooks, chaos/resilience testing, capacity forecasting, availability/SLA formalization, and production reporting.
**Business Scope:** Zero new banking functionality.
**Focus:** Incident lifecycle, Runbooks (hung thread + JDBC pool exhaustion), 4 chaos scenarios, capacity/SLA reporting from real v31–v34 data, javacore/hung-thread diagnostics.
**Expected Outcome:** A simulated hung-thread incident worked start-to-finish via runbook; all four chaos scenarios run with detection outcomes recorded; Monthly Capacity and SLA Reports generated from real data.
**Prerequisites:** P04 v34 signed off.

### Sprint 1
**Goal:** Write the production runbooks.
**Learning Objective:** Runbook shape: Alert → Symptoms → Investigation Commands → Diagnosis → Resolution → Verification → Closure.
**Deliverables:** Runbook — hung-thread incident (fully worked); Runbook — JDBC connection-pool exhaustion (fully worked).
**Acceptance Criteria:** Both runbooks complete with real commands against this environment.

### Sprint 2
**Goal:** Chaos Scenario 1 & 2 — cluster member kill and MQ Queue Manager kill.
**Chaos discipline:** Run once each; record detection time (feeds MTTD) and caught-vs-missed outcome.
- **Scenario 1:** Kill a WAS cluster member mid-transaction → confirm failover (P01 v5 session replication) visible in Grafana within seconds and no v33 SLO breach.
- **Scenario 2:** Kill the MQ Queue Manager → confirm a Fund Transfer's external leg (P02 v19) lands in the DLQ and fires a v34 alert — not a silent failure.
**Acceptance Criteria:** Both scenarios executed; detection times and outcomes recorded.

### Sprint 3
**Goal:** Chaos Scenario 3 & 4 — JDBC pool saturation and monitoring-process resilience.
- **Scenario 3:** Saturate the JDBC pool deliberately → confirm the v34 alert fires before manual notice; respond via the Sprint 1 pool-exhaustion runbook.
- **Scenario 4 (single-VM restatement):** Kill the Prometheus *process* on dsb-monitor (not the VM) → confirm "monitoring system degraded" alert fires and Grafana shows data gaps; restart and confirm re-scrape/back-fill within retention. Repeat for the OpenSearch process on dsb-elk.
**Acceptance Criteria:** Both scenarios executed per the restatement; runbook-driven response exercised in Scenario 3.

### Sprint 4
**Goal:** Work a simulated hung-thread incident end-to-end via its runbook.
**Learning Objective:** Full incident lifecycle — detected via v34 alerting, diagnosed via v32 logs + v31 JMX data, resolved, closed with documented RCA. Deeper WAS diagnostics: javacore analysis, PMI counter interpretation, session/JDBC leak investigation.
**Acceptance Criteria:** Incident detected → diagnosed → resolved → RCA'd → closed, entirely runbook-driven.

### Sprint 5
**Goal:** Capacity analysis and availability/SLA formalization from real data.
**Learning Objective:** Capacity planning from collected metrics, not estimates.
**Infrastructure:** Trend analysis, peak load analysis, JVM sizing/DB growth projection from v31–v34 metrics; availability tracking, MTTR/MTTD/MTBF from the incident + chaos runs; finalize SLA for Fund Transfer + Login against v33's SLO/error budget.
**Acceptance Criteria:** Capacity forecast and availability/MTTR/MTTD/MTBF figures computed from real collected data.

### Sprint 6
**Goal:** Generate production reports; write and execute test cases.
**Deliverables:** One of each, generated once from real data: Daily Health Report, Weekly Operations Report, Monthly Capacity Report (with JVM heap growth forecast), Incident Trend Report, Availability Report, SLA Report (Fund Transfer actual availability vs. v33 SLO), Executive Summary. TestCases-v35.md.
**Acceptance Criteria:** All reports generated from real data (no placeholder templates); all Critical/High test cases pass per TCS01 §2.7.

### Sprint 7
**Goal:** Sign off Version 35.
**WebSphere Admin:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v35.md — runbooks, chaos outcomes, capacity/SLA methodology, RCA documents.
**Acceptance Criteria:** SetupDoc complete; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 35 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 35.
**Learning Objective:** The whole version is fault-driven — inject one failure mode NOT covered by the existing runbooks, work it without a runbook, then write the runbook afterward (the "new incident teaches the next one" pattern).
**Deliverables:** FaultDrill-v35.md + the new runbook that comes out of it.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, new runbook authored, environment restored. Non-gating.

**Version 35 Deliverables:** Two runbooks (+ one from Sprint 8), chaos execution records (4 scenarios, detection times, outcomes), capacity forecast, SLA/availability reports, seven production reports, RCA documents, SetupDoc-v35.md, TestCases-v35.md, FaultDrill-v35.md.
**Exit Criteria (target, not yet verified):** Runbook-driven incident worked end-to-end; all 4 chaos scenarios run with outcomes recorded; capacity + SLA reports from real data; Fault drill complete (non-gating).
**Lessons Learned:** Chaos testing proves the observability stack works — detection time is a first-class metric.
**Technical Debt:** ServiceNow listed as concepts-only — made practical in v35.5 via stub.

---

# Version 35.5 — Enterprise Incident Management & Unified Monitoring Portal

## Version Overview
**Objective:** Capstone integration — `monitoring.digistack.cloud` single-pane-of-glass portal (V1 Monitor / V2 Observe / V3 Operate / V4 Manage Incident) plus ServiceNow-stub automated incident lifecycle: Alert → Auto-ticket → Assignment → Investigation → Resolution → RCA → Closure.
**Business Scope:** Zero new banking functionality. New infrastructure EAR only.
**Focus:** Portal architecture, ServiceNow stub integration, alert-to-ticket deduplication, incident lifecycle automation.
**Expected Outcome:** A live P2 alert flows end-to-end through one interface — raised in V2, auto-ticketed and assigned in V4 within seconds, investigated inline, root-caused, fixed, resolved, verified, RCA'd, closed — with a sustained alert updating one incident, not spawning duplicates.
**Prerequisites:** P04 v35 signed off. Note: portal deploys to a standalone (non-federated) WAS profile on `dsb-monitor` — deliberately outside the banking cell so it survives a full cell outage.

### Sprint 1
**Goal:** Create the standalone WAS profile on dsb-monitor; build the ServiceNow stub.
**Learning Objective:** Managing a standalone profile alongside an ND cell.
**WebSphere Admin:** Create standalone (non-federated) profile on `dsb-monitor` — NOT a Node Agent/DMgr member; admin console on restricted local port. Build `digistack-servicenow-stub` (accepts ServiceNow REST API calls, returns realistic INC numbers; API contract documented).
**Acceptance Criteria:** Standalone profile started; stub responds to API calls with INC-number responses.
**Enterprise Outcome:** 10th deployable's home is ready (excluded from the 9-app banking count — infrastructure).

### Sprint 2
**Goal:** Build Portal V1 (Monitor) and V2 (Observe).
**App Dev:** `digistack-monitoring-portal.ear` — V1: WAS Cell/Cluster/DB tiles, Node Agent status, per-app status table (all 9), JVM health, JDBC gauges, events feed. V2: active alerts by priority (P1–P3), health bars, business health, observability-service status strip. Links into Prometheus/Grafana/OpenSearch/Jaeger/Alertmanager — composes, never reimplements.
**Acceptance Criteria:** V1/V2 render live data from all underlying tools.

### Sprint 3
**Goal:** Build Portal V3 (Operate) and V4 (Manage Incident).
**App Dev:** V3: production overview, open incidents, MTTD/MTTR/MTBF KPIs, SLO/SLA panel, capacity trends, shortcuts to v35's runbooks/reports/RACAs. V4: incident counts by priority, incident cards with inline Metrics/Logs/Trace/Runbook/ServiceNow links, lifecycle strip (ALERT → TICKET → ASSIGNED → ACKNOWLEDGED → INVESTIGATING → ROOT CAUSE → FIX → RESOLVED → VERIFICATION → RCA → CLOSED).
**Acceptance Criteria:** V3 surfaces real v35 data; V4 lifecycle states functional.

### Sprint 4
**Goal:** Wire Alertmanager → stub auto-ticketing; implement ticket-level deduplication.
**Learning Objective:** Alert-to-ticket automation; dedup at the ticketing layer (complements — does not duplicate — v34's Alertmanager dedup): sustained alert updates the single existing INC; alert clear resolves it. Correlation ID shared across both layers.
**Infrastructure:** Alertmanager webhook → stub; auto-populated ticket fields (Priority, Application, Server, Cluster, Alert Name, Current Metric, Threshold, Detected Time, Correlation ID, Portal/Grafana/OpenSearch/Jaeger/Runbook URLs); team assignment (e.g., WAS-L3); ties to RACI01 §4's Incident Commander for multi-team incidents.
**Acceptance Criteria:** Sustained JDBC-pool condition creates exactly ONE INC, updated on continuation, RESOLVED on clear.

### Sprint 5
**Goal:** Deploy the portal; verify single-pane-of-glass operation.
**WebSphere Admin:** Deploy `digistack-monitoring-portal.ear` to the standalone profile; context root `/monitoring`; confirm reachable independently of the banking cluster (temporarily stop the cluster and confirm the portal still serves — surviving a full cell outage).
**Acceptance Criteria:** `monitoring.digistack.cloud` reachable; all four views live; portal survives cluster outage.

### Sprint 6
**Goal:** Write and execute test cases for Version 35.5.
**Deliverables:** TestCases-v35.5.md — must include: full alert→ticket→closure lifecycle test; dedup test (sustained alert = one INC); ticket auto-populated fields test; portal-survives-cluster-outage test.
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7.

### Sprint 7
**Goal:** Sign off Version 35.5.
**WebSphere Admin:** Capture backupConfig baseline (including the standalone profile + stub); final smoke test.
**Deliverables:** SetupDoc-v35.5.md — deployment target (dsb-monitor co-located, standalone profile, context root `/monitoring`, exclusion from the 9-app count) and stub API contract documented for future replacement with a real ServiceNow developer instance.
**Acceptance Criteria:** SetupDoc complete; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 35.5 signed off — P04 complete.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 35.5.
**Learning Objective:** Inject a real failure and drive the ENTIRE v31–v35.5 stack through the portal alone: detection (v34 alert) → portal V2 visibility → auto-ticket (V4) → runbook-driven resolution (v35) → RCA → stub closure.
**Deliverables:** FaultDrill-v35.5.md.
**Acceptance Criteria:** Full lifecycle completed through the single interface; dedup held; environment restored. Non-gating.

**Version 35.5 Deliverables:** `digistack-monitoring-portal.ear` (V1–V4), `digistack-servicenow-stub`, Alertmanager→stub integration, deduplication logic, SetupDoc-v35.5.md, TestCases-v35.5.md, FaultDrill-v35.5.md.
**Exit Criteria (target, not yet verified):** Portal live with all four views; one real alert traced end-to-end to closure; dedup proven; portal survives cluster outage; Fault drill complete (non-gating).
**Lessons Learned:** A portal composes specialized tools — it never replaces them; ticket dedup is what stops alert storms becoming ticket storms.
**Technical Debt:** ServiceNow is a stub, not a live instance — API contract documented so a real ServiceNow developer instance can be swapped in with identical webhook config.

---

# P04 — Observability: Planned Application State (once complete)

**Banking Applications:** Unchanged from P03 (9 deployables) — instrumented only, zero new banking functionality.

**New WAS Application (infrastructure, not in the 9-app count):** `digistack-monitoring-portal.ear` — standalone profile on `dsb-monitor`, context root `/monitoring`.

**New VMs:** `dsb-monitor` (v31), `dsb-elk` (v32), `dsb-tracing` (v33, may co-locate on dsb-monitor).

## Technical Debt Plan (introduced and resolved within P04, per schedule)

| Debt Introduced At | Planned Resolution At | Item |
|---|---|---|
| v31 | v34 | Bare Grafana → purpose-built dashboards |
| v35 (concepts) | v35.5 | ServiceNow concepts → stub-based practical integration |
| v35.5 (stub) | Future | Stub → real ServiceNow developer instance (API contract documented) |

## Planned Hand-off to P05
The full observability stack (Prometheus/Grafana, OpenSearch, Jaeger, Alertmanager) becomes the tooling that validates P05's DR drills; v35's chaos discipline is the direct precedent for DR failover drills; the v35.5 Unified Portal is the single screen used to observe/manage them; the Correlation ID standard must survive failover and remain traceable in the DR site's log pipeline.

---

*This is the consolidated P04_Sprint_Plan.md — the planning document for P04, not a completion record. No versions have been built or signed off yet. Actual progress is tracked in Progress_Log.md.*
