ID: P02
Version: 1.0
Status: Active

Title: WebSphere / Enterprise Middleware Integration

Imports:
IDX
CONTEXT_PACK
ARCH02
RACI01


Exports:
Versions 15-22 (incl. suffix-slot v16.5 Transaction History Pagination and v22.5 Database Migration)
JMS / SIBus / MDB / DLQ
Web Services (REST/SOAP)
Security Hardening
Monitoring & Logging (PMI/JMX)
IBM MQ Integration
IHS Advanced Administration
Enterprise Load Balancer
Middleware Integration Capstone

Used By:
P03
P04

Next:
P03

---

Purpose
-------
Same discipline as P01 — the WebSphere/middleware topic is the deliverable
of each version; the banking feature is only the minimum vehicle needed to
exercise it. No feature is added because "a real bank would have it" —
only because a topic in this Part genuinely needs it to exist.

Deployment Model
------------------
Still ONE deployable application — digistack-bank-vN.ear. No separate CBS
application yet. The real Portal-vs-CBS split (Portal stops touching the DB
directly, calls CBS exclusively via REST/SOAP) is a deliberate pivot
reserved for P03, Version 23. Everything in P02 — including the new
REST/SOAP endpoints in v16 and the MQ integration in v19 — is still
internal to the same single EAR.

Prerequisite
------------------
P01 Completion Checkpoint satisfied. Starting state (see P01's "Application
State After P01"): Home, Login/Logout, Balance, Deposit, Withdraw,
Freeze/Unfreeze, one Transaction Report, one Withdraw email — running on a
WAS cluster behind IHS with SSL, security roles, JNDI DataSource, and JNDI
Mail Session.

Architectural Note Carried Over From P01
--------------------------------------------
P01 deliberately never built Customer, Account-as-a-first-class-entity,
Fund Transfer, Beneficiary, or Transaction History as real modules — only
Balance/Deposit/Withdraw existed. Several of P02's original topics (JMS
async transfer, REST Fund Transfer, MQ payment queues) assumed those
modules already existed. This revision fixes that continuity gap by having
P02 grow those business modules naturally, each one introduced at the
version where a middleware topic genuinely needs it — not before, and not
padded with extra fields/screens beyond what that topic requires.

Licensing/Tooling Reality Check (unchanged)
------------------------------------------------
IBM MQ (v19) and enterprise load balancers (v21) are commercial products.
Use free IBM MQ Advanced for Developers and NGINX/HAProxy as F5/Citrix ADC
stand-ins — same as originally noted.

---
Part-Start Architecture Diagram (generate first, before Version 15 work begins)
----------------------------------------------------------------------------------
Pruned to what P02 (v15-v22) adds or extends on top of P01's tree. Files
P01 already populated (Network, VM Layout, Request Flows, Cluster,
Deployment, DB ER, Security) aren't redrawn here — only what's new or
materially extended this Part.

                 DIGISTACK BANK — P02 (v15–v22)
                              |
                              |
                              v
        +---------------------------------------------+
        | P01 INHERITED FOUNDATION                   |
        |                                             |
        | Network / Deployment / Cluster / Security  |
        | tree remains unchanged from P01            |
        |                                             |
        | VM Layout extended at v22.5                |
        | dsb-oracle powers on                       |
        +---------------------------------------------+
                              |
                              v
                    +-------------------+
                    |   WebSphere ND    |
                    |                   |
                    | Network Deployment|
                    +-------------------+
                              |
              +---------------+---------------+
              |                               |
              v                               v
        +-----------+                    +-----------+
        | REQUESTS  |                    |    MQ     |
        +-----------+                    +-----------+
              |                               |
              |                               |
              v                               v
   +-------------------------+       +---------------------------+
   | 03_Request_Flows.md     |       | 05_MQ_Architecture.md     |
   |                         |       |                           |
   | Extended in v15         |       | NEW in v19                |
   |                         |       |                           |
   | JMS / SIBus             |       | IBM MQ Queue Manager       |
   | producer / consumer     |       |                           |
   | added to request path  |       | BANK.PAYMENT.REQUEST.Q    |
   |                         |       | BANK.PAYMENT.RESPONSE.Q   |
   | v15 creates SIBus       |       | DLQ                       |
   | queue:                  |       | CHLAUTH                   |
   |                         |       |                           |
   | BANK.FUNDTRANSFER.Q     |       +---------------------------+
   |                         |
   | Queue depth is read by  |
   | v18 dashboard            |
   |                         |
   | NOTE: SIBus queue is    |
   | separate from IBM MQ    |
   | BANK.PAYMENT.* queues   |
   | introduced in v19       |
   +-------------------------+
              |
              v
   +-------------------------+
   |      APPLICATION        |
   |                         |
   | Banking application     |
   | request processing      |
   |                         |
   | JMS/SIBus producer      |
   |        |                |
   |        v                |
   | BANK.FUNDTRANSFER.Q     |
   |        |                |
   |        v                |
   | JMS/SIBus consumer      |
   +-------------------------+
              |
              |
              v
   +--------------------------------+
   |          PostgreSQL             |
   |                                |
   | 06_Database_ER_Diagram.md      |
   |                                |
   | Extended in v15:               |
   | • Account                      |
   | • Beneficiary                  |
   | • Fund Transfer                |
   | • Customer-related columns     |
   |   on users/account             |
   |                                |
   | Customer is NOT a separate     |
   | table yet                      |
   |                                |
   | Separate CIF arrives in P03    |
   | v24                             |
   |                                |
   | Extended in v17:               |
   | • OTP fields                   |
   | • Lockout fields               |
   | • Audit fields                 |
   +--------------------------------+
              |
              |
              v
   +--------------------------------+
   |       v22.5 ORACLE             |
   |                                |
   | dsb-oracle VM                 |
   | powers on                     |
   |                                |
   | Oracle Database               |
   | DIGISTACK_CBS PDB             |
   +--------------------------------+


              IMPORTANT QUEUE SEPARATION
              ==========================

   WebSphere SIBus                         IBM MQ
   Introduced v15                         Introduced v19
          |                                      |
          v                                      v
   BANK.FUNDTRANSFER.Q                  BANK.PAYMENT.REQUEST.Q
          |                                      |
          |                                      v
          |                              BANK.PAYMENT.RESPONSE.Q
          |
          v                                      |
   v18 Dashboard                               DLQ
   reads queue depth                           CHLAUTH


- v15 introduces the Beneficiary table including the is_external
  column (default FALSE in v15 — all beneficiaries are internal).
  v19 is where is_external is first SET to TRUE and acted upon —
  it distinguishes internal beneficiary (own account, SIBus route)
  from external beneficiary (another customer of the same bank,
  IBM MQ route). External beneficiaries identify both the
  destination customer_id and destination account number.

Not in scope this Part: 09_DR_Architecture.md (still P05). Cluster
topology (04) is unchanged from P01 — no new members added in P02.

Version 15 — JMS & Asynchronous Banking
--------------------------------------------
WebSphere Topic: Service Integration Bus (SIBus), JMS Queues,
Message-Driven Beans (MDB), Dead Letter Queue (DLQ), asynchronous
processing.

Minimum App Needed
Async processing needs *something* moving between two parties to be worth
doing asynchronously — Deposit/Withdraw against a single account don't
qualify. So this version introduces the smallest possible new features
that make async meaningful:
- Customer — minimal: the existing users row (from v2) represents a
  customer/login identity and gets a customer_id and name. The application
  must support creation of multiple independent customers, each with a
  separate login identity. No KYC or verification workflow is required in
  P02 — customer creation is intentionally lightweight. Each customer can
  subsequently own more than one account.
- Account (multi-account) — the existing accounts table (v3) gains a
  customer_id foreign key, so one customer can have 2+ accounts.
- Beneficiary — minimal (INTERNAL ONLY in this version): register your own
  other account (Account1 → Account2, same customer) as an internal
  beneficiary you're allowed to transfer to. One table, no approval
  workflow. Transfers to ANOTHER customer (external beneficiaries) do NOT
  exist yet — that is v19's IBM MQ leg.
- Fund Transfer (INTERNAL) — the actual new transaction: move money from
  one of your own accounts (Account1) to another of your own registered
  accounts (Account2). This is the feature that gets processed
  asynchronously via SIBus. Customer-to-Customer (external) transfers are
  explicitly out of scope until v19.

How It's Wired
INTERNAL Fund Transfer (Account1 → Account2, same customer) returns an
immediate "accepted" response to the customer, then the actual balance
update on Account2 happens via an MDB consuming from a JMS Queue on SIBus.
Failure semantics are split explicitly: a business-rule failure discovered
during async processing (e.g., insufficient funds) consumes the message and
marks the transfer FAILED — no retry, no DLQ. A technical/system failure
(e.g., DB fault, forced MDB exception) redelivers per the retry policy and,
on exhaustion, lands in the DLQ and is inspected. The deliberate DLQ drill
in Sprint 6 uses the technical-failure path.

Transaction Boundary Note (added 2026-08-25)
------------------------------------------------
Because Fund Transfer is the roadmap's first operation that writes TWO
balances from ONE business action, its transaction boundaries are stated
explicitly — this is the version where 2PC/distributed-transaction
reasoning first becomes visible, per the gap flagged against P01's
transfer/withdraw logic:

- The MDB's debit of Account1 AND credit of Account2 both run inside a
  single LOCAL transaction (one EAR, one DataSource, one JVM) — the debit
  and credit legs are never split across a distributed XA transaction
  at this stage.
- The MDB consumes with a transactional JMS session: if the balance
  update fails/rolls back, the message is NOT acknowledged — it redelivers
  per the retry policy, and only exhausts to the DLQ after repeated
  failure. This is the atomicity story: message consumption and balance
  write succeed or fail together.
- A deliberate distributed (XA/2PC) transaction — spanning multiple
  EARs or DataSources — is deliberately NOT used here. Why that becomes
  the right call, and where Saga/compensating-transaction patterns take
  over, is formalized at P03 v25 (Payment Hub) and P03 v23 (single-writer
  CBS decision). v15 is where the LOCAL boundary is exercised and
  observed; the rejection of XA is documented, not silent.

Routing rule established here and honored for the rest of the roadmap:
- Internal beneficiary (own account)  → SIBus JMS/MDB (this version)
- External beneficiary (another customer, Customer2) → IBM MQ (v19)

Application Evolution This Version

Customer
│
▼
Account
│
▼
Beneficiary
│
▼
Fund Transfer
│
▼
JMS Queue
│
▼
MDB

This makes the version's build order explicit: each concept exists only to
support the next, ending in the actual WebSphere topic (JMS Queue → MDB)
being practiced.

Topics Covered: Service Integration Bus (SIBus), Bus Members, JMS Queues,
Connection Factories, Queue Destinations, Activation Specifications,
Message-Driven Beans (MDB), Queue Monitoring, Retry Queue, Dead Letter Queue
(DLQ), Producer/Consumer Pattern, Asynchronous Processing.

Sprint Deliverable: Fund Transfer (to a registered Beneficiary, from one of
a Customer's own Accounts) returns an immediate response while the actual
balance update happens asynchronously via an MDB on SIBus; a deliberately
failing transfer lands in the DLQ and is inspected.

Dashboard UI Note (added 2026-08-24)
--------------------------------------
The "Payments/Transfers" tile on the Dashboard (placeholder since
P01 v3, shown as "Coming soon — v15" per P01 v2's UI convention) goes
live here — Fund Transfer (to a registered Beneficiary) is reachable
directly from the Dashboard's tile, not just a standalone form. The
Transfer Money screen at this point offers internal transfer only (no
IMPS/NEFT/RTGS types yet — those transfer-type options don't exist
until P03 v25, and RTGS is never added — see P03 v25's UI note).

Dashboard UI Note — Multi-Account + Alerts (added 2026-08-24)
-----------------------------------------------------------------
Two Dashboard changes land here, both driven by this version's own
schema changes:
1. "Your Accounts" becomes a list/switcher (not a single hardcoded
   account) — required the moment a customer can hold 2+ accounts
   (this version's Account/customer_id change). Each account in the list
   gets its own balance-toggle and transaction feed.
2. The notification bell (P01 v13) is extended to include Fund Transfer
   confirmations, reusing the same in-app alert list.

---

Version 16 — Web Services (SOAP/REST)
--------------------------------------------
WebSphere Topic: JAX-RS/JAX-WS, WSDL generation/publishing, SOAP binding,
REST endpoint deployment, API endpoint configuration.

Minimum App Needed
No new banking feature — this version exposes what already exists as
callable services, grouped by technology so it's immediately obvious which
endpoints share a binding style:

REST APIs
- Balance Inquiry (from v3)
- Fund Transfer (from v15) — deliberately added ahead of "just exposing
  what exists," because P03's Mobile Banking, ATM, and Card Portal
  simulators all need to consume Fund Transfer over REST. Building the
  endpoint here, on the single EAR, gives P03 a REST contract to build
  against instead of inventing one from scratch under channel-simulator
  pressure.

SOAP Service
- Account Statement (reusing v14's Transaction Report data, and formalizing
  it as a proper queryable Transaction History rather than a PDF-only
  dump)

No Partner Bank onboarding workflow, no Customer KYC Verification Service,
no API versioning scheme yet — three endpoints (2 REST + 1 SOAP) are enough
to exercise both binding styles without inventing more surface than the
topic needs.

Topics Covered: Web Services Engine (JAX-WS/JAX-RS), WSDL Generation &
Publishing, SOAP Binding, REST Endpoint Deployment, API Endpoint
Configuration in Admin Console, Request/Response Logging.

Sprint Deliverable: Two REST endpoints (Balance Inquiry, Fund Transfer) and
one SOAP endpoint (Account Statement / Transaction History) are live; a
WSDL is published and browsable; a simple external client (Postman/SoapUI)
successfully calls all three and logs are captured for each
request/response.

Dashboard UI Note (added 2026-08-24)
--------------------------------------
The Dashboard's "Your Recent Transactions" section (placeholder since
P01 v3, shown as "Coming soon — v16" per P01 v2's UI convention)
activates here — the SOAP Account Statement / Transaction History
service built this version is queried to populate the Dashboard's
last-10-transactions list (date, description, amount), per the standing
Dashboard-first UI standard. The Statements sidebar item (also
"Coming soon — v16") activates in lockstep with this. This is a read-only UI consumer of the SOAP endpoint built in this
version — no backend logic is added for the Dashboard beyond the
AccountStatementService itself (Sprints 3–4); the UI wiring consumes it
as-is.

Dashboard UI Note — Download Statement (added 2026-08-24)
---------------------------------------------------------------
A "Download Statement" link is added next to the Dashboard's Recent
Transactions section, calling this version's SOAP Account
Statement/Transaction History service to produce the same PDF/CSV
format P01 v14's Transaction Report already generates. No new report
logic is written — the link reuses the statement service's output;
"existing" means the v14 report format and underlying transaction data,
not a pre-existing service.

---

Version 16.5 — Transaction History Pagination (UI-only)
-----------------------------------------------------------
WebSphere Topic: None — this is a pure UI/servlet-layer sprint with
zero new WebSphere administration work.

Rationale: The SOAP Account Statement / Transaction History service
introduced at v16 returns all matching records in one response. The
Transaction History screen (Statement sidebar) needs client-side
pagination for usability — the backend already supports date-range
filtering (the SOAP contract's dateRange parameter), but a long result
set needs page-by-page navigation. This is
filed as v16.5 rather than inside v16 (to keep v16 a clean single-topic
SOAP/REST sprint) and before v17 (so the UI is complete before security
hardening locks down the endpoints).

What Is Built
- The Transaction History JSP/Servlet is updated to pass page and
  pageSize parameters to the existing SOAP Account Statement service
  call.
- The service response is sliced server-side in the Servlet layer
  (not in the SOAP service itself — no change to the published WSDL
  or endpoint contract) and rendered as a paged table.
- Navigation controls rendered: [ ← 1  2  3  4 → ] below the table,
  consistent with the Dashboard UI mockup.
- Page size: 10 rows per page (fixed for now — no user-configurable
  page-size selector).
- No new database queries, no new backend service, no new WSDL changes.

Scope Boundary
Pagination lives entirely in the application's presentation layer
(TransactionHistoryServlet + TransactionHistory.jsp — still one EAR,
per P02's single-deployable model; no CBS exists until P03 v23). The
SOAP endpoint is unchanged. If the full result set is very large, the filter
control (date range, live since v16 — the SOAP contract's only filter
parameter) is the primary tool for narrowing — pagination handles the
remainder.

Sprint Deliverable: Transaction History screen displays results 10 per
page with working ← / → navigation; navigating pages does not trigger a
new SOAP call per page (result is fetched once, paged in servlet memory);
the WSDL and SOAP endpoint are confirmed unchanged after this sprint.

---

Version 17 — Security Hardening
-------------------------------------
WebSphere Topic: Global/Application Security, LDAP, LTPA, JAAS, role
mapping, CSRF/XSS protection.

Minimum App Needed
No new banking feature — hardens what already exists:
- MFA/OTP added onto v2's Login
- Account lockout after N failed attempts (extends v2's Login)
- v16's REST/SOAP endpoints now require a token/API key, retroactively
  securing them
- Security Event Detection (renamed from "fraud detection" — this isn't a
  rules engine or ML, it's exactly one threshold check): rapid repeated
  Fund Transfers (from v15) raise a security audit log entry.
- Immutable Audit Trail — NEW (added 2026-08-25): a dedicated
  `audit_log` table in the application database (digistack_bank on
  PostgreSQL at this version; carried into Oracle DIGISTACK_CBS by the
  v22.5 migration) records every balance-affecting
  operation (Deposit, Withdraw from P01 v3; internal Fund Transfers from
  v15 at this version) with actor, timestamp, before/after balances,
  and correlation ID; the external (MQ-routed) transfer audit hook is
  added in v19 when external transfers come into existence. Append-only by design and enforced by grants: the
  application DB user has INSERT only — no UPDATE, no DELETE, ever. This
  retroactively closes the gap flagged at P01 v3 (whose `transaction`
  table is the customer-facing ledger, not an audit record) without
  touching v3's delivered code. Security events (rapid transfers,
  lockouts, OTP failures) are recorded in the SAME table with a
  distinct event class — one audit trail, not two.

Rapid Transfers / Balance-Affecting Operation
│
▼
Audit Log Entry (INSERT-only, immutable)


Topics Covered: Global Security, Administrative Security, Application
Security, LDAP, LTPA, SSL, JAAS, Role Mapping, Secure Cookies, CSRF & XSS
Protection.

Sprint Deliverable: MFA/OTP enforced on login; account locks after N failed
attempts; LTPA token validated across the cluster; v16's REST/SOAP
endpoints reject unauthenticated calls; a rapid-repeated-transfer test
triggers a security audit log entry. The new `audit_log` table receives
an entry for every balance-affecting operation with before/after balances
and actor identity; a negative test proves the app DB user cannot UPDATE
or DELETE audit rows (grant-level immutability, not just convention).

---

Version 18 — Monitoring & Logging
----------------------------------------
WebSphere Topic: HPEL, SystemOut/SystemErr, FFDC, PMI, JMX, heap/thread
dumps, GC logs, log rotation.

Minimum App Needed
Zero new banking functionality. One Operations Dashboard reads live JVM
health, active session count, JMS queue depth (from v15), and DB pool usage
(from P01 v7) via PMI/JMX — a viewer over infrastructure that already
exists, not a new feature.

Topics Covered: HPEL, SystemOut, SystemErr, FFDC, PMI, JMX, Heap Dump,
Thread Dump, GC Logs, Log Rotation.

Sprint Deliverable: Operations Dashboard shows live JVM health, session
count, JMS queue depth, and DB pool usage via PMI/JMX; a manually triggered
thread dump and heap dump are captured and reviewed; log rotation confirmed
working on SystemOut/SystemErr.

> Note: this custom Operations Dashboard is later superseded and retired at
> P04 v31, once Prometheus/Grafana reaches equivalent coverage — see P04's
> Version 31 note for the retirement/decommission detail.

> Note (forward reference, added 2026-08-26): P04 v32 establishes a project
> Correlation ID Standard (format FT-{YYYYMMDD}-{seq}) applied retroactively
> as a code fix to this Part's Fund Transfer from v15 onward — the transfer
> servlet generates the ID, and every service touching the transfer logs it.
> The propagation mechanics are defined in P04 v32; no change to this Part's
> scope or version numbering results from that retrofit.

---
Version 18.5 — DynaCache (Dynamic Caching)
---------------------------------------------
WebSphere Topic: WebSphere Dynamic Cache (DynaCache) — object cache,
servlet cache, cache instances, cache replication across the cluster,
cache monitoring via PMI/JMX and CacheMonitor.

Rationale (slot placement): v16 exposed REST/SOAP endpoints and v16.5
built the paged Transaction History — both now have cacheable hot paths
(Balance Inquiry responses, Dashboard "Recent Transactions" fragments,
Beneficiary lookup results). v18's Operations Dashboard then gains a
cache hit-ratio panel, so DynaCache lands immediately before it.

Minimum App:
- Servlet cache: cache the Dashboard's Recent Transactions fragment
  (read-heavy, identical per customer for the duration of a session)
  with an appropriate invalidation rule on new Fund Transfer events.
- Object cache: a dedicated cache instance for Beneficiary lookups
  (v15 data) — read on nearly every transfer, rarely changes.
- Zero new banking features. Deliberate invalidation test: a successful
  Fund Transfer invalidates the cached fragment; next read reflects the
  new balance.

Topics Covered: Dynamic Cache Service, Object Cache Instances, Servlet
Caching (cachespec.xml), Cache Replication (cluster-wide, via the existing
memory-to-memory session replication infrastructure from P01 v9), Cache
Invalidation Rules, DynaCache MBeans / PMI monitoring, edge cases
(cache vs. security: never caching per-user data into shared instances).

Sprint Deliverable: cachespec.xml drives servlet caching on the Recent
Transactions fragment and a dedicated cache instance holds Beneficiary
lookups; a balance-affecting transfer triggers visible invalidation (stale
balance proved, then corrected); cache replication confirmed by hitting
the cached entry from both cluster members; hit/miss ratios visible via the existing PMI/JMX Operations Dashboard
introduced in Version 18.

Security guardrail (cross-ref v17): the Balance Inquiry endpoint is
deliberately NOT cached at the shared-instance level — documented as a
negative decision with reasoning (per-user data + shared cache = data
leak risk). Proving you know what NOT to cache is the interview-grade
skill here.
---

Version 19 — IBM MQ Integration
--------------------------------------
WebSphere Topic: IBM MQ Queue Manager, local/remote/transmission queues,
channels, MQ JMS Connection Factory, MQ-level DLQ.

Minimum App Needed
Extends v15's Fund Transfer with the customer-to-customer (EXTERNAL) leg:
- "External beneficiary" here means a destination account owned by ANOTHER
  CUSTOMER of the same bank (Customer2), registered by Customer1 as an
  external beneficiary — NOT an external bank. The beneficiary record must
  identify Customer2 and the destination account number so the payment-leg
  simulator can credit the correct Customer2 account.
- A Fund Transfer to an external beneficiary (Customer1 → Customer2) now
  routes its message through IBM MQ instead of the internal SIBus queue
  used for own-account transfers at v15 — simulating a Payment Request
  sent to the external payment leg and a Payment Response received back.
- When Customer2 next logs in, the credited amount is reflected in
  Customer2's account (balance + transaction history), proving the MQ
  round-trip completed the settlement.
- Internal transfers (Customer's own Account1 → Account2) continue to use
  SIBus exactly as built at v15 — both paths coexist from this version.

Topics Covered: IBM MQ, Queue Manager, Local Queue, Remote Queue,
Transmission Queue, Channels, Listener, Triggering, MQ JMS, MQ Connection
Factory, Dead Letter Queue, MQ Monitoring.

Sprint Deliverable: IBM MQ Queue Manager created and connected via MQ JMS Connection Factory;
Customer1 registers Customer2 as an external beneficiary and sends money —
the payment travels as a Payment Request message through IBM MQ and
receives a Payment Response back through a dedicated response queue; the
amount is then credited to Customer2's account, visible when Customer2
logs in. Own-account (internal) transfers still work over SIBus, proving
both routing paths independently.


Security note (unchanged from original): MQ channels connecting to the
external simulator use channel authentication records (CHLAUTH) and
SSL/TLS, building on P01's SSL work and v17's hardening.

---

Version 20 — IBM HTTP Server Advanced Administration
-----------------------------------------------------------
WebSphere Topic: Apache modules, plugin-cfg.xml refinement, URL rewrite,
maintenance page toggling, health check URLs, SSL termination, compression,
KeepAlive.

Minimum App Needed
Zero new banking functionality. Applies purely to the existing single app
already fronted by IHS since P01 v8:
- URL rewrite rule(s) on existing paths
- A maintenance page that can be toggled on without touching WAS
- A health check URL usable by an external monitor/load balancer (sets up
  v21)

Topics Covered: IBM HTTP Server, Apache Modules, Plugin Configuration,
plugin-cfg.xml, Reverse Proxy, Virtual Hosts, URL Rewrite, SSL Termination,
Compression, KeepAlive.

Sprint Deliverable: URL rewrite rule demonstrated on an existing path;
maintenance page toggled on/off without a WAS restart; health check URL
confirmed usable by an external monitor.

---

Version 21 — Enterprise Load Balancer (NGINX/HAProxy as F5/Citrix ADC stand-in)
-------------------------------------------------------------------------------------
WebSphere Topic: Layer-4/Layer-7 load balancing, health checks, sticky
sessions, SSL offloading, blue-green deployment.

Minimum App Needed
Zero new banking functionality. NGINX (or chosen LB) sits in front of the
existing IHS tier from v20; the "deployment" being blue-green tested is a
trivial, cosmetic change to the existing app (e.g., a version label),
reusing the same rollback discipline established in P01 v4 — not a new
feature.

Topics Covered: Layer-4 Load Balancing, Layer-7 Load Balancing, Health
Checks, Sticky Sessions, SSL Offloading, Session Persistence, Blue-Green
Deployment, High Availability.

Sprint Deliverable: NGINX fronts two IHS instances with active health
checks; a blue-green deployment of a trivial change is performed with zero
customer-visible downtime; a simulated IHS node failure is automatically
routed around.

Scope note (unchanged from original): "Regional Routing" stays out of scope
here — single-region simulation at most, true multi-region is P06.

---

Version 22 — Enterprise Middleware Integration Capstone
--------------------------------------------------------------
WebSphere Topic: End-to-end integration validation across everything built
in P01 + P02: Deployment Manager, Cell/Node administration, clustering,
JDBC, JMS, IBM MQ, IHS, SSL, Security, Monitoring, Load Balancing,
Change/Release Management, Backup & Recovery.

Minimum App Needed
Zero new banking functionality. This version validates the full stack, it
doesn't add features. Everything exercised here already exists: Customer,
Account, Balance, Deposit, Withdraw, Freeze/Unfreeze, Beneficiary, Fund
Transfer (internal via v15, external via v19), Transaction
History/Statement (REST+SOAP via v16), Notifications (P01 v13), Reports
(P01 v14).

End-to-End Request Flow

Internet
│
▼
Enterprise Load Balancer (v21)
│
▼
IBM HTTP Server (v20)
│
▼
WebSphere ND Cluster
│
├────────► Banking Application (single EAR)
├────────► REST/SOAP Services (v16)
├────────► JMS / SIBus (v15)
├────────► IBM MQ (v19)
├────────► Notification Service (P01 v13)
└────────► Reporting Service (P01 v14)
│
▼
PostgreSQL Database


Topics Covered: Deployment Manager, Cell Administration, Node
Administration, Clustering, JDBC, JMS, IBM MQ, IBM HTTP Server, Security,
SSL, Monitoring, Performance Tuning, Thread/Heap Analysis, High
Availability, Load Balancing, Deployment Automation, Troubleshooting,
Change & Release Management.

Sprint Deliverable: A full end-to-end request (customer performs a Fund
Transfer) completes successfully through every layer (LB → IHS → WAS
Cluster → App/JMS/MQ/Notifications → PostgreSQL); a mock production
incident is worked through a documented runbook (detect via monitoring →
diagnose → resolve → document); a full backup/recovery of WAS configuration
is performed and restored.

What was deliberately dropped from the original Capstone scope: Fixed
Deposits, Recurring Deposits, and Loan Management were listed in the
original v22 as things this Capstone "combines," but none of them were ever
built anywhere in P01 or P02 — including them here would repeat the same
continuity gap this rewrite just fixed. They stay out of scope; Loan
Management is picked up explicitly in P03, Version 30, and Fixed/Recurring
Deposits remain unscoped (tracked as an open item in the Progress Log).

---

---

Version 22.5 — Database Migration (PostgreSQL → Oracle 21c XE)
-----------------------------------------------------------------
Objective: Migrate the existing digistack_bank PostgreSQL database to
Oracle 21c XE as a dedicated Pluggable Database (DIGISTACK_CBS), making
it ready for P03 v23's CBS split. This version is a pure infrastructure
migration — zero new banking features, zero application code changes
visible to end users. The two databases never share a host: Oracle runs on its own dedicated VM
(dsb-oracle) while PostgreSQL remains untouched on dsb-db during the
migration window; dsb-db is fully decommissioned and deleted at
P03 v23 Sprint 4.

This version follows the established suffix-slot convention (v4.5, v16.5,
v35.5) — it does not shift any existing version number.

Deployment Model (revised 2026-08-25 — separate-VM design)
Oracle 21c XE is installed on a NEW, dedicated VM: dsb-oracle. It is NEVER
installed on dsb-db (the PostgreSQL VM) — the two database engines never
share a host, matching real enterprise estates where OLTP databases get
dedicated servers. dsb-oracle is provisioned with 4 GB RAM / 2 vCPU / 60 GB
disk (sized for Oracle XE + OS headroom). PostgreSQL remains untouched on
dsb-db during the migration window. Once P03 v23 Sprint 4 decommissions
the Portal's PostgreSQL DataSource, the entire dsb-db VM is shut down,
snapshotted once, and deleted — Oracle on dsb-oracle becomes the sole
database for the remainder of the roadmap.

Oracle 21c XE Installation & Setup Guide (dsb-oracle — for Oracle beginners)
-----------------------------------------------------------------------------
Step 1 — Provision the VM
  - Create VM dsb-oracle: 2 vCPU, 4 GB RAM, 60 GB disk, Oracle Linux 8
    (or RHEL/Rocky 8 — free, closest to what Oracle runs on in real banks).
  - Network: same subnet as the WAS VMs; static IP; hostname dsb-oracle.
  - Open firewall port 1521 only:
      sudo firewall-cmd --permanent --add-port=1521/tcp
      sudo firewall-cmd --reload

Step 2 — Download & Install Oracle 21c XE (free, no license needed)
  - Download oracle-database-xe-21c-1.0-1.ol8.x86_64.rpm from Oracle's
    website (free Oracle account required).
  - Transfer to dsb-oracle (scp) and install:
      sudo dnf localinstall oracle-database-xe-21c-1.0-1.ol8.x86_64.rpm
  - This installs the binaries under /opt/oracle/product/21c/dbhomeXE.

Step 3 — Configure the Database (one command; creates CDB + XEPDB1 PDB)
      sudo /etc/init.d/oracle-xe-21c configure
  - It prompts for: SYS/SYSTEM/SYSMAN passwords (record in the vault per
    STD Golden Rules — never in docs) and the listener port (accept 1521).
  - This takes 10–20 minutes. Verify afterwards:
      sudo systemctl status oracle-xe-21c

Step 4 — Environment Setup (so sqlplus works in your shell)
  Add to /home/oracle/.bashrc (as the oracle OS user):
      export ORACLE_BASE=/opt/oracle
      export ORACLE_HOME=$ORACLE_BASE/product/21c/dbhomeXE
      export ORACLE_SID=XE
      export PATH=$ORACLE_HOME/bin:$PATH
      export LD_LIBRARY_PATH=$ORACLE_HOME/lib

Step 5 — Create the DIGISTACK_CBS Pluggable Database
  Connect as SYS:
      sqlplus sys/<password>@localhost:1521/XE as sysdba
  Then:
      CREATE PLUGGABLE DATABASE DIGISTACK_CBS
        ADMIN USER cbsadmin IDENTIFIED BY "<vault password>"
        FILE_NAME_CONVERT=('/opt/oracle/oradata/XE/pdbseed/',
                           '/opt/oracle/oradata/XE/DIGISTACK_CBS/');
      ALTER PLUGGABLE DATABASE DIGISTACK_CBS OPEN;
      ALTER PLUGGABLE DATABASE DIGISTACK_CBS SAVE STATE;
  (SAVE STATE = PDB auto-opens on VM reboot — do not skip this.)

Step 6 — Create the Application Schema & Tablespace
      ALTER SESSION SET CONTAINER=DIGISTACK_CBS;
      CREATE TABLESPACE digistack_data
        DATAFILE '/opt/oracle/oradata/XE/DIGISTACK_CBS/digistack_data01.dbf'
        SIZE 500M AUTOEXTEND ON NEXT 100M MAXSIZE 12G;
      CREATE USER DIGISTACK_APP IDENTIFIED BY "<vault password>"
        DEFAULT TABLESPACE digistack_data QUOTA UNLIMITED ON digistack_data;
      GRANT CREATE SESSION, CREATE TABLE, CREATE SEQUENCE, CREATE VIEW,
            CREATE PROCEDURE, CREATE TRIGGER TO DIGISTACK_APP;
  (DIGISTACK_APP is the schema owner the JAAS Auth Alias "OracleAlias"
  points at — credentials only in the vault, never hardcoded.)

Step 7 — Enable Remote Listener Access (required so WAS VMs can connect)
      ALTER SYSTEM SET LOCAL_LISTENER='(ADDRESS=(PROTOCOL=TCP)
        (HOST=0.0.0.0)(PORT=1521))' SCOPE=BOTH;
      ALTER SYSTEM REGISTER;
  Verify from a WAS VM:
      sqlplus DIGISTACK_APP/<password>@dsb-oracle:1521/DIGISTACK_CBS

Step 8 — Place the Oracle JDBC Driver (ojdbc8.jar)
  - ojdbc8.jar ships inside $ORACLE_HOME/jdbc/lib/ojdbc8.jar on
    dsb-oracle; copy it to the WAS VM and register it in a WAS shared
    library per the WebSphere Topics Covered below (IBM Java 8 compatible).
  - Confirm Java 8 compatibility: ojdbc8.jar supports JDK 8 through 11.

Step 9 — First expdp Backup (verify the discipline before migration)
      expdp system/<password>@localhost:1521/DIGISTACK_CBS \
        full=y directory=DATA_PUMP_DIR dumpfile=digistack_pre.dmp logfile=digistack_pre.log
  (Run as the oracle OS user; DATA_PUMP_DIR exists by default in XE.)

Step 10 — Document everything in SetupDoc-v22.5.md: VM specs, install
  steps executed, passwords location (vault reference only), listener
  config, PDB name, schema user, ojdbc8.jar shared-library path, and the
  pre-migration expdp verification result.

Migration Approach
A JDBC-based Java migration utility runs inside WAS as a temporary
servlet, holding two simultaneous DataSource connections — one to
PostgreSQL (jdbc/BankDS, existing) and one to Oracle (jdbc/OracleDS,
new) — reading from PostgreSQL and writing to Oracle table by table,
with row-count verification at each step. This dual-DataSource pattern
is a genuine WAS administration exercise: two JDBC Providers, two
DataSources, two JAAS Auth Aliases, coexisting in the same WAS cell
during the migration window.

Oracle 21c XE Limits (apply for the remainder of the roadmap)
- CPU threads used by Oracle: 2 (matches dsb-oracle vCPU allocation)
- RAM used by Oracle: 2 GB maximum (dsb-oracle has 4 GB total, leaving
  2 GB OS headroom — dedicated DB VM, no other engines present)- User data limit: 12 GB (more than sufficient for this lab)
- Pluggable Databases: 3 maximum (we use 1 here: DIGISTACK_CBS; a second
  PDB is never required in this roadmap)
- No RAC support: P05's DR uses Oracle Data Guard, not RAC — compatible

**Tables Migrated** (from PostgreSQL 16, schema `bank`, into Oracle DIGISTACK_CBS, via the JDBC-based migration utility — expdp is the backup discipline from this version onward, not the migration mechanism):
- `app_config` (v1) — application configuration key/value pairs
- `users` (v2) — user identity + role
- `accounts` (v3) — includes embedded customer/holder columns (v15 did NOT model a
  separate customer table; customer data lives as columns on `account` and `users`
  until P03 v24 introduces the CIF model — consistent with P03 v24's note)
- `beneficiary` (v15)
- `fund_transfer` (v15)
- `transaction` (v3, Deposit/Withdraw ledger per P01 v3) — migrated with only its existing P01/P02-era rows; later Teller-channel entries (P03 v29) and REST-led entries accumulate in Oracle after migration
- `audit_log` (v17, immutable INSERT-only audit trail per P02 v17) — migrated with its existing P02-era rows; all later entries (v23+ balance-affecting operations, security events) accumulate in Oracle after migration



DDL Dialect Changes (PostgreSQL → Oracle 21c XE)
- SERIAL / BIGSERIAL → NUMBER GENERATED ALWAYS AS IDENTITY
- VARCHAR → VARCHAR2
- BOOLEAN → NUMBER(1) with CHECK constraint (0/1)
- NOW() → SYSTIMESTAMP
- TRUE / FALSE → 1 / 0
- Sequences: Oracle uses implicit identity columns; no separate
  CREATE SEQUENCE statements needed for these tables
- Constraint naming: unchanged — pk=id, fk=<table>_id, idx=idx_<table>_
  <col>, chk=chk_<table>_<rule>, uq=uq_<table>_<col> per STD

JNDI Resources Introduced
- JDBC Provider: Oracle JDBC Provider (ojdbc8.jar, IBM Java 8 compatible)
- DataSource: jdbc/OracleDS (Oracle DIGISTACK_CBS PDB)
- JAAS Auth Alias: OracleAlias (DIGISTACK_APP schema owner credentials,
  never hardcoded per STD Golden Rules)
- Existing: jdbc/BankDS (PostgreSQL) remains active until P03 v23 Sprint 4

Port Change (revised 2026-08-25 — separate-VM design)
- Oracle listener port: 1521 on dsb-oracle (new VM)
- PostgreSQL port 5432 remains open on dsb-db during the migration window
  (v22.5) only — the two engines are on different hosts, so no port
  coexistence is needed on any single VM
- At P03 v23 Sprint 4, dsb-db is fully decommissioned: DataSource removed,
  final pg_dump archived, VM shut down, snapshotted once, then deleted
- Firewall on dsb-oracle: only port 1521 open (restricted to the WAS
  subnet); SSH 22 restricted to the admin bastion

Backup Discipline Change
- PostgreSQL: pg_dump (used through v22)
- Oracle: expdp (Oracle Data Pump export) — effective from this version
  onward. Weekly expdp full export, restore tested every 15 days, last
  2 exports retained — same cadence as the prior pg_dump discipline

WebSphere Topics Covered
Dual JDBC Provider configuration, dual DataSource coexistence, JAAS Auth
Alias per database, Oracle JDBC driver shared library placement, SESSIONS
and PROCESSES init parameter sizing, connection pool math against Oracle's
session model, migration utility deployment and decommission lifecycle.

Enterprise Learning
Enterprise database migration patterns, Oracle 21c XE architecture (CDB/
PDB model), dual-DataSource WAS administration, Oracle session/process
parameter model vs. PostgreSQL max_connections, Data Pump (expdp) backup
discipline, migration verification discipline (row-count reconciliation
before cutover).

Sprint Deliverable
Oracle 21c XE installed on dsb-oracle; DIGISTACK_CBS PDB created;
jdbc/OracleDS DataSource live in WAS alongside jdbc/BankDS; all tables
migrated with row-count verification passing for every table; JDBC-based
migration utility deployed, executed, and then undeployed from WAS;
expdp backup of DIGISTACK_CBS captured and verified restorable;
dsb-oracle confirmed stable (PDB auto-opens after reboot via SAVE STATE,
remote connection from WAS VMs verified); dsb-db (PostgreSQL VM)
untouched and running independently; existing
application (digistack-bank-v22.ear) confirmed fully functional against
jdbc/OracleDS after migration; jdbc/BankDS remains available only as the
rollback/source DataSource until P03 v23 Sprint 4.

Technical Debt Introduced
- PostgreSQL digistack_bank remains live on its dedicated VM (dsb-db)
  until P03 v23 Sprint 4 decommissions the Portal's DataSource and
  deletes the dsb-db VM entirely (final pg_dump archived first) —
  documented open debt, not a silent gap
- ojdbc8.jar placed in WAS shared library — must be retained for the
  remainder of the roadmap; never removed without a documented version
  referencing its removal

---

Completion Checklist
------------------------
□ Customer/Account model supports multiple accounts per customer
  (introduced v15)
□ Beneficiary registration and Fund Transfer proven on both routing paths:
  internal (own Account1 → Account2 via SIBus) and external (Customer1 →
  Customer2 via IBM MQ, credit visible on Customer2's login), with DLQ
  handling proven on both paths
□ Transaction History/Account Statement available via both REST (Balance
  Inquiry) and SOAP (Statement) endpoints, WSDL published
□ MFA/OTP, account lockout, and basic security-event detection enforced;
  v16's endpoints require authentication
□ Operations Dashboard live with PMI/JMX-backed JVM, session, queue, and DB
  pool monitoring
□ IBM MQ Queue Manager operational for the external Fund Transfer leg
□ IHS serving URL rewrite, maintenance mode, and health checks; external
  load balancer fronting IHS with blue-green capability proven
□ Full platform integration validated end-to-end (Capstone) with a
  documented runbook and tested backup/recovery
□ Oracle 21c XE live on dedicated VM dsb-oracle; DIGISTACK_CBS PDB created;
  jdbc/OracleDS coexisting with jdbc/BankDS; all tables (incl. audit_log)
  migrated with row-count verification passing; expdp backup captured and
  verified restorable; dsb-db (PostgreSQL VM) untouched and running
  independently (decommission deferred to P03 v23 Sprint 4)
□ Still one EAR (digistack-bank-v22.ear) — no Portal/CBS split yet; that's
  P03 v23

Application State After P02
--------------------------------
Application: digistack-bank-v22.ear

Modules (built on top of P01's Home/Login/Balance/Deposit/Withdraw/
Freeze/Report/Email)
- Customer (multi-account)
- Account
- Beneficiary
- Fund Transfer (internal + external via MQ)
- Transaction History / Account Statement (REST + SOAP)

Infrastructure (added on top of P01's DMGR/Node/Cluster/DataSource/JNDI/
IHS/SSL/Security/JVM/Mail/Reports)
- SIBus / JMS (queues, MDB, DLQ)
- Web Services engine (JAX-RS + JAX-WS, WSDL)
- Hardened security (MFA, LTPA, CSRF/XSS, API auth)
- PMI/JMX monitoring dashboard
- IBM MQ Queue Manager
- IHS advanced admin (rewrite, maintenance mode, health checks)
- External Load Balancer (blue-green, HA)
- Oracle 21c XE (DIGISTACK_CBS PDB) on dedicated VM dsb-oracle, with
  jdbc/OracleDS + OracleAlias live alongside jdbc/BankDS (PostgreSQL on
  dsb-db) until the P03 v23 cutover — per v22.5

Carried Forward to P03
---------------------------
This is the exact starting point P03 picks up from — where the real
Portal/CBS application split (v23), the two Tomcat-based channel simulators
(Mobile v26, ATM v27), the WAS-hosted Card Portal (v28), and Branch Portal
(v29) begin.