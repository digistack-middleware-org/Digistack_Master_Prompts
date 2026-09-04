# Current Sprint — P01 Version 6 — Application Administration

## Version Overview
**Objective:** Deep-dive DMgr/federation plumbing from v5; introduce wsadmin scripting alongside Freeze/Unfreeze.
**Business Scope:** One admin action — Freeze/Unfreeze an account (blocks Deposit/Withdraw when frozen). No dashboard/approval workflow/audit UI.
**WebSphere Focus:** DMgr, Node Federation/Synchronization, wsadmin Scripting, Application/Server Lifecycle Management.
**Expected Outcome:** DMgr manages federated nodes; Freeze/Unfreeze toggled via app and verified; at least one action performed via wsadmin script.
**Prerequisites:** P01 v5 signed off.

### Sprint 1
**Goal:** Deep-dive Node Synchronization mechanics.
**Learning Objective:** Node Sync vs. Full Resynchronization.
**WebSphere Admin:** Trigger manual Node Sync; deliberately drift node repo, then Full Resync to correct.
**Acceptance Criteria:** Drift detected and corrected; repos confirmed in agreement.

### Sprint 2
**Goal:** Add the `is_frozen` flag to `accounts`.
**App Dev:** DB: `accounts.is_frozen` column (`V4__add_frozen_flag.sql`) — V4 in migration numbering because Version 4 introduced no schema change (see v4 "no schema change" deliverables).
**Acceptance Criteria:** Column added; defaults unfrozen.

### Sprint 3
**Goal:** Build Freeze/Unfreeze logic; gate Deposit/Withdraw against it.
**Business Features:** Freeze/Unfreeze; Deposit/Withdraw blocked when frozen.
**App Dev:** UI: Freeze/Unfreeze toggle. Backend: `AccountService.freeze()`/`unfreeze()`.
**Acceptance Criteria:** Frozen account rejects Deposit/Withdraw; unfreeze restores operation.

### Sprint 4
**Goal:** Write a wsadmin script to perform Freeze/Unfreeze outside the UI.
**Learning Objective:** wsadmin (Jython) scripting fundamentals.
**WebSphere Admin:** Write and run wsadmin script against the live cluster on the seed account.
**Acceptance Criteria:** Script successfully freezes/unfreezes, verified via UI.

### Sprint 5
**Goal:** Package/deploy `digistack-bank-v6.ear` to the cluster.
**Learning Objective:** Application/Server Lifecycle Management on a clustered deployment.
**WebSphere Admin:** Deploy v6 to both members; confirm synchronized status.
**Acceptance Criteria:** Both members serve v6 identically, no drift.

### Sprint 6
**Goal:** Write and execute test cases for Version 6.
**Learning Objective:** Test Case discipline (TCS01/TCS02).
**WebSphere Admin:** Confirm app status for test execution.
**Deliverables:** TestCases-v6.md.
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7.
**Enterprise Outcome:** Version 6 test coverage complete.

### Sprint 7
**Goal:** Sign off Version 6.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Admin:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v6.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 6 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 6.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Admin:** Phase 1 — inject a realistic fault tied to this version's topic. Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v6.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 6 fault drill complete. Non-gating — does not block sign-off.

**Version 6 Deliverables:** `digistack-bank-v6.ear`, `V4__add_frozen_flag.sql`, SetupDoc-v6.md, TestCases-v6.md, FaultDrill-v6.md, wsadmin script.
**Exit Criteria (target, not yet verified):** Home + DB read functional; DB validated; Deployment successful; Smoke passed; Fault drill complete (Sprint 8, non-gating).
**Lessons Learned:** Node Sync vs. Full Resync; wsadmin as a genuine operational path; admin features layer cleanly onto Service-layer code.
**Technical Debt:** Freeze/Unfreeze open to any logged-in user — deferred to Version 10 (role gating).

---