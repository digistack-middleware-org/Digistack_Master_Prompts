# Current Sprint — P01 Version 9 — Session Management

## Version Overview
**Objective:** Harden session behavior across the cluster — sticky sessions, persistence/failover, memory-to-memory replication tuning.
**Business Scope:** Zero new functionality. Session Timeout (auto-logout after N idle minutes).
**WebSphere Focus:** HTTP Sessions, Sticky Sessions, Session Persistence, Session Failover, Memory-to-Memory Replication.
**Expected Outcome:** Session timeout enforced; session survives cluster member restart; sticky-session routing confirmed via logs.
**Prerequisites:** P01 v8 signed off.

### Sprint 1
**Goal:** Configure sticky session routing at the plugin layer.
**WebSphere Admin:** Confirm plugin `CloneID`-based affinity active; enable plugin request logging.
**Acceptance Criteria:** Plugin log shows a session's requests consistently routed to the same member.

### Sprint 2
**Goal:** Configure and compare the sticky-only control case and enable memory-to-memory session replication.
**Learning Objective:** The three persistence strategies (per P01_Foundation.md v9): (c) sticky-only (no replication) as the control case, and (a) memory-to-memory (default).
**WebSphere Admin:** First configure sticky-only (no replication) and exercise the control case — log in, kill a member, confirm session lost (baseline). Then enable replication domain for `devdsbinappcluster01`; confirm both members registered as partners; observe single-replica vs. multi-replica tuning.
**Acceptance Criteria:** Sticky-only baseline observed (session lost on member kill); Admin Console shows both members actively replicating; M2M kill-test shows session survives; replica-count tuning observed and documented.

### Sprint 3
**Goal:** Implement Session Timeout (auto-logout after N minutes idle).
**Business Features:** Session Timeout.
**App Dev:** UI: "Session expired" redirect. Backend: configure timeout in `web.xml`/session config.
**Acceptance Criteria:** Session expires after configured idle period; redirected to Login with message.

### Sprint 4
**Goal:** Prove session failover via a cluster member restart.
**WebSphere Admin:** Log in, hold active session on Member 1; restart Member 1 (graceful, distinct from v5's kill test); confirm session survives on Member 2.
**Acceptance Criteria:** No forced re-login during restart window.

### Sprint 5
**Goal:** Package/deploy `digistack-bank-v9.ear`; build and evaluate the DB-backed persistence case; record the three-way comparison.
**Learning Objective:** Database-backed session persistence (session table in PostgreSQL, dedicated DataSource `jdbc/SessionDS` or reusing `jdbc/BankDS`), persistence frequency tuning, and trade-off analysis (performance vs. reliability vs. DB load).
**WebSphere Admin:** Deploy v9; configure strategy (b) DB-backed session persistence; kill a member and confirm session survives; observe session table growth and timeout/purge behavior; measure latency difference between (a) M2M and (b) DB-backed under load; assess DB connection-pool impact of (b) reusing the P01 v7 pool-sizing math (per P01_Foundation.md v7 Connection Pool Sizing worked example).
**Acceptance Criteria:** All three strategies configured and exercised in turn ((c) sticky-only baseline, (a) M2M, (b) DB-backed); comparison recorded in SetupDoc-v9.md covering latency (a) vs (b), pool impact of (b), and a stated ship decision (memory-to-memory, with DB-backed documented as the fallback for non-replicable session state); interview-anchor three-way write-up (when each is appropriate, sizing implications, DR implications) included as a SetupDoc-v9.md section.

### Sprint 6
**Goal:** Write and execute test cases for Version 9.
**Learning Objective:** Test Case discipline (TCS01/TCS02).
**Deliverables:** TestCases-v9.md (including TP01 Pipeline Results section).
**WebSphere Admin:** Execute TP01_Test_Pipeline.md stages 1–5 (DEV → SIT → UAT → PRE-PROD → PROD) and record every stage in the "TP01 Pipeline Results — v9" table.
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7; all TP01 pipeline stages Pass (Critical/High) per TP01 R3.
**Enterprise Outcome:** Version 9 test coverage complete.

### Sprint 7
**Goal:** Sign off Version 9.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Admin:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v9.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 9 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 9.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Admin:** Phase 1 — inject a realistic fault tied to this version's topic. Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v9.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 9 fault drill complete. Non-gating — does not block sign-off.

**Version 9 Deliverables:** `digistack-bank-v9.ear`, SetupDoc-v9.md (including three-way persistence comparison + interview-anchor write-up), TestCases-v9.md, FaultDrill-v9.md, replication domain/session timeout config, DB-backed session persistence config (session table + DataSource).
**Exit Criteria (target, not yet verified):** Home + DB read functional; DB validated; Deployment successful; Smoke passed; Fault drill complete (Sprint 8, non-gating).
**Lessons Learned:** Sticky sessions (routing) and replication (data protection) are distinct mechanisms.
**Technical Debt:** None introduced.

---