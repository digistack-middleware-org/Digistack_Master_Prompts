# P03 — Enterprise Banking Systems (CBS, Payments, Channel Simulators, Loans)
## Consolidated Sprint Plan (Versions 23–30)

**Part:** P03 — Enterprise Banking Systems (CBS, Payments, Channel Simulators, Loans)
**Versions Covered:** 23, 24, 25, 26, 27, 28, 29, 30
**Status:** Complete — all 8 versions signed off
**Prerequisite:** P02 Completion Checkpoint satisfied (`digistack-bank-v22.ear`, full middleware stack — LB, IHS, WAS Cluster, SIBus JMS, IBM MQ, Web Services, Security Hardening, Monitoring — all operational)
**Next:** P03.1 — Interview Preparation

---

# Version 23 — Core Banking System (CBS)

## Version Overview

**Version Objective:** Stand up DigiStack CBS as the central banking platform and pivot architecture — Internet Banking stops touching the database directly and calls CBS exclusively via REST/SOAP; CBS becomes the sole writer of `digistack_cbs`.

**Business Scope:** Customer Information, Customer Verification, Open/Close Account, Deposit, Withdrawal, Balance Inquiry, Fund Transfer, Mini Statement, Savings/Current Account products.

**WebSphere Focus:** Enterprise Service Layer, Shared Business Services, XA Transactions, Clustered Business Services, JDBC Optimization/Connection Pool Tuning, Enterprise Deployment, Application Ownership Migration (data, endpoints, messaging, satellite service extraction).

**Expected Outcome:** Internet Banking Portal invokes CBS exclusively via REST/SOAP for every transaction; direct Portal→DB access is removed entirely; CBS enforces business validation and XA transaction integrity; existing P01/P02 data is migrated into `digistack_cbs` and verified; Notification Service and Reporting Service stood up as independent applications.

**Prerequisites:** P02 Completion Checkpoint satisfied (`digistack-bank-v22.ear` — full middleware stack operational).

---

### Sprint 1
**Sprint Goal:** Provision CBS's dedicated database and DataSource, separate from the P01/P02 shared DB.
**Learning Objective:** Isolating a new business-critical datastore with its own connection pool and JAAS auth alias.
**Business Features:** None (pure infrastructure — enables everything else this version).
**Application Development:**
- UI: N/A
- Backend: N/A
- Database: Create `digistack_cbs` (banking/audit/reporting schemas per STD)
- API: N/A

**WebSphere Administration:**
- Configuration: New JDBC Provider + `jdbc/CBSDataSource`, new JAAS Auth Alias (separate from P01's pool)
- Deployment: N/A this sprint
- JDBC / JMS / JNDI: `jdbc/CBSDataSource` bound to JNDI, connection pool sized per CAP01 §4
- Security: Least-privilege DB account for CBS (per DBS01 §4.2)
- Performance: N/A

**Dependencies:** P01 v7 JNDI/DataSource pattern, STD §Database.
**Deliverables:** `digistack_cbs` database live, `jdbc/CBSDataSource` bound and tested.
**Acceptance Criteria:** A JNDI lookup of `jdbc/CBSDataSource` from a test servlet returns a live connection to `digistack_cbs`, distinct from the P01/P02 shared DB.
**Enterprise Outcome:** Core banking data isolated from channel-layer data before a single row moves — mirrors real banking blast-radius separation.

---

### Sprint 2
**Sprint Goal:** Scaffold the CBS application (multi-module EAR) and migrate existing data.
**Learning Objective:** Multi-module EAR structure; one-time verified data migration discipline.
**Business Features:** Customer Information (base), Account (Open/Close), Deposit, Withdrawal, Balance Inquiry — ported from P01/P02, not rebuilt.
**Application Development:**
- UI: N/A (CBS is service-only; Portal keeps existing UI)
- Backend: CBS EAR scaffold — `controller/`, `service/`, `dao/`, `model/`, `config/` per ARCH02
- Database: `V23__migrate_existing_data_to_cbs.sql` (Customer, Account, Beneficiary, Fund Transfer, Transaction History)
- API: N/A this sprint

**WebSphere Administration:**
- Configuration: Multi-module EAR project structure, shared library setup
- Deployment: Deploy CBS skeleton (`digistack-cbs-v23.ear`) to existing cluster
- JDBC / JMS / JNDI: N/A (reuses Sprint 1's DataSource)
- Security: N/A this sprint

**Dependencies:** Sprint 1's DataSource, P01 v3 accounts table, P02 v15 Customer/Account/Beneficiary/Fund Transfer tables.
**Deliverables:** CBS EAR skeleton deployed; migration script executed with row-count verification.
**Acceptance Criteria:** Row counts in `digistack_cbs` match the pre-migration shared DB exactly for every migrated table.
**Enterprise Outcome:** First proof that data ownership can move without data loss — the foundation the rest of the version depends on.

---

### Sprint 3
**Sprint Goal:** Relocate v16's REST/SOAP endpoints and v15/v19's SIBus/MQ messaging into CBS.
**Learning Objective:** Service and messaging relocation without breaking existing contracts.
**Business Features:** Fund Transfer (internal via SIBus, external via MQ), Balance Inquiry (REST), Account Statement (SOAP) — relocated, not re-designed.
**Application Development:**
- UI: N/A
- Backend: `FundTransferService`, `AccountService`, `TransactionService` implemented in CBS, calling `jdbc/CBSDataSource`
- Database: N/A (reuses Sprint 2 migration)
- API: Same REST/SOAP contracts as P02 v16 — same paths, same WSDL, now served by CBS

**WebSphere Administration:**
- Configuration: SIBus Bus Member, JMS Queue, Activation Spec, MQ JNDI bindings re-pointed to CBS module
- Deployment: Redeploy `digistack-cbs-v23.ear` with relocated services
- JDBC / JMS / JNDI: SIBus/MQ Connection Factories re-bound to CBS's JNDI namespace
- Security: N/A this sprint

**Dependencies:** P02 v15 SIBus/MDB, P02 v16 REST/SOAP, P02 v19 MQ.
**Deliverables:** REST/SOAP endpoints and JMS/MQ relocated into CBS, contracts unchanged.
**Acceptance Criteria:** A Postman/SoapUI call against the existing v16 endpoint paths succeeds and is now served by CBS; an external Fund Transfer still round-trips via MQ.
**Enterprise Outcome:** Confirms relocation is invisible to existing clients — a real-world zero-breaking-change migration pattern.

---

### Sprint 4
**Sprint Goal:** Cut over Internet Banking Portal to call CBS exclusively; decommission Portal's direct DB access.
**Learning Objective:** Enforcing the single-writer rule at the application boundary, not just by convention.
**Business Features:** All existing Portal transactions (Deposit, Withdraw, Balance, Fund Transfer) now proxy through CBS.
**Application Development:**
- UI: No visible change — same screens, now backed by REST/SOAP calls instead of direct JDBC
- Backend: Portal's DAO layer removed; replaced with CBS REST/SOAP client stubs (`client/`)
- Database: Portal's old DataSource unbound from JNDI
- API: N/A (consumes CBS's existing contracts)

**WebSphere Administration:**
- Configuration: Remove Portal's JDBC Provider/DataSource JNDI binding entirely
- Deployment: Redeploy Portal EAR with DB access removed
- JDBC / JMS / JNDI: Negative test — confirm Portal's old DataSource JNDI lookup now fails
- Security: N/A this sprint

**Dependencies:** Sprint 3's relocated endpoints, ARCH01 P-5 (presentation-only channels).
**Deliverables:** Portal redeployed with zero direct DB access; negative test proving old DataSource lookup fails.
**Acceptance Criteria:** Every existing Portal transaction still works end-to-end via CBS; a deliberate JNDI lookup of Portal's old DataSource throws a NameNotFoundException.
**Enterprise Outcome:** The Governing Rule ("only CBS writes to digistack_cbs") becomes enforced, not aspirational — the architectural pivot point of the whole project.

---

### Sprint 5
**Sprint Goal:** Extract Notification Service and Reporting Service as independent EARs.
**Learning Objective:** Satellite service extraction — independent deployment lifecycle, event/data consumption without write access.
**Business Features:** Withdraw-triggered email (relocated from P01 v13), Transaction Report + Account Statement (relocated from P01 v14/P02 v16).
**Application Development:**
- UI: N/A (Reporting Service reuses existing report output)
- Backend: `NotificationService.ear` (consumes CBS-published MQ events), `ReportingService.ear` (reads `digistack_cbs` directly — accepted tradeoff)
- Database: Negative-test grants — both services' DB accounts are read-only or no-access
- API: N/A this sprint

**WebSphere Administration:**
- Configuration: Two new independent application deployments, own classloaders/context roots
- Deployment: Deploy `digistack-notificationservice-v23.ear`, `digistack-reportingservice-v23.ear`
- JDBC / JMS / JNDI: Notification Service binds to CBS's published MQ topic; Reporting Service granted read-only DB access
- Security: Negative test — confirm neither service can write to `digistack_cbs`

**Dependencies:** Sprint 3's CBS event publish, P01 v13/v14.
**Deliverables:** Two new independent EARs live; negative write-access tests passing.
**Acceptance Criteria:** A Withdraw still triggers an email via Notification Service; a Transaction Report still generates via Reporting Service; both confirmed unable to write to `digistack_cbs`.
**Enterprise Outcome:** First genuine multi-EAR satellite topology — independent scaling/deployment lifecycle proven, not just theorized.

---

### Sprint 6
**Sprint Goal:** Full migration validation, documentation, and sign-off of Version 23.
**Learning Objective:** End-to-end validation discipline for a version that changes data, endpoint, and infrastructure ownership simultaneously.
**Business Features:** None (validation sprint).
**Application Development:** Bug-fix only, no new work.
**WebSphere Administration:**
- Configuration: Final review of Ownership Matrix (JNDI names, config references) against what was actually built
- Deployment: Redeploy final versions of `digistack-cbs-v23.ear`, `digistack-portal-v23.ear`, `digistack-notificationservice-v23.ear`, `digistack-reportingservice-v23.ear`
- JDBC / JMS / JNDI: Full regression of every JNDI-bound resource across all four EARs
- Security: Full negative-test pass (Portal DB access, Notification/Reporting write access)

**Dependencies:** Sprints 1–5 complete.
**Deliverables:** `SetupDoc-v23.md` (with required "Migration & Ownership Transfer" section per STDGAP01 §3.8), `TestCases-v23.md` (including negative tests), old Portal DB formally decommissioned.
**Acceptance Criteria:** Every item in the Ownership Matrix confirmed correct; full regression pack (all prior TestCases-v1–v22) passes against the new topology; no open Critical/High defects.
**Enterprise Outcome:** Version 23 signed off — the architectural pivot is complete and verified, not just deployed. CBS is now the single system of record for the rest of the roadmap.

---

## Version 23 Deliverables
- `digistack-cbs-v23.ear`, `digistack-portal-v23.ear`, `digistack-notificationservice-v23.ear`, `digistack-reportingservice-v23.ear`
- `V23__migrate_existing_data_to_cbs.sql`
- JDBC/JNDI configuration exports, SIBus/MQ re-binding exports
- SetupDoc-v23.md (with Migration & Ownership Transfer section), TestCases-v23.md

## Version 23 Exit Criteria
- ✅ Application functionality complete (CBS is sole writer; Portal, Notification, Reporting all confirmed presentation/consumption-only)
- ✅ Database validated (row-count-verified migration; old shared DB decommissioned)
- ✅ WebSphere deployment successful (4 independent EARs operational on existing cluster)
- ✅ Smoke testing passed (full regression + all negative ownership tests)
- ✅ Ready for Version 24

## Lessons Learned
- **Key learnings:** Relocating endpoints/messaging without changing contracts is what makes a migration invisible to clients — the real skill is in Sprint 3/4's discipline, not in writing new business logic.
- **Known issues:** None expected if Sprint 2's row-count verification is completed before Sprint 4's cutover begins.
- **Technical debt:** Reporting Service's direct OLTP read is a deliberately accepted tradeoff (per ARCH01 Risks), closed later at P09 v64's RDS read replica — documented, not hidden.

---
---

# Version 24 — Customer Information File (CIF) & Account Lifecycle

## Version Overview

**Version Objective:** Introduce the Customer Information File (CIF) as the master customer repository used by every banking channel, supporting one customer → multiple accounts, with simulated Aadhaar/PAN verification gating creation.

**Business Scope:** Create CIF, Modify CIF, Customer Search, Aadhaar Verification, PAN Verification, Primary Holder, Nominee.

**WebSphere Focus:** Multi-module EAR, Shared Libraries, Service-to-Service Communication, JDBC Transactions, Data Integrity.

**Expected Outcome:** A single CIF record supports multiple linked accounts; Aadhaar/PAN verification (simulated) gates CIF creation; CIF Service and Account Service communicate as separate modules within CBS's multi-module EAR, not as one monolithic class.

**Prerequisites:** P03 Version 23 Completion Checkpoint satisfied — CBS is sole writer of `digistack_cbs`, Internet Banking Portal calls CBS exclusively, Notification/Reporting Services live as independent EARs.

---

### Sprint 1
**Sprint Goal:** Design and create the CIF schema as the new master customer table.
**Learning Objective:** Data modeling for a master-record entity that supersedes the simpler v15 Customer table.
**Business Features:** CIF record structure (Primary Holder, Nominee fields).
**Application Development:**
- UI: N/A this sprint
- Backend: N/A this sprint
- Database: `V24__create_cif_table.sql` (cif_id, primary_holder fields, nominee fields, verification status)
- API: N/A

**WebSphere Administration:**
- Configuration: N/A this sprint
- Deployment: N/A this sprint
- JDBC / JMS / JNDI: Reuses `jdbc/CBSDataSource` (v23)
- Security: N/A this sprint

**Dependencies:** P03 v23's `digistack_cbs`, `jdbc/CBSDataSource`.
**Deliverables:** `cif` table live in `digistack_cbs`.
**Acceptance Criteria:** Table created with correct constraints (PK/FK per STD naming); a manual insert/select round-trips correctly.
**Enterprise Outcome:** Master customer data model established as its own first-class entity, distinct from the old `users`/simple `customer_id` from P02 v15.

---

### Sprint 2
**Sprint Goal:** Build the CIF Service module within CBS's multi-module EAR.
**Learning Objective:** Structuring a new service module inside an existing multi-module EAR without disturbing existing modules.
**Business Features:** Create CIF, Modify CIF.
**Application Development:**
- UI: CIF creation/edit form (Internet Banking Portal)
- Backend: `CIFService` (session bean) in CBS's `service/` package
- Database: N/A (reuses Sprint 1 schema)
- API: REST endpoint for CIF create/modify

**WebSphere Administration:**
- Configuration: New module registered within `digistack-cbs-v24.ear`'s multi-module structure, shared library dependency wiring
- Deployment: Redeploy `digistack-cbs-v24.ear`
- JDBC / JMS / JNDI: N/A (reuses existing DataSource)
- Security: N/A this sprint

**Dependencies:** Sprint 1's `cif` table, P03 v23's CBS EAR scaffold.
**Deliverables:** Working CIF create/modify flow.
**Acceptance Criteria:** A new CIF record is created via the Portal UI and persists correctly; modifying an existing CIF record updates the correct row.
**Enterprise Outcome:** First new service module added cleanly to the multi-module EAR pattern established at v23.

---

### Sprint 3
**Sprint Goal:** Link Account to CIF, enabling multiple accounts per customer.
**Learning Objective:** Service-to-service communication within the same JVM (CIF Service ↔ Account Service).
**Business Features:** One CIF can hold 2+ linked accounts.
**Application Development:**
- UI: Account list view updated to show accounts grouped under one CIF
- Backend: `AccountService` updated to reference `cif_id` FK; CIF Service and Account Service call each other directly in-process (per ARCH02 CBS internal layering rule)
- Database: `V25__add_cif_id_to_accounts.sql` (FK from accounts to cif)
- API: N/A this sprint

**WebSphere Administration:**
- Configuration: N/A this sprint
- Deployment: Redeploy `digistack-cbs-v24.ear`
- JDBC / JMS / JNDI: N/A (single local transaction, same JVM)
- Security: N/A this sprint

**Dependencies:** Sprint 2's CIF Service, P02 v15's multi-account Account model.
**Deliverables:** Accounts correctly linked to CIF via FK.
**Acceptance Criteria:** A test CIF has 2+ accounts, all correctly attributed and independently balance-checkable via the existing Balance Inquiry endpoint.
**Enterprise Outcome:** Demonstrates CBS's internal layering rule in practice — CIF Service and Account Service collaborate in one local transaction, no network call between them.

---

### Sprint 4
**Sprint Goal:** Implement simulated Aadhaar and PAN verification gating CIF creation.
**Learning Objective:** Business validation logic layered ahead of a persistence operation, with a hard gate (not just a warning).
**Business Features:** Aadhaar Verification, PAN Verification.
**Application Development:**
- UI: Aadhaar/PAN entry fields added to CIF creation form, inline validation feedback
- Backend: `VerificationService` (simulated rule-based check, not a real government API call)
- Database: `verification_status` column on `cif` (from Sprint 1) populated by this check
- API: N/A this sprint

**WebSphere Administration:**
- Configuration: N/A this sprint
- Deployment: Redeploy `digistack-cbs-v24.ear`
- JDBC / JMS / JNDI: N/A this sprint
- Security: N/A this sprint

**Dependencies:** Sprint 2's CIF Service.
**Deliverables:** Working Aadhaar/PAN gate on CIF creation.
**Acceptance Criteria:** A CIF creation attempt with an invalid/malformed Aadhaar or PAN is rejected before the record persists; a valid pair succeeds.
**Enterprise Outcome:** Proves a real-world compliance gate pattern — validation blocks persistence, it doesn't just log a warning after the fact.

---

### Sprint 5
**Sprint Goal:** Implement Customer Search across CIF records.
**Learning Objective:** Query design against a growing master table, transaction isolation for read-heavy operations.
**Business Features:** Customer Search (by name, CIF ID, Aadhaar/PAN last-4).
**Application Development:**
- UI: Search form + results list (Internet Banking Portal / internal use)
- Backend: `CIFService.search()` method, parameterized query (no raw SQL concatenation)
- Database: Index added if needed (`idx_cif_<column>` per STD naming)
- API: REST endpoint for search

**WebSphere Administration:**
- Configuration: N/A this sprint
- Deployment: Redeploy `digistack-cbs-v24.ear`
- JDBC / JMS / JNDI: Confirm connection pool handles search query load without exhaustion
- Security: PII masking confirmed on any Aadhaar/PAN shown in search results (last-4 only, applied proactively per P04 v31's forthcoming discipline)

**Dependencies:** Sprint 1's `cif` table, Sprint 4's verification data.
**Deliverables:** Working Customer Search.
**Acceptance Criteria:** A search by partial name returns the correct CIF record(s); Aadhaar/PAN in results are masked to last-4 digits only.
**Enterprise Outcome:** First read-heavy query pattern against the CIF master table, with PII discipline applied early rather than retrofitted.

---

### Sprint 6
**Sprint Goal:** Full validation and sign-off of Version 24.
**Learning Objective:** Confirming multi-module EAR changes don't regress anything built at v23.
**Business Features:** None (validation sprint).
**Application Development:** Bug-fix only, no new work.
**WebSphere Administration:**
- Configuration: Final review of CIF/Account module wiring within the multi-module EAR
- Deployment: Redeploy final `digistack-cbs-v24.ear`
- JDBC / JMS / JNDI: Full regression of v23's JNDI-bound resources, confirming no drift from this version's changes
- Security: Re-run v23's negative ownership tests to confirm no regression

**Dependencies:** Sprints 1–5 complete, all of v23.
**Deliverables:** SetupDoc-v24.md, TestCases-v24.md.
**Acceptance Criteria:** CIF/Account/Verification/Search all pass together in one combined validation pass; full regression pack (v1–v23) passes; no open Critical/High defects.
**Enterprise Outcome:** Version 24 signed off — CIF becomes the master customer repository every future channel and satellite service will reference going forward.

---

## Version 24 Deliverables
- `digistack-cbs-v24.ear` (CIF Service, updated Account Service)
- `V24__create_cif_table.sql`, `V25__add_cif_id_to_accounts.sql`
- SetupDoc-v24.md, TestCases-v24.md

## Version 24 Exit Criteria
- ✅ Application functionality complete (CIF create/modify/search, Aadhaar/PAN gating, multi-account linkage)
- ✅ Database validated (V24–V25 migrations applied and verified, FK integrity confirmed)
- ✅ WebSphere deployment successful (new module integrated into existing multi-module EAR, no regression)
- ✅ Smoke testing passed (full regression against v1–v23 passes)
- ✅ Ready for Version 25

## Lessons Learned
- **Key learnings:** Adding a new service module to an already-multi-module EAR is low-risk when the internal layering rule (services call each other in-process) is followed consistently from v23 onward.
- **Known issues:** None expected if Sprint 3's FK migration is verified before Sprint 4's verification gate work begins.
- **Technical debt:** None new. "Enterprise Validation" content folded directly into Sprint 4/Enterprise Learning.

---
---

# Version 25 — Payment Systems (NEFT, IMPS)

## Version Overview

**Version Objective:** Develop DigiStack Payment Hub as its own independent EAR, responsible for routing all electronic payments (NEFT, IMPS), coordinating settlement without ever writing balances directly.

**Business Scope:** NEFT Transfer, IMPS Transfer, Add/Modify/Delete Beneficiary, Payment Validation, Beneficiary Validation, Payment Routing, Settlement, Failed Payment Handling.

**WebSphere Focus:** JMS, IBM MQ, Distributed Transactions (Saga, not XA), Asynchronous Processing, Retry Mechanisms, Queue Monitoring, Transaction Recovery, Multi-EAR startup dependency management.

**Expected Outcome:** IMPS transfer completes in real time (synchronous validation, async settlement via JMS/MQ); NEFT transfer is processed in a batch window; a deliberately failed payment is retried per policy and, if unresolved, lands in a reviewable failed-payment queue; Payment Hub never writes directly to `digistack_cbs` — all confirmed settlements are written by CBS.

**Prerequisites:** P03 Version 24 Completion Checkpoint satisfied — CIF supports multiple linked accounts, Aadhaar/PAN verification gating CIF creation, Customer Search live.

---

### Sprint 1
**Sprint Goal:** Scaffold Payment Hub as a new, independent EAR with a startup dependency on CBS.
**Learning Objective:** Multi-EAR startup-dependency ordering — Payment Hub must come up after CBS is available.
**Business Features:** None (pure infrastructure).
**Application Development:**
- UI: N/A this sprint
- Backend: `digistack-paymenthub-v25.ear` skeleton — `controller/`, `service/`, `client/` (REST/SOAP client stub calling CBS)
- Database: N/A (Payment Hub never gets its own writable schema — routes only)
- API: N/A this sprint

**WebSphere Administration:**
- Configuration: New independent application deployment target on existing cluster; startup order configured so Payment Hub starts after CBS
- Deployment: Deploy `digistack-paymenthub-v25.ear` skeleton
- JDBC / JMS / JNDI: N/A this sprint
- Security: N/A this sprint

**Dependencies:** P03 v23's CBS (must be running first), STD Deployment Dependency Order.
**Deliverables:** Payment Hub EAR skeleton deployed, startup-order dependency confirmed.
**Acceptance Criteria:** Restarting the cluster confirms Payment Hub only starts successfully once CBS is up; a deliberate CBS-down test shows Payment Hub failing its own startup health check.
**Enterprise Outcome:** First genuine cross-EAR startup dependency practiced in this roadmap — real multi-application admin territory.

---

### Sprint 2
**Sprint Goal:** Implement Beneficiary management (Add/Modify/Delete) as CBS-facing calls from Payment Hub.
**Learning Objective:** Payment Hub as a pure coordinator — even beneficiary data lives in CBS, not locally.
**Business Features:** Add Beneficiary, Modify Beneficiary, Delete Beneficiary.
**Application Development:**
- UI: Beneficiary management form (Internet Banking Portal)
- Backend: `BeneficiaryService` in Payment Hub, delegates persistence to CBS via REST/SOAP client stub
- Database: N/A (Beneficiary table already exists in `digistack_cbs` per P02 v15/v23 migration)
- API: REST endpoints for Add/Modify/Delete Beneficiary

**WebSphere Administration:**
- Configuration: N/A this sprint
- Deployment: Redeploy `digistack-paymenthub-v25.ear`
- JDBC / JMS / JNDI: N/A (Payment Hub has no DataSource of its own)
- Security: N/A this sprint

**Dependencies:** Sprint 1's Payment Hub skeleton, P02 v15 Beneficiary table (migrated to `digistack_cbs` at v23).
**Deliverables:** Working Beneficiary CRUD via Payment Hub.
**Acceptance Criteria:** A beneficiary added via Payment Hub's endpoint is correctly persisted in `digistack_cbs` by CBS, not by Payment Hub directly.
**Enterprise Outcome:** Reinforces the Governing Rule at a new service — Payment Hub touches beneficiary data only through CBS, never directly.

---

### Sprint 3
**Sprint Goal:** Implement IMPS as a real-time, synchronous-validation/async-settlement transfer.
**Learning Objective:** Real-time payment path design — validate synchronously, settle asynchronously via JMS.
**Business Features:** IMPS Transfer.
**Application Development:**
- UI: IMPS transfer form (amount, beneficiary)
- Backend: `PaymentRoutingService.routeIMPS()` — synchronous validation, then publishes a settlement message to CBS's SIBus queue (reused from P02 v15)
- Database: N/A (settlement write happens in CBS)
- API: REST endpoint for IMPS transfer

**WebSphere Administration:**
- Configuration: N/A this sprint
- Deployment: Redeploy `digistack-paymenthub-v25.ear`
- JDBC / JMS / JNDI: Payment Hub's JMS Connection Factory bound to CBS's existing SIBus queue (P02 v15) via JNDI
- Security: N/A this sprint

**Dependencies:** Sprint 2's Beneficiary flow, P02 v15's SIBus/JMS Queue/MDB.
**Deliverables:** Working IMPS transfer, real-time response with async settlement confirmation.
**Acceptance Criteria:** An IMPS transfer returns a real-time acceptance response; settlement completes within seconds and is confirmed back to CBS's ledger, never written by Payment Hub itself.
**Enterprise Outcome:** First payment product that behaves like a real-time bank rail — synchronous UX, asynchronous integrity.

---

### Sprint 4
**Sprint Goal:** Implement NEFT as a batch-window processed transfer.
**Learning Objective:** Batch-window financial processing pattern, distinct from IMPS's real-time path.
**Business Features:** NEFT Transfer.
**Application Development:**
- UI: NEFT transfer form (amount, beneficiary) — shows "processed in next batch window" messaging
- Backend: `PaymentRoutingService.routeNEFT()` — queues request, `NEFTBatchProcessor` (EJB Timer-driven) processes accumulated requests in a scheduled window
- Database: `V26__create_neft_batch_queue.sql` (staging table for queued NEFT requests, status: QUEUED/PROCESSED/FAILED)
- API: REST endpoint for NEFT transfer submission

**WebSphere Administration:**
- Configuration: EJB Timer Service scheduled job configured for the batch window
- Deployment: Redeploy `digistack-paymenthub-v25.ear`
- JDBC / JMS / JNDI: N/A this sprint
- Security: N/A this sprint

**Dependencies:** Sprint 3's routing infrastructure.
**Deliverables:** Working NEFT batch queue and scheduled processor.
**Acceptance Criteria:** A NEFT transfer submitted outside the batch window sits QUEUED; when the scheduled window runs, it processes and settles correctly via CBS.
**Enterprise Outcome:** Demonstrates the real NEFT-vs-IMPS distinction that a real bank's payment switch must handle — not just two REST endpoints with different names.

---

### Sprint 5
**Sprint Goal:** Implement retry logic and failed-payment handling for both NEFT and IMPS.
**Learning Objective:** Standardized retry policy design, reviewable failure queue as a production-support artifact.
**Business Features:** Failed Payment Handling.
**Application Development:**
- UI: Failed Payments review screen (internal/ops use)
- Backend: `RetryPolicy` (max attempts, exponential backoff — shared constant, reused later by P08's shared retry constants), `FailedPaymentQueue` for exhausted retries
- Database: `V27__create_failed_payment_queue.sql`
- API: REST endpoint to list/review failed payments

**WebSphere Administration:**
- Configuration: N/A this sprint
- Deployment: Redeploy `digistack-paymenthub-v25.ear`
- JDBC / JMS / JNDI: Confirm MQ-level DLQ (P02 v19) still correctly catches unroutable settlement messages
- Security: N/A this sprint

**Dependencies:** Sprint 3/4's IMPS/NEFT flows, P02 v19's MQ DLQ.
**Deliverables:** Working retry + failed-payment review flow.
**Acceptance Criteria:** A deliberately failing payment (e.g., invalid beneficiary mid-flight) retries per policy, then lands in the reviewable Failed Payments queue after exhausting attempts.
**Enterprise Outcome:** First standardized retry/failure-handling pattern in the roadmap — the template P08 later reuses as shared retry constants.

---

### Sprint 6
**Sprint Goal:** Full validation and sign-off of Version 25.
**Learning Objective:** Confirming Payment Hub's coordination pattern holds end-to-end without ever writing balances.
**Business Features:** None (validation sprint).
**Application Development:** Bug-fix only, no new work.
**WebSphere Administration:**
- Configuration: Final review of startup-order dependency (Sprint 1) under a full cluster restart
- Deployment: Redeploy final `digistack-paymenthub-v25.ear`
- JDBC / JMS / JNDI: Full regression of JMS/MQ bindings across CBS and Payment Hub
- Security: Negative test — confirm Payment Hub has no DataSource/DB write path to `digistack_cbs` at all

**Dependencies:** Sprints 1–5 complete.
**Deliverables:** SetupDoc-v25.md, TestCases-v25.md (including the Payment-Hub-never-writes negative test).
**Acceptance Criteria:** IMPS/NEFT/retry/failed-payment flows all pass together; negative test confirms zero direct write path from Payment Hub; full regression pack (v1–v24) passes.
**Enterprise Outcome:** Version 25 signed off — Payment Hub proven as a genuine Saga-pattern coordinator, the deliberate architectural counterpoint to CBS's single-writer design.

---

## Version 25 Deliverables
- `digistack-paymenthub-v25.ear`
- `V26__create_neft_batch_queue.sql`, `V27__create_failed_payment_queue.sql`
- JMS/MQ binding exports, EJB Timer Service configuration export
- SetupDoc-v25.md, TestCases-v25.md

## Version 25 Exit Criteria
- ✅ Application functionality complete (Beneficiary CRUD, IMPS real-time, NEFT batch, retry/failed-payment handling)
- ✅ Database validated (V26–V27 migrations applied and verified; confirmed Payment Hub holds no writable balance data)
- ✅ WebSphere deployment successful (independent EAR, startup-order dependency on CBS proven)
- ✅ Smoke testing passed (full regression + Payment-Hub-never-writes negative test)
- ✅ Ready for Version 26

## Lessons Learned
- **Key learnings:** Modeling Payment Hub as a Saga coordinator rather than a distributed-XA participant kept failure domains cleanly separated — CBS's local transaction remains the only place atomic balance updates happen.
- **Known issues:** None expected if Sprint 1's startup-order dependency is verified before Sprint 3/4's routing logic is exercised.
- **Technical debt:** None new — "Reconciliation" is deliberately deferred to P03 v29's EOD process, not built here.

---
---

# Version 26 — Mobile Banking Simulator (Tomcat — mobile.digistack.cloud)

## Version Overview

**Version Objective:** Build Mobile Banking as a small, standalone, neat-UI application deployed on Apache Tomcat (not WebSphere), under its own subdomain, calling CBS exclusively via REST — proving heterogeneous WAS+Tomcat topology routing at the IHS/LB tier.

**Business Scope:** Mobile Login (delegates auth to CBS, same MFA/OTP rules as P02 v17), Balance Inquiry, Mini Statement, Fund Transfer (IMPS only), Quick Pay to Saved Beneficiary.

**WebSphere Focus:** Heterogeneous backend routing (WAS + Tomcat behind one IHS/LB tier), Virtual Host configuration per subdomain, Reverse proxy rules distinguishing plugin-routed vs. proxy-routed traffic, SSL/certificate handling for the new subdomain, Cross-server topology documentation.

**Expected Outcome:** `mobile.digistack.cloud` resolves through the IHS/LB tier and routes to Tomcat (not the WAS plugin); a customer can log in (CBS auth, MFA intact), check balance, view mini statement, and complete an IMPS Quick Pay — all via REST calls to CBS, zero direct database access from the Tomcat app.

**Prerequisites:** P03 Version 25 Completion Checkpoint satisfied — Payment Hub live, IMPS/NEFT functional, Beneficiary CRUD operational.

---

### Sprint 1
**Sprint Goal:** Install Apache Tomcat and stand up the Mobile Banking app skeleton.
**Learning Objective:** Introducing a non-WAS runtime into the existing enterprise topology for the first time.
**Business Features:** None (pure infrastructure).
**Application Development:**
- UI: Static "Mobile Banking" landing page (Bootstrap 5 shell)
- Backend: Servlet/JSP skeleton, `digistack-mobile` project scaffold (own Git repo per doc 02)
- Database: N/A (Mobile never touches the DB directly)
- API: N/A this sprint

**WebSphere Administration:**
- Configuration: Apache Tomcat installed on `dsb-tomcat` (per STD VM Hostnames), context root configured
- Deployment: Deploy Mobile skeleton WAR to Tomcat
- JDBC / JMS / JNDI: N/A this sprint
- Security: N/A this sprint

**Dependencies:** STD §VM Hostnames (`dsb-tomcat`).
**Deliverables:** Tomcat installed, Mobile skeleton WAR deployed and reachable directly on Tomcat's port.
**Acceptance Criteria:** Browser hit directly against Tomcat's port shows the static landing page.
**Enterprise Outcome:** First non-WebSphere runtime introduced into the enterprise estate — practicing real multi-vendor server administration.

---

### Sprint 2
**Sprint Goal:** Route `mobile.digistack.cloud` through IHS to Tomcat, not the WAS plugin.
**Learning Objective:** Virtual host routing distinguishing plugin-routed traffic from standard reverse-proxy traffic on the same IHS instance.
**Business Features:** None (pure infrastructure).
**Application Development:** N/A this sprint.
**WebSphere Administration:**
- Configuration: IHS Virtual Host rule for `mobile.digistack.cloud` → Tomcat (standard reverse proxy, not plugin-cfg.xml)
- Deployment: N/A this sprint
- JDBC / JMS / JNDI: N/A this sprint
- Security: SSL certificate provisioned for the new subdomain (per CI01 §5.1 naming convention)

**Dependencies:** Sprint 1's Tomcat install, P02 v20's IHS advanced admin, P02 v21's Enterprise LB.
**Deliverables:** `mobile.digistack.cloud` correctly routed via IHS to Tomcat.
**Acceptance Criteria:** Browser hit on `mobile.digistack.cloud` reaches the Sprint 1 landing page via IHS, confirmed via access logs showing proxy (not plugin) routing.
**Enterprise Outcome:** First proof that IHS can route distinct subdomains to entirely different backend server types on the same instance — the actual valuable admin skill this version teaches.

---

### Sprint 3
**Sprint Goal:** Implement Mobile Login, delegating authentication to CBS.
**Learning Objective:** A channel app authenticating against a shared identity source rather than maintaining its own user store.
**Business Features:** Mobile Login (MFA/OTP intact, per P02 v17).
**Application Development:**
- UI: Login screen (username/password + OTP), Bootstrap 5
- Backend: `MobileLoginServlet` calls CBS's authentication REST endpoint (relocated to CBS at v23, hardened at P02 v17)
- Database: N/A
- API: Consumes CBS's existing auth/MFA REST contract

**WebSphere Administration:**
- Configuration: N/A this sprint
- Deployment: Redeploy Mobile WAR to Tomcat
- JDBC / JMS / JNDI: N/A this sprint
- Security: Token/API key required per v17's hardened endpoint contract

**Dependencies:** Sprint 2's routing, P02 v17's MFA/OTP + CBS relocation (v23).
**Deliverables:** Working Mobile Login with MFA intact.
**Acceptance Criteria:** Login fails without correct OTP; succeeds with correct OTP; no local user store exists in the Mobile app.
**Enterprise Outcome:** Proves channel apps can share one authentication source cleanly — no duplicated identity logic across 9 applications.

---

### Sprint 4
**Sprint Goal:** Implement Balance Inquiry and Mini Statement via CBS REST/SOAP.
**Learning Objective:** Consuming existing CBS contracts from a completely different runtime (Tomcat vs. WAS) with zero contract changes.
**Business Features:** Balance Inquiry, Mini Statement.
**Application Development:**
- UI: Balance display, Mini Statement list view
- Backend: `BalanceServlet`, `StatementServlet` — REST client calls to CBS's existing Balance Inquiry (v16) and SOAP Account Statement (v16) endpoints
- Database: N/A
- API: Consumes existing CBS contracts, unchanged

**WebSphere Administration:**
- Configuration: N/A this sprint
- Deployment: Redeploy Mobile WAR
- JDBC / JMS / JNDI: N/A this sprint
- Security: N/A this sprint

**Dependencies:** Sprint 3's authenticated session, P02 v16's REST/SOAP contracts.
**Deliverables:** Working Balance Inquiry and Mini Statement screens.
**Acceptance Criteria:** A logged-in customer sees their correct live balance and a correctly ordered mini statement.
**Enterprise Outcome:** Confirms REST/SOAP contracts are genuinely runtime-agnostic — the same endpoint serves both WAS-hosted Internet Banking and Tomcat-hosted Mobile identically.

---

### Sprint 5
**Sprint Goal:** Implement Fund Transfer (IMPS only) and Quick Pay to Saved Beneficiary.
**Learning Objective:** Consuming Payment Hub's IMPS path from a second, independent channel.
**Business Features:** Fund Transfer (IMPS only), Quick Pay to Saved Beneficiary.
**Application Development:**
- UI: IMPS transfer form, Quick Pay shortcut (pre-filled from saved beneficiaries)
- Backend: `QuickPayServlet` calls Payment Hub's IMPS REST endpoint (P03 v25); Beneficiary list pulled via CBS's Beneficiary contract
- Database: N/A
- API: Consumes Payment Hub's existing IMPS endpoint, unchanged

**WebSphere Administration:**
- Configuration: N/A this sprint
- Deployment: Redeploy Mobile WAR
- JDBC / JMS / JNDI: N/A this sprint
- Security: N/A this sprint

**Dependencies:** P03 v25's IMPS/Beneficiary endpoints, Sprint 4's authenticated session.
**Deliverables:** Working IMPS Quick Pay from Mobile.
**Acceptance Criteria:** A Quick Pay to a saved beneficiary completes successfully via IMPS, confirmed against CBS's ledger — no NEFT option exposed on Mobile (real-time-bias, by design).
**Enterprise Outcome:** Second independent consumer of Payment Hub's IMPS path — validates the endpoint is genuinely reusable across channels, not hardcoded to Internet Banking.

---

### Sprint 6
**Sprint Goal:** Full validation and sign-off of Version 26.
**Learning Objective:** Confirming a heterogeneous WAS+Tomcat topology holds together end-to-end.
**Business Features:** None (validation sprint).
**Application Development:** Bug-fix only, no new work.
**WebSphere Administration:**
- Configuration: Final review of virtual host routing rules (mobile → Tomcat vs. all other subdomains → WAS plugin)
- Deployment: Redeploy final Mobile WAR
- JDBC / JMS / JNDI: N/A this sprint
- Security: Negative test — confirm Mobile app has zero direct database connectivity of any kind

**Dependencies:** Sprints 1–5 complete.
**Deliverables:** SetupDoc-v26.md, TestCases-v26.md.
**Acceptance Criteria:** Login, Balance, Mini Statement, and IMPS Quick Pay all pass together in one combined validation pass; negative DB-access test confirms zero direct connectivity; full regression pack (v1–v25) passes.
**Enterprise Outcome:** Version 26 signed off — first Tomcat-based channel proven fully functional and architecturally clean, setting the pattern ATM Simulator (v27) reuses directly.

---

## Version 26 Deliverables
- `digistack-mobile.war` (deployed to Tomcat, not WAS)
- IHS virtual host configuration export (`mobile.digistack.cloud` routing), SSL certificate export
- SetupDoc-v26.md, TestCases-v26.md

## Version 26 Exit Criteria
- ✅ Application functionality complete (Login/MFA, Balance, Mini Statement, IMPS Quick Pay)
- ✅ Database validated (N/A — confirmed zero direct DB access, by design)
- ✅ WebSphere deployment successful (heterogeneous IHS routing to Tomcat proven, virtual host + SSL confirmed)
- ✅ Smoke testing passed (full regression + zero-DB-access negative test)
- ✅ Ready for Version 27

## Lessons Learned
- **Key learnings:** The real admin skill here wasn't Tomcat itself — it was configuring IHS to cleanly distinguish plugin-routed (WAS) traffic from proxy-routed (Tomcat) traffic on the same instance, for the first time in this roadmap.
- **Known issues:** None expected if Sprint 2's virtual host rule is verified before Sprint 3's login work begins.
- **Technical debt:** None new — channel-specific rate limiting at the IHS layer was noted as an optional stretch goal and is deliberately not built here.

---
---

# Version 27 — ATM Simulator (Tomcat — atm.digistack.cloud)

## Version Overview

**Version Objective:** Build the ATM channel as a small, standalone, neat-UI application on Apache Tomcat, simulating physical ATM interactions while calling CBS exclusively via REST/SOAP — reusing the Channel Simulator pattern established at v26.

**Business Scope:** Card/PIN Entry (simulated card swipe → card number + PIN form), Balance Inquiry, Cash Withdrawal, Mini Statement, PIN Change.

**WebSphere Focus:** Connection Pools sized for high-frequency low-latency ATM-style calls, Thread Pool tuning for synchronous request patterns, High Availability considerations specific to always-on ATM traffic.

**Expected Outcome:** ATM Simulator UI (styled to resemble a real ATM screen) performs Balance Inquiry, Cash Withdrawal, Mini Statement, and PIN Change against CBS entirely through REST/SOAP; PIN validation and a blocked/incorrect-PIN negative case are both provable.

**Prerequisites:** P03 Version 26 Completion Checkpoint satisfied — Mobile Banking live on Tomcat, heterogeneous IHS routing proven, zero-DB-access discipline confirmed.

---

### Sprint 1
**Sprint Goal:** Scaffold the ATM Simulator app and route `atm.digistack.cloud` via IHS to Tomcat.
**Learning Objective:** Reusing v26's heterogeneous-routing pattern for a second subdomain on the same Tomcat host.
**Business Features:** None (pure infrastructure).
**Application Development:**
- UI: ATM-screen-styled landing page (Bootstrap 5) — menu: Balance Inquiry / Cash Withdrawal / Mini Statement / Change PIN / Exit
- Backend: `digistack-atm-sim` project scaffold (own Git repo per doc 02)
- Database: N/A (ATM never touches the DB directly)
- API: N/A this sprint

**WebSphere Administration:**
- Configuration: IHS Virtual Host rule for `atm.digistack.cloud` → Tomcat (second subdomain on `dsb-tomcat`, alongside v26's mobile routing)
- Deployment: Deploy ATM skeleton WAR to Tomcat
- JDBC / JMS / JNDI: N/A this sprint
- Security: SSL certificate provisioned for the new subdomain (per CI01 §5.1)

**Dependencies:** Sprint 1-2 pattern from P03 v26, STD §VM Hostnames (`dsb-tomcat`).
**Deliverables:** `atm.digistack.cloud` routed via IHS to Tomcat, ATM-screen landing page live.
**Acceptance Criteria:** Browser hit on `atm.digistack.cloud` reaches the styled landing menu via IHS, confirmed via access logs; `mobile.digistack.cloud` routing (v26) unaffected.
**Enterprise Outcome:** Confirms IHS can host multiple distinct subdomains routed to the same Tomcat instance without cross-contamination — realistic multi-tenant reverse-proxy configuration.

---

### Sprint 2
**Sprint Goal:** Implement simulated Card/PIN entry as the ATM's authentication step.
**Learning Objective:** Card-based auth pattern, distinct from Mobile's username/password/OTP flow.
**Business Features:** Card/PIN Entry.
**Application Development:**
- UI: Simulated card swipe screen (card number entry) → PIN entry screen
- Backend: `ATMAuthServlet` calls CBS's Card Service PIN-check endpoint (build against the contract Card Portal will formalize at v28 — coordinated here per SetupDoc-v27.md)
- Database: N/A
- API: Consumes CBS Card Service authentication contract

**WebSphere Administration:**
- Configuration: N/A this sprint
- Deployment: Redeploy ATM WAR
- JDBC / JMS / JNDI: N/A this sprint
- Security: N/A this sprint

**Dependencies:** Sprint 1's ATM skeleton, P02 v17's security hardening pattern.
**Deliverables:** Working card number + PIN entry flow.
**Acceptance Criteria:** Correct card + PIN combination authenticates successfully; the flow visually resembles a real ATM screen sequence (card entry → PIN entry → menu).
**Enterprise Outcome:** First card-based (not username-based) authentication pattern in this roadmap, setting up the negative-PIN test this version's Sprint Deliverable requires.

---

### Sprint 3
**Sprint Goal:** Implement Balance Inquiry and Mini Statement for the ATM channel.
**Learning Objective:** Reusing existing CBS contracts (already proven runtime-agnostic at v26) from a third consuming application.
**Business Features:** Balance Inquiry, Mini Statement.
**Application Development:**
- UI: Balance display, Mini Statement list (ATM-screen styled)
- Backend: `ATMBalanceServlet`, `ATMStatementServlet` — REST client calls to CBS's existing Balance Inquiry and SOAP Account Statement endpoints (P02 v16)
- Database: N/A
- API: Consumes existing CBS contracts, unchanged

**WebSphere Administration:**
- Configuration: N/A this sprint
- Deployment: Redeploy ATM WAR
- JDBC / JMS / JNDI: Connection pool behavior observed under ATM-style call pattern (short, frequent requests)
- Security: N/A this sprint

**Dependencies:** Sprint 2's card auth, P02 v16's REST/SOAP contracts.
**Deliverables:** Working Balance Inquiry and Mini Statement screens on ATM.
**Acceptance Criteria:** An authenticated card session shows correct live balance and correctly ordered mini statement.
**Enterprise Outcome:** Third independent consumer of the same CBS contracts (Internet Banking, Mobile, now ATM) — reinforces the contract-stability principle from ARCH02.

---

### Sprint 4
**Sprint Goal:** Implement Cash Withdrawal against CBS.
**Learning Objective:** A write operation (not just reads) flowing through the full Check PIN → Check Balance → Debit → Update Ledger chain.
**Business Features:** Cash Withdrawal.
**Application Development:**
- UI: Withdrawal amount entry, simulated cash dispense confirmation screen
- Backend: `ATMWithdrawalServlet` calls CBS's Withdrawal endpoint (P01 v3, exposed via REST since v16); enforces PIN re-check → balance check → debit sequence per the roadmap's request flow
- Database: N/A (write happens in CBS)
- API: Consumes CBS's existing Withdrawal contract

**WebSphere Administration:**
- Configuration: N/A this sprint
- Deployment: Redeploy ATM WAR
- JDBC / JMS / JNDI: N/A this sprint
- Security: N/A this sprint

**Dependencies:** Sprint 3's authenticated flow, P01 v3 Withdrawal, P02 v17 Freeze/Unfreeze enforcement.
**Deliverables:** Working Cash Withdrawal end-to-end.
**Acceptance Criteria:** A withdrawal correctly debits the account in `digistack_cbs` and returns a simulated dispense confirmation; an insufficient-funds attempt is correctly rejected before any debit occurs.
**Enterprise Outcome:** First write-path transaction on the ATM channel, proving the full Check PIN → Check Balance → Debit → Update Ledger sequence documented in the roadmap's request flow.

---

### Sprint 5
**Sprint Goal:** Implement PIN Change and prove the blocked/incorrect-PIN negative case.
**Learning Objective:** Negative-path testing as a first-class deliverable, not an afterthought.
**Business Features:** PIN Change.
**Application Development:**
- UI: PIN Change form (old PIN, new PIN, confirm)
- Backend: `ATMPinChangeServlet` calls CBS's PIN update endpoint; incorrect-PIN lockout logic reuses P02 v17's account lockout pattern
- Database: N/A (write happens in CBS)
- API: Consumes CBS's PIN Change contract

**WebSphere Administration:**
- Configuration: N/A this sprint
- Deployment: Redeploy ATM WAR
- JDBC / JMS / JNDI: N/A this sprint
- Security: Reuses P02 v17's lockout-after-N-failed-attempts pattern for repeated incorrect PIN entry

**Dependencies:** Sprint 2's card auth, P02 v17's lockout pattern.
**Deliverables:** Working PIN Change; proven blocked/incorrect-PIN negative test.
**Acceptance Criteria:** PIN Change succeeds with correct old PIN; a deliberately incorrect PIN is rejected, and repeated incorrect attempts trigger the same lockout behavior proven at P02 v17.
**Enterprise Outcome:** Delivers the specific negative test this version's roadmap Sprint Deliverable calls out by name — a blocked/incorrect-PIN case, provable end-to-end.

---

### Sprint 6
**Sprint Goal:** Full validation and sign-off of Version 27, confirming cross-channel integration with Card Portal groundwork.
**Learning Objective:** Validating a heterogeneous, multi-subdomain Tomcat topology holds under combined load with Mobile (v26).
**Business Features:** None (validation sprint).
**Application Development:** Bug-fix only, no new work.
**WebSphere Administration:**
- Configuration: Final review of both Tomcat subdomains (`mobile.`, `atm.`) routing correctly and independently via IHS
- Deployment: Redeploy final ATM WAR
- JDBC / JMS / JNDI: N/A this sprint
- Security: Negative test — confirm ATM app has zero direct database connectivity; confirm blocked-PIN negative case one final time

**Dependencies:** Sprints 1–5 complete, P03 v26.
**Deliverables:** SetupDoc-v27.md, TestCases-v27.md (including the blocked/incorrect-PIN negative test).
**Acceptance Criteria:** Card auth, Balance, Mini Statement, Withdrawal, and PIN Change all pass together in one combined pass; zero-DB-access confirmed; full regression pack (v1–v26) passes, including Mobile's continued correct operation.
**Enterprise Outcome:** Version 27 signed off — two independent Tomcat-hosted channels (Mobile, ATM) now proven to coexist cleanly on shared infrastructure, ready for Card Portal (v28) to complete the card-lifecycle picture.

---

## Version 27 Deliverables
- `digistack-atm-sim.war` (deployed to Tomcat, alongside v26's Mobile WAR)
- IHS virtual host configuration export (`atm.digistack.cloud` routing), SSL certificate export
- SetupDoc-v27.md, TestCases-v27.md

## Version 27 Exit Criteria
- ✅ Application functionality complete (Card/PIN auth, Balance, Withdrawal, Mini Statement, PIN Change)
- ✅ Database validated (N/A — confirmed zero direct DB access, by design)
- ✅ WebSphere deployment successful (second Tomcat subdomain routed cleanly alongside Mobile's)
- ✅ Smoke testing passed (full regression + blocked/incorrect-PIN negative test)
- ✅ Ready for Version 28

## Lessons Learned
- **Key learnings:** Card Portal's PIN-check contract needed light coordination ahead of its own v28 build (Sprint 2) — a useful reminder that channel simulators and the services they'll eventually integrate against aren't fully independent even when architecturally decoupled.
- **Known issues:** None expected if Sprint 4's withdrawal sequence (PIN re-check → balance check → debit) is verified before Sprint 5's PIN Change/lockout work begins.
- **Technical debt:** None new — full ISO 8583 switch implementation and POS-as-a-separate-app are explicitly out of scope per the roadmap's scope note, not oversights.

---
---

# Version 28 — Card Portal (WebSphere — card.digistack.cloud)

## Version Overview

**Version Objective:** Build the Card Management channel as its own independent WebSphere EAR (not Tomcat), giving customers/staff a portal for card lifecycle operations — calling CBS exclusively via REST/SOAP.

**Business Scope:** Issue Card, Activate Card, Block Card, Generate PIN, Reset PIN, Card Status Lookup, Hotlisting.

**WebSphere Focus:** Deploying and administering a seventh independent WAS EAR alongside Portal/CBS/Payment Hub/Notification/Reporting/Branch Portal; Virtual host routing distinguishing plugin-routed Card Portal traffic from Mobile/ATM's Tomcat-routed traffic on the same IHS instance; Service-to-service call into CBS's Card Service specifically; IBM MQ/JMS touchpoint for async "Card Issued" notification.

**Expected Outcome:** A card can be issued, activated, blocked, and have its PIN reset through the Card Portal UI; a blocked card correctly fails authorization when tested against the ATM Simulator (v27) — proving Card Portal (WAS), ATM Simulator (Tomcat), and CBS are properly integrated across the heterogeneous topology.

**Prerequisites:** P03 Version 27 Completion Checkpoint satisfied — ATM Simulator live, Card/PIN auth flow built (coordinated ahead against this version's contract), blocked/incorrect-PIN negative test proven.

---

### Sprint 1
**Sprint Goal:** Scaffold Card Portal as its own WAS EAR and route `card.digistack.cloud` via IHS to the WAS plugin.
**Learning Objective:** Distinguishing plugin-routed WAS traffic from Tomcat-routed traffic on the same IHS instance, now with three subdomains in play.
**Business Features:** None (pure infrastructure).
**Application Development:**
- UI: Card Portal landing page (Bootstrap 5) — menu shell for Issue/Activate/Block/PIN operations
- Backend: `digistack-cardportal-v28.ear` skeleton — `controller/`, `client/` (REST/SOAP stub calling CBS's Card Service)
- Database: N/A (Card Portal never touches the DB directly)
- API: N/A this sprint

**WebSphere Administration:**
- Configuration: IHS Virtual Host rule for `card.digistack.cloud` → WAS plugin (not Tomcat, unlike v26/v27); new independent EAR deployment target on existing cluster
- Deployment: Deploy Card Portal skeleton to WAS cluster
- JDBC / JMS / JNDI: N/A this sprint
- Security: SSL certificate provisioned for the new subdomain (per CI01 §5.1)

**Dependencies:** P02 v20/v21 IHS/LB admin, STD Deployment Dependency Order.
**Deliverables:** `card.digistack.cloud` routed via IHS to WAS plugin, Card Portal skeleton live.
**Acceptance Criteria:** Browser hit on `card.digistack.cloud` reaches the landing shell via the WAS plugin, confirmed via access logs (plugin-routed, not proxy-routed); `mobile.` and `atm.` routing (v26/v27) unaffected.
**Enterprise Outcome:** Deliberate architectural counterpoint to v26/v27 — proves IHS can route one subdomain to WAS while two others go to Tomcat, all on one instance, giving genuine heterogeneous-topology practice from both directions.

---

### Sprint 2
**Sprint Goal:** Implement Card Issuance and CBS's Card Service, including the async "Card Issued" notification.
**Learning Objective:** A new CBS internal service (Card Service) exposed specifically to Card Portal, plus an MQ/JMS touchpoint triggered by a card lifecycle event.
**Business Features:** Issue Card.
**Application Development:**
- UI: Card Issuance form (linked to CIF/Account)
- Backend: `CardService` (new module within CBS's multi-module EAR) — issues card record; Card Portal's `client/` stub calls this new CBS endpoint specifically (not CBS's Account/Transaction services)
- Database: `V28__create_card_table.sql` (card_number, cif_id FK, status, PIN hash)
- API: REST endpoint for Card Issuance

**WebSphere Administration:**
- Configuration: New CBS module (`CardService`) registered alongside existing CIF/Account/Transaction modules
- Deployment: Redeploy `digistack-cbs-v28.ear` and `digistack-cardportal-v28.ear`
- JDBC / JMS / JNDI: CBS publishes "Card Issued" event to IBM MQ (reusing P02 v19 infrastructure); Notification Service (v23) consumes it
- Security: N/A this sprint

**Dependencies:** Sprint 1's Card Portal skeleton, P03 v24's CIF, P02 v19's MQ.
**Deliverables:** Working Card Issuance with async notification.
**Acceptance Criteria:** A card issued via Card Portal correctly persists in `digistack_cbs` via CBS's Card Service; a "Card Issued" notification is confirmed delivered via Notification Service.
**Enterprise Outcome:** First example in this roadmap of routing to a specific internal CBS service (Card Service, not the monolithic "CBS") — a genuine service-boundary discipline exercise.

---

### Sprint 3
**Sprint Goal:** Implement Card Activation and PIN Generation.
**Learning Objective:** Multi-step card lifecycle state transitions (Issued → Activated) with a dependent PIN-setup step.
**Business Features:** Activate Card, Generate PIN.
**Application Development:**
- UI: Card Activation form, PIN Generation/setup screen
- Backend: `CardService.activate()`, `CardService.generatePin()` — enforces Issued-before-Activated state transition
- Database: N/A (reuses Sprint 2's `card` table, updates `status`/PIN hash)
- API: REST endpoints for Activate Card, Generate PIN

**WebSphere Administration:**
- Configuration: N/A this sprint
- Deployment: Redeploy `digistack-cbs-v28.ear`, `digistack-cardportal-v28.ear`
- JDBC / JMS / JNDI: N/A this sprint
- Security: PIN stored as hash, never plaintext (per STD Golden Rules — no hardcoded/exposed credentials)

**Dependencies:** Sprint 2's Card Issuance.
**Deliverables:** Working Activate Card + Generate PIN flow.
**Acceptance Criteria:** A card cannot be activated before issuance completes; PIN generation succeeds only on an activated card; an attempt to activate an already-active card is correctly rejected.
**Enterprise Outcome:** Proves genuine state-machine discipline on the card lifecycle, not just independent CRUD operations.

---

### Sprint 4
**Sprint Goal:** Implement Card Blocking and Hotlisting, and prove cross-application integration with ATM Simulator.
**Learning Objective:** A write from Card Portal (WAS) that must be immediately observable by a completely different application on a different runtime (ATM Simulator, Tomcat).
**Business Features:** Block Card, Hotlisting.
**Application Development:**
- UI: Block Card / Hotlist form (with reason code)
- Backend: `CardService.block()` — sets card status to BLOCKED/HOTLISTED, checked by CBS's PIN-check endpoint (the same contract ATM's Sprint 2 authentication calls)
- Database: N/A (reuses Sprint 2's `card` table)
- API: REST endpoint for Block Card / Hotlist

**WebSphere Administration:**
- Configuration: N/A this sprint
- Deployment: Redeploy `digistack-cbs-v28.ear`, `digistack-cardportal-v28.ear`
- JDBC / JMS / JNDI: N/A this sprint
- Security: N/A this sprint

**Dependencies:** Sprint 3's Activate/PIN flow, P03 v27's ATM card-auth endpoint (same CBS Card Service contract).
**Deliverables:** Working Block Card/Hotlist, proven against ATM Simulator.
**Acceptance Criteria:** A card blocked via Card Portal is immediately rejected when a card+PIN authentication attempt is made at the ATM Simulator (v27) using that card — no caching/staleness gap.
**Enterprise Outcome:** The version's headline integration proof — Card Portal (WAS) and ATM Simulator (Tomcat) genuinely share live state through CBS, not two apps that happen to look connected.

---

### Sprint 5
**Sprint Goal:** Implement PIN Reset and Card Status Lookup.
**Learning Objective:** Completing the card lifecycle surface with the remaining self-service operations.
**Business Features:** Reset PIN, Card Status Lookup.
**Application Development:**
- UI: PIN Reset form, Card Status lookup view (shows Issued/Activated/Blocked/Hotlisted)
- Backend: `CardService.resetPin()`, `CardService.getStatus()`
- Database: N/A (reuses Sprint 2's `card` table)
- API: REST endpoints for PIN Reset, Card Status Lookup

**WebSphere Administration:**
- Configuration: N/A this sprint
- Deployment: Redeploy `digistack-cbs-v28.ear`, `digistack-cardportal-v28.ear`
- JDBC / JMS / JNDI: N/A this sprint
- Security: PIN Reset reuses the same hashing discipline as Sprint 3's Generate PIN

**Dependencies:** Sprint 3's PIN Generation, Sprint 4's status field.
**Deliverables:** Working PIN Reset and Card Status Lookup.
**Acceptance Criteria:** PIN Reset succeeds on an activated, non-blocked card; Status Lookup correctly reflects the card's current lifecycle state at every stage tested so far.
**Enterprise Outcome:** Closes out the card lifecycle feature set — every state (Issued/Activated/Blocked/Hotlisted) is now both settable and observable end-to-end.

---

### Sprint 6
**Sprint Goal:** Full validation and sign-off of Version 28, confirming the seventh WAS EAR integrates cleanly.
**Learning Objective:** Validating a seven-EAR WAS cluster plus two Tomcat apps all coexist correctly under one IHS/LB tier.
**Business Features:** None (validation sprint).
**Application Development:** Bug-fix only, no new work.
**WebSphere Administration:**
- Configuration: Final review of all three subdomain routing rules (`card.` → WAS plugin, `mobile.`/`atm.` → Tomcat) together
- Deployment: Redeploy final `digistack-cbs-v28.ear`, `digistack-cardportal-v28.ear`
- JDBC / JMS / JNDI: N/A this sprint
- Security: Negative test — confirm Card Portal has zero direct database connectivity; re-confirm Block Card → ATM rejection (Sprint 4) one final time

**Dependencies:** Sprints 1–5 complete, P03 v27.
**Deliverables:** SetupDoc-v28.md, TestCases-v28.md (including the Card Portal-never-writes and Block-Card-blocks-ATM negative/integration tests).
**Acceptance Criteria:** Issue/Activate/Generate PIN/Block/Hotlist/Reset PIN/Status Lookup all pass together in one combined pass; zero-DB-access confirmed; cross-app Block Card integration re-verified; full regression pack (v1–v27) passes.
**Enterprise Outcome:** Version 28 signed off — seven independent WAS EARs plus two Tomcat apps now coexist and cross-integrate correctly, the fullest heterogeneous topology proof point so far in the roadmap.

---

## Version 28 Deliverables
- `digistack-cbs-v28.ear` (new CardService module), `digistack-cardportal-v28.ear`
- `V28__create_card_table.sql`
- IHS virtual host configuration export (`card.digistack.cloud` → WAS plugin), SSL certificate export, MQ "Card Issued" event export
- SetupDoc-v28.md, TestCases-v28.md

## Version 28 Exit Criteria
- ✅ Application functionality complete (Issue/Activate/Generate PIN/Block/Hotlist/Reset PIN/Status Lookup)
- ✅ Database validated (V28 migration applied and verified; Card Portal confirmed zero direct DB access)
- ✅ WebSphere deployment successful (seventh independent WAS EAR live, plugin-routed subdomain confirmed distinct from Tomcat subdomains)
- ✅ Smoke testing passed (full regression + Card Portal negative test + Block-Card/ATM integration re-verification)
- ✅ Ready for Version 29

## Lessons Learned
- **Key learnings:** Sprint 4's cross-application proof (Card Portal block → ATM rejection) is the clearest demonstration yet that CBS's single-writer/system-of-record design genuinely eliminates staleness between independently deployed, differently-hosted applications.
- **Known issues:** None expected if Sprint 2's CardService module registration is verified before Sprint 3's state-transition logic is built.
- **Technical debt:** Card expiry/renewal is a deliberate, documented omission per the roadmap's scope note — not built here, not an oversight.

---
---

# Version 29 — Enterprise Banking Operations (Branch Portal)

## Version Overview

**Version Objective:** Implement day-to-day banking operations performed by branch staff — Branch Portal deployed as its own WebSphere EAR, presentation-only, with scheduled BOD/EOD batch jobs closing the banking day against CBS's ledger.

**Business Scope:** Teller Login, Cash Deposit, Cash Withdrawal, Beginning of Day (BOD), End of Day (EOD), EOD Reconciliation Report (NEFT/IMPS settlement tie-out against CBS ledger).

**WebSphere Focus:** Batch Scheduling, JVM Monitoring, Thread Pools, JMS Batch Queue, Log Analysis, Performance Tuning, Production Troubleshooting.

**Expected Outcome:** A Teller can log in to Branch Portal and process cash deposits/withdrawals against CBS; a scheduled BOD job opens the banking day and an EOD job closes it — including a reconciliation report, generated by Reporting Service, that ties out the day's NEFT/IMPS settlements against CBS's own ledger, surfacing any mismatch — both run as WAS-scheduled batch jobs, not manually triggered code.

**Prerequisites:** P03 Version 28 Completion Checkpoint satisfied — Card Portal live, seven WAS EARs + two Tomcat apps coexisting, Block Card → ATM cross-app integration proven.

---

### Sprint 1
**Sprint Goal:** Scaffold Branch Portal as its own WAS EAR with Teller Login.
**Learning Objective:** An eighth independent WAS EAR, authenticating against the same shared CBS identity source as every other channel.
**Business Features:** Teller Login.
**Application Development:**
- UI: Teller Login screen (Bootstrap 5)
- Backend: `digistack-branchportal-v29.ear` skeleton — `controller/`, `client/` (REST/SOAP stub calling CBS); `TellerLoginServlet` delegates auth to CBS
- Database: N/A (Branch Portal never touches the DB directly)
- API: Consumes CBS's existing auth/MFA REST contract

**WebSphere Administration:**
- Configuration: New independent EAR deployment target on existing cluster; virtual host rule for Branch Portal (internal-only routing, per SetupDoc-v29.md)
- Deployment: Deploy Branch Portal skeleton to WAS cluster
- JDBC / JMS / JNDI: N/A this sprint
- Security: Token/API key required per P02 v17's hardened endpoint contract; role check for Teller-specific authorization (extends P01 v10's Administrator/Customer role model)

**Dependencies:** P02 v17 MFA/auth, P01 v10 role model, P03 v23's CBS relocation.
**Deliverables:** Branch Portal EAR skeleton deployed, Teller Login working.
**Acceptance Criteria:** A Teller logs in successfully against CBS's shared auth; no local user store exists in Branch Portal.
**Enterprise Outcome:** Eighth independent WAS EAR proven functional from day one — reinforces the shared-identity pattern first established at Mobile (v26).

---

### Sprint 2
**Sprint Goal:** Implement Cash Deposit and Cash Withdrawal through CBS's Operations Service.
**Learning Objective:** A new CBS internal service (Operations Service) exposed specifically to Branch Portal, mirroring Card Portal's dedicated-service pattern from v28.
**Business Features:** Cash Deposit, Cash Withdrawal (teller-initiated, distinct from customer self-service Deposit/Withdraw).
**Application Development:**
- UI: Cash Deposit form, Cash Withdrawal form (account lookup + amount)
- Backend: `OperationsService` (new module within CBS's multi-module EAR) — teller-initiated deposit/withdrawal, reusing P01 v3's core debit/credit logic but via a distinct teller-facing entry point
- Database: N/A (reuses existing `accounts`/transaction tables in `digistack_cbs`)
- API: REST endpoints for teller Cash Deposit / Cash Withdrawal

**WebSphere Administration:**
- Configuration: New CBS module (`OperationsService`) registered alongside CIF/Account/Transaction/Card modules
- Deployment: Redeploy `digistack-cbs-v29.ear`, `digistack-branchportal-v29.ear`
- JDBC / JMS / JNDI: N/A this sprint
- Security: N/A this sprint

**Dependencies:** Sprint 1's Teller Login, P01 v3's Deposit/Withdraw logic.
**Deliverables:** Working teller Cash Deposit/Withdrawal via Branch Portal.
**Acceptance Criteria:** A teller-initiated cash deposit/withdrawal correctly updates the customer's balance in `digistack_cbs`, distinguishable in transaction history from a self-service transaction.
**Enterprise Outcome:** Reinforces the dedicated-internal-service pattern from Card Portal — Branch Portal routes to CBS's Operations Service specifically, not the monolithic "CBS."

---

### Sprint 3
**Sprint Goal:** Implement the Beginning of Day (BOD) scheduled batch job.
**Learning Objective:** WAS-scheduled batch job configuration (EJB Timer Service, reused from P03 v25's NEFT batch pattern) applied to a day-open operational process.
**Business Features:** Beginning of Day (BOD).
**Application Development:**
- UI: BOD status indicator (internal ops view)
- Backend: `BatchService.runBOD()` — interest accrual prep, opens NEFT batch window (per P03 v25's NEFT queue)
- Database: `V29__create_bod_eod_log.sql` (job run log: start time, end time, status)
- API: N/A this sprint

**WebSphere Administration:**
- Configuration: EJB Timer Service scheduled job for BOD, configured to run at a defined start-of-day time
- Deployment: Redeploy `digistack-cbs-v29.ear`
- JDBC / JMS / JNDI: N/A this sprint
- Security: N/A this sprint

**Dependencies:** P03 v25's EJB Timer Service pattern (NEFT batch), Sprint 2's Operations Service.
**Deliverables:** Working scheduled BOD job.
**Acceptance Criteria:** BOD runs automatically at its scheduled time (not manually triggered), opens the NEFT batch window, and logs a completed run in the BOD/EOD log.
**Enterprise Outcome:** Reuses and reinforces the EJB Timer Service scheduling pattern first proven at v25, now applied to a whole-of-day operational process rather than a single payment type.

---

### Sprint 4
**Sprint Goal:** Implement the End of Day (EOD) scheduled batch job.
**Learning Objective:** Closing the banking day as a scheduled job, distinct from BOD's opening job but reusing the same infrastructure.
**Business Features:** End of Day (EOD).
**Application Development:**
- UI: EOD status indicator (internal ops view)
- Backend: `BatchService.runEOD()` — closes the banking day, finalizes the day's NEFT batch window, triggers reconciliation (Sprint 5)
- Database: N/A (reuses Sprint 3's BOD/EOD log table)
- API: N/A this sprint

**WebSphere Administration:**
- Configuration: EJB Timer Service scheduled job for EOD, configured to run at a defined end-of-day time
- Deployment: Redeploy `digistack-cbs-v29.ear`
- JDBC / JMS / JNDI: N/A this sprint
- Security: N/A this sprint

**Dependencies:** Sprint 3's BOD job, P03 v25's NEFT batch window.
**Deliverables:** Working scheduled EOD job.
**Acceptance Criteria:** EOD runs automatically at its scheduled time, closes the day's NEFT batch window (no further NEFT requests accepted until next BOD), and logs a completed run.
**Enterprise Outcome:** Completes the BOD/EOD pairing as genuine scheduled infrastructure — the banking day now has a real open/close lifecycle, not an implicit always-on state.

---

### Sprint 5
**Sprint Goal:** Implement the EOD Reconciliation Report, tying out NEFT/IMPS settlements against CBS's ledger.
**Learning Objective:** Cross-service batch reporting — Reporting Service consuming settlement data produced by two other independent services (CBS, Payment Hub) to surface mismatches.
**Business Features:** EOD Reconciliation Report.
**Application Development:**
- UI: Reconciliation Report view (internal ops), flags any mismatch
- Backend: `ReconciliationService` (within Reporting Service, P03 v23) — reads CBS's ledger and Payment Hub's settled NEFT/IMPS records (P03 v25), ties them out, flags discrepancies
- Database: N/A (read-only, per Reporting Service's accepted OLTP-read tradeoff)
- API: REST endpoint to retrieve the reconciliation report

**WebSphere Administration:**
- Configuration: N/A this sprint
- Deployment: Redeploy `digistack-reportingservice-v29.ear`
- JDBC / JMS / JNDI: N/A this sprint
- Security: N/A this sprint

**Dependencies:** Sprint 4's EOD close, P03 v25's NEFT/IMPS settlement data, P03 v23's Reporting Service.
**Deliverables:** Working EOD Reconciliation Report.
**Acceptance Criteria:** After a full BOD→transactions→EOD cycle, the reconciliation report correctly ties out every NEFT/IMPS settlement against CBS's ledger; a deliberately introduced mismatch (e.g., a manually altered test record) is correctly flagged, not silently passed.
**Enterprise Outcome:** This is where "Reconciliation" — previously only named under v25's Enterprise Learning with no concrete feature — gets its actual implementation, exactly where a real bank would put it: at EOD close.

---

### Sprint 6
**Sprint Goal:** Full validation and sign-off of Version 29, and of Part-3's core banking-application scope as a whole.
**Learning Objective:** Confirming an eighth independent WAS EAR plus a new scheduled-batch operational layer integrate cleanly with everything built since v23.
**Business Features:** None (validation sprint).
**Application Development:** Bug-fix only, no new work.
**WebSphere Administration:**
- Configuration: Final review of BOD/EOD scheduling alongside v25's NEFT batch timer (no scheduling conflicts)
- Deployment: Redeploy final `digistack-cbs-v29.ear`, `digistack-branchportal-v29.ear`, `digistack-reportingservice-v29.ear`
- JDBC / JMS / JNDI: N/A this sprint
- Security: Negative test — confirm Branch Portal has zero direct database connectivity

**Dependencies:** Sprints 1–5 complete, P03 v25 (NEFT), P03 v23 (Reporting Service).
**Deliverables:** SetupDoc-v29.md, TestCases-v29.md (including the Branch-Portal-never-writes negative test).
**Acceptance Criteria:** Teller Login, Cash Deposit/Withdrawal, BOD, EOD, and Reconciliation Report all pass together in one combined pass; zero-DB-access confirmed; full regression pack (v1–v28) passes.
**Enterprise Outcome:** Version 29 signed off — eight independent WAS EARs plus two Tomcat apps now operate together, with a genuine scheduled operational day-cycle proven end-to-end; only Loan Management (v30) remains before Part-3 closes.

---

## Version 29 Deliverables
- `digistack-cbs-v29.ear` (new OperationsService, BatchService modules), `digistack-branchportal-v29.ear`, updated `digistack-reportingservice-v29.ear`
- `V29__create_bod_eod_log.sql`
- EJB Timer Service configuration exports (BOD, EOD), Reconciliation Report export
- SetupDoc-v29.md, TestCases-v29.md

## Version 29 Exit Criteria
- ✅ Application functionality complete (Teller Login, Cash Deposit/Withdrawal, BOD, EOD, Reconciliation Report)
- ✅ Database validated (V29 migration applied and verified; Branch Portal confirmed zero direct DB access)
- ✅ WebSphere deployment successful (eighth independent WAS EAR live, BOD/EOD scheduled jobs confirmed running automatically)
- ✅ Smoke testing passed (full regression + Branch Portal negative test)
- ✅ Ready for Version 30

## Lessons Learned
- **Key learnings:** Reusing v25's EJB Timer Service pattern for BOD/EOD (rather than inventing new scheduling infrastructure) kept this version focused on the operational logic itself, not re-solving a scheduling problem already solved.
- **Known issues:** None expected if Sprint 3's BOD job is verified running cleanly before Sprint 4's EOD/reconciliation dependency chain is built.
- **Technical debt:** None new — Branch Operator and Auditor roles remain unbuilt per P01 v10's clarification note; Teller operates under the existing Administrator-adjacent role model documented there, not a new role introduced here.

---
---

# Version 30 — Loan Management

## Version Overview

**Version Objective:** Introduce lending as a core CBS product line — closing the realism gap referenced but never built in P02's Capstone (v22) feature list — covering Loan Origination and Loan Servicing, including EJB Timer-driven EMI auto-debit.

**Business Scope:** Loan Application, Eligibility Check (simulated), Loan Approval/Rejection, Loan Disbursement, EMI Schedule Generation, EMI Auto-Debit, Loan Statement, Foreclosure/Prepayment, Overdue/NPA Flagging (simulated), Personal Loan, Home Loan (simplified).

**WebSphere Focus:** EJB Timer Service (EMI due-date scheduling), Batch Interest Accrual, Long-Running Transaction Patterns, Scheduled Task Administration in WAS, Transaction Isolation for Concurrent EMI Processing.

**Expected Outcome:** A customer can apply for a Personal Loan, pass a simulated eligibility check, get approved, and have the loan amount disbursed straight into their CBS savings account; an EMI schedule is generated and at least one EMI auto-debits on its due date via EJB Timer Service, correctly reducing the outstanding principal.

**Prerequisites:** P03 Version 29 Completion Checkpoint satisfied — Branch Portal live, BOD/EOD scheduled jobs operational, EOD Reconciliation Report proven.

---

### Sprint 1
**Sprint Goal:** Design and create the Loan schema within `digistack_cbs`, and scaffold the Loan Service module.
**Learning Objective:** Adding a new product-line service module to CBS's already-multi-module EAR (CIF/Account/Transaction/Card/Operations), consistent with the "CBS stays a single application" decision from v23.
**Business Features:** Loan product structure (Personal Loan, Home Loan).
**Application Development:**
- UI: N/A this sprint
- Backend: `LoanService` module scaffold within CBS's `service/` package
- Database: `V30__create_loan_tables.sql` (loans, loan_products, emi_schedule tables — cif_id/account_id FKs)
- API: N/A this sprint

**WebSphere Administration:**
- Configuration: New CBS module (`LoanService`) registered alongside CIF/Account/Transaction/Card/Operations modules
- Deployment: Redeploy `digistack-cbs-v30.ear` with the new module scaffold
- JDBC / JMS / JNDI: Reuses existing `jdbc/CBSDataSource`
- Security: N/A this sprint

**Dependencies:** P03 v24's CIF, P01 v3's Account model.
**Deliverables:** Loan schema live in `digistack_cbs`; LoanService module registered.
**Acceptance Criteria:** Tables created with correct FK constraints; a manual insert/select against `loans` and `loan_products` round-trips correctly.
**Enterprise Outcome:** Sixth internal CBS service module added cleanly — reinforces that CBS's single-application design scales to a genuinely new product line without decomposition.

---

### Sprint 2
**Sprint Goal:** Implement Loan Application and simulated Eligibility Check.
**Learning Objective:** A hard business gate (eligibility) blocking progression, mirroring v24's Aadhaar/PAN verification gate pattern.
**Business Features:** Loan Application, Eligibility Check (simulated credit/income rules).
**Application Development:**
- UI: Loan Application form (product selection, amount requested, income declaration)
- Backend: `LoanService.apply()`, `EligibilityService` (simulated rule-based check — income multiple, existing loan exposure)
- Database: N/A (reuses Sprint 1 schema)
- API: REST endpoint for Loan Application

**WebSphere Administration:**
- Configuration: N/A this sprint
- Deployment: Redeploy `digistack-cbs-v30.ear`
- JDBC / JMS / JNDI: N/A this sprint
- Security: N/A this sprint

**Dependencies:** Sprint 1's Loan schema, P03 v24's verification-gate pattern.
**Deliverables:** Working Loan Application with Eligibility gate.
**Acceptance Criteria:** An application failing the simulated eligibility rule is rejected before persisting as anything beyond a rejected record; a passing application proceeds to Approval status.
**Enterprise Outcome:** Reuses the compliance-gate pattern established at v24, now applied to credit risk instead of identity verification.

---

### Sprint 3
**Sprint Goal:** Implement Loan Approval/Rejection and Disbursement.
**Learning Objective:** A disbursement write that credits an existing CBS account — reusing Account Service rather than duplicating balance-update logic.
**Business Features:** Loan Approval/Rejection, Loan Disbursement.
**Application Development:**
- UI: Approval/Rejection screen (internal ops), Disbursement confirmation
- Backend: `LoanService.approve()/reject()`, `LoanService.disburse()` — calls existing `AccountService.credit()` (not a new, parallel balance-update path)
- Database: N/A (reuses Sprint 1 schema; updates loan status)
- API: REST endpoints for Approval/Rejection, Disbursement

**WebSphere Administration:**
- Configuration: N/A this sprint
- Deployment: Redeploy `digistack-cbs-v30.ear`
- JDBC / JMS / JNDI: N/A this sprint
- Security: N/A this sprint

**Dependencies:** Sprint 2's Eligibility Check, P03 v23's AccountService.
**Deliverables:** Working Approval/Rejection and Disbursement flow.
**Acceptance Criteria:** An approved loan disburses the full amount into the linked CBS savings account, correctly reflected in Balance Inquiry; a rejected application never disburses.
**Enterprise Outcome:** Proves loan disbursement is a genuine local transaction within CBS — reusing the same single-writer discipline as every other CBS write path, not a special case.

---

### Sprint 4
**Sprint Goal:** Implement EMI Schedule Generation.
**Learning Objective:** Amortization schedule computation as a batch-style generation step following disbursement.
**Business Features:** EMI Schedule Generation.
**Application Development:**
- UI: EMI Schedule view (customer-facing, amount/due-date per installment)
- Backend: `EMIScheduleService.generate()` — amortization calculation, populates `emi_schedule` table
- Database: N/A (reuses Sprint 1's `emi_schedule` table)
- API: REST endpoint to retrieve EMI Schedule

**WebSphere Administration:**
- Configuration: N/A this sprint
- Deployment: Redeploy `digistack-cbs-v30.ear`
- JDBC / JMS / JNDI: N/A this sprint
- Security: N/A this sprint

**Dependencies:** Sprint 3's Disbursement.
**Deliverables:** Working EMI Schedule generation.
**Acceptance Criteria:** Immediately after disbursement, a correctly computed EMI schedule (principal + interest split, due dates) is generated and viewable.
**Enterprise Outcome:** First amortization-style computed schedule in the roadmap — sets up the due-date-driven auto-debit this version's headline deliverable depends on.

---

### Sprint 5
**Sprint Goal:** Implement EMI Auto-Debit via EJB Timer Service, Loan Statement, Foreclosure/Prepayment, and Overdue/NPA Flagging.
**Learning Objective:** EJB Timer Service applied to per-loan due-date scheduling (many independent timers, not one shared batch job like BOD/EOD), plus transaction isolation for concurrent EMI processing across multiple loans.
**Business Features:** EMI Auto-Debit (scheduled), Loan Statement, Foreclosure/Prepayment, Overdue/NPA Flagging (simulated).
**Application Development:**
- UI: Loan Statement view, Foreclosure/Prepayment form, Overdue flag indicator
- Backend: `EMIAutoDebitTimer` (EJB Timer Service, per-schedule-entry due-date trigger) — debits the linked account, reduces outstanding principal; `LoanService.foreclose()`; `NPAFlagService` (simulated overdue threshold check)
- Database: N/A (reuses Sprint 1 schema; updates `emi_schedule` status, loan outstanding balance)
- API: REST endpoints for Loan Statement, Foreclosure/Prepayment

**WebSphere Administration:**
- Configuration: EJB Timer Service configured for due-date-driven (not fixed-schedule) triggering, distinct from v25/v29's fixed-window batch timers
- Deployment: Redeploy `digistack-cbs-v30.ear`
- JDBC / JMS / JNDI: Transaction isolation verified — concurrent EMI debits across multiple loans do not interfere with each other's local transactions
- Security: N/A this sprint

**Dependencies:** Sprint 4's EMI Schedule, P03 v25's EJB Timer precedent (applied here to a materially different trigger pattern — due-date-per-loan vs. fixed daily window).
**Deliverables:** Working EMI Auto-Debit, Loan Statement, Foreclosure/Prepayment, NPA Flagging.
**Acceptance Criteria:** At least one EMI auto-debits correctly on its due date via EJB Timer Service, reducing outstanding principal and updating the schedule entry to PAID; a foreclosure correctly closes the loan early; a deliberately overdue EMI is flagged NPA per the simulated threshold.
**Enterprise Outcome:** The version's core WebSphere deliverable — EJB Timer Service proven at a genuinely different scheduling granularity (per-loan due dates) than the fixed-window batch pattern already established at v25/v29.

---

### Sprint 6
**Sprint Goal:** Full validation and sign-off of Version 30, and of Part-3 as a whole.
**Learning Objective:** Confirming the full lending lifecycle integrates cleanly with all eight prior versions, closing out Phase-1's application-development scope.
**Business Features:** None (validation sprint).
**Application Development:** Bug-fix only, no new work.
**WebSphere Administration:**
- Configuration: Final review of all EJB Timer Service jobs in the cell (NEFT batch v25, BOD/EOD v29, EMI Auto-Debit v30) confirming no scheduling conflicts
- Deployment: Redeploy final `digistack-cbs-v30.ear`
- JDBC / JMS / JNDI: Full regression of all CBS modules (CIF, Account, Transaction, Card, Operations, Loan)
- Security: Re-run every prior version's negative ownership tests (v23–v29) to confirm no regression

**Dependencies:** Sprints 1–5 complete, all of P03 v23–v29.
**Deliverables:** SetupDoc-v30.md, TestCases-v30.md; full P03 Completion Checklist signed off.
**Acceptance Criteria:** Application → Eligibility → Approval → Disbursement → EMI Schedule → Auto-Debit → Statement/Foreclosure/NPA all pass together in one combined pass; every item in P03's Completion Checklist passes; no open Critical/High defects; full regression pack (v1–v29) passes.
**Enterprise Outcome:** Version 30 signed off — Part-3 complete. Nine distinct deployable applications (7 WAS EARs + 2 Tomcat apps) now operate together under CBS's single-writer governing rule, with lending closing the last referenced-but-unbuilt gap from P02's Capstone.

---

## Version 30 Deliverables
- `digistack-cbs-v30.ear` (new LoanService, EMIScheduleService, NPAFlagService modules)
- `V30__create_loan_tables.sql`
- EJB Timer Service configuration export (EMI Auto-Debit), Loan Statement export
- SetupDoc-v30.md, TestCases-v30.md

## Version 30 Exit Criteria
- ✅ Application functionality complete (Application, Eligibility, Approval, Disbursement, EMI Schedule, Auto-Debit, Statement, Foreclosure, NPA Flagging)
- ✅ Database validated (V30 migration applied and verified)
- ✅ WebSphere deployment successful (sixth CBS internal module live, per-loan EJB Timer scheduling confirmed alongside existing fixed-window timers)
- ✅ Smoke testing passed (full regression across all P03 versions, v23–v30, passes)
- ✅ Ready for P03.1 (Interview Preparation) — Part-3 application-development scope now complete

## Lessons Learned
- **Key learnings:** Per-loan due-date EJB Timers behave meaningfully differently from the fixed-daily-window timers used at v25/v29 — concurrent timer firing across many loans is what actually exercised the transaction-isolation topic this version names.
- **Known issues:** None expected if Sprint 4's EMI Schedule generation is verified correct before Sprint 5's auto-debit timers are wired against it.
- **Technical debt:** None new — Loan Management closes the P02 Capstone reference gap as intended; CBS decomposition (splitting Loans out) remains a flagged-but-unbuilt future option per v23's architectural decision, not pursued here.

---

# P03 — Overall Completion Summary

**All 8 versions (23–30), 48 sprints total, complete.**

## P03 Final Application State
Total deployable applications: Internet Banking Portal, CBS, Payment Hub, Notification Service, Reporting Service, Branch Portal, Card Portal (7 WAS EARs) + Mobile Banking, ATM Simulator (2 Tomcat apps) = **9 distinct deployable applications**.

Governing Rule in force: only CBS writes to `digistack_cbs` — every other application invokes CBS services or consumes CBS-published events. Verified via negative tests at every version from v23 onward.

CBS internal modules (single EAR, per v23's architectural decision): CIF, Account, Transaction, Card, Operations, Loan.

## Carried Forward to P03.1
CBS as system of record, Payment Hub, Notification Service, Reporting Service, the two Tomcat-based channel simulators (Mobile/ATM), the WAS-hosted Card Portal, Branch Portal, and Loan Servicing all become subjects of Interview Preparation's Project Walkthrough, WebSphere Administration Q&A, Production Support, Troubleshooting Scenarios, and Banking Production Environment Q&A — followed by P04's observability instrumentation (APM, distributed tracing, chaos testing) once P03.1 is complete.
