# Current Sprint — Version 12.5 — Customer Onboarding & Registry-Based Login

## Version Overview
**Objective:** Let the Administrator onboard new customers who log in with their own username and password, and complete v10's security model by making the container (not application code) authenticate every login.
**Business Scope:** One Administrator-only Onboard Customer screen, one Change Password screen, first Savings account opened at 0.00. No self-registration, no KYC, no welcome email.
**WebSphere Focus:** Programmatic container authentication (`request.login`), identity provisioning to the file-based federated repository, Administrative Security in practice, role-protected servlets, wsadmin Jython, JDBC local transactions.
**Expected Outcome:** Seeded and onboarded users authenticate through the registry; Administrator onboards a customer; customer is forced to change the temporary password at first login; `users` table holds no password data for onboarded customers.
**Prerequisites:** P01 v12 signed off; v10 file registry operational with `Customer` and `Administrator` groups.
**Clarification:** v10 declared roles and bound them to groups, but `Login.jsp` still posted to `/Login` and `LoginServlet` verified the hash itself, so the container never authenticated the caller. This version closes that gap. Only Customer and Administrator roles exist until P03 v29 (Teller).

### Sprint 1
**Goal:** Prove the prerequisites before changing any code.
**WebSphere Admin:** Verify `customer1` and `admin1` exist in the file registry in the `Customer` and `Administrator` groups with their current passwords. Prove `request.login()` on WAS 9.0.5.28 with Administrative Security enabled using a one-off test. Decide the registry-write mechanism (service API vs wsadmin) and the service ID's minimum permission; test it by creating and deleting a throwaway user. Externalize service ID credentials (never hardcoded, same rule as v6 `freezeAccount.py`). Take `backupConfig` first.
**Deliverables:** SetupDoc-v12.5.md decision section.
**Acceptance Criteria:** `request.login` succeeds for a valid user and fails for a wrong password; throwaway user created and deleted from the chosen mechanism; decisions recorded.

### Sprint 2
**Goal:** Move login to the registry.
**App Dev:** Backend: Flyway migration (next free number) — add `users.must_change_password BOOLEAN NOT NULL DEFAULT FALSE`, make `password_hash` and `password_salt` nullable, add the account-number sequence. In `LoginServlet.doPost` replace `PasswordUtil.verify()` with `request.login()`, map `ServletException` to "Invalid username or password", load the profile via `getRemoteUser()`, check `is_active`, redirect to `/ChangePassword` when `must_change_password` is TRUE. In `doGet`, handle `?reason=` before the already-logged-in redirect. `Login.jsp` unchanged.
**Acceptance Criteria:** `customer1` and `admin1` log in through the container; wrong password shows the error; a logged-in Customer hitting Freeze sees "Access denied"; v9 session timeout behaviour unchanged.

### Sprint 3
**Goal:** Build the registry gateway and onboarding service.
**App Dev:** Backend: `RegistryGateway` interface (`createUser`, `deleteUser`, `changePassword`, `userExists`) with a file-registry implementation; `OnboardingService` doing one JDBC transaction (`users` + `accounts`) plus `createUser`, with a compensating `deleteUser` if the commit fails.
**Acceptance Criteria:** Duplicate username or email rejected; a forced failure after the registry call leaves no orphan in either the registry or the database.

### Sprint 4
**Goal:** Onboard Customer and Change Password screens.
**Business Features:** Onboard Customer (Administrator only); forced password change at first login.
**App Dev:** Frontend: `OnboardCustomer.jsp`, `ChangePassword.jsp`. Backend: `OnboardCustomerServlet`, `ChangePasswordServlet`; `<security-constraint>` for `Administrator` on Onboard Customer; clear `must_change_password` only after the registry password update succeeds. Temporary password shown once, never stored, logged or emailed.
**Acceptance Criteria:** Customer-role direct URL access rejected (403 with message); unauthenticated access redirected to login; onboarded customer is forced to `/ChangePassword` and cannot reach Dashboard, Deposit or Withdraw until the password is changed.

### Sprint 5
**Goal:** Package/deploy `digistack-bank-v12.5.ear`; validate end to end.
**WebSphere Admin:** Update `finalName` and the `application.xml` display-name, deploy to the cluster, run onboarding then first login on both members.
**Acceptance Criteria:** Administrator onboards a customer; customer logs in with the temporary password, changes it, sees their account at balance 0.00; the onboarded user's `users` row has no hash or salt; v9 session survival and v11/v12 SSL path unchanged.

### Sprint 6
**Goal:** Write and execute test cases for Version 12.5.
**Learning Objective:** Test Case discipline (TCS01/TCS02).
**Deliverables:** TestCases-v12.5.md (including TP01 Pipeline Results section).
**WebSphere Admin:** Execute TP01_Test_Pipeline.md stages 1–5 (DEV → SIT → UAT → PRE-PROD → PROD) and record every stage in the "TP01 Pipeline Results — v12.5" table.
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7; all TP01 pipeline stages Pass (Critical/High) per TP01 R3.
**Enterprise Outcome:** Version 12.5 test coverage complete.

### Sprint 7
**Goal:** Sign off Version 12.5.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Admin:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v12.5.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 12.5 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 12.5.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Admin:** Phase 1 — inject a realistic fault tied to this version's topic (for example, remove an onboarded user from the registry while the `users` row remains). Phase 2 — incident ticket raised from real symptoms (customer reports "Invalid username or password"). Phase 3 — investigate live by comparing the registry with the `users` table, perform RCA, restore environment.
**Deliverables:** FaultDrill-v12.5.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 12.5 fault drill complete. Non-gating — does not block sign-off.

**Version 12.5 Deliverables:** `digistack-bank-v12.5.ear`, SetupDoc-v12.5.md, TestCases-v12.5.md, FaultDrill-v12.5.md, Flyway migration, `RegistryGateway`/`OnboardingService`, `createCustomerUser.py` (wsadmin break-glass path).
**Exit Criteria (target, not yet verified):** Container authenticates all logins via `request.login`; Administrator can onboard a customer; temporary password forced to change at first login; Customer cannot reach Onboard Customer; duplicate or failed onboarding leaves no partial state; Smoke passed; Fault drill complete (Sprint 8, non-gating).
**Lessons Learned:** Credentials belong in the identity store, not application tables; two systems cannot share one commit, so failure handling must be designed.
**Technical Debt:** File registry only — LDAP replaces `RegistryGateway` in P06 v42. Registry and database writes are not atomic (compensating delete only). `PasswordUtil.java` and the unused hash columns remain until cleanup. "Forgot Password?" remains unscoped.


---