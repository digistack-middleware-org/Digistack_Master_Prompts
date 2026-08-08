# Phase 6 — Enterprise Operations: Versions & Sprints

Goal: DR planning + drills, ITIL change management, capacity planning, monitoring, final runbooks + portfolio capstone.

Every version follows the same 6-sprint structure:
1. Learning Session (Concept)
2. Hands-on Build
3. Administration (Console + wsadmin)
4. Incident Simulation
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-1: Disaster Recovery Planning
1. Concept — RTO/RPO, why DR planning matters
2. Hands-on Build — document the current SPOF audit (VM1 DMGR + cluster-member risk from ADR-003)
3. Administration — define DR strategy config (backup targets, replication points)
4. Incident Simulation — simulate total VM1 failure, assess impact
5. Documentation + Interview Questions
6. Hands-on Challenge — write a full DR plan document for the platform

## Version-2: DR Drills
1. Concept — DR drill methodology, drill types
2. Hands-on Build — prepare drill runbook drafts
3. Administration — execute a DMGR recovery drill
4. Incident Simulation — full node/DB/MQ/cluster recovery drill
5. Documentation + Interview Questions
6. Hands-on Challenge — run an unannounced recovery drill end-to-end

## Version-3: Change Management
1. Concept — ITIL change management (RFC, CAB, change types)
2. Hands-on Build — draft a sample RFC for a production change
3. Administration — implement a change through a formal approval gate
4. Incident Simulation — an unauthorized/emergency change causes an incident
5. Documentation + Interview Questions
6. Hands-on Challenge — run a full change through RFC → CAB → implementation → closure

## Version-4: Capacity Planning
1. Concept — capacity planning fundamentals, validating ADR-003's VM sizing
2. Hands-on Build — collect current utilization baselines across VMs/clusters
3. Administration — PMI/TPV metrics collection & analysis
4. Incident Simulation — capacity exhaustion during peak load (billing run)
5. Documentation + Interview Questions
6. Hands-on Challenge — produce a capacity plan recommendation

## Version-5: Health Checks & Synthetic Monitoring — Extended to Platform Scale
1. Concept — recap Phase 2's app-level health checks; extend to synthetic transaction monitoring across the whole platform
2. Hands-on Build — design synthetic transactions simulating real user flows (order → billing → payment)
3. Administration — configure scheduled synthetic checks + alerting
4. Incident Simulation — a synthetic monitor catches a silent failure before customers do
5. Documentation + Interview Questions
6. Hands-on Challenge — build an end-to-end synthetic monitoring suite

## Version-6: Monitoring Stack — Prometheus, Grafana, Node Exporter
1. Concept — why Prometheus/Grafana per ADR-001, deployed on existing VMs
2. Hands-on Build — install Node Exporter + Prometheus on existing VMs
3. Administration — configure Grafana dashboards for WAS/MQ/DB metrics
4. Incident Simulation — a dashboard anomaly is traced back to root cause
5. Documentation + Interview Questions
6. Hands-on Challenge — build a full monitoring dashboard for one cluster

## Version-7: Production Runbooks & Portfolio Package (Capstone)
1. Concept — what makes a good runbook; portfolio structure for interviews
2. Hands-on Build — compile runbooks for the top 5 incident types across the whole project
3. Administration — final validation walkthrough of the full platform
4. Incident Simulation — a multi-failure scenario drawing on Phases 1–5
5. Documentation + Interview Questions — compile the master documentation set
6. Hands-on Challenge — present the complete portfolio (capstone)
