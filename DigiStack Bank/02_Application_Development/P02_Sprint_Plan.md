# P02 — WebSphere / Enterprise Middleware Integration
## Consolidated Sprint Plan (Versions 15–22)

**Part:** P02 — WebSphere / Enterprise Middleware Integration
**Versions Covered:** 15, 16, 17, 18, 19, 20, 21, 22
**Status:** Complete — all 8 versions signed off
**Prerequisite:** P01 Completion Checkpoint satisfied (`digistack-bank-v14.ear`, 2-member cluster, IHS, SSL end-to-end, JNDI DataSource, Customer/Administrator roles, JNDI Mail Session)
**Next:** P03 — Enterprise Banking Systems (CBS, Payments, Channel Simulators, Loans)

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
**Acceptance Criteria:** A deliberately failing transfer retries the configured number of times, then is found sitting in the DLQ, inspectable via Admin Console.
**Enterprise Outcome:** Full asynchronous processing lifecycle — including failure — demonstrated end-to-end.

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
**Sprint Goal:** Full external client validation, smoke test, and sign off.
**Learning Objective:** End-to-end validation discipline for a multi-endpoint, multi-binding-style release.
**Business Features:** None (validation sprint).
**Application Development:** Bug-fix only, no new work.
**WebSphere Administration:**
- Redeploy final EAR for this version, `digistack-bank-v16.ear`
- Confirm all three endpoints reachable post-redeploy

**Dependencies:** Sprints 1–5 complete.
**Deliverables:** `SetupDoc-v16.md`, `TestCases-v16.md` (per SDD01/TCS01).
**Acceptance Criteria:** A single Postman/SoapUI test pass calls all three endpoints successfully, with logs captured for each request/response.
**Enterprise Outcome:** Version 16 signed off — REST and SOAP contracts both live and stable, ready for Version 17 (Security Hardening) to secure them.

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
- ✅ Smoke testing passed (Postman + SoapUI calls succeed with logged request/response)
- ✅ Ready for Version 17

## Lessons Learned
- **Key learnings:** REST and SOAP coexist cleanly on the same EAR without conflict; WSDL publishing is mostly an Admin Console configuration exercise once the JAX-WS service class is correct.
- **Known issues:** None expected if Sprint 3's WSDL generation is verified before Sprint 4's binding work begins.
- **Technical debt:** None new — these endpoints are deliberately unauthenticated at this version; MFA/token auth is intentionally deferred to Version 17 (Security Hardening), not an oversight.

---
---

# Version 17 — Security Hardening

## Version Overview

**Version Objective:** Harden everything already built — MFA/OTP, account lockout, endpoint authentication, and a basic Security Event Detection check — without adding new banking features.

**Business Scope:** No new banking feature — hardens v2's Login, and retroactively secures v16's REST/SOAP endpoints.

**WebSphere Focus:** Global/Application Security, LDAP, LTPA, JAAS, role mapping, CSRF/XSS protection.

**Expected Outcome:** MFA/OTP enforced on login; account locks after N failed attempts; LTPA token validated across the cluster; v16's REST/SOAP endpoints reject unauthenticated calls; a rapid-repeated-transfer test triggers a security audit log entry.

**Prerequisites:** P02 Version 16 Completion Checkpoint satisfied — Balance Inquiry/Fund Transfer (REST) and Account Statement (SOAP) live and traceable.

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
**Sprint Goal:** Implement Security Event Detection and sign off Version 17.
**Learning Objective:** Basic threshold-based audit logging (not a rules engine or ML — exactly one check).
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
**Deliverables:** Security Event Detection working; `SetupDoc-v17.md`, `TestCases-v17.md`.
**Acceptance Criteria:** A rapid-repeated-transfer test (e.g., 5 transfers in quick succession) produces exactly one security audit log entry.
**Enterprise Outcome:** Version 17 signed off — MFA/lockout/LTPA/endpoint auth/CSRF-XSS/audit detection all proven, ready for Version 18 (Monitoring & Logging).

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
**Sprint Goal:** Confirm log rotation and sign off Version 18.
**Learning Objective:** Log Rotation discipline on SystemOut/SystemErr.
**Business Features:** None (validation sprint).
**Application Development:** Bug-fix only, no new work.
**WebSphere Administration:**
- Verify SystemOut/SystemErr log rotation is active and correctly configured (per STD Golden Rule — rotation only, no manual deletion)
- Redeploy final EAR for this version, `digistack-bank-v18.ear`

**Dependencies:** Sprints 1–5 complete.
**Deliverables:** `SetupDoc-v18.md`, `TestCases-v18.md`.
**Acceptance Criteria:** Log rotation confirmed working (new log file created at rotation threshold); all four dashboard panels verified live in one final pass.
**Enterprise Outcome:** Version 18 signed off — full Operations Dashboard (JVM/Session/Queue/DB Pool) and diagnostic capture discipline both proven, ready for Version 19 (IBM MQ Integration).

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
**Sprint Goal:** Configure MQ-level DLQ, apply CHLAUTH/SSL, and sign off Version 19.
**Learning Objective:** MQ Dead Letter Queue handling, channel authentication and security.
**Business Features:** None (hardening/validation sprint).
**Application Development:** Bug-fix only, no new work.
**WebSphere Administration:**
- Configure MQ-level DLQ on the Queue Manager
- Apply CHLAUTH channel authentication records and SSL/TLS to the MQ channel (building on P01 SSL work and v17's hardening)
- Redeploy final EAR for this version, `digistack-bank-v19.ear`
- Confirm MQ Monitoring shows queue depth/channel status correctly

**Dependencies:** Sprint 5's end-to-end flow, P01 v11/v12 SSL, P02 v17 security hardening.
**Deliverables:** MQ DLQ + CHLAUTH/SSL configured; `SetupDoc-v19.md`, `TestCases-v19.md`.
**Acceptance Criteria:** A deliberately malformed/unroutable message lands in the MQ DLQ; a channel connection attempt without valid CHLAUTH/SSL credentials is rejected.
**Enterprise Outcome:** Version 19 signed off — external payment integration is now both functional and secured, ready for Version 20 (IBM HTTP Server Advanced Administration).

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
**Sprint Goal:** Validate all advanced IHS features together and sign off Version 20.
**Learning Objective:** End-to-end validation of multiple concurrent IHS-layer changes.
**Business Features:** None (validation sprint).
**Application Development:** Bug-fix only, no new work.
**WebSphere Administration:**
- Redeploy final EAR for this version, `digistack-bank-v20.ear`
- Confirm plugin-cfg.xml still correctly reflects cluster topology after all Sprint 1–5 changes
- Final validation pass: URL rewrite, maintenance toggle, health check, SSL termination, compression/KeepAlive all together

**Dependencies:** Sprints 1–5 complete.
**Deliverables:** `SetupDoc-v20.md`, `TestCases-v20.md`.
**Acceptance Criteria:** All five IHS-layer features function correctly in a single combined validation pass with no regressions to existing routing.
**Enterprise Outcome:** Version 20 signed off — IHS now supports rewrite, maintenance mode, health checks, SSL termination, and performance tuning, ready for Version 21 (Enterprise Load Balancer).

---

## Version 20 Deliverables
- `digistack-bank-v20.ear` (adds health check servlet; URL rewrite, maintenance mode, health check, SSL termination, compression/KeepAlive all configured at the IHS layer around it)
- IHS configuration exports (rewrite rules, maintenance toggle, health check routing, SSL termination, compression/KeepAlive settings)
- SetupDoc-v20.md, TestCases-v20.md

## Version 20 Exit Criteria
- ✅ Application functionality complete (no new banking functionality; all five IHS features proven)
- ✅ Database validated (no schema change this version)
- ✅ WebSphere deployment successful (plugin-cfg.xml confirmed accurate post-change)
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
**Sprint Goal:** Perform a blue-green deployment and sign off Version 21.
**Learning Objective:** Blue-Green Deployment mechanics at the LB tier.
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
**Deliverables:** Proven blue-green deployment; `SetupDoc-v21.md`, `TestCases-v21.md`.
**Acceptance Criteria:** The version-label change is live on green with zero dropped requests during cutover; a rollback to blue is demonstrated successfully.
**Enterprise Outcome:** Version 21 signed off — full enterprise LB tier (health checks, sticky sessions, SSL offloading, blue-green) proven, ready for Version 22 (Middleware Integration Capstone).

---

## Version 21 Deliverables
- `digistack-bank-v21.ear` (trivial version-label change only, used as the blue-green deployment payload)
- NGINX configuration exports (upstream pool, health checks, sticky sessions, SSL offloading)
- SetupDoc-v21.md, TestCases-v21.md

## Version 21 Exit Criteria
- ✅ Application functionality complete (no new banking functionality; blue-green deployment proven on a trivial change)
- ✅ Database validated (no schema change this version)
- ✅ WebSphere deployment successful (two-IHS-instance topology confirmed load-balanced)
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
**Sprint Goal:** Final capstone validation and sign off Version 22 / P02 as a whole.
**Learning Objective:** Consolidating and confirming every P02 checklist item passes together.
**Business Features:** None (validation sprint).
**Application Development:** Bug-fix only, no new work.
**WebSphere Administration:**
- Re-run the full P02 Completion Checklist (Fund Transfer internal/external, REST/SOAP, security hardening, dashboard, MQ, IHS advanced admin, LB) as one combined pass
- Redeploy final EAR for this version, `digistack-bank-v22.ear`

**Dependencies:** Sprints 1–5 complete, all of P02 v15–v21.
**Deliverables:** `SetupDoc-v22.md`, `TestCases-v22.md`; full P02 Completion Checklist signed off.
**Acceptance Criteria:** Every item in P02's Completion Checklist passes in one combined validation pass; no open Critical/High defects.
**Enterprise Outcome:** Version 22 signed off — P02 complete. Still one EAR (`digistack-bank-v22.ear`) — no Portal/CBS split yet; that begins at P03 v23.

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
- ✅ Smoke testing passed (end-to-end internal + external Fund Transfer, mock incident, backup/recovery all proven)
- ✅ Ready for P03 (Enterprise Banking Systems — CBS, Payments, Channel Simulators, Loans)

## Lessons Learned
- **Key learnings:** Validating features individually (as each version did) is not the same as validating them together under combined, realistic load — this capstone surfaced no regressions, confirming P02's incremental approach held up structurally.
- **Known issues:** None outstanding at capstone sign-off.
- **Technical debt:** None new. Explicitly deferred, not a defect: Fixed Deposits, Recurring Deposits, and Loan Management were referenced in earlier planning as things this Capstone "combines," but were never actually built in P01/P02 — they remain correctly out of scope here (Loan Management is picked up in P03 v30; Fixed/Recurring Deposits remain unscoped project-wide).

---

# P02 — Overall Completion Summary

**All 8 versions (15–22), 48 sprints total, complete.**

## P02 Final Application State
- Modules: Customer (multi-account), Account, Beneficiary, Fund Transfer (internal via SIBus/MDB, external via IBM MQ), Transaction History/Account Statement (REST + SOAP), MFA/OTP, account lockout, Security Event Detection, Operations Dashboard (JVM/Session/Queue/DB Pool)
- Infrastructure added on top of P01: SIBus/JMS (queues, MDB, DLQ), Web Services engine (JAX-RS + JAX-WS, WSDL), hardened security (MFA, LTPA, CSRF/XSS, API auth), PMI/JMX monitoring dashboard, IBM MQ Queue Manager, IHS advanced admin (rewrite, maintenance mode, health checks), External Load Balancer (blue-green, HA)
- Still one EAR: `digistack-bank-v22.ear` — no Portal/CBS split yet (that's P03 v23)

## Carried Forward to P03
This is the exact starting point P03 picks up from — where the real Portal/CBS application split (v23), the two Tomcat-based channel simulators (Mobile v26, ATM v27), the WAS-hosted Card Portal (v28), and Branch Portal (v29) begin.
