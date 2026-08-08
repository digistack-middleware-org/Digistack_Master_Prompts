# Phase 1 — Foundation: Versions & Sprints (Revised for Consistency)

Goal: Get the middleware platform fully operational — 3 HA clusters, IHS, MQ, PostgreSQL, Flyway — before any application touches it.

Every version follows the same 6-sprint structure (matching Phase 2):
1. Learning Session (Concept)
2. Hands-on Build/Setup
3. Administration (Console/CLI + automation scripting, WebSphere Admin where applicable)
4. Incident Simulation
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-1: Project Setup & Standards
1. Concept — Git & version control, why it matters for a multi-app project
2. Hands-on Build — repo init, first commit, branching strategy setup
3. Administration — Git workflows (branch protection, PR review), coding conventions & ADR practice
4. Incident Simulation — merge conflict / accidental force-push recovery
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-2: Infrastructure Basics
1. Concept — VM roles, networking basics, why 5 VMs
2. Hands-on Build — configure IP/hostnames across all 5 VMs
3. Administration — DNS/hosts file setup, RHEL user/permission management, firewall rules
4. Incident Simulation — hostname resolution failure between VMs
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-3: Shared Framework
1. Concept — Maven modules, why a shared framework
2. Hands-on Build — build logging utility, exception classes, base DAO
3. Administration — Maven build lifecycle, installing to local repo
4. Incident Simulation — dependency version conflict across apps
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-4: PostgreSQL 16 — Install & Configure
1. Concept — why PostgreSQL, ADR-002 recap
2. Hands-on Build — install PostgreSQL 16 on VM4
3. Administration — postgresql.conf/pg_hba.conf, users/roles/databases
4. Incident Simulation — DB connection refused / authentication failure
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-5: Flyway Basics
1. Concept — schema migration, why Flyway
2. Hands-on Build — install Flyway, project structure
3. Administration — migration naming convention, migrate/info/validate commands
4. Incident Simulation — failed migration / checksum mismatch
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-6: IBM MQ — Install & Queue Manager Basics
1. Concept — why MQ, messaging basics
2. Hands-on Build — install IBM MQ on VM5
3. Administration — create Queue Manager + local queue (MQ Explorer + runmqsc)
4. Incident Simulation — queue manager down / queue full
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-7: WebSphere ND — Concepts, Profiles, Install
1. Concept — WAS ND vs base WAS
2. Hands-on Build — install WAS ND via Installation Manager
3. WebSphere Administration — create standalone profile (Console + manageprofiles)
4. Incident Simulation — profile creation failure / port conflict
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-8: Cell, DMGR & Node Federation
1. Concept — Cell, DMGR, Node Agent
2. Hands-on Build — create DMGR profile + custom profiles on VM1/VM2
3. WebSphere Administration — federate Node1 & Node2 (Console + addNode.sh)
4. Incident Simulation — federation failure / node agent not starting
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-9: Clusters & Cluster Members
1. Concept — clusters, why members span both nodes
2. Hands-on Build — create all 3 clusters + members
3. WebSphere Administration — Save → Sync → Apply cycle (Console + wsadmin)
4. Incident Simulation — cluster member out of sync
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-10: IBM HTTP Server
1. Concept — why IHS is separate from WAS
2. Hands-on Build — install IHS on VM3
3. Administration — httpd.conf, virtual hosts
4. Incident Simulation — IHS won't start / port conflict
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-11: Plugin Generation & Propagation
1. Concept — plugin-cfg.xml, routing
2. Hands-on Build — generate plugin (Console + wsadmin)
3. WebSphere Administration — propagate plugin to IHS (manual + automated)
4. Incident Simulation — stale plugin / 404 routing failure
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-12: JDBC Providers & Datasources
1. Concept — JDBC provider vs. datasource
2. Hands-on Build — create JDBC provider for PostgreSQL
3. WebSphere Administration — create datasource + pool settings (Console + wsadmin)
4. Incident Simulation — datasource connection test failure
5. Documentation + Interview Questions
6. Hands-on Challenge
