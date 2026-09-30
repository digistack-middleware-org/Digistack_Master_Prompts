# Current Sprint — Version 10 — Users & Groups

## Version Overview
**Objective:** Introduce administrative security — real user registry, roles, groups — closing v6's Freeze/Unfreeze open-access debt.
**Business Scope:** Zero new functionality beyond role enforcement. Freeze/Unfreeze gated to Administrator; Customer role limited to Deposit/Withdraw.
**WebSphere Focus:** Administrative Security, File Registry (or LDAP), Users, Groups, Roles, Authorization.
**Expected Outcome:** File-based registry configured; Customer/Administrator roles defined; Freeze/Unfreeze unreachable by Customer role.
**Prerequisites:** P01 v9 signed off.
**Clarification:** Only two roles are built in P01: Customer and Administrator (this version). Auditor is not built anywhere in P01–P10. Branch Operator is not built in P01–P02 — P03 v29's Branch Portal (Teller Login) introduces a Teller role there. Until that version, only Customer and Administrator exist; no role is assumed to already exist (per P01_Foundation.md v10 "Roles Actually Built").

### Sprint 1
**Goal:** Configure a file-based user registry in WAS.
**WebSphere Admin:** Enable Administrative Security (Global Security); configure File-based Federated Repository.
**Acceptance Criteria:** DMgr Admin Console requires login; registry test succeeds.

### Sprint 2
**Goal:** Define Customer and Administrator groups; assign test users.
**WebSphere Admin:** Create `Customer`/`Administrator` groups; assign seed users to each.
**Acceptance Criteria:** Both users authenticate against the new registry.

### Sprint 3
**Goal:** Define security roles and map them to groups.
**App Dev:** Backend: declare `Customer`/`Administrator` roles in `web.xml`.
**WebSphere Admin:** Map roles → groups via Admin Console.
**Acceptance Criteria:** Role mapping visible and correct.

### Sprint 4
**Goal:** Gate Freeze/Unfreeze behind the Administrator role.
**Business Features:** Freeze/Unfreeze restricted to Administrator.
**App Dev:** Backend: `<security-constraint>` in `web.xml`.
**Acceptance Criteria:** Customer-role direct URL access to Freeze/Unfreeze rejected (403).

### Sprint 5
**Goal:** Package/deploy `digistack-bank-v10.ear`; validate both role paths.
**WebSphere Admin:** Deploy v10; log in as each role and confirm access boundaries.
**Acceptance Criteria:** Customer can Deposit/Withdraw but not Freeze/Unfreeze; Administrator can do both.

### Sprint 6
**Goal:** Write and execute test cases for Version 10.
**Learning Objective:** Test Case discipline (TCS01/TCS02).
**Deliverables:** TestCases-v10.md (including TP01 Pipeline Results section).
**WebSphere Admin:** Execute TP01_Test_Pipeline.md stages 1–5 (DEV → SIT → UAT → PRE-PROD → PROD) and record every stage in the "TP01 Pipeline Results — v10" table.
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7; all TP01 pipeline stages Pass (Critical/High) per TP01 R3.
**Enterprise Outcome:** Version 10 test coverage complete.

### Sprint 7
**Goal:** Sign off Version 10.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Admin:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v10.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 10 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 10.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Admin:** Phase 1 — inject a realistic fault tied to this version's topic. Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v10.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 10 fault drill complete. Non-gating — does not block sign-off.

**Version 10 Deliverables:** `digistack-bank-v10.ear`, SetupDoc-v10.md, TestCases-v10.md, FaultDrill-v10.md, File Registry/groups/role mapping/web.xml constraints.
**Exit Criteria (target, not yet verified):** File-based Federated Repository operational; Customer and Administrator groups/users authenticate successfully; security roles mapped correctly; Customer cannot invoke Freeze/Unfreeze; Administrator can perform Freeze/Unfreeze; Smoke passed; Fault drill complete (Sprint 8, non-gating).
**Lessons Learned:** Container-managed security constraints enforce authorization independent of UI.
**Technical Debt:** File registry only — LDAP federation is a deliberate future step (P06 v42).

---