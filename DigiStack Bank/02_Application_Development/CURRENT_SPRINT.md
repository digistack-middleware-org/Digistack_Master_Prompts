# Current Sprint — Version 13 — Notifications (JavaMail / JNDI Mail Session)

## Version Overview
**Objective:** Configure JavaMail via JNDI Mail Session; trigger a real email on Withdraw.
**Business Scope:** One trigger point — successful Withdraw sends one email. No SMS/push/multi-channel matrix.
**WebSphere Focus:** JavaMail, SMTP Configuration, Resource Environment Entries, JNDI Mail Session, External Resource Configuration, Logging/Troubleshooting Mail Delivery.
**Expected Outcome:** Mail Session configured; Withdraw triggers real delivered email; delivery failure visible in logs when deliberately misconfigured.
**Prerequisites:** P01 v12 signed off.
**Note:** Fund Transfer doesn't exist yet (deferred to P02) — Withdraw is the trigger point here.

## Standing Reminders For This Version
- This is a CODE version. Rules in SESSION_STATE.md "Editing Existing Code" apply: user pastes current file first, Claude returns the complete updated file.
- JSPs: logic and structure only. Styling goes in css/pages/<name>.css or css/common/common.css. No <style> blocks, no inline style="" (two documented exceptions only).
- SMTP credentials are externalized like v7's JAAS Auth Alias — never hardcoded in code or config.
- A mail failure must NOT roll back or fail a Withdraw (log it and carry on).
- TEST01: all 25 unit tests must still pass.
- Every WebSphere task shows Admin Console AND wsadmin steps (NDS01 Rule 7). wsadmin needs -user/-password.

### Sprint 1
**Goal:** Configure an SMTP resource and JNDI Mail Session.
**Learning Objective:** JNDI Mail Session as external-resource configuration (mirrors v7's DataSource pattern).
**WebSphere Admin:** Configure Mail Provider/SMTP host; create `mail/BankMailSession`. SMTP credentials externalized the same way as v7's JAAS Auth Alias — never hardcoded in config or code, per STD's Golden Rule and DBS01 §4.2's Connection & Credentials Standard.
**Acceptance Criteria:** Test Connection confirms SMTP reachability; no plaintext SMTP credential found anywhere in config (grep-verified, same discipline as v7's negative test).

### Sprint 2
**Goal:** Configure a Resource Environment Entry for sender/recipient defaults.
**WebSphere Admin:** Create Resource Environment Entry for sender address (`noreply@digistack.cloud`).
**Acceptance Criteria:** Value resolves correctly via JNDI lookup.

### Sprint 3
**Goal:** Build email-sending logic; wire it to successful Withdraw.
**Business Features:** Withdraw triggers confirmation email.
**App Dev:** Backend: `NotificationService.sendWithdrawEmail()` via JavaMail/JNDI, called from `AccountService.withdraw()`. UI: notification/alert bell icon on Dashboard header showing Withdraw email event count (per P01_Foundation.md v13 UI note; extended at P02 v15).
**Acceptance Criteria:** Successful Withdraw triggers real, delivered email with correct details.

### Sprint 4
**Goal:** Deliberately misconfigure mail delivery; troubleshoot via logs.
**WebSphere Admin:** Break SMTP config; trigger Withdraw, confirm failure visible/diagnosable in logs; restore, confirm recovery.
**Acceptance Criteria:** Failure clearly logged, actionable; recovery confirmed.

### Sprint 5
**Goal:** Package/deploy `digistack-bank-v13.ear` to the cluster.
**WebSphere Admin:** Deploy to both members; confirm email trigger works from either.
**Acceptance Criteria:** Email trigger works consistently cluster-wide.

### Sprint 6
**Goal:** Write and execute test cases for Version 13.
**Learning Objective:** Test Case discipline (TCS01/TCS02).
**Deliverables:** TestCases-v13.md (including TP01 Pipeline Results section).
**WebSphere Admin:** Execute TP01_Test_Pipeline.md stages 1–5 (DEV → SIT → UAT → PRE-PROD → PROD) and record every stage in the "TP01 Pipeline Results — v13" table.
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7; all TP01 pipeline stages Pass (Critical/High) per TP01 R3.
**Enterprise Outcome:** Version 13 test coverage complete.

### Sprint 7
**Goal:** Sign off Version 13.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Admin:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v13.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 13 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 13.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Admin:** Phase 1 — inject a realistic fault tied to this version's topic. Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v13.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 13 fault drill complete. Non-gating — does not block sign-off.

**Version 13 Deliverables:** `digistack-bank-v13.ear`, SetupDoc-v13.md, TestCases-v13.md, FaultDrill-v13.md, SMTP Mail Provider/JNDI Mail Session/Resource Environment Entry.
**Exit Criteria (target, not yet verified):** JNDI Mail Session operational; successful Withdraw delivers the expected email; deliberately broken SMTP configuration produces actionable logs; recovery verified; cluster-wide mail trigger tested; Smoke passed; Fault drill complete (Sprint 8, non-gating).
**Lessons Learned:** JNDI Mail Session mirrors the DataSource externalization pattern; breaking a working integration is the fastest way to learn its failure mode.
**Technical Debt:** Single channel (email only) — SMS/push explicitly out of scope for this Part.

---