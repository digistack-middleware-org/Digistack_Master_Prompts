# P02 — WebSphere / Enterprise Middleware Integration
## Consolidated Sprint Plan (Versions 15–22)

**Part:** P02 — WebSphere / Enterprise Middleware Integration
**Versions Covered:** 15, 16, 16.5, 17, 18, 19, 20, 21, 22
**Status:** ⏳ Not Started — planning document only, no versions built or signed off
**Prerequisite:** P01 Completion Checkpoint satisfied (`digistack-bank-v14.ear`, 2-member cluster, IHS, SSL end-to-end, JNDI DataSource, Customer/Administrator roles, JNDI Mail Session)
**Next:** P03 — Enterprise Banking Systems (CBS, Payments, Channel Simulators, Loans)
**Sprint Structure:** 8 sprints per version — Sprint 1–4 Build, Sprint 5 Package and Deploy, Sprint 6 Test Cases (executes the full TP01 5-stage pipeline per TP01_Test_Pipeline.md), Sprint 7 Sign-off, Sprint 8 Fault Injection + Incident.
**Test Pipeline:** TP01 (mandatory from v8 onward) — every version's Sprint 6 runs DEV → SIT → UAT → PRE-PROD → PROD stages; results recorded in each TestCases-v<N>.md under "## TP01 Pipeline Results — v<N>".


---

# Version 15 — JMS & Asynchronous Banking

## Version Overview

**Version Objective:** Introduce Service Integration Bus (SIBus), JMS Queues, and Message-Driven Beans to process Fund Transfer asynchronously — while growing the application just enough (Customer, multi-Account, Beneficiary) to make async processing meaningful.

**Business Scope:** Customer (multi-account), Account (linked to Customer), Beneficiary registration, Fund Transfer (async, processed via MDB).

**WebSphere Focus:** SIBus, Bus Members, JMS Queues, Connection Factories, Activation Specifications, Message-Driven Beans, Dead Letter Queue, Producer/Consumer pattern.

**Expected Outcome:** A Fund Transfer request returns an immediate "Accepted" response; actual balance update happens asynchronously via an MDB consuming from a JMS Queue; a deliberately failing transfer lands in the DLQ.

**Prerequisites:** P01 Completion Checkpoint satisfied — `digistack-bank-v14.ear` running on a 2-member cluster behind IHS, SSL end-to-end, JNDI DataSource, Customer/Administrator roles enforced, JNDI Mail Session working.

---

### Sprint 1
**Sprint Goal:** Extend the data model for multi-account customers.
**Learning Objective:** Schema evolution without breaking existing deployables.
**Business Features:** Customer entity (customer_id, name) linked to existing `users` row; Account gains `customer_id` FK, supports 2+ accounts per customer.
**Application Development:**
- UI: Account list view (replaces single-balance view)
- Backend: CustomerService, AccountService (multi-account aware)
- Database: `V15__create_customer_account_beneficiary_fundtransfer.sql`
- API: N/A

**WebSphere Administration:**
- Redeploy `digistack-bank-v15.ear` over v14
- No new JDBC/JMS/JNDI config

**Dependencies:** P01 v3 accounts table, P01 v7 DataSource.
**Deliverables:** Updated schema, redeployed EAR, multi-account UI.
**Acceptance Criteria:** A test customer has 2 accounts, both visible and independently balance-checkable.
**Enterprise Outcome:** Baseline multi-account data model ready for async work.

---

### Sprint 2
**Sprint Goal:** Add Beneficiary registration.
**Learning Objective:** Model a minimal cross-account relationship without an approval workflow.
**Business Features:** Register a Beneficiary (own or test external account); view registered Beneficiaries.
**Application Development:**
- UI: Beneficiary registration form + list
- Backend: BeneficiaryService
- Database: (part of `V15__create_customer_account_beneficiary_fundtransfer.sql`, per Sprint 1)
- API: N/A

**WebSphere Administration:**
- Redeploy over v15, no new config

**Dependencies:** Sprint 1's Account model.
**Deliverables:** Beneficiary table + UI.
**Acceptance Criteria:** A customer registers one internal and one "external" test beneficiary successfully.
**Enterprise Outcome:** Fund Transfer (Sprint 3+) has a real target to transfer to.

---

### Sprint 3
**Sprint Goal:** Stand up SIBus and a JMS Queue.
**Learning Objective:** Configure SIBus Bus, Bus Members, and a JMS Queue Destination from scratch.
**Business Features:** None (pure middleware).
**Application Development:** N/A this sprint.
**WebSphere Administration:**
- Create SIBus, add cluster as Bus Member
- Create Queue Destination `BANK.FUNDTRANSFER.Q`
- Create JMS Connection Factory + Queue JNDI binding

**Dependencies:** P01 v5 cluster, P01 v6 DMgr.
**Deliverables:** Operational SIBus with one Queue Destination.
**Acceptance Criteria:** A test message can be manually sent to and browsed on the queue via Admin Console.
**Enterprise Outcome:** Messaging backbone exists ahead of any application code touching it.

---

### Sprint 4
**Sprint Goal:** Implement Fund Transfer as an async-triggering transaction.
**Learning Objective:** Producer pattern — send a message, return immediately.
**Business Features:** Fund Transfer: select source Account, Beneficiary, amount.
**Application Development:**
- UI: Fund Transfer form
- Backend: FundTransferService (producer) — validates request, sends JMS message, returns "Accepted"
- Database: (part of `V15__create_customer_account_beneficiary_fundtransfer.sql`, status: PENDING/PROCESSED/FAILED)
- API: N/A (REST comes in Version 16)

**WebSphere Administration:**
- Application code looks up Connection Factory + Queue via JNDI
- Redeploy over prior sprint changes

**Dependencies:** Sprint 3's SIBus/Queue.
**Deliverables:** Working Fund Transfer producer path.
**Acceptance Criteria:** Submitting a transfer returns "Accepted" immediately; a PENDING row appears in fund_transfer table; message visible on queue.
**Enterprise Outcome:** Decoupled request/response proven at the producer side.

---

### Sprint 5
**Sprint Goal:** Implement the MDB consumer and complete the async loop.
**Learning Objective:** Message-Driven Bean lifecycle, Activation Specification, consumer pattern.
**Business Features:** None (completes Sprint 4's feature).
**Application Development:**
- UI: Fund Transfer status now reflects PROCESSED after async completion
- Backend: FundTransferMDB — consumes message, performs balance debit/credit, updates status
- Database: N/A (reuses Sprint 4 table)
- API: N/A

**WebSphere Administration:**
- Create Activation Specification bound to `BANK.FUNDTRANSFER.Q`
- Deploy MDB module as part of EAR
- Size MDB Listener Port thread pool (baseline from CAP01)

**Dependencies:** Sprint 4's producer, Sprint 3's Queue.
**Deliverables:** End-to-end async Fund Transfer.
**Acceptance Criteria:** A submitted transfer moves from PENDING → PROCESSED within seconds; balances update correctly.
**Enterprise Outcome:** First true asynchronous banking transaction, WebSphere's core JMS/MDB pattern proven.

---

### Sprint 6
**Sprint Goal:** Prove failure handling via Dead Letter Queue.
**Learning Objective:** DLQ configuration, retry semantics, message inspection.
**Business Features:** Deliberately failing transfer (e.g., insufficient funds discovered only during async processing).
**Application Development:**
- UI: Failed transfer shown with FAILED status
- Backend: MDB throws on invalid business rule, message retried per configured attempts, then lands in DLQ
- Database: N/A
- API: N/A

**WebSphere Administration:**
- Configure DLQ on the Queue Destination, set max retry count
- Redeploy final EAR for this version, `digistack-bank-v15.ear`
- Verify DLQ via Admin Console message browsing

**Dependencies:** Sprint 5's MDB.
**Deliverables:** DLQ configured and proven; `SetupDoc-v15.md`, `TestCases-v15.md` drafted.
**TP01 Pipeline (mandatory, per TP01_Test_Pipeline.md):** Sprint 6 executes the full 5-stage test pipeline — DEV (Unit/Component, Developer, Code Quality/Security) → SIT (API, Integration, Database, Middleware, End-to-End, Negative, Regression Pack v1–v<N-1>) → UAT (Business Process, Customer Journey, Financial/Accounting Validation, Business Acceptance) → PRE-PROD (Production-like Smoke, Performance, Security, DR/Recovery, Operational Readiness, Deployment/Rollback) → PROD (Smoke, Sanity, Monitoring Verification, Business Validation). Results recorded in `TestCases-v<N>.md` under "## TP01 Pipeline Results — v<N>" using the TP01 stage table. All Critical/High rows must Pass before Sprint 7 sign-off (TP01 R1–R3).
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7; all TP01 pipeline stages Pass (Critical/High) per TP01 R3.
**Enterprise Outcome:** Full asynchronous processing lifecycle — including failure — demonstrated end-to-end.

---
### Sprint 7
**Sprint Goal:** Sign off Version 15.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Administration:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v15.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 15 signed off.

---

### Sprint 8
**Sprint Goal:** Fault Injection + Incident Simulation for Version 15.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Administration:** Phase 1 — inject a realistic fault tied to this version's topic (e.g., disable the Activation Specification on `BANK.FUNDTRANSFER.Q` so messages queue but are never consumed). Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v15.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 15 fault drill complete. Non-gating — does not block sign-off.

---

## Version 15 Deliverables
- `digistack-bank-v15.ear` (Customer, Account, Beneficiary, Fund Transfer, MDB module)
- `V15__create_customer_account_beneficiary_fundtransfer.sql`
- SIBus, Queue Destination, Connection Factory, Activation Specification, DLQ configuration exports
- SetupDoc-v15.md, TestCases-v15.md

## Version 15 Exit Criteria
- ✅ Application functionality complete (Customer/Account/Beneficiary/Fund Transfer async)
- ✅ Database validated (V15 migration applied and verified)
- ✅ WebSphere deployment successful (SIBus, MDB, DLQ all operational)
- ✅ TP01 pipeline passed (all 5 stages, all Critical/High rows Pass in TP01 Pipeline Results table)
- ✅ Smoke testing passed (successful transfer + deliberate DLQ failure both proven)
- ✅ Ready for Version 16

## Lessons Learned
- **Key learnings:** SIBus/MDB configuration is a one-time setup cost that pays off immediately once the producer/consumer pattern is wired — most of this version's complexity is Admin Console configuration, not code.
- **Known issues:** None expected if Sprint 3's queue setup is verified before Sprint 4 begins.
- **Technical debt:** None new — DLQ retry tuning may be revisited later once real load-testing exists (P04 v33).

---
---

# Version 16 — Web Services (SOAP/REST)

## Version Overview

**Version Objective:** Expose existing banking functionality as callable services — two REST endpoints and one SOAP endpoint — giving P03's channel simulators (Mobile, ATM, Card Portal) a stable contract to build against later.

**Business Scope:** No new banking feature — exposes what already exists: Balance Inquiry (REST), Fund Transfer (REST), Account Statement/Transaction History (SOAP, formalized from v14's report data).

**WebSphere Focus:** Web Services Engine (JAX-WS/JAX-RS), WSDL generation/publishing, SOAP binding, REST endpoint deployment, Admin Console API endpoint configuration, request/response logging.

**Expected Outcome:** Two REST endpoints (Balance Inquiry, Fund Transfer) and one SOAP endpoint (Account Statement) are live; WSDL is published and browsable; an external client (Postman/SoapUI) successfully calls all three with logged request/response.

**Prerequisites:** P02 Version 15 Completion Checkpoint satisfied — Customer/Account/Beneficiary/Fund Transfer (async via SIBus/MDB) operational.

---

### Sprint 1
**Sprint Goal:** Stand up the JAX-RS engine and expose Balance Inquiry as REST.
**Learning Objective:** REST endpoint deployment fundamentals in WAS.
**Business Features:** Balance Inquiry (existing feature, now callable externally).
**Application Development:**
- UI: N/A (service-only sprint)
- Backend: BalanceInquiryResource (JAX-RS)
- Database: N/A (reuses existing Account table)
- API: `GET /api/accounts/{id}/balance`

**WebSphere Administration:**
- Enable JAX-RS engine on the application
- Configure REST endpoint in Admin Console

**Dependencies:** P02 v15 Account model.
**Deliverables:** Working REST Balance Inquiry endpoint.
**Acceptance Criteria:** Postman call returns correct live balance for a known account.
**Enterprise Outcome:** First externally-callable service, proving JAX-RS deployment works end-to-end.

---

### Sprint 2
**Sprint Goal:** Expose Fund Transfer as REST.
**Learning Objective:** Wiring an existing async-triggering business method behind a REST contract.
**Business Features:** Fund Transfer (existing feature, now callable externally).
**Application Development:**
- UI: N/A
- Backend: FundTransferResource (JAX-RS) — delegates to existing FundTransferService producer
- Database: N/A (reuses v15's fund_transfer table)
- API: `POST /api/transfers`

**WebSphere Administration:**
- Configure REST endpoint in Admin Console
- Confirm endpoint request/response logging enabled

**Dependencies:** Sprint 1's JAX-RS engine, P02 v15 Fund Transfer producer.
**Deliverables:** Working REST Fund Transfer endpoint.
**Acceptance Criteria:** Postman POST returns "Accepted" immediately; transfer completes asynchronously as in v15.
**Enterprise Outcome:** REST contract established for the exact flow P03's Mobile/ATM simulators will consume later.
**Note:** No API versioning scheme is introduced at this version — endpoint contracts are expected to stay stable; a breaking change would require an explicit, newly-scoped decision (per ARCH02 §3).

---

### Sprint 3
**Sprint Goal:** Formalize Transaction History as a queryable SOAP service.
**Learning Objective:** JAX-WS service authoring and SOAP binding basics.
**Business Features:** Account Statement / Transaction History (reuses v14's Transaction Report data, now queryable rather than PDF-only).
**Application Development:**
- UI: N/A
- Backend: AccountStatementService (JAX-WS)
- Database: N/A (reuses existing transaction log)
- API: SOAP operation `getAccountStatement(accountId, dateRange)`

**WebSphere Administration:**
- Enable JAX-WS engine on the application
- Generate initial WSDL

**Dependencies:** P01 v14 Transaction Report data.
**Deliverables:** SOAP service skeleton with generated WSDL.
**Acceptance Criteria:** WSDL is reachable via browser at the expected `?wsdl` URL.
**Enterprise Outcome:** SOAP binding style proven alongside REST, giving practice across both service styles.

---

### Sprint 4
**Sprint Goal:** Publish and validate the SOAP WSDL end-to-end.
**Learning Objective:** WSDL publishing, SOAP binding configuration in Admin Console.
**Business Features:** None (completes Sprint 3's feature).
**Application Development:**
- UI: N/A
- Backend: Finalize AccountStatementService response mapping (POJO → SOAP response)
- Database: N/A
- API: SOAP response now returns real transaction rows, not a stub

**WebSphere Administration:**
- Publish WSDL via Admin Console endpoint configuration
- Configure SOAP binding settings

**Dependencies:** Sprint 3's service skeleton.
**Deliverables:** Fully functional SOAP endpoint.
**Acceptance Criteria:** SoapUI call against the published WSDL returns real transaction history for a test account.
**Enterprise Outcome:** First real enterprise SOAP contract, independently testable via SoapUI.

---

### Sprint 5
**Sprint Goal:** Enable request/response logging across all three endpoints.
**Learning Objective:** API endpoint configuration in Admin Console beyond just "deployed."
**Business Features:** None (cross-cutting hardening).
**Application Development:**
- UI: N/A
- Backend: Common request/response logging filter/interceptor applied to REST + SOAP endpoints
- Database: N/A
- API: No new endpoints — instruments the existing three

**WebSphere Administration:**
- Configure request/response logging at the Admin Console/application level
- Verify log output location (per STD logging conventions)

**Dependencies:** Sprints 1–4's three endpoints.
**Deliverables:** Logging enabled and verified across Balance Inquiry, Fund Transfer, Account Statement.
**Acceptance Criteria:** A call to each endpoint produces a corresponding request/response log entry.
**Enterprise Outcome:** Endpoints are now traceable, not just callable — foundation for later observability work (P04).

---

### Sprint 6
**Sprint Goal:** Write and execute test cases for Version 16.
**Learning Objective:** Test Case discipline (TCS01/TCS02).
**Business Features:** None (validation sprint).
**Application Development:** Bug-fix only, no new work.
**WebSphere Administration:**
- Redeploy final EAR for this version, `digistack-bank-v16.ear`
- Confirm all three endpoints reachable post-redeploy

**Dependencies:** Sprints 1–5 complete.
**Deliverables:** `TestCases-v16.md`.
**TP01 Pipeline (mandatory, per TP01_Test_Pipeline.md):** Sprint 6 executes the full 5-stage test pipeline — DEV (Unit/Component, Developer, Code Quality/Security) → SIT (API, Integration, Database, Middleware, End-to-End, Negative, Regression Pack v1–v<N-1>) → UAT (Business Process, Customer Journey, Financial/Accounting Validation, Business Acceptance) → PRE-PROD (Production-like Smoke, Performance, Security, DR/Recovery, Operational Readiness, Deployment/Rollback) → PROD (Smoke, Sanity, Monitoring Verification, Business Validation). Results recorded in `TestCases-v<N>.md` under "## TP01 Pipeline Results — v<N>" using the TP01 stage table. All Critical/High rows must Pass before Sprint 7 sign-off (TP01 R1–R3).
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7; all TP01 pipeline stages Pass (Critical/High) per TP01 R3.
**Enterprise Outcome:** Version 16 test coverage complete.

---
### Sprint 7
**Sprint Goal:** Sign off Version 16.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Administration:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v16.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 16 signed off.

---

### Sprint 8
**Sprint Goal:** Fault Injection + Incident Simulation for Version 16.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Administration:** Phase 1 — inject a realistic fault tied to this version's topic (e.g., corrupt/misconfigure the published WSDL location so SoapUI calls fail). Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v16.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 16 fault drill complete. Non-gating — does not block sign-off.

---

## Version 16 Deliverables
- `digistack-bank-v16.ear` (REST: Balance Inquiry, Fund Transfer; SOAP: Account Statement)
- No new SQL migrations this version (reuses existing tables)
- WSDL export, Admin Console endpoint configuration exports
- SetupDoc-v16.md, TestCases-v16.md

## Version 16 Exit Criteria
- ✅ Application functionality complete (2 REST + 1 SOAP endpoint live)
- ✅ Database validated (no schema change; existing tables confirmed serving both bindings correctly)
- ✅ WebSphere deployment successful (JAX-RS + JAX-WS engines both operational)
- ✅ TP01 pipeline passed (all 5 stages, all Critical/High rows Pass in TP01 Pipeline Results table)
- ✅ Smoke testing passed (Postman + SoapUI calls succeed with logged request/response)
- ✅ Ready for Version 17

## Lessons Learned
- **Key learnings:** REST and SOAP coexist cleanly on the same EAR without conflict; WSDL publishing is mostly an Admin Console configuration exercise once the JAX-WS service class is correct.
- **Known issues:** None expected if Sprint 3's WSDL generation is verified before Sprint 4's binding work begins.
- **Technical debt:** None new — these endpoints are deliberately unauthenticated at this version; MFA/token auth is intentionally deferred to Version 17 (Security Hardening), not an oversight.

---
---

# Version 16.5 — Transaction History Pagination (UI-only)

## Version Overview

**Version Objective:** Add client-side pagination to the Transaction History screen built at v16, with zero new WebSphere administration work — a pure UI/servlet-layer sprint.

**Business Scope:** No new banking feature — the SOAP Account Statement/Transaction History service (v16) already returns full result sets; this version paginates that existing response in the servlet layer for usability.

**WebSphere Focus:** None. Filed as a suffix-slot version specifically because it carries no WebSphere admin topic — kept separate from v16 (to keep that a clean single-topic SOAP/REST sprint) and before v17 (so the UI is complete before security hardening locks down the endpoints).

**Expected Outcome:** Transaction History screen displays results 10 per page with working ← / → navigation; navigating pages does not trigger a new SOAP call per page (result fetched once, paged in servlet memory); the WSDL and SOAP endpoint are confirmed unchanged after this sprint.

**Prerequisites:** P02 Version 16 Completion Checkpoint satisfied — SOAP Account Statement/Transaction History service live and traceable.

---

### Sprint 1
**Sprint Goal:** Add `page`/`pageSize` request parameters to the Transaction History servlet.
**Learning Objective:** Passing pagination parameters through an existing servlet without touching the underlying SOAP contract.
**Business Features:** None (infra for this sprint's feature).
**Application Development:**
- UI: N/A this sprint
- Backend: `TransactionHistoryServlet` updated to accept `page`/`pageSize` params (default page=1, pageSize=10)
- Database: N/A
- API: N/A (no WSDL/endpoint change)

**WebSphere Administration:** N/A this sprint.
**Dependencies:** P02 v16 SOAP Account Statement service.
**Deliverables:** Servlet accepts and validates `page`/`pageSize` params.
**Acceptance Criteria:** A request with `page=2` returns without error; invalid/out-of-range page values are handled gracefully (clamped, not a 500).
**Enterprise Outcome:** Pagination groundwork laid without touching the published SOAP contract.

---

### Sprint 2
**Sprint Goal:** Slice the full SOAP response into pages in the servlet layer.
**Learning Objective:** Server-side (non-DB) pagination — the full result is fetched once, then paged in memory, not re-queried per page.
**Business Features:** None (completes Sprint 1's plumbing).
**Application Development:**
- UI: N/A this sprint
- Backend: Servlet calls the SOAP service once per session/filter-change, caches the full result, slices per `page`/`pageSize`
- Database: N/A
- API: N/A (WSDL/endpoint unchanged)

**WebSphere Administration:** N/A this sprint.
**Dependencies:** Sprint 1.
**Deliverables:** Working in-memory slicing logic.
**Acceptance Criteria:** Navigating between pages does not trigger a new SOAP call, confirmed via request/response logging (P02 v16 Sprint 5).
**Enterprise Outcome:** Confirms pagination is a presentation-layer concern only — no load added to the SOAP endpoint per page view.

---

### Sprint 3
**Sprint Goal:** Build the paged table UI (`TransactionHistory.jsp`).
**Learning Objective:** Rendering a fixed-page-size table consistent with the Dashboard UI mockup.
**Business Features:** Transaction History displayed 10 rows per page.
**Application Development:**
- UI: `TransactionHistory.jsp` renders the current page's 10 rows
- Backend: N/A (reuses Sprint 2's sliced data)
- Database: N/A
- API: N/A

**WebSphere Administration:** N/A this sprint.
**Dependencies:** Sprint 2.
**Deliverables:** Working paged table.
**Acceptance Criteria:** Table shows exactly 10 rows (or fewer on the last page); row content matches the corresponding slice of the full result set.
**Enterprise Outcome:** First visible pagination UI, ready for navigation controls.

---

### Sprint 4
**Sprint Goal:** Add ← / → navigation controls below the table.
**Learning Objective:** Wiring navigation controls to the existing `page` parameter without a full page reload triggering a new SOAP call.
**Business Features:** Working page navigation.
**Application Development:**
- UI: `[ ← 1  2  3  4 → ]` navigation controls per the Dashboard UI mockup
- Backend: N/A (reuses Sprint 1–2 logic)
- Database: N/A
- API: N/A

**WebSphere Administration:** N/A this sprint.
**Dependencies:** Sprint 3.
**Deliverables:** Working navigation controls.
**Acceptance Criteria:** Clicking a page number or arrow navigates correctly; current page is visually indicated; boundary pages disable the inapplicable arrow.
**Enterprise Outcome:** Completes the Sprint Deliverable's core UI requirement.

---

### Sprint 5
**Sprint Goal:** Confirm date/type filter controls (live since v16) interact correctly with pagination.
**Learning Objective:** Ensuring two independently-built features (filtering, pagination) compose correctly rather than conflicting.
**Business Features:** Filtering + pagination working together.
**Application Development:**
- UI: Confirm filter changes reset pagination to page 1
- Backend: Confirm filter change triggers a fresh SOAP call (not a stale cached result)
- Database: N/A
- API: N/A

**WebSphere Administration:** N/A this sprint.
**Dependencies:** Sprint 4, P02 v16's existing filter controls.
**Deliverables:** Verified filter+pagination interaction.
**Acceptance Criteria:** Changing a filter resets to page 1 and re-fetches; paging within an active filter does not re-fetch.
**Enterprise Outcome:** Confirms the scope boundary — pagination handles page-to-page navigation, filters remain the primary tool for narrowing large result sets.

---

### Sprint 6
**Sprint Goal:** Write and execute test cases for Version 16.5.
**Learning Objective:** Test Case discipline (TCS01/TCS02) applied to a UI-only version.
**Business Features:** None (validation sprint).
**Application Development:** Bug-fix only, no new work.
**WebSphere Administration:**
- Redeploy final EAR for this version, `digistack-bank-v16.5.ear`
- Confirm WSDL and SOAP endpoint remain byte-identical to v16 (no contract drift)

**Dependencies:** Sprints 1–5 complete.
**Deliverables:** `TestCases-v16.5.md`.
**TP01 Pipeline (mandatory, per TP01_Test_Pipeline.md):** Sprint 6 executes the full 5-stage test pipeline — DEV (Unit/Component, Developer, Code Quality/Security) → SIT (API, Integration, Database, Middleware, End-to-End, Negative, Regression Pack v1–v16) → UAT (Business Process, Customer Journey, Financial/Accounting Validation, Business Acceptance) → PRE-PROD (Production-like Smoke, Performance, Security, DR/Recovery, Operational Readiness, Deployment/Rollback) → PROD (Smoke, Sanity, Monitoring Verification, Business Validation). Results recorded in `TestCases-v16.5.md` under "## TP01 Pipeline Results — v16.5" using the TP01 stage table. All Critical/High rows must Pass before Sprint 7 sign-off (TP01 R1–R3).
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7; all TP01 pipeline stages Pass (Critical/High) per TP01 R3; WSDL/endpoint contract confirmed unchanged.
**Enterprise Outcome:** Version 16.5 test coverage complete.

---

### Sprint 7
**Sprint Goal:** Sign off Version 16.5.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Administration:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v16.5.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 16.5 signed off.

---

### Sprint 8
**Sprint Goal:** Fault Injection + Incident Simulation for Version 16.5.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Administration:** Phase 1 — inject a realistic fault tied to this version's topic (e.g., corrupt the servlet's cached-result key so page 2+ silently returns page 1's data). Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v16.5.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 16.5 fault drill complete. Non-gating — does not block sign-off.

---

## Version 16.5 Deliverables
- `digistack-bank-v16.5.ear` (paginated Transaction History screen)
- No new SQL migrations this version
- No WSDL/endpoint changes (confirmed byte-identical to v16)
- SetupDoc-v16.5.md, TestCases-v16.5.md

## Version 16.5 Exit Criteria
- ✅ Application functionality complete (10-per-page pagination with working ← / → navigation)
- ✅ Database validated (N/A — no schema change)
- ✅ WebSphere deployment successful (redeploy confirmed, no WSDL/endpoint drift)
- ✅ TP01 pipeline passed (all 5 stages, all Critical/High rows Pass in TP01 Pipeline Results table)
- ✅ Smoke testing passed (pagination + existing filters both verified together)
- ✅ Ready for Version 17

## Lessons Learned
- **Key learnings:** Pagination belongs entirely in the presentation layer when the backend already returns a complete result set — no new backend service or WSDL change was needed.
- **Known issues:** None expected if Sprint 2's single-fetch-per-filter-change behavior is verified before Sprint 4's navigation controls are wired.
- **Technical debt:** None new — a user-configurable page-size selector was considered and deliberately deferred (fixed at 10 rows for now).

---
---

# Version 17 — Security Hardening

## Version Overview

**Version Objective:** Harden everything already built — MFA/OTP, account lockout, endpoint authentication, and a basic Security Event Detection check — without adding new banking features.

**Business Scope:** No new banking feature — hardens v2's Login, and retroactively secures v16's REST/SOAP endpoints.

**WebSphere Focus:** Global/Application Security, LDAP, LTPA, JAAS, role mapping, CSRF/XSS protection.

**Expected Outcome:** MFA/OTP enforced on login; account locks after N failed attempts; LTPA token validated across the cluster; v16's REST/SOAP endpoints reject unauthenticated calls; a rapid-repeated-transfer test triggers a security audit log entry.

**Prerequisites:** P02 Version 16.5 Completion Checkpoint satisfied — Balance Inquiry/Fund Transfer (REST), Account Statement (SOAP), and paginated Transaction History all live and traceable.

---

### Sprint 1
**Sprint Goal:** Add MFA/OTP onto existing Login.
**Learning Objective:** Extending Global/Application Security with a second authentication factor.
**Business Features:** Login now requires OTP after password.
**Application Development:**
- UI: OTP entry screen post-password
- Backend: OTPService (simulated OTP generation/validation)
- Database: (part of `V17__add_otp_lockout_fields_and_security_audit_log.sql`)
- API: N/A

**WebSphere Administration:**
- No new WAS config yet (application-level OTP logic)
- Redeploy over v16

**Dependencies:** P01 v2 Login/session.
**Deliverables:** Working MFA/OTP login flow.
**Acceptance Criteria:** Login fails without correct OTP; succeeds with correct OTP.
**Enterprise Outcome:** Second-factor authentication proven on top of existing session mechanics.

---

### Sprint 2
**Sprint Goal:** Enforce account lockout after N failed attempts.
**Learning Objective:** Application-level security state tied to WAS session/login flow.
**Business Features:** Account locks after N consecutive failed login attempts.
**Application Development:**
- UI: "Account locked" message
- Backend: LoginAttemptTracker, lockout check in Login flow
- Database: (part of `V17__add_otp_lockout_fields_and_security_audit_log.sql`)
- API: N/A

**WebSphere Administration:**
- Redeploy over Sprint 1 changes

**Dependencies:** Sprint 1's OTP flow.
**Deliverables:** Working account lockout.
**Acceptance Criteria:** N consecutive failed attempts locks the account; correct credentials afterward are still rejected until unlocked.
**Enterprise Outcome:** Brute-force protection proven at the application layer.

---

### Sprint 3
**Sprint Goal:** Configure Global/Application Security, LDAP, and LTPA across the cluster.
**Learning Objective:** WAS Global Security model, LTPA token propagation, SSO across cluster members.
**Business Features:** None (pure middleware hardening).
**Application Development:** N/A this sprint.
**WebSphere Administration:**
- Enable/confirm Global Security + Application Security
- Configure/verify LDAP (or file registry, per P01 v10) as the security realm
- Confirm LTPA token generation and validation across both cluster members

**Dependencies:** P01 v10 Users & Groups (roles/registry).
**Deliverables:** Verified LTPA/SSO configuration.
**Acceptance Criteria:** A login on Cluster Member 1 is recognized as authenticated on Cluster Member 2 without re-login.
**Enterprise Outcome:** SSO proven at the cluster level, foundational for later multi-app/multi-region security work.

---

### Sprint 4
**Sprint Goal:** Secure v16's REST/SOAP endpoints with token/API key authentication.
**Learning Objective:** Retroactively applying JAAS-based authentication to existing service endpoints.
**Business Features:** None (secures existing Balance Inquiry, Fund Transfer, Account Statement).
**Application Development:**
- UI: N/A
- Backend: Authentication filter/interceptor on REST endpoints; SOAP endpoint requires WS-Security token
- Database: N/A
- API: All three v16 endpoints now reject unauthenticated calls

**WebSphere Administration:**
- Configure JAAS login module for endpoint authentication
- Verify via Admin Console that unauthenticated requests are rejected

**Dependencies:** P02 v16 endpoints, Sprint 3's LTPA/security realm.
**Deliverables:** Authenticated REST/SOAP endpoints.
**Acceptance Criteria:** Postman/SoapUI calls without a valid token/API key are rejected (401/403); calls with a valid token succeed as before.
**Enterprise Outcome:** Previously open endpoints now enforce authentication, closing a real security gap before P03's channel simulators consume them.

---

### Sprint 5
**Sprint Goal:** Add CSRF/XSS protection and Secure Cookies.
**Learning Objective:** Web-tier security hardening beyond authentication.
**Business Features:** None (cross-cutting hardening).
**Application Development:**
- UI: CSRF token embedded in forms (Login, Fund Transfer, Beneficiary registration)
- Backend: CSRF token validation filter; output encoding for XSS prevention
- Database: N/A
- API: N/A

**WebSphere Administration:**
- Configure Secure Cookies (HttpOnly, Secure flags) at the session management level

**Dependencies:** Existing UI forms (P01 v2, P02 v15 Beneficiary/Fund Transfer).
**Deliverables:** CSRF-protected forms, secure session cookies.
**Acceptance Criteria:** A form submission without a valid CSRF token is rejected; session cookie confirmed HttpOnly/Secure via browser dev tools.
**Enterprise Outcome:** Standard web-tier attack surface (CSRF/XSS) closed across all existing forms.

---

### Sprint 6
**Sprint Goal:** Implement Security Event Detection and write test cases for Version 17.
**Learning Objective:** Basic threshold-based audit logging (not a rules engine or ML — exactly one check); Test Case discipline (TCS01/TCS02).
**Business Features:** Rapid repeated Fund Transfers raise a security audit log entry.
**Application Development:**
- UI: N/A
- Backend: SecurityAuditService — threshold check on repeated Fund Transfer attempts within a time window
- Database: (part of `V17__add_otp_lockout_fields_and_security_audit_log.sql`)
- API: N/A

**WebSphere Administration:**
- Redeploy final EAR for this version, `digistack-bank-v17.ear`
- Confirm audit log entries written to standard log location (per SOE01 §12)

**Dependencies:** P02 v15 Fund Transfer, Sprint 4's authenticated endpoints.
**Deliverables:** Security Event Detection working; `TestCases-v17.md`.
**TP01 Pipeline (mandatory, per TP01_Test_Pipeline.md):** Sprint 6 executes the full 5-stage test pipeline — DEV (Unit/Component, Developer, Code Quality/Security) → SIT (API, Integration, Database, Middleware, End-to-End, Negative, Regression Pack v1–v<N-1>) → UAT (Business Process, Customer Journey, Financial/Accounting Validation, Business Acceptance) → PRE-PROD (Production-like Smoke, Performance, Security, DR/Recovery, Operational Readiness, Deployment/Rollback) → PROD (Smoke, Sanity, Monitoring Verification, Business Validation). Results recorded in `TestCases-v<N>.md` under "## TP01 Pipeline Results — v<N>" using the TP01 stage table. All Critical/High rows must Pass before Sprint 7 sign-off (TP01 R1–R3).
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7; all TP01 pipeline stages Pass (Critical/High) per TP01 R3.
**Enterprise Outcome:** Version 17 test coverage complete — MFA/lockout/LTPA/endpoint auth/CSRF-XSS/audit detection all proven.

---

### Sprint 7
**Sprint Goal:** Sign off Version 17.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Administration:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v17.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 17 signed off.

---

### Sprint 8
**Sprint Goal:** Fault Injection + Incident Simulation for Version 17.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Administration:** Phase 1 — inject a realistic fault tied to this version's topic (e.g., let LTPA keys drift out of sync between cluster members, breaking SSO). Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v17.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 17 fault drill complete. Non-gating — does not block sign-off.

---

## Version 17 Deliverables
- `digistack-bank-v17.ear` (MFA/OTP, lockout, secured REST/SOAP endpoints, CSRF/XSS protection, security audit logging)
- `V17__add_otp_lockout_fields_and_security_audit_log.sql`
- LTPA/LDAP configuration exports, JAAS login module config, Secure Cookie config
- SetupDoc-v17.md, TestCases-v17.md

## Version 17 Exit Criteria
- ✅ Application functionality complete (MFA/lockout/endpoint auth/CSRF-XSS/audit detection)
- ✅ Database validated (V17 migration applied and verified)
- ✅ WebSphere deployment successful (Global/Application Security, LTPA/SSO confirmed cluster-wide)
- ✅ TP01 pipeline passed (all 5 stages, all Critical/High rows Pass in TP01 Pipeline Results table)
- ✅ Smoke testing passed (unauthenticated endpoint calls rejected, MFA/lockout/CSRF all proven)
- ✅ Ready for Version 18

## Lessons Learned
- **Key learnings:** Retroactively securing already-deployed endpoints (Sprint 4) is a good practice run for the kind of hardening pass real production systems require after initial feature delivery.
- **Known issues:** None expected if Sprint 3's LTPA/SSO verification is completed before Sprint 4's endpoint authentication work begins.
- **Technical debt:** None new — Security Event Detection remains a single threshold check by design; a fuller fraud-detection capability is out of scope for this roadmap entirely (never planned to expand).

---
---

# Version 18 — Monitoring & Logging

## Version Overview

**Version Objective:** Build a live Operations Dashboard reading JVM health, session count, JMS queue depth, and DB pool usage via PMI/JMX — a viewer over infrastructure that already exists, not a new feature.

**Business Scope:** No new banking functionality. One Operations Dashboard consuming existing infrastructure signals.

**WebSphere Focus:** HPEL, SystemOut, SystemErr, FFDC, PMI, JMX, Heap Dump, Thread Dump, GC Logs, Log Rotation.

**Expected Outcome:** Operations Dashboard shows live JVM health, session count, JMS queue depth, and DB pool usage via PMI/JMX; a manually triggered thread dump and heap dump are captured and reviewed; log rotation confirmed working on SystemOut/SystemErr.

**Prerequisites:** P02 Version 17 Completion Checkpoint satisfied — MFA/lockout/LTPA/endpoint auth/CSRF-XSS/audit detection all operational.

---

### Sprint 1
**Sprint Goal:** Enable PMI and confirm JMX access to WAS runtime data.
**Learning Objective:** PMI (Performance Monitoring Infrastructure) and JMX fundamentals.
**Business Features:** None (pure middleware).
**Application Development:** N/A this sprint.
**WebSphere Administration:**
- Enable PMI on both cluster members (Basic → Extended monitoring level)
- Confirm JMX connectivity to each cluster member's MBean server

**Dependencies:** P01 v5 cluster.
**Deliverables:** PMI enabled cluster-wide; verified JMX connection.
**Acceptance Criteria:** A JMX client (e.g., JConsole or a test script) successfully reads a live PMI counter (e.g., JVM heap used) from each cluster member.
**Enterprise Outcome:** Runtime data source proven available before any dashboard is built on top of it.

---

### Sprint 2
**Sprint Goal:** Build the JVM health panel of the Operations Dashboard.
**Learning Objective:** Reading JVM heap/GC/thread pool data via PMI/JMX from application code.
**Business Features:** Operations Dashboard — JVM Health panel.
**Application Development:**
- UI: Operations Dashboard skeleton, JVM Health panel (heap used/max, thread pool utilization)
- Backend: JMXMetricsService — polls PMI counters via JMX
- Database: N/A
- API: N/A

**WebSphere Administration:**
- No new config (consumes Sprint 1's PMI/JMX setup)

**Dependencies:** Sprint 1's PMI/JMX access.
**Deliverables:** Working JVM Health panel.
**Acceptance Criteria:** Panel shows live heap usage that visibly changes under generated load.
**Enterprise Outcome:** First real-time infrastructure view built directly on WAS-native monitoring data.

---

### Sprint 3
**Sprint Goal:** Add session count and JMS queue depth panels.
**Learning Objective:** Extending JMX polling to session management and SIBus metrics.
**Business Features:** Operations Dashboard — Session Count and JMS Queue Depth panels.
**Application Development:**
- UI: Session Count panel, JMS Queue Depth panel (reads P02 v15's `BANK.FUNDTRANSFER.Q`)
- Backend: Extend JMXMetricsService for session MBeans and SIBus queue depth MBeans
- Database: N/A
- API: N/A

**WebSphere Administration:**
- No new config (reuses Sprint 1's PMI/JMX + P02 v15's SIBus)

**Dependencies:** Sprint 2's dashboard skeleton, P02 v15 SIBus.
**Deliverables:** Session Count and JMS Queue Depth panels live.
**Acceptance Criteria:** Session count reflects active logged-in users; queue depth visibly increases during a burst of Fund Transfers.
**Enterprise Outcome:** Dashboard now surfaces both web-tier and messaging-tier health in one place.

---

### Sprint 4
**Sprint Goal:** Add DB connection pool usage panel.
**Learning Objective:** Reading JDBC connection pool metrics via PMI.
**Business Features:** Operations Dashboard — DB Pool Usage panel.
**Application Development:**
- UI: DB Pool Usage panel (active/free connections, wait time)
- Backend: Extend JMXMetricsService for DataSource pool MBeans
- Database: N/A
- API: N/A

**WebSphere Administration:**
- No new config (reuses P01 v7's DataSource)

**Dependencies:** Sprint 3's dashboard, P01 v7 JNDI DataSource.
**Deliverables:** Complete 4-panel Operations Dashboard (JVM, Session, Queue, DB Pool).
**Acceptance Criteria:** Pool usage panel shows active connection count rising under simulated concurrent load.
**Enterprise Outcome:** Operations Dashboard now covers every infrastructure layer this version set out to expose.

---

### Sprint 5
**Sprint Goal:** Capture and review a manual thread dump and heap dump.
**Learning Objective:** HPEL, FFDC, heap/thread dump generation and analysis.
**Business Features:** None (pure operational exercise).
**Application Development:** N/A this sprint.
**WebSphere Administration:**
- Trigger a manual thread dump via Admin Console during simulated load
- Trigger a manual heap dump via Admin Console
- Review both dumps, correlate against FFDC logs and HPEL viewer output

**Dependencies:** Sprint 1–4's dashboard providing context for when to trigger dumps.
**Deliverables:** Captured thread dump and heap dump, with a documented review.
**Acceptance Criteria:** Both dumps are successfully generated and at least one thread/heap observation is documented (e.g., active thread count matches dashboard's session/queue activity at capture time).
**Enterprise Outcome:** First hands-on diagnostic capture exercise, directly reusable for later troubleshooting scenarios (P03.1 Interview-4).

---

### Sprint 6
**Sprint Goal:** Confirm log rotation and write test cases for Version 18.
**Learning Objective:** Log Rotation discipline on SystemOut/SystemErr; Test Case discipline (TCS01/TCS02).
**Business Features:** None (validation sprint).
**Application Development:** Bug-fix only, no new work.
**WebSphere Administration:**
- Verify SystemOut/SystemErr log rotation is active and correctly configured (per STD Golden Rule — rotation only, no manual deletion)
- Redeploy final EAR for this version, `digistack-bank-v18.ear`

**Dependencies:** Sprints 1–5 complete.
**Deliverables:** `TestCases-v18.md`.
**TP01 Pipeline (mandatory, per TP01_Test_Pipeline.md):** Sprint 6 executes the full 5-stage test pipeline — DEV (Unit/Component, Developer, Code Quality/Security) → SIT (API, Integration, Database, Middleware, End-to-End, Negative, Regression Pack v1–v<N-1>) → UAT (Business Process, Customer Journey, Financial/Accounting Validation, Business Acceptance) → PRE-PROD (Production-like Smoke, Performance, Security, DR/Recovery, Operational Readiness, Deployment/Rollback) → PROD (Smoke, Sanity, Monitoring Verification, Business Validation). Results recorded in `TestCases-v<N>.md` under "## TP01 Pipeline Results — v<N>" using the TP01 stage table. All Critical/High rows must Pass before Sprint 7 sign-off (TP01 R1–R3).
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7; all TP01 pipeline stages Pass (Critical/High) per TP01 R3.
**Enterprise Outcome:** Version 18 test coverage complete.

---

### Sprint 7
**Sprint Goal:** Sign off Version 18.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Administration:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v18.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 18 signed off.

---

### Sprint 8
**Sprint Goal:** Fault Injection + Incident Simulation for Version 18.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Administration:** Phase 1 — inject a realistic fault tied to this version's topic (e.g., disable PMI on one cluster member, causing that member's dashboard panel to silently go stale). Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v18.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 18 fault drill complete. Non-gating — does not block sign-off.

---

## Version 18 Deliverables
- `digistack-bank-v18.ear` (Operations Dashboard: JVM Health, Session Count, JMS Queue Depth, DB Pool Usage panels)
- No new SQL migrations this version
- PMI configuration exports, captured thread dump/heap dump samples
- SetupDoc-v18.md, TestCases-v18.md

## Version 18 Exit Criteria
- ✅ Application functionality complete (4-panel Operations Dashboard live)
- ✅ Database validated (no schema change; DB pool panel confirmed reading live pool metrics)
- ✅ WebSphere deployment successful (PMI/JMX enabled and confirmed cluster-wide)
- ✅ TP01 pipeline passed (all 5 stages, all Critical/High rows Pass in TP01 Pipeline Results table)
- ✅ Smoke testing passed (all four panels verified live; thread/heap dump captured and reviewed; log rotation confirmed)
- ✅ Ready for Version 19

## Lessons Learned
- **Key learnings:** PMI/JMX exposes nearly everything needed for a meaningful operations view without any third-party tooling — a good foundation before Prometheus/Grafana formalizes this at P04 v31.
- **Known issues:** None expected if Sprint 1's PMI enablement is verified cluster-wide before building any panel on top of it.
- **Technical debt:** None new — this custom dashboard is explicitly a stepping-stone; per P04 v31's note, it is formally superseded and retired once Prometheus/Grafana reaches equivalent coverage. This is a planned, documented retirement, not oversight.

---
---

# Version 19 — IBM MQ Integration

## Version Overview

**Version Objective:** Extend v15's Fund Transfer with an external leg — a transfer to an "external bank" beneficiary routes through IBM MQ instead of the internal SIBus queue, simulating a Payment Request sent to and Payment Response received from an external banking system.

**Business Scope:** No new banking feature — extends existing Fund Transfer with an external-routing path.

**WebSphere Focus:** IBM MQ, Queue Manager, Local Queue, Remote Queue, Transmission Queue, Channels, Listener, Triggering, MQ JMS, MQ Connection Factory, Dead Letter Queue, MQ Monitoring.

**Expected Outcome:** IBM MQ Queue Manager created and connected via MQ JMS Connection Factory; an "external" Fund Transfer sends a Payment Request message to an external banking system simulator and receives a Payment Response back through a dedicated response queue.

**Prerequisites:** P02 Version 18 Completion Checkpoint satisfied — Operations Dashboard (JVM/Session/Queue/DB Pool) live; log rotation confirmed.

---

### Sprint 1
**Sprint Goal:** Install and configure the IBM MQ Queue Manager.
**Learning Objective:** IBM MQ Queue Manager fundamentals — creation, basic configuration.
**Business Features:** None (pure middleware).
**Application Development:** N/A this sprint.
**WebSphere Administration:**
- Install IBM MQ Advanced for Developers on `dsb-mq` (per STD VM Hostnames)
- Create Queue Manager, confirm it starts and is reachable

**Dependencies:** STD §VM Hostnames (`dsb-mq`), SOE01 §9 (MQ version pin).
**Deliverables:** Running IBM MQ Queue Manager.
**Acceptance Criteria:** Queue Manager status = Running; reachable via `runmqsc` locally.
**Enterprise Outcome:** External messaging backbone stood up independently of SIBus, ready for WAS integration.

---

### Sprint 2
**Sprint Goal:** Create local, remote, and transmission queues for the external payment flow.
**Learning Objective:** Distinguishing local vs. remote vs. transmission queue roles.
**Business Features:** None (pure middleware).
**Application Development:** N/A this sprint.
**WebSphere Administration:**
- Create `BANK.PAYMENT.REQUEST.Q` (local), `BANK.PAYMENT.RESPONSE.Q` (local)
- Create a transmission queue and remote queue definition simulating the "external bank" endpoint
- Configure a sender/receiver channel pair

**Dependencies:** Sprint 1's Queue Manager.
**Deliverables:** Request/response queue pair, channel configuration.
**Acceptance Criteria:** A test message sent to the request queue is confirmed transmitted across the channel to the simulated external endpoint.
**Enterprise Outcome:** Queue topology proven before any application code depends on it.

---

### Sprint 3
**Sprint Goal:** Configure MQ JMS Connection Factory and JNDI bindings in WAS.
**Learning Objective:** Integrating IBM MQ with WebSphere via MQ JMS resources.
**Business Features:** None (pure middleware).
**Application Development:** N/A this sprint.
**WebSphere Administration:**
- Create MQ JMS Connection Factory in WAS, bound to JNDI
- Bind `BANK.PAYMENT.REQUEST.Q` and `BANK.PAYMENT.RESPONSE.Q` to JNDI names

**Dependencies:** Sprint 2's queues, P01 v7's JNDI pattern precedent.
**Deliverables:** MQ Connection Factory and Queue JNDI bindings operational in WAS.
**Acceptance Criteria:** A simple JMS test client running inside WAS successfully sends/receives a message via the JNDI-bound MQ resources.
**Enterprise Outcome:** WAS-to-MQ integration proven at the resource-configuration level, before business logic is layered on top.

---

### Sprint 4
**Sprint Goal:** Route "external" Fund Transfers through IBM MQ instead of SIBus.
**Learning Objective:** Conditional routing logic — internal vs. external payment leg.
**Business Features:** A Fund Transfer whose Beneficiary is flagged "external bank" routes via IBM MQ.
**Application Development:**
- UI: Beneficiary flag ("external bank") surfaced in the existing registration form
- Backend: FundTransferService updated to check the Beneficiary flag and route to MQ (via Sprint 3's Connection Factory) instead of SIBus when external
- Database: `V19__add_external_flag_to_beneficiary.sql`
- API: N/A (reuses v16's REST endpoint)

**WebSphere Administration:**
- Redeploy over v18

**Dependencies:** P02 v15 Beneficiary/Fund Transfer, Sprint 3's MQ JNDI bindings.
**Deliverables:** Working conditional routing (internal → SIBus, external → MQ).
**Acceptance Criteria:** A transfer to an internal beneficiary still uses SIBus (unchanged from v15); a transfer to an external-flagged beneficiary sends a message on `BANK.PAYMENT.REQUEST.Q`.
**Enterprise Outcome:** First dual-messaging-path banking transaction, proving the app can route to the correct backbone based on business rules.

---

### Sprint 5
**Sprint Goal:** Build the external banking system simulator and complete the response leg.
**Learning Objective:** Simulating an external counterparty system consuming/producing MQ messages.
**Business Features:** Payment Response received back and applied to the Fund Transfer's status.
**Application Development:**
- UI: Fund Transfer status reflects response from external simulator (PROCESSED/FAILED)
- Backend: ExternalBankSimulatorMDB — consumes from `BANK.PAYMENT.REQUEST.Q`, simulates processing, sends a Payment Response to `BANK.PAYMENT.RESPONSE.Q`; PaymentResponseConsumerMDB — consumes response, updates Fund Transfer status
- Database: N/A (reuses v15's fund_transfer table)
- API: N/A

**WebSphere Administration:**
- Deploy both MDB modules, bound to the Sprint 3 Activation Specs/queues

**Dependencies:** Sprint 4's routing, Sprint 3's JNDI bindings.
**Deliverables:** End-to-end external Fund Transfer (request → simulator → response → status update).
**Acceptance Criteria:** An external transfer completes with status PROCESSED after round-tripping through the simulator via MQ.
**Enterprise Outcome:** Full external payment integration pattern proven — the same shape a real bank's external clearing/settlement integration would follow.

---

### Sprint 6
**Sprint Goal:** Configure MQ-level DLQ, apply CHLAUTH/SSL, and write test cases for Version 19.
**Learning Objective:** MQ Dead Letter Queue handling, channel authentication and security; Test Case discipline (TCS01/TCS02).
**Business Features:** None (hardening/validation sprint).
**Application Development:** Bug-fix only, no new work.
**WebSphere Administration:**
- Configure MQ-level DLQ on the Queue Manager
- Apply CHLAUTH channel authentication records and SSL/TLS to the MQ channel (building on P01 SSL work and v17's hardening)
- Redeploy final EAR for this version, `digistack-bank-v19.ear`
- Confirm MQ Monitoring shows queue depth/channel status correctly

**Dependencies:** Sprint 5's end-to-end flow, P01 v11/v12 SSL, P02 v17 security hardening.
**Deliverables:** MQ DLQ + CHLAUTH/SSL configured; CI01 §5.2 updated (`digistack-mq-chlauth.crt`); `TestCases-v19.md`.
**TP01 Pipeline (mandatory, per TP01_Test_Pipeline.md):** Sprint 6 executes the full 5-stage test pipeline — DEV (Unit/Component, Developer, Code Quality/Security) → SIT (API, Integration, Database, Middleware, End-to-End, Negative, Regression Pack v1–v<N-1>) → UAT (Business Process, Customer Journey, Financial/Accounting Validation, Business Acceptance) → PRE-PROD (Production-like Smoke, Performance, Security, DR/Recovery, Operational Readiness, Deployment/Rollback) → PROD (Smoke, Sanity, Monitoring Verification, Business Validation). Results recorded in `TestCases-v<N>.md` under "## TP01 Pipeline Results — v<N>" using the TP01 stage table. All Critical/High rows must Pass before Sprint 7 sign-off (TP01 R1–R3).
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7; all TP01 pipeline stages Pass (Critical/High) per TP01 R3.
**Enterprise Outcome:** Version 19 test coverage complete — external payment integration functional and secured.

---

### Sprint 7
**Sprint Goal:** Sign off Version 19.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Administration:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v19.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 19 signed off.

---

### Sprint 8
**Sprint Goal:** Fault Injection + Incident Simulation for Version 19.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Administration:** Phase 1 — inject a realistic fault tied to this version's topic (e.g., stop the MQ channel's listener, causing external transfers to queue but never transmit). Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v19.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 19 fault drill complete. Non-gating — does not block sign-off.

---

## Version 19 Deliverables
- `digistack-bank-v19.ear` (external Fund Transfer routing, external bank simulator MDBs)
- `V19__add_external_flag_to_beneficiary.sql`
- IBM MQ Queue Manager, queue/channel configuration exports, MQ JMS Connection Factory/JNDI bindings, CHLAUTH/SSL config
- SetupDoc-v19.md, TestCases-v19.md

## Version 19 Exit Criteria
- ✅ Application functionality complete (internal → SIBus, external → MQ routing both proven)
- ✅ Database validated (V21 migration applied and verified)
- ✅ WebSphere deployment successful (MQ Queue Manager, channels, JNDI bindings all operational)
- ✅ TP01 pipeline passed (all 5 stages, all Critical/High rows Pass in TP01 Pipeline Results table)
- ✅ Smoke testing passed (end-to-end external transfer completes; DLQ and CHLAUTH/SSL both proven)
- ✅ Ready for Version 20

## Lessons Learned
- **Key learnings:** Running SIBus and IBM MQ side-by-side, routed by a simple business flag, is a clean way to practice both messaging technologies without them colliding — this dual-path pattern recurs later at P09 v66's SQS/SNS decision.
- **Known issues:** None expected if Sprint 2's queue/channel topology is verified before Sprint 3's WAS-side JNDI work begins.
- **Technical debt:** None new — the external banking system simulator is intentionally minimal (accepts/echoes a response); a fuller ISO 8583-style switch is out of scope for this roadmap.

---
---

# Version 20 — IBM HTTP Server Advanced Administration

## Version Overview

**Version Objective:** Apply advanced IHS administration to the existing single-app topology — URL rewrite, maintenance page toggling, a health check URL, SSL termination, compression, and KeepAlive — with zero new banking functionality.

**Business Scope:** No new banking functionality. Applies purely to the existing app already fronted by IHS since P01 v8.

**WebSphere Focus:** IBM HTTP Server, Apache Modules, Plugin Configuration, plugin-cfg.xml, Reverse Proxy, Virtual Hosts, URL Rewrite, SSL Termination, Compression, KeepAlive.

**Expected Outcome:** URL rewrite rule demonstrated on an existing path; maintenance page toggled on/off without a WAS restart; health check URL confirmed usable by an external monitor.

**Prerequisites:** P02 Version 19 Completion Checkpoint satisfied — IBM MQ external Fund Transfer leg operational, DLQ/CHLAUTH/SSL proven.

---

### Sprint 1
**Sprint Goal:** Configure a URL rewrite rule on an existing path.
**Learning Objective:** Apache mod_rewrite fundamentals within IHS.
**Business Features:** None (pure middleware).
**Application Development:** N/A this sprint.
**WebSphere Administration:**
- Enable mod_rewrite on IHS
- Configure a rewrite rule on an existing path (e.g., friendly URL → actual servlet path)

**Dependencies:** P01 v8 IHS install.
**Deliverables:** Working URL rewrite rule.
**Acceptance Criteria:** Browser hit on the friendly URL correctly resolves to the underlying servlet path, confirmed via IHS access log.
**Enterprise Outcome:** First Apache-module-level customization proven on top of the existing reverse proxy setup.

---

### Sprint 2
**Sprint Goal:** Build a maintenance page that can be toggled without touching WAS.
**Learning Objective:** IHS-level maintenance mode independent of the application server lifecycle.
**Business Features:** None (pure middleware).
**Application Development:**
- UI: Static maintenance.html page
- Backend: N/A
- Database: N/A
- API: N/A

**WebSphere Administration:**
- Configure IHS to serve maintenance.html via a conditional rewrite/redirect toggle (e.g., a marker file check)

**Dependencies:** Sprint 1's rewrite configuration.
**Deliverables:** Maintenance page + toggle mechanism.
**Acceptance Criteria:** Toggling maintenance mode on serves maintenance.html to all requests without restarting WAS; toggling off restores normal routing.
**Enterprise Outcome:** Real operational capability proven — taking the app "offline" at the web tier without touching the app server.

---

### Sprint 3
**Sprint Goal:** Add a health check URL usable by an external monitor.
**Learning Objective:** Health check endpoint configuration at the IHS layer (precursor to v21's Load Balancer health checks).
**Business Features:** None (pure middleware).
**Application Development:**
- UI: N/A
- Backend: Lightweight health servlet (returns 200 OK if app reachable)
- Database: N/A
- API: `GET /health` (IHS-fronted)

**WebSphere Administration:**
- Configure IHS routing for the health check path, bypassing maintenance-mode toggle from Sprint 2 (health checks should still respond even during maintenance)

**Dependencies:** Sprint 2's maintenance toggle.
**Deliverables:** Working health check URL.
**Acceptance Criteria:** `/health` returns 200 OK under normal operation; a simulated external monitor (curl loop) confirms consistent reachability.
**Enterprise Outcome:** Health check foundation established ahead of v21's Load Balancer integration.

---

### Sprint 4
**Sprint Goal:** Configure SSL termination at IHS.
**Learning Objective:** SSL termination point decisions — where encryption ends vs. continues.
**Business Features:** None (pure middleware).
**Application Development:** N/A this sprint.
**WebSphere Administration:**
- Confirm/reconfigure SSL termination behavior at IHS (building on P01 v11/v12's end-to-end SSL)
- Document where termination occurs vs. where mTLS continues to WAS

**Dependencies:** P01 v11/v12 SSL configuration.
**Deliverables:** Documented and verified SSL termination point.
**Acceptance Criteria:** Browser confirms HTTPS terminates correctly at IHS; internal hop to WAS still enforces mTLS per P01 v12.
**Enterprise Outcome:** SSL topology clarified and re-verified as IHS administration matures beyond basic HTTPS enforcement.

---

### Sprint 5
**Sprint Goal:** Enable compression and tune KeepAlive.
**Learning Objective:** Performance-oriented Apache module configuration.
**Business Features:** None (pure middleware).
**Application Development:** N/A this sprint.
**WebSphere Administration:**
- Enable mod_deflate (or equivalent) compression for static assets and text responses
- Tune KeepAlive settings (timeout, max requests per connection)

**Dependencies:** Sprint 4's SSL configuration.
**Deliverables:** Compression and KeepAlive tuning applied.
**Acceptance Criteria:** Response headers confirm compression is active for eligible content types; KeepAlive behavior confirmed via connection reuse in a load test snippet.
**Enterprise Outcome:** First explicit performance-tuning pass at the web tier, independent of JVM-level tuning already done in P01 v14.

---

### Sprint 6
**Sprint Goal:** Validate all advanced IHS features together and write test cases for Version 20.
**Learning Objective:** End-to-end validation of multiple concurrent IHS-layer changes; Test Case discipline (TCS01/TCS02).
**Business Features:** None (validation sprint).
**Application Development:** Bug-fix only, no new work.
**WebSphere Administration:**
- Redeploy final EAR for this version, `digistack-bank-v20.ear`
- Confirm plugin-cfg.xml still correctly reflects cluster topology after all Sprint 1–5 changes
- Final validation pass: URL rewrite, maintenance toggle, health check, SSL termination, compression/KeepAlive all together

**Dependencies:** Sprints 1–5 complete.
**Deliverables:** `TestCases-v20.md`.
**TP01 Pipeline (mandatory, per TP01_Test_Pipeline.md):** Sprint 6 executes the full 5-stage test pipeline — DEV (Unit/Component, Developer, Code Quality/Security) → SIT (API, Integration, Database, Middleware, End-to-End, Negative, Regression Pack v1–v<N-1>) → UAT (Business Process, Customer Journey, Financial/Accounting Validation, Business Acceptance) → PRE-PROD (Production-like Smoke, Performance, Security, DR/Recovery, Operational Readiness, Deployment/Rollback) → PROD (Smoke, Sanity, Monitoring Verification, Business Validation). Results recorded in `TestCases-v<N>.md` under "## TP01 Pipeline Results — v<N>" using the TP01 stage table. All Critical/High rows must Pass before Sprint 7 sign-off (TP01 R1–R3).
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7; all TP01 pipeline stages Pass (Critical/High) per TP01 R3.
**Enterprise Outcome:** Version 20 test coverage complete.

---

### Sprint 7
**Sprint Goal:** Sign off Version 20.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Administration:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v20.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 20 signed off.

---

### Sprint 8
**Sprint Goal:** Fault Injection + Incident Simulation for Version 20.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Administration:** Phase 1 — inject a realistic fault tied to this version's topic (e.g., leave a stale maintenance-mode marker file in place after a deploy, silently blocking all live traffic). Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v20.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 20 fault drill complete. Non-gating — does not block sign-off.

---

## Version 20 Deliverables
- `digistack-bank-v20.ear` (adds health check servlet; URL rewrite, maintenance mode, health check, SSL termination, compression/KeepAlive all configured at the IHS layer around it)
- IHS configuration exports (rewrite rules, maintenance toggle, health check routing, SSL termination, compression/KeepAlive settings)
- SetupDoc-v20.md, TestCases-v20.md

## Version 20 Exit Criteria
- ✅ Application functionality complete (no new banking functionality; all five IHS features proven)
- ✅ Database validated (no schema change this version)
- ✅ WebSphere deployment successful (plugin-cfg.xml confirmed accurate post-change)
- ✅ TP01 pipeline passed (all 5 stages, all Critical/High rows Pass in TP01 Pipeline Results table)
- ✅ Smoke testing passed (rewrite, maintenance toggle, health check, SSL termination, compression/KeepAlive all verified together)
- ✅ Ready for Version 21

## Lessons Learned
- **Key learnings:** IHS administration extends well beyond "reverse proxy exists" — rewrite rules, maintenance mode, and performance tuning are all genuinely separate admin skills worth practicing distinctly.
- **Known issues:** None expected if Sprint 2's maintenance toggle is verified not to block Sprint 3's health check path.
- **Technical debt:** None new — this version is purely additive configuration on the existing IHS install, no deferred items.

---
---

# Version 21 — Enterprise Load Balancer

## Version Overview

**Version Objective:** Front the existing IHS tier with an enterprise load balancer (NGINX/HAProxy as F5/Citrix ADC stand-in), proving Layer-4/Layer-7 load balancing, health checks, sticky sessions, SSL offloading, and blue-green deployment.

**Business Scope:** No new banking functionality. NGINX (or chosen LB) sits in front of the existing IHS tier from v20; the "deployment" being blue-green tested is a trivial, cosmetic change to the existing app, reusing P01 v4's rollback discipline.

**WebSphere Focus:** Layer-4 Load Balancing, Layer-7 Load Balancing, Health Checks, Sticky Sessions, SSL Offloading, Session Persistence, Blue-Green Deployment, High Availability.

**Expected Outcome:** NGINX fronts two IHS instances with active health checks; a blue-green deployment of a trivial change is performed with zero customer-visible downtime; a simulated IHS node failure is automatically routed around.

**Prerequisites:** P02 Version 20 Completion Checkpoint satisfied — IHS advanced administration (rewrite, maintenance mode, health check, SSL termination, compression/KeepAlive) all proven.

---

### Sprint 1
**Sprint Goal:** Install and configure NGINX in front of the existing single IHS instance.
**Learning Objective:** Enterprise LB installation and basic Layer-7 reverse proxy configuration.
**Business Features:** None (pure middleware).
**Application Development:** N/A this sprint.
**WebSphere Administration:**
- Install NGINX (F5/Citrix ADC stand-in, per STD Licensing Reality Check)
- Configure basic Layer-7 reverse proxy pointing to existing IHS instance

**Dependencies:** P02 v20's IHS install.
**Deliverables:** NGINX installed and proxying to IHS.
**Acceptance Criteria:** Browser hit through NGINX correctly reaches the app via IHS, confirmed via response headers/access log.
**Enterprise Outcome:** First enterprise LB tier stood up ahead of any multi-instance IHS topology.

---

### Sprint 2
**Sprint Goal:** Stand up a second IHS instance and configure Layer-4/Layer-7 load balancing across both.
**Learning Objective:** Distinguishing Layer-4 vs. Layer-7 load balancing decisions.
**Business Features:** None (pure middleware).
**Application Development:** N/A this sprint.
**WebSphere Administration:**
- Provision second IHS instance (mirroring the first)
- Configure NGINX upstream pool with both IHS instances, using round-robin or least-connections

**Dependencies:** Sprint 1's NGINX config.
**Deliverables:** Two-IHS-instance load-balanced pool.
**Acceptance Criteria:** Repeated requests through NGINX are distributed across both IHS instances, confirmed via access logs on each.
**Enterprise Outcome:** True multi-instance web-tier redundancy proven, not just a single reverse-proxy hop.

---

### Sprint 3
**Sprint Goal:** Configure active health checks and automatic traffic routing.
**Learning Objective:** LB-driven health monitoring and automatic server removal.
**Business Features:** None (pure middleware).
**Application Development:** N/A this sprint.
**WebSphere Administration:**
- Configure NGINX active health checks against v20's `/health` endpoint
- Verify automatic removal of an unhealthy IHS instance from the pool

**Dependencies:** P02 v20's health check URL, Sprint 2's two-instance pool.
**Deliverables:** Working health-check-driven routing.
**Acceptance Criteria:** Stopping one IHS instance causes NGINX to stop routing to it within the configured health check interval, and traffic continues uninterrupted via the remaining instance.
**Enterprise Outcome:** First proven automatic failover at the load balancer tier.

---

### Sprint 4
**Sprint Goal:** Configure sticky sessions / session affinity.
**Learning Objective:** Cookie-based routing at the LB layer, distinguished from WebSphere's own session replication.
**Business Features:** None (pure middleware).
**Application Development:** N/A this sprint.
**WebSphere Administration:**
- Configure NGINX sticky-session routing (duration-based cookie)
- Document the distinction: sticky sessions keep a user on one IHS/backend path, while WAS's own session replication (P01 v5/v9) is what actually protects session data if that path disappears

**Dependencies:** Sprint 3's health-check-driven pool.
**Deliverables:** Sticky session routing configured and documented.
**Acceptance Criteria:** A logged-in user's subsequent requests consistently route to the same IHS instance, confirmed via access logs, while a killed instance still fails over cleanly (per Sprint 3).
**Enterprise Outcome:** LB-level session affinity proven as a distinct concern from WAS-level session replication.

---

### Sprint 5
**Sprint Goal:** Configure SSL offloading at the LB tier.
**Learning Objective:** Deciding where SSL terminates when an LB sits in front of IHS's own SSL termination (v20).
**Business Features:** None (pure middleware).
**Application Development:** N/A this sprint.
**WebSphere Administration:**
- Configure NGINX to terminate SSL (offloading), re-encrypting or passing through to IHS per documented decision
- Update SetupDoc note clarifying the full SSL topology (browser → NGINX → IHS → WAS)

**Dependencies:** P02 v20's SSL termination at IHS, Sprint 1's NGINX config.
**Deliverables:** SSL offloading configured and documented.
**Acceptance Criteria:** Browser confirms HTTPS terminates at NGINX; downstream hop to IHS is confirmed per the documented decision (re-encrypted or internal-only).
**Enterprise Outcome:** Full, unambiguous SSL topology across LB → IHS → WAS documented for the first time.

---

### Sprint 6
**Sprint Goal:** Perform a blue-green deployment and write test cases for Version 21.
**Learning Objective:** Blue-Green Deployment mechanics at the LB tier; Test Case discipline (TCS01/TCS02).
**Business Features:** None (validation sprint) — trivial cosmetic change (e.g., version label) as the deployment payload.
**Application Development:**
- UI: Trivial version-label change (reuses P01 v4's rollback discipline)
- Backend: N/A
- Database: N/A
- API: N/A

**WebSphere Administration:**
- Deploy the trivial change to one IHS/WAS path ("green") while the other ("blue") continues serving live traffic
- Cut over NGINX to the green path, confirm zero customer-visible downtime, keep blue as rollback target

**Dependencies:** Sprints 1–5 complete, P01 v4 rollback discipline.
**Deliverables:** Proven blue-green deployment; `TestCases-v21.md`.
**TP01 Pipeline (mandatory, per TP01_Test_Pipeline.md):** Sprint 6 executes the full 5-stage test pipeline — DEV (Unit/Component, Developer, Code Quality/Security) → SIT (API, Integration, Database, Middleware, End-to-End, Negative, Regression Pack v1–v<N-1>) → UAT (Business Process, Customer Journey, Financial/Accounting Validation, Business Acceptance) → PRE-PROD (Production-like Smoke, Performance, Security, DR/Recovery, Operational Readiness, Deployment/Rollback) → PROD (Smoke, Sanity, Monitoring Verification, Business Validation). Results recorded in `TestCases-v<N>.md` under "## TP01 Pipeline Results — v<N>" using the TP01 stage table. All Critical/High rows must Pass before Sprint 7 sign-off (TP01 R1–R3).
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7; all TP01 pipeline stages Pass (Critical/High) per TP01 R3.
**Enterprise Outcome:** Version 21 test coverage complete — full enterprise LB tier proven.

---

### Sprint 7
**Sprint Goal:** Sign off Version 21.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Administration:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v21.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 21 signed off.

---

### Sprint 8
**Sprint Goal:** Fault Injection + Incident Simulation for Version 21.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Administration:** Phase 1 — inject a realistic fault tied to this version's topic (e.g., misconfigure NGINX's health-check interval so a dead IHS instance stays in the pool far longer than expected). Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v21.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 21 fault drill complete. Non-gating — does not block sign-off.

---

## Version 21 Deliverables
- `digistack-bank-v21.ear` (trivial version-label change only, used as the blue-green deployment payload)
- NGINX configuration exports (upstream pool, health checks, sticky sessions, SSL offloading)
- SetupDoc-v21.md, TestCases-v21.md

## Version 21 Exit Criteria
- ✅ Application functionality complete (no new banking functionality; blue-green deployment proven on a trivial change)
- ✅ Database validated (no schema change this version)
- ✅ WebSphere deployment successful (two-IHS-instance topology confirmed load-balanced)
- ✅ TP01 pipeline passed (all 5 stages, all Critical/High rows Pass in TP01 Pipeline Results table)
- ✅ Smoke testing passed (health-check failover, sticky sessions, SSL offloading, blue-green cutover all verified)
- ✅ Ready for Version 22

## Lessons Learned
- **Key learnings:** Distinguishing LB-level sticky sessions from WAS-level session replication is a subtle but important interview-relevant distinction — the LB decides *where* a request goes, WAS decides whether the session *survives* if that destination fails.
- **Known issues:** None expected if Sprint 2's two-instance IHS topology is verified before Sprint 3's health-check work begins.
- **Technical debt:** None new — "Regional Routing" is explicitly out of scope here (single-region simulation only); true multi-region routing is P06's job, not this version's.

---
---

# Version 22 — Enterprise Middleware Integration Capstone

## Version Overview

**Version Objective:** Validate the full P01+P02 stack end-to-end — Deployment Manager, Cell/Node administration, clustering, JDBC, JMS, IBM MQ, IHS, SSL, Security, Monitoring, Load Balancing, Change/Release Management, Backup & Recovery — all working together as one integrated platform.

**Business Scope:** No new banking functionality. Everything exercised already exists: Customer, Account, Balance, Deposit, Withdraw, Freeze/Unfreeze, Beneficiary, Fund Transfer (internal via v15, external via v19), Transaction History/Statement (REST+SOAP via v16), Notifications (P01 v13), Reports (P01 v14).

**WebSphere Focus:** Deployment Manager, Cell Administration, Node Administration, Clustering, JDBC, JMS, IBM MQ, IBM HTTP Server, Security, SSL, Monitoring, Performance Tuning, Thread/Heap Analysis, High Availability, Load Balancing, Deployment Automation, Troubleshooting, Change & Release Management.

**Expected Outcome:** A full end-to-end request (customer performs a Fund Transfer) completes successfully through every layer (LB → IHS → WAS Cluster → App/JMS/MQ/Notifications → PostgreSQL); a mock production incident is worked through a documented runbook; a full backup/recovery of WAS configuration is performed and restored.

**Prerequisites:** P02 Version 21 Completion Checkpoint satisfied — Enterprise LB (health checks, sticky sessions, SSL offloading, blue-green) fully proven.

---

### Sprint 1
**Sprint Goal:** Validate the full end-to-end request flow for an internal Fund Transfer.
**Learning Objective:** Tracing a single business transaction through every architectural layer built so far.
**Business Features:** Internal Fund Transfer (existing, v15/v16).
**Application Development:**
- UI: N/A (validation only)
- Backend: N/A
- Database: N/A
- API: Reuses v16's REST Fund Transfer endpoint

**WebSphere Administration:**
- Trace one Fund Transfer request through LB → IHS → WAS Cluster → SIBus/MDB → PostgreSQL
- Confirm each layer's logs (IHS access log, WAS SystemOut, MDB processing, DB write) correlate to the same transaction

**Dependencies:** All prior P01/P02 versions (v1–v21).
**Deliverables:** Documented end-to-end trace of one internal Fund Transfer.
**Acceptance Criteria:** A single Fund Transfer is followed successfully from LB entry to DB write with no unexplained gaps in the layer-by-layer logs.
**Enterprise Outcome:** Full internal request flow validated as one coherent system, not a set of independently-working pieces.

---

### Sprint 2
**Sprint Goal:** Validate the external Fund Transfer path and Notification/Reporting consumption.
**Learning Objective:** Confirming cross-cutting flows (external MQ leg, event consumption) integrate correctly under the full stack.
**Business Features:** External Fund Transfer (v19), Withdraw email notification (P01 v13), Transaction Report (P01 v14).
**Application Development:**
- UI: N/A
- Backend: N/A
- Database: N/A
- API: N/A

**WebSphere Administration:**
- Trace one external Fund Transfer through IBM MQ round-trip (v19)
- Confirm a Withdraw triggers its email notification correctly (P01 v13) under current security hardening (v17)
- Generate a Transaction Report (P01 v14) and confirm it completes without OOM under current JVM tuning

**Dependencies:** Sprint 1's internal trace, P02 v19 MQ, P01 v13/v14.
**Deliverables:** Validated external transfer trace, notification, and report generation.
**Acceptance Criteria:** External transfer completes via MQ round-trip; email notification delivered; report generates successfully.
**Enterprise Outcome:** Confirms P01's foundational features still work correctly layered underneath everything P02 added.

---

### Sprint 3
**Sprint Goal:** Validate clustering, JDBC, and Operations Dashboard under combined load.
**Learning Objective:** Confirming infrastructure resilience and observability hold up together, not just individually.
**Business Features:** None (validation sprint).
**Application Development:** N/A this sprint.
**WebSphere Administration:**
- Generate combined load (multiple concurrent Fund Transfers, internal + external)
- Confirm cluster session replication (P01 v5/v9) holds during load
- Confirm JDBC pool sizing (P01 v7) doesn't exhaust under combined load
- Confirm Operations Dashboard (v18) accurately reflects live JVM/session/queue/pool state during the test

**Dependencies:** P01 v5/v7/v9, P02 v18.
**Deliverables:** Documented load validation results.
**Acceptance Criteria:** No session loss, no pool exhaustion, and dashboard panels track load changes in near-real-time during the combined test.
**Enterprise Outcome:** Confirms the platform's infrastructure and observability layers scale together under realistic concurrent activity.

---

### Sprint 4
**Sprint Goal:** Work a mock production incident through a documented runbook.
**Learning Objective:** First hands-on incident response exercise using existing detection tools.
**Business Features:** None (operational exercise).
**Application Development:** N/A this sprint.
**WebSphere Administration:**
- Deliberately induce a failure (e.g., kill one cluster member during active Fund Transfer traffic)
- Detect via Operations Dashboard (v18)
- Diagnose using SystemOut/FFDC and Sprint 1's tracing discipline
- Resolve (restart/rejoin cluster member) and document the full detect → diagnose → resolve → document runbook

**Dependencies:** P02 v18 dashboard, P01 v5 clustering, Sprint 1's tracing approach.
**Deliverables:** Documented mock-incident runbook with real detect/diagnose/resolve timestamps.
**Acceptance Criteria:** The induced failure is detected via the dashboard, diagnosed via logs, and resolved with the cluster returning to full health — all captured in the runbook.
**Enterprise Outcome:** First full incident-response cycle exercised end-to-end, direct precedent for P04 v35's formal incident management.

---

### Sprint 5
**Sprint Goal:** Perform a full backup and recovery of the WAS configuration.
**Learning Objective:** backupConfig/restoreConfig discipline at the cell level, combined with DB backup per STD.
**Business Features:** None (operational exercise).
**Application Development:** N/A this sprint.
**WebSphere Administration:**
- Execute `backupConfig` capturing the full current cell configuration
- Execute a PostgreSQL `pg_dump` per STD §Backup
- Deliberately restore from the captured backupConfig onto a test scenario, confirm configuration matches pre-backup state

**Dependencies:** STD §Backup, all prior configuration work (v1–v21).
**Deliverables:** Verified backupConfig + pg_dump backup, confirmed restorable.
**Acceptance Criteria:** A restore from the backupConfig capture reproduces the cell's configuration correctly, confirmed via a diff/comparison against the pre-backup state.
**Enterprise Outcome:** First full backup/recovery cycle proven — not just "backup exists" but "backup is genuinely restorable."

---

### Sprint 6
**Sprint Goal:** Final capstone validation and write test cases for Version 22 / P02 as a whole.
**Learning Objective:** Consolidating and confirming every P02 checklist item passes together; Test Case discipline (TCS01/TCS02).
**Business Features:** None (validation sprint).
**Application Development:** Bug-fix only, no new work.
**WebSphere Administration:**
- Re-run the full P02 Completion Checklist (Fund Transfer internal/external, REST/SOAP, security hardening, dashboard, MQ, IHS advanced admin, LB) as one combined pass
- Redeploy final EAR for this version, `digistack-bank-v22.ear`

**Dependencies:** Sprints 1–5 complete, all of P02 v15–v21.
**Deliverables:** `TestCases-v22.md`; full P02 Completion Checklist signed off.
**TP01 Pipeline (mandatory, per TP01_Test_Pipeline.md):** Sprint 6 executes the full 5-stage test pipeline — DEV (Unit/Component, Developer, Code Quality/Security) → SIT (API, Integration, Database, Middleware, End-to-End, Negative, Regression Pack v1–v<N-1>) → UAT (Business Process, Customer Journey, Financial/Accounting Validation, Business Acceptance) → PRE-PROD (Production-like Smoke, Performance, Security, DR/Recovery, Operational Readiness, Deployment/Rollback) → PROD (Smoke, Sanity, Monitoring Verification, Business Validation). Results recorded in `TestCases-v<N>.md` under "## TP01 Pipeline Results — v<N>" using the TP01 stage table. All Critical/High rows must Pass before Sprint 7 sign-off (TP01 R1–R3).
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7; all TP01 pipeline stages Pass (Critical/High) per TP01 R3.
**Enterprise Outcome:** Version 22 test coverage complete — P02 capstone validated.

---
### Sprint 7
**Sprint Goal:** Sign off Version 22.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Administration:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v22.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 22 signed off.

---

### Sprint 8
**Sprint Goal:** Fault Injection + Incident Simulation for Version 22.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Administration:** Phase 1 — inject a realistic fault tied to this version's topic (e.g., a cross-layer fault spanning LB→IHS→Cluster, exercising the full capstone stack's diagnostic tools at once). Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v22.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 22 fault drill complete. Non-gating — does not block sign-off.

---

## Version 22 Deliverables
- `digistack-bank-v22.ear` (final single-EAR build for Phase-1's middleware-integration stage)
- backupConfig export, pg_dump backup artifact
- Consolidated end-to-end trace documentation, mock-incident runbook
- SetupDoc-v22.md, TestCases-v22.md

## Version 22 Exit Criteria
- ✅ Application functionality complete (all P01+P02 features validated together, no new functionality added)
- ✅ Database validated (JDBC pool behavior confirmed under load, pg_dump backup verified restorable)
- ✅ WebSphere deployment successful (full stack — DMgr, cluster, JDBC, JMS, MQ, IHS, LB, security, monitoring — validated as one integrated platform)
- ✅ TP01 pipeline passed (all 5 stages, all Critical/High rows Pass in TP01 Pipeline Results table)
- ✅ Smoke testing passed (end-to-end internal + external Fund Transfer, mock incident, backup/recovery all proven)
- ✅ Ready for P03 (Enterprise Banking Systems — CBS, Payments, Channel Simulators, Loans)

## Lessons Learned
- **Key learnings:** Validating features individually (as each version did) is not the same as validating them together under combined, realistic load — this capstone surfaced no regressions, confirming P02's incremental approach held up structurally.
- **Known issues:** None outstanding at capstone sign-off.
- **Technical debt:** None new. Explicitly deferred, not a defect: Fixed Deposits, Recurring Deposits, and Loan Management were referenced in earlier planning as things this Capstone "combines," but were never actually built in P01/P02 — they remain correctly out of scope here (Loan Management is picked up in P03 v30; Fixed/Recurring Deposits remain unscoped project-wide).

---
---
---

# Version 22.5 — Database Migration (PostgreSQL → Oracle 21c XE)

## Version Overview

**Version Objective:** Migrate the existing digistack_bank PostgreSQL
database to Oracle 21c XE as the DIGISTACK_CBS Pluggable Database —
making it ready for P03 v23's CBS application split. Zero new banking
features. Zero visible application change. Pure infrastructure migration
with dual-DataSource WAS administration as the core WebSphere topic.

**Business Scope:** No new banking functionality. All existing features
(Login, Deposit/Withdraw, Freeze/Unfreeze, Fund Transfer, Transaction
History, Notifications, Reports, MFA/OTP, MQ integration, IHS, LB)
continue operating identically against the migrated Oracle database.

**WebSphere Focus:** Dual JDBC Provider configuration, dual DataSource
coexistence, JAAS Auth Alias per database engine, Oracle JDBC driver
shared library placement, Oracle SESSIONS/PROCESSES parameter sizing,
connection pool math against Oracle's session model, migration utility
deployment and lifecycle management.

**Expected Outcome:** Oracle 21c XE installed on the new dedicated VM dsb-oracle; DIGISTACK_CBS PDB created and populated; jdbc/OracleDS DataSource live in WAS; all tables migrated with row-count verification; migration utility deployed, run, and decommissioned; expdp backup captured; existing application confirmed functional; dsb-db (PostgreSQL) untouched and running independently throughout.

**Prerequisites:** P02 Version 22 Completion Checkpoint satisfied — full middleware stack (LB, IHS, WAS Cluster, SIBus JMS, IBM MQ, Web Services, Security Hardening, Monitoring) operational on PostgreSQL.

**VM Change:** A new VM, dsb-oracle, is provisioned before Sprint 1 begins (2 vCPU / 4 GB RAM / 60 GB disk, Oracle Linux 8). dsb-db (PostgreSQL) is not resized and not touched — the two database engines never share a host.

---

### Sprint 1
**Sprint Goal:** Provision dsb-oracle, install Oracle 21c XE, and create\nthe DIGISTACK_CBS Pluggable Database.
**Learning Objective:** Oracle 21c XE CDB/PDB architecture — understanding
the Container Database (CDB) / Pluggable Database (PDB) model that
replaces PostgreSQL's flat database model; SESSIONS and PROCESSES
init parameters that replace PostgreSQL's max_connections.
**Business Features:** None (pure infrastructure).
**Application Development:** N/A this sprint.
**WebSphere Administration:**
- Create VM dsb-oracle in VMware Workstation: 2 vCPU, 4 GB RAM, 60 GB\n  disk, Oracle Linux 8; static IP, same subnet as the WAS VMs; power on\n  and confirm vCPU/RAM visible to OS\n- Install Oracle 21c XE on dsb-oracle following Oracle's silent install\n  method (response file); confirm CDB (XEPDB1 default, we rename to\n  DIGISTACK_CBS) is created
- Set Oracle init parameters:
  - SESSIONS: set to 100 (2 × PROCESSES + 5 formula; matches our
    connection pool headroom requirement)
  - PROCESSES: set to 50 (2 cluster members × 20 connections each =
    40 peak; 50 gives admin headroom per CAP01 §4 lab-adjusted math)
  - SGA_TARGET: 1 GB (within Oracle XE's 2 GB RAM ceiling)
  - PGA_AGGREGATE_TARGET: 512 MB
- Create PDB: DIGISTACK_CBS with schema owner DIGISTACK_APP
  (credentials externalized — never hardcoded, per STD Golden Rules)
- Open Oracle listener on port 1521 on dsb-oracle only (firewall\n  restricts to the WAS subnet); confirm reachable from dsb-dmgr
- Confirm PostgreSQL 16 still running independently on dsb-db, port\n  5432 — dsb-db is untouched by this sprint

**Dependencies:** New dsb-oracle VM provisioned per SOE01.
**Deliverables:** Oracle 21c XE installed on dsb-oracle; DIGISTACK_CBS PDB created and open; listener on 1521; dsb-db (PostgreSQL) confirmed unaffected and still running independently.
**Acceptance Criteria:**
- `sqlplus DIGISTACK_APP/<pwd>@DIGISTACK_CBS` connects successfully from dsb-oracle locally
- `sqlplus DIGISTACK_APP/<pwd>@dsb-oracle:1521/DIGISTACK_CBS` connects successfully from dsb-dmgr (cross-VM)
- PostgreSQL on dsb-db still accepts connections on 5432 — existing application unaffected, dsb-db untouched
- dsb-oracle confirmed stable with expected free memory after Oracle starts (no OOM condition)

**Enterprise Outcome:** Oracle 21c XE operational on its own dedicated VM — separate-host design proven before any migration work begins.

---

### Sprint 2
**Sprint Goal:** Place ojdbc8.jar in WAS shared library; create Oracle
JDBC Provider and jdbc/OracleDS DataSource with JAAS Auth Alias.
**Learning Objective:** Dual JDBC Provider coexistence in one WAS cell —
how WAS isolates two completely different database drivers via shared
libraries and separate JDBC Provider definitions; Oracle's JDBC URL
format (thin driver, service name) vs. PostgreSQL's URL format.
**Business Features:** None (pure middleware).
**Application Development:** N/A this sprint.
**WebSphere Administration (GUI):**
- Download ojdbc8.jar from Oracle or Maven Central; place at
  `/apps/IBM/WebSphere/AppServer/lib/ext/ojdbc8.jar` on both cluster
  nodes (or use a WAS shared library scoped to cell level)
- Environment → Shared Libraries → New:
  - Name: OracleJDBC
  - Classpath: full path to ojdbc8.jar
- Resources → JDBC → JDBC Providers → New:
  - Provider type: User-defined
  - Implementation class: oracle.jdbc.pool.OracleConnectionPoolDataSource
  - Name: Oracle JDBC Provider
  - Classpath: reference OracleJDBC shared library
- Security → Global Security → JAAS Auth Aliases → New:
  - Alias: OracleAlias
  - User: DIGISTACK_APP
  - Password: (externalized — never hardcoded)
- Resources → JDBC → Data Sources → New:
  - Name: OracleDS
  - JNDI: jdbc/OracleDS
  - Provider: Oracle JDBC Provider
  - URL: jdbc:oracle:thin:@dsb-oracle:1521/DIGISTACK_CBS
  - Auth Alias: OracleAlias
  - Connection pool: min 1, max 20 per member
    (2 members × 20 = 40 peak vs. PROCESSES=50 — confirmed headroom)
- Test Connection → confirm green

**WebSphere Administration (wsadmin Jython):**
```python
# Create Oracle JDBC Provider
AdminTask.createJDBCProvider(
    '-scope Cell=devdsbincell01',
    ['-databaseType', 'USER_DEFINED',
     '-providerType', 'USER_DEFINED',
     '-implementationType', 'CONNECTION_POOL_DATA_SOURCE',
     '-name', 'Oracle JDBC Provider',
     '-classpath',
     '/apps/IBM/WebSphere/AppServer/lib/ext/ojdbc8.jar',
     '-implementationClassName',
     'oracle.jdbc.pool.OracleConnectionPoolDataSource']
)

# Create JAAS Auth Alias
AdminTask.createAuthDataEntry(
    '-alias', 'OracleAlias',
    '-user', 'DIGISTACK_APP',
    '-password', '<externalized>'
)

# Create DataSource
AdminTask.createDatasource(
    'Oracle JDBC Provider',
    ['-name', 'OracleDS',
     '-jndiName', 'jdbc/OracleDS',
     '-dataStoreHelperClassName',
     'com.ibm.websphere.rsadapter.GenericDataStoreHelper',
     '-componentManagedAuthenticationAlias', 'OracleAlias',
     '-configureResourceProperties',
     [['URL', 'java.lang.String',
       'jdbc:oracle:thin:@dsb-oracle:1521/DIGISTACK_CBS'],
      ['connectionSharedPool', 'java.lang.Integer', '20']]]
)
AdminConfig.save()
print "Oracle DataSource created."
```

**Dependencies:** Sprint 1 Oracle XE install, ojdbc8.jar.
**Deliverables:** OracleJDBC shared library, Oracle JDBC Provider,
jdbc/OracleDS DataSource, OracleAlias JAAS Auth Alias — all live in
WAS Admin Console alongside existing jdbc/BankDS.
**Acceptance Criteria:**
- Admin Console Test Connection on jdbc/OracleDS returns success
- Admin Console Test Connection on jdbc/BankDS still returns success —
  existing DataSource unaffected
- wsadmin script completes without error; DataSource visible in
  Admin Console after save
**Enterprise Outcome:** Dual-DataSource WAS configuration proven —
both JDBC Providers, both DataSources, both JAAS Auth Aliases coexist
in the same cell without conflict.

---

### Sprint 3
**Sprint Goal:** Write Oracle DDL scripts for all tables; create schema
in DIGISTACK_CBS PDB.
**Learning Objective:** Oracle DDL dialect vs. PostgreSQL DDL —
identity columns, VARCHAR2, NUMBER(1) for boolean, SYSTIMESTAMP,
constraint naming conventions unchanged (per STD).
**Business Features:** None.
**Application Development:**
- Write Oracle DDL migration scripts (one file per table, Oracle dialect):
  - `V1__create_app_config_oracle.sql`
  - `V2__create_users_oracle.sql`
  - `V3__create_accounts_oracle.sql`
  - `V4__add_frozen_flag_oracle.sql`
  - `V15__create_customer_account_beneficiary_fundtransfer_oracle.sql`
  - `V17__add_otp_lockout_fields_and_security_audit_log_oracle.sql`

**Oracle DDL dialect rules applied in every script:**

```sql
-- PostgreSQL SERIAL → Oracle IDENTITY
-- PostgreSQL:
id SERIAL PRIMARY KEY

-- Oracle:
id NUMBER GENERATED ALWAYS AS IDENTITY PRIMARY KEY

-- PostgreSQL VARCHAR → Oracle VARCHAR2
-- PostgreSQL:
username VARCHAR(50) NOT NULL

-- Oracle:
username VARCHAR2(50) NOT NULL

-- PostgreSQL BOOLEAN → Oracle NUMBER(1) with CHECK
-- PostgreSQL:
is_frozen BOOLEAN DEFAULT FALSE

-- Oracle:
is_frozen NUMBER(1) DEFAULT 0
  CONSTRAINT chk_accounts_is_frozen CHECK (is_frozen IN (0,1))

-- PostgreSQL NOW() → Oracle SYSTIMESTAMP
-- PostgreSQL:
created_at TIMESTAMP DEFAULT NOW()

-- Oracle:
created_at TIMESTAMP DEFAULT SYSTIMESTAMP
```

**WebSphere Administration:**
- Run all DDL scripts against DIGISTACK_CBS via sqlplus from dsb-oracle
- Confirm all tables created with correct constraints
- Confirm DIGISTACK_APP schema owner has SELECT/INSERT/UPDATE/DELETE
  on all tables

**Dependencies:** Sprint 1 PDB, Sprint 2 DataSource.
**Deliverables:** All Oracle DDL scripts; all tables created in
DIGISTACK_CBS.
**Acceptance Criteria:**
- All tables present in DIGISTACK_CBS: app_config, users, accounts,
  customer, beneficiary, fund_transfer, security_audit_log
- All constraints verified (PK, FK, CHECK, NOT NULL) via
  `SELECT * FROM USER_CONSTRAINTS WHERE TABLE_NAME = '<TABLE>'`
- DIGISTACK_APP schema owner confirmed with correct grants
**Enterprise Outcome:** Oracle schema ready to receive migrated data —
structurally equivalent to the PostgreSQL source, Oracle-dialect correct.

---

### Sprint 4
**Sprint Goal:** Build, deploy, and execute the JDBC-based Java migration
utility inside WAS.
**Learning Objective:** Dual-DataSource application pattern in WAS —
a servlet that holds two simultaneous JNDI-looked-up connections
(one PostgreSQL, one Oracle) and migrates data table by table with
row-count verification; migration utility deployment and lifecycle
management as a WAS admin concern.
**Business Features:** None.
**Application Development:**

Full migration utility servlet (complete, runnable file per NDS01):

```java
package com.digistack.migration;

import javax.naming.InitialContext;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.sql.DataSource;
import java.io.IOException;
import java.io.PrintWriter;
import java.sql.*;
import java.util.LinkedHashMap;
import java.util.Map;

@WebServlet("/migrate")
public class MigrationServlet extends HttpServlet {

    private static final Map<String, String> TABLE_MAP =
        new LinkedHashMap<>();

    static {
        TABLE_MAP.put("app_config",
            "INSERT INTO app_config (id, config_key, config_value, " +
            "created_at) VALUES (?, ?, ?, ?)");
        TABLE_MAP.put("users",
            "INSERT INTO users (id, username, password_hash, " +
            "last_login, otp_secret, login_attempts, locked, " +
            "created_at) VALUES (?, ?, ?, ?, ?, ?, ?, ?)");
        TABLE_MAP.put("accounts",
            "INSERT INTO accounts (id, user_id, balance, " +
            "is_frozen, created_at) VALUES (?, ?, ?, ?, ?)");
        TABLE_MAP.put("customer",
            "INSERT INTO customer (id, user_id, customer_id, " +
            "name, created_at) VALUES (?, ?, ?, ?, ?)");
        TABLE_MAP.put("beneficiary",
            "INSERT INTO beneficiary (id, customer_id, " +
            "account_number, name, is_external, created_at) " +
            "VALUES (?, ?, ?, ?, ?, ?)");
        TABLE_MAP.put("fund_transfer",
            "INSERT INTO fund_transfer (id, from_account_id, " +
            "to_account_id, amount, status, created_at) " +
            "VALUES (?, ?, ?, ?, ?, ?)");
        TABLE_MAP.put("security_audit_log",
            "INSERT INTO security_audit_log (id, user_id, " +
            "event_type, event_time, details) " +
            "VALUES (?, ?, ?, ?, ?)");
    }

    @Override
    protected void doGet(HttpServletRequest req,
                         HttpServletResponse res)
            throws ServletException, IOException {

        res.setContentType("text/plain");
        PrintWriter out = res.getWriter();

        Connection pgConn = null;
        Connection orConn = null;

        try {
            InitialContext ctx = new InitialContext();
            DataSource pgDs =
                (DataSource) ctx.lookup("java:comp/env/jdbc/BankDS");
            DataSource orDs =
                (DataSource) ctx.lookup("java:comp/env/jdbc/OracleDS");

            pgConn = pgDs.getConnection();
            orConn = orDs.getConnection();
            orConn.setAutoCommit(false);

            out.println("=== DigiStack Migration Utility v22.5 ===");
            out.println();

            for (Map.Entry<String, String> entry :
                    TABLE_MAP.entrySet()) {
                String table = entry.getKey();
                String insertSql = entry.getValue();
                migrateTable(pgConn, orConn, table,
                             insertSql, out);
            }

            orConn.commit();
            out.println();
            out.println("=== Migration Complete. Commit successful. ===");

        } catch (Exception e) {
            out.println("ERROR: " + e.getMessage());
            if (orConn != null) {
                try {
                    orConn.rollback();
                    out.println("Oracle transaction rolled back.");
                } catch (SQLException ex) {
                    out.println("Rollback failed: " + ex.getMessage());
                }
            }
        } finally {
            closeQuietly(pgConn);
            closeQuietly(orConn);
        }
    }

    private void migrateTable(Connection src, Connection tgt,
                               String table, String insertSql,
                               PrintWriter out) throws SQLException {
        long srcCount = count(src, table);
        out.println("Table: " + table +
                    " | PostgreSQL rows: " + srcCount);

        Statement stmt = src.createStatement();
        ResultSet rs = stmt.executeQuery(
            "SELECT * FROM " + table);
        ResultSetMetaData meta = rs.getMetaData();
        int cols = meta.getColumnCount();

        PreparedStatement ps = tgt.prepareStatement(insertSql);
        long inserted = 0;

        while (rs.next()) {
            for (int i = 1; i <= cols; i++) {
                ps.setObject(i, rs.getObject(i));
            }
            ps.addBatch();
            inserted++;
            if (inserted % 500 == 0) {
                ps.executeBatch();
            }
        }
        ps.executeBatch();
        rs.close();
        stmt.close();
        ps.close();

        long tgtCount = count(tgt, table);
        String result = (srcCount == tgtCount) ? "PASS" : "FAIL";
        out.println("  Oracle rows after insert: " + tgtCount +
                    " | Verification: " + result);
        if (!result.equals("PASS")) {
            throw new SQLException(
                "Row count mismatch on table: " + table);
        }
    }

    private long count(Connection conn,
                       String table) throws SQLException {
        Statement s = conn.createStatement();
        ResultSet r = s.executeQuery("SELECT COUNT(*) FROM " + table);
        r.next();
        long c = r.getLong(1);
        r.close();
        s.close();
        return c;
    }

    private void closeQuietly(Connection c) {
        if (c != null) {
            try { c.close(); } catch (SQLException ignored) {}
        }
    }
}
```

**WebSphere Administration:**
- Package migration utility as `digistack-migration-v22.5.ear`
- Deploy to WAS cluster via Admin Console
- Navigate to `http://dsb-dmgr:9080/digistack-migration/migrate`
  to execute migration
- Confirm output shows PASS for every table
- Undeploy migration utility immediately after successful run —
  this EAR has zero production use; decommission is mandatory

**Dependencies:** Sprints 2 and 3.
**Deliverables:** Migration utility EAR; migration executed with
all-PASS row-count verification; utility undeployed post-run.
**Acceptance Criteria:**
- Browser output shows PASS for all 7 tables
- Zero rows show FAIL in the verification output
- Migration utility EAR undeployed from Admin Console immediately
  after successful run — confirmed absent from Applications list
- Oracle transaction committed (no partial state)
**Enterprise Outcome:** All digistack_bank data lives in
DIGISTACK_CBS Oracle PDB, verified row-for-row. Dual-DataSource
WAS pattern exercised and migration utility lifecycle managed
correctly (deploy → run → decommission).

---

### Sprint 5
**Sprint Goal:** Verify the existing application against the Oracle
DataSource; capture expdp backup; confirm dsb-oracle stability.
**Learning Objective:** Application validation after a DataSource
change; Oracle Data Pump (expdp) backup discipline replacing pg_dump;
confirming Oracle SESSIONS/PROCESSES headroom under real connection
pool load.
**Business Features:** None (validation and backup sprint).
**Application Development:** N/A this sprint.
**WebSphere Administration (GUI):**
- Admin Console → Resources → JDBC → Data Sources → jdbc/OracleDS
  → Test Connection → confirm green under simulated load
- Run a manual Fund Transfer and a Deposit/Withdraw end-to-end,
  confirm data lands in Oracle DIGISTACK_CBS tables (verify via
  sqlplus SELECT)
- Monitor Oracle V$SESSION during load — confirm active session
  count stays below PROCESSES=50
- Run expdp backup from dsb-oracle:

```bash
expdp SYSTEM/<pwd>@DIGISTACK_CBS \
  directory=DATA_PUMP_DIR \
  dumpfile=digistack_cbs_v22.5_%DATE%.dmp \
  logfile=digistack_cbs_v22.5_%DATE%.log \
  schemas=DIGISTACK_APP \
  compression=ALL
```

- Verify dump file exists and is non-zero
- Restore-test: import into a scratch schema to confirm the dump
  is genuinely restorable (impdp with REMAP_SCHEMA)

**WebSphere Administration (wsadmin Jython):**
```python
# Verify DataSource connection pool status
import AdminControl
poolMBean = AdminControl.queryNames(
    'type=DataSource,name=OracleDS,*')
print AdminControl.getAttribute(poolMBean, 'pool.size')
print AdminControl.getAttribute(poolMBean,
                                'pool.connections.inUse')
print AdminControl.getAttribute(poolMBean,
                                'pool.freeConnections')
```

**Dependencies:** Sprint 4 migration complete.
**Deliverables:** Application validated against Oracle; expdp backup
captured and restore-tested; dsb-oracle stability confirmed..
**Acceptance Criteria:**
- Fund Transfer, Deposit/Withdraw, Login all function correctly
  with data landing in DIGISTACK_CBS Oracle tables
- V$SESSION confirms session count within PROCESSES=50 ceiling
- expdp dump file exists, non-zero, restore-tested successfully
- dsb-oracle free memory confirmed positive after 30 minutes of
  both engines running under load
**Enterprise Outcome:** Oracle DIGISTACK_CBS is the confirmed,
backup-protected target database — ready for P03 v23 to adopt
as CBS's sole datastore.

---

### Sprint 6
**Sprint Goal:** Write and execute test cases for Version 22.5.
**Learning Objective:** Test Case discipline (TCS01/TCS02) applied
to a migration version — includes both functional regression tests
(existing features still work) and migration-specific tests (data
integrity, row counts, constraint verification).
**Business Features:** None (validation sprint).
**Application Development:** Bug-fix only, no new work.
**WebSphere Administration:**
- Confirm both DataSources green in Admin Console
- Confirm existing digistack-bank-v22.ear application status = Started
- Re-run smoke test (Login, Deposit/Withdraw, Fund Transfer,
  Transaction History) confirming all pass against Oracle data

**Deliverables:** `TestCases-v22.5.md`
**TP01 Pipeline (mandatory, per TP01_Test_Pipeline.md):** Sprint 6 executes the full 5-stage test pipeline — DEV (Unit/Component, Developer, Code Quality/Security) → SIT (API, Integration, Database, Middleware, End-to-End, Negative, Regression Pack v1–v<N-1>) → UAT (Business Process, Customer Journey, Financial/Accounting Validation, Business Acceptance) → PRE-PROD (Production-like Smoke, Performance, Security, DR/Recovery, Operational Readiness, Deployment/Rollback) → PROD (Smoke, Sanity, Monitoring Verification, Business Validation). Results recorded in `TestCases-v<N>.md` under "## TP01 Pipeline Results — v<N>" using the TP01 stage table. All Critical/High rows must Pass before Sprint 7 sign-off (TP01 R1–R3).
**Acceptance Criteria:** All Critical and High test cases pass per
TCS01 §2.7. Migration-specific test cases include:
- TC-v22.5-01 (Critical): Row count match — all 7 tables PostgreSQL
  vs. Oracle counts identical
- TC-v22.5-02 (Critical): Fund Transfer end-to-end — data persists
  in Oracle DIGISTACK_CBS
- TC-v22.5-03 (Critical): expdp backup restorable — impdp completes
  without error on restore-test
- TC-v22.5-04 (High): Oracle SESSIONS ceiling — V$SESSION count
  stays below PROCESSES=50 under simulated load
- TC-v22.5-05 (High): PostgreSQL still live — jdbc/BankDS Test
  Connection still green (not yet decommissioned)
- TC-v22.5-06 (High): Migration utility absent — confirm EAR
  undeployed from Admin Console
- TC-v22.5-07 (Medium): Constraint verification — all Oracle
  constraints confirmed via USER_CONSTRAINTS query
**Enterprise Outcome:** Version 22.5 test coverage complete.

---

### Sprint 7
**Sprint Goal:** Sign off Version 22.5.
**Learning Objective:** SetupDoc discipline (SDD01) for a migration
version — requires a dedicated Migration Verification section
(same precedent as v23's Migration & Ownership Transfer section,
per STDGAP01 §3.8).
**WebSphere Administration:**
- Capture backupConfig for current WAS cell configuration
  (includes both JDBC Providers, both DataSources, both JAAS
  Auth Aliases)
- Final smoke test: Login → Deposit → Withdraw → Fund Transfer →
  Transaction History — all against Oracle DIGISTACK_CBS
- Confirm expdp backup exists and restore-test result is documented
  in SetupDoc

**Deliverables:** `SetupDoc-v22.5.md` — must include:
- §1 Overview
- §2 VM Setup (dsb-oracle new VM: 2 vCPU / 4 GB / 60 GB))
- §3 Pre-Deployment Checklist (01_Architecture diagram check
  per standing rule)
- §4 Step-by-Step Configuration (Oracle XE install, PDB creation,
  JDBC Provider, DataSource, JAAS Alias — both GUI and wsadmin
  per NDS01 Rule 7)
- §5 Verification (cross-references TestCases-v22.5.md)
- §6 Rollback Procedure (restore from expdp if Oracle migration
  fails — PostgreSQL remains live as rollback target)
- §7 Migration Verification section (row-count reconciliation
  table, expdp restore-test result, V$SESSION ceiling confirmation)
- §8 Sign-off table

**Acceptance Criteria:** SetupDoc complete and followed
start-to-finish; backupConfig captured; smoke test passes;
Migration Verification section fully populated.
**Enterprise Outcome:** Version 22.5 signed off.

---

### Sprint 8
**Sprint Goal:** Fault Injection + Incident Simulation for
Version 22.5.
**Learning Objective:** Real fault diagnosis on Oracle's
SESSIONS/PROCESSES exhaustion — an Oracle-specific failure mode
with no PostgreSQL equivalent, directly tied to WAS connection
pool administration.
**WebSphere Administration:**

**Phase 1 — Fault Injection (exact steps, per NDS01 Rules 1 and 2):**

Step 1. Connect to Oracle on dsb-oracle as SYSDBA:
```bash
sqlplus / as sysdba
```

Step 2. Reduce PROCESSES parameter to force exhaustion under
normal connection pool load:
```sql
ALTER SYSTEM SET PROCESSES=5 SCOPE=SPFILE;
SHUTDOWN IMMEDIATE;
STARTUP;
```

Step 3. Confirm Oracle has restarted with the new parameter:
```sql
SHOW PARAMETER PROCESSES;
-- Expected output: processes  integer  5
```

Step 4. In WAS Admin Console, confirm jdbc/OracleDS DataSource
Test Connection now shows an error (connection refused or
ORA-00018: maximum number of sessions exceeded).

Step 5. Leave the fault in place. Do not state the effect,
symptom, or cause to the trainee.

**Phase 2 — Incident Ticket (on "continue sprint" trigger):**

```
INCIDENT ID:      INC-DSB-022.5-001
SEVERITY:         P2
TIME:             [current timestamp]
APPLICATION:      DigiStack Banking Platform
SERVICE:          Oracle Database — DIGISTACK_CBS PDB

BUSINESS IMPACT:
All banking transactions requiring database access are failing.
Customers cannot login, deposit, withdraw, or transfer funds.
The platform appears operational at the WAS and IHS layers.

CUSTOMER/BUSINESS SYMPTOM:
Customers attempting to login receive a generic error page.
Fund Transfers submitted via the REST API return HTTP 500.
The IHS access log shows requests reaching WAS successfully.

INITIAL ALERT/TICKET:
Automated alert: jdbc/OracleDS DataSource Test Connection
failure detected at [timestamp]. Raised by monitoring.

OBSERVED ERROR:
WAS SystemOut.log contains repeated entries of the form:
  com.ibm.ws.rsadapter.exceptions.DataStoreAdapterException
  ...Caused by: java.sql.SQLException
  The application requested a connection from pool
  jdbc/OracleDS but none was available.

SCOPE:
All applications using jdbc/OracleDS DataSource.
Affects: Login, Deposit, Withdraw, Fund Transfer,
         Transaction History, Freeze/Unfreeze.

NOT AFFECTED:
IHS, WAS cluster members, Node Agents, DMgr — all show
Started/Running status in Admin Console.
Network connectivity between VMs — confirmed reachable.

RECENT CHANGE:
Oracle 21c XE database migrated from PostgreSQL at Version
22.5. Oracle SESSIONS/PROCESSES parameters configured during
Sprint 1.

STARTING EVIDENCE:
1. WAS Admin Console → jdbc/OracleDS → Test Connection: FAIL
2. WAS SystemOut.log: DataStoreAdapterException on pool
   jdbc/OracleDS
3. Oracle listener port 1521 reachable from dsb-dmgr:
   telnet dsb-oracle 1521 → connected
4. sqlplus DIGISTACK_APP/<pwd>@dsb-oracle:1521/DIGISTACK_CBS from dsb-oracle: [observe and report result]

STOP HERE.
```

**Phase 3 — Investigation (trainee-driven):**
Trainee investigates using real tools (sqlplus, WAS Admin Console,
SystemOut.log, V$SESSION, SHOW PARAMETER). Claude confirms or
denies specific hypotheses only. Proportional hints on explicit
request. RCA on explicit request only.

**Environment restoration (after RCA):**
```sql
-- Connect as SYSDBA on dsb-oracle
sqlplus / as sysdba
ALTER SYSTEM SET PROCESSES=50 SCOPE=SPFILE;
SHUTDOWN IMMEDIATE;
STARTUP;
-- Verify
SHOW PARAMETER PROCESSES;
-- Confirm jdbc/OracleDS Test Connection green in Admin Console
```

**Deliverables:** `FaultDrill-v22.5.md`
**Acceptance Criteria:** Fault injected per exact steps above;
incident ticket raised; RCA completed; Oracle PROCESSES parameter
restored to 50; jdbc/OracleDS Test Connection confirmed green;
environment returned to known-good state.
**Enterprise Outcome:** Version 22.5 fault drill complete.
Non-gating — does not block sign-off.

---

## Version 22.5 Deliverables
- `digistack-migration-v22.5.ear` (deployed, run, and undeployed)
- Oracle DDL scripts (7 files, Oracle dialect)
- `SetupDoc-v22.5.md` (with Migration Verification section)
- `TestCases-v22.5.md`
- `FaultDrill-v22.5.md`
- expdp backup artifact: `digistack_cbs_v22.5_<date>.dmp`
- backupConfig of WAS cell (dual-DataSource configuration)

## Version 22.5 Exit Criteria
- ✅ Oracle 21c XE installed on dsb-oracle; DIGISTACK_CBS PDB created
- ✅ jdbc/OracleDS DataSource live alongside jdbc/BankDS in WAS cell
- ✅ All 7 tables migrated — row-count verification PASS for every table
- ✅ Migration utility EAR undeployed from Admin Console
- ✅ Application functional against Oracle (Login, Deposit/Withdraw,
  Fund Transfer, Transaction History all confirmed)
- ✅ expdp backup captured and restore-tested
- ✅ dsb-oracle stable, dedicated single-engine host
- ✅ PostgreSQL still live (not yet decommissioned — P03 v23 Sprint 4)
- ✅ Ready for P03 v23 (CBS split, Oracle as sole CBS datastore)

## Lessons Learned
- **Key learnings:** Oracle's CDB/PDB model is architecturally
  distinct from PostgreSQL's flat database model — understanding
  the Container Database wrapper is the first conceptual shift a
  DBA or WAS admin makes when moving between the two platforms.
  SESSIONS/PROCESSES replaces max_connections as the concurrency
  ceiling, and sizing it correctly against the WAS connection pool
  math (per CAP01 §4) is the same exercise with different
  parameter names.
- **Known issues:** None expected if Sprint 1's dual-engine
  provisioning on dsb-oracle is verified stable before Sprint 3's DDL
  work begins.
- **Technical debt:** PostgreSQL digistack_bank remains live on
  dsb-db (unchanged, PostgreSQL-only) until P03 v23 Sprint 4 —\n  documented open debt, tracked
  explicitly in P03 v23's Migration & Ownership Transfer section.

---

# P02 — Overall Completion Summary

**All 10 versions (15–22, plus suffix-slot versions v16.5 and v22.5), 80 sprints total, complete.**

## P02 Final Application State
- Modules: Customer (multi-account), Account, Beneficiary, Fund Transfer (internal via SIBus/MDB, external via IBM MQ), Transaction History/Account Statement (REST + SOAP), MFA/OTP, account lockout, Security Event Detection, Operations Dashboard (JVM/Session/Queue/DB Pool)
- Infrastructure added on top of P01: SIBus/JMS (queues, MDB, DLQ), Web Services engine (JAX-RS + JAX-WS, WSDL), hardened security (MFA, LTPA, CSRF/XSS, API auth), PMI/JMX monitoring dashboard, IBM MQ Queue Manager, IHS advanced admin (rewrite, maintenance mode, health checks), External Load Balancer (blue-green, HA)
- Still one EAR: `digistack-bank-v22.ear` — no Portal/CBS split yet (that's P03 v23)

## Carried Forward to P03
This is the exact starting point P03 picks up from — where the real Portal/CBS application split (v23), the two Tomcat-based channel simulators (Mobile v26, ATM v27), the WAS-hosted Card Portal (v28), and Branch Portal (v29) begin.

---

*This is the consolidated P02_Sprint_Plan.md — the planning document for P02, not a completion record. No versions have been built or signed off yet. Actual progress is tracked in Progress_Log.md.*