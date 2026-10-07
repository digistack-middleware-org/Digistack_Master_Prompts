# Current Sprint — Version 12 — WAS SSL Configuration (End-to-End)

## Version Overview
**Objective:** Extend v11's SSL to the full hop chain (IHS↔plugin↔AppServer↔DB) with mTLS on ≥1 internal hop.
**Business Scope:** Zero new functionality.
**WebSphere Focus:** SSL Repertoires, NodeDefaultSSLSettings, CellDefaultSSLSettings, Mutual SSL (mTLS), Plugin SSL, Certificate Renewal, SSL Troubleshooting.
**Expected Outcome:** SSL end-to-end; mTLS on ≥1 internal hop; cert expiry/renewal process documented and tested.
**Prerequisites:** P01 v11 signed off.

### Sprint 1
**Goal:** Configure the IHS plugin↔AppServer SSL hop.
**WebSphere Admin:** Configure plugin SSL settings for HTTPS transport; regenerate/propagate plugin-cfg.xml.
**Acceptance Criteria:** Plugin logs confirm HTTPS used for IHS→AppServer hop.

### Sprint 2
**Goal:** Configure SSL Repertoires and Cell/Node Default SSL Settings.
**WebSphere Admin:** Configure `NodeDefaultSSLSettings`/`CellDefaultSSLSettings`; create dedicated SSL Repertoire for internal traffic.
**Acceptance Criteria:** Repertoire correctly referenced by both members.

### Sprint 3
**Goal:** Enable mutual TLS (mTLS) on the AppServer↔DB hop.
**WebSphere Admin:** Configure PostgreSQL to require client cert auth; configure `jdbc/BankDS` to present a client cert.
**Acceptance Criteria:** Connection succeeds only with correct cert; wrong/missing cert rejected.

### Sprint 4
**Goal:** Validate the full end-to-end SSL chain; re-test all features.
**WebSphere Admin:** Trace request end-to-end confirming SSL/mTLS at every hop; deliberately break one hop, diagnose via logs.
**Acceptance Criteria:** All features function over full SSL chain; deliberate break correctly diagnosed.

### Sprint 5
**Goal:** Package/deploy `digistack-bank-v12.ear`; document/test certificate renewal.
**WebSphere Admin:** Deploy v12; perform deliberate cert renewal (internal-hop cert), confirm zero downtime.
**Acceptance Criteria:** Renewal completes with no interruption; CI01 updated (`digistack-mtls-internal-hop.crt`).

### Sprint 6
**Goal:** Write and execute test cases for Version 12.
**Learning Objective:** Test Case discipline (TCS01/TCS02).
**Deliverables:** TestCases-v12.md (including TP01 Pipeline Results section).
**WebSphere Admin:** Execute TP01_Test_Pipeline.md stages 1–5 (DEV → SIT → UAT → PRE-PROD → PROD) and record every stage in the "TP01 Pipeline Results — v12" table.
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7; all TP01 pipeline stages Pass (Critical/High) per TP01 R3.
**Enterprise Outcome:** Version 12 test coverage complete.

### Sprint 7
**Goal:** Sign off Version 12.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Admin:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v12.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 12 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 12.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Admin:** Phase 1 — inject a realistic fault tied to this version's topic. Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v12.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 12 fault drill complete. Non-gating — does not block sign-off.

**Version 12 Deliverables:** `digistack-bank-v12.ear`, SetupDoc-v12.md, TestCases-v12.md, FaultDrill-v12.md, plugin SSL/SSL Repertoires/mTLS config.
**Exit Criteria (target, not yet verified):** IHS → plugin → AppServer → DB SSL path verified; NodeDefaultSSLSettings and CellDefaultSSLSettings correctly configured; mTLS enforced on the selected internal hop; deliberate SSL failure diagnosed; certificate renewal tested; Smoke passed; Fault drill complete (Sprint 8, non-gating).
**Lessons Learned:** SSL Repertoires provide explicit, reusable scoping; mTLS requires client authentication too.
**Technical Debt:** Only one internal hop carries mTLS, per NFR matrix's "≥1 internal hop" requirement — intentional scope.

---
