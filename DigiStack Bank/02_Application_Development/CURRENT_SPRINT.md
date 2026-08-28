# Current Sprint — P01 v1: Project Setup & Enterprise Architecture

## Version Overview
**Objective:** Stand up the first deployable EAR on a standalone WebSphere ND AppServer and prove basic PostgreSQL connectivity.
**Business Scope:** Static Home page + one live DB read (`app_config` table). No transactions, no login yet.
**WebSphere Focus:** EAR/WAR structure, deployment layout, context root, virtual host, first EAR deployment via Admin Console.
**Expected Outcome:** `digistack-bank-v1.ear` deployed to WAS, reachable via context root/virtual host, Home page renders and confirms a live PostgreSQL read.
**Prerequisites:** WebSphere ND = 9.0.5.28 (target/placeholder pin per STD, to be confirmed against the actual install during this Sprint), Rocky Linux 8.x VM (`dsb-dmgr`), PostgreSQL 16 (target pin per STD §Version Pins and SOE01 §9 — confirm actual installed version during this Sprint and promote pin to CONFIRMED in STD/SOE01 once verified), SOE01 Golden Image checklist passed.

### Sprint 1
**Goal:** Provision the base VM and validate the WebSphere ND install.
**Learning Objective:** EAR/WAR structure and profile creation.
**Business Features:** None (infra only).
**WebSphere Admin:** Create standalone AppServer profile `devdsbinappserver01` with cell name `devdsbincell01` and server name `server1` (per STD v1.10 naming convention); validate against SOE01 checklist; confirm Admin Console reachable (9060/9043).
**Dependencies:** SOE01 §1/§9.
**Deliverables:** Standalone profile created.
**Acceptance Criteria:** Admin Console loads; profile status = Started.
**Enterprise Outcome:** Baseline AppServer ready.

### Sprint 2
**Goal:** Scaffold the EAR/WAR project structure.
**Learning Objective:** EAR/WAR packaging, context root, virtual host.
**Business Features:** Static Home page (skeleton).
**App Dev:** UI: Home.jsp skeleton.
**WebSphere Admin:** Define context root (`/digistack-bank`); define virtual host mapping.
**Dependencies:** Sprint 1.
**Deliverables:** EAR/WAR skeleton in Git (`feature/v1-setup`).
**Acceptance Criteria:** Project builds locally into a valid EAR.
**Enterprise Outcome:** Deployment-ready packaging structure established.

### Sprint 3
**Goal:** Configure PostgreSQL connectivity (direct JDBC — JNDI comes in v7).
**Learning Objective:** Basic JDBC connectivity.
**Business Features:** `app_config` table read.
**App Dev:** Backend: simple servlet reading one row. DB: `app_config` table (`V1__create_app_config.sql`).
**WebSphere Admin:** Confirm PostgreSQL JDBC driver placed in WAS shared library path.
**Dependencies:** Sprint 2, PostgreSQL running.
**Deliverables:** Migration script; servlet class.
**Acceptance Criteria:** Servlet returns row value in server log.
**Enterprise Outcome:** DB connectivity path proven.

### Sprint 4
**Goal:** Wire Home page to display live DB read.
**Learning Objective:** Basic logging framework setup.
**Business Features:** Home page renders `app_config` value.
**App Dev:** UI: Home.jsp displays DB value. Backend: servlet → JSP forward.
**WebSphere Admin:** Configure SystemOut/SystemErr logging levels.
**Dependencies:** Sprint 3.
**Deliverables:** Working Home.jsp.
**Acceptance Criteria:** Browser hit shows live value, not hardcoded.
**Enterprise Outcome:** First end-to-end request path proven.

### Sprint 5
**Goal:** Package and deploy `digistack-bank-v1.ear` via Admin Console.
**Learning Objective:** First EAR Deployment via Admin Console.
**WebSphere Admin:** Deploy EAR via Admin Console (manual); verify status = Started.
**Dependencies:** Sprints 2–4.
**Deliverables:** `digistack-bank-v1.ear` deployed.
**Acceptance Criteria:** App reachable via context root/virtual host.
**Enterprise Outcome:** First real WAS deployment completed.

### Sprint 6
**Goal:** Write and execute test cases for Version 1.
**Learning Objective:** Test Case discipline (TCS01/TCS02).
**WebSphere Admin:** Confirm app status for test execution.
**Deliverables:** TestCases-v1.md.
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7.
**Enterprise Outcome:** Version 1 test coverage complete.

### Sprint 7
**Goal:** Sign off Version 1.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Admin:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v1.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 1 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 1.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Admin:** Phase 1 — inject a realistic fault tied to this version's topic. Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v1.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 1 fault drill complete. Non-gating — does not block sign-off.

**Version 1 Deliverables:** `digistack-bank-v1.ear`, `V1__create_app_config.sql`, SetupDoc-v1.md, TestCases-v1.md, FaultDrill-v1.md.
**Exit Criteria (target, not yet verified):** Home + DB read functional; DB validated; Deployment successful; Smoke passed; Fault drill complete (Sprint 8, non-gating).
**Lessons Learned:** EAR/WAR/context-root/virtual-host mechanics; direct JDBC deliberately precedes JNDI (deferred to v7).
**Technical Debt:** Direct JDBC (no pooling) — resolved at Version 7.
