# Phase 3 — WebSphere Administration & Platform Operations: Versions & Sprints

Goal: Platform-wide administrative depth on top of the 11 apps built in Phase 2 — deployment automation, performance tuning, security hardening, wsadmin scripting, logging, testing, and packaging/handoff.

Every version follows the same 6-sprint structure:
1. Learning Session (Concept)
2. Application Development
3. WebSphere Administration (Console + wsadmin)
4. Production Incident Simulation
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-1: Deployment Pipeline & Rollback
1. Concept — EAR versioning, rolling deployment, zero-downtime deployment, rollback deployment, deployment checklist
2. Application Development — set up EAR versioning scheme for apps built so far
3. WAS Admin — perform a rolling deployment across cluster members
4. Incident Simulation — bad deployment mid-rollout requiring rollback
5. Documentation + Interview Questions — deployment checklist doc
6. Hands-on Challenge — execute a zero-downtime deploy + rollback drill

## Version-2: JVM & WebSphere Performance Tuning
1. Concept — JVM heap/GC, thread pools, web container tuning, ORB tuning, connection pool tuning, session manager tuning, Dynamic Cache, HTTP session tuning
2. Application Development — identify tuning targets per app (I/O-heavy vs. CPU-heavy)
3. WAS Admin — configure JVM args, thread pools, web container, ORB, connection pools, session manager, Dynamic Cache
4. Incident Simulation — OutOfMemoryError + thread pool exhaustion under load
5. Documentation + Interview Questions
6. Hands-on Challenge — tune a cluster member under simulated load

## Version-3: WebSphere Security & SSL, Cell-Wide
1. Concept — cell-wide security domains, SSL; administrative security, application security, LDAP integration, LTPA key rotation, JAAS authentication, security auditing
2. Application Development — integrate app-level security constraints
3. WAS Admin — configure SSL certs/keystores, admin security, LDAP registry, LTPA key generation/rotation
4. Incident Simulation — SSL handshake failure + LDAP auth outage
5. Documentation + Interview Questions
6. Hands-on Challenge — rotate LTPA keys without downtime

## Version-4: wsadmin & Jython, Deep Dive
1. Concept — wsadmin architecture (AdminConfig, AdminControl, AdminApp, AdminTask)
2. Application Development — build reusable Jython automation library
3. WAS Admin — automate deployment tasks via scripts
4. Incident Simulation — script failure troubleshooting
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-5: Centralized Logging & Exception Handling
1. Concept — logging strategy across all apps
2. Application Development — integrate centralized logging framework
3. WAS Admin — configure log rotation, SystemOut/SystemErr
4. Incident Simulation — missing logs during an incident
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-6: Testing & Code Review
1. Concept — testing strategy (unit/integration) for the fleet
2. Application Development — build test cases for key apps
3. WAS Admin — set up a deployment testing environment
4. Incident Simulation — bug reaching production due to a missed test
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-7: Maven Build, EAR Packaging & Developer Handoff
1. Concept — EAR packaging, multi-module builds
2. Application Development — package all 11 apps as EARs
3. WAS Admin — deploy all EARs
4. Incident Simulation — deployment failure due to a packaging issue
5. Documentation + Interview Questions — reconcile all 11 handoff packages
6. Hands-on Challenge (Phase 3 capstone)
