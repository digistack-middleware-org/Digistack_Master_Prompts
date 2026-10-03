# Current Sprint — Version 11 — SSL (HTTPS at the Web Tier)

## Version Overview
**Objective:** Move all existing pages to HTTPS; establish certificates, keystore/truststore, certificate chain fundamentals.
**Business Scope:** Zero new functionality. All existing pages move to HTTPS.
**WebSphere Focus:** SSL Basics, Certificates, KeyStore, TrustStore, HTTPS, Certificate Chains.
**Expected Outcome:** Self-signed certificate generated/imported; HTTPS enforced on IHS; HTTP redirects to HTTPS.
**Prerequisites:** P01 v10 signed off.

### Sprint 1
**Goal:** Generate a self-signed certificate; configure keystore/truststore.
**WebSphere Admin:** Generate cert for `www.digistack.cloud` (`digistack-ihs-webtier.crt`, per CI01); populate IHS KeyStore/TrustStore.
**Acceptance Criteria:** Cert details (CN, validity) confirmed correct.

### Sprint 2
**Goal:** Configure IHS to serve HTTPS on port 443.
**WebSphere Admin:** Configure IHS `httpd.conf` for SSL; restart IHS; confirm HTTPS reachable.
**Acceptance Criteria:** Home page loads over `https://`.

### Sprint 3
**Goal:** Enforce HTTP → HTTPS redirect.
**WebSphere Admin:** Configure IHS redirect rule (port 80 → 443).
**Acceptance Criteria:** `http://` requests redirect cleanly to `https://`, path preserved.

### Sprint 4
**Goal:** Validate certificate chain trust; re-test all existing pages over HTTPS.
**WebSphere Admin:** Walk through cert chain validation; re-run Login, Deposit/Withdraw, Freeze/Unfreeze under HTTPS.
**Acceptance Criteria:** All features function over HTTPS; no mixed-content warnings.

### Sprint 5
**Goal:** Package/deploy `digistack-bank-v11.ear`; update Certificate Inventory.
**WebSphere Admin:** Deploy v11 (unchanged app); add `digistack-ihs-webtier.crt` to CI01 §5.2.
**Acceptance Criteria:** App unchanged functionally; cert entry recorded with Annual renewal cadence.

### Sprint 6
**Goal:** Write and execute test cases for Version 11.
**Learning Objective:** Test Case discipline (TCS01/TCS02).
**Deliverables:** TestCases-v11.md (including TP01 Pipeline Results section).
**WebSphere Admin:** Execute TP01_Test_Pipeline.md stages 1–5 (DEV → SIT → UAT → PRE-PROD → PROD) and record every stage in the "TP01 Pipeline Results — v11" table.
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7; all TP01 pipeline stages Pass (Critical/High) per TP01 R3.
**Enterprise Outcome:** Version 11 test coverage complete.

### Sprint 7
**Goal:** Sign off Version 11.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Admin:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v11.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 11 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 11.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Admin:** Phase 1 — inject a realistic fault tied to this version's topic. Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v11.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 11 fault drill complete. Non-gating — does not block sign-off.

**Version 11 Deliverables:** `digistack-bank-v11.ear`, SetupDoc-v11.md, TestCases-v11.md, FaultDrill-v11.md, IHS SSL config.
**Exit Criteria (target, not yet verified):** IHS HTTPS on port 443 operational; certificate and truststore configuration verified; HTTP → HTTPS redirect working with path preservation; all existing application features function over HTTPS; Smoke passed; Fault drill complete (Sprint 8, non-gating).
**Lessons Learned:** Web-tier SSL is distinct from end-to-end SSL (deferred to v12).
**Technical Debt:** Self-signed cert only; internal hops beyond IHS unencrypted until v12.

---
