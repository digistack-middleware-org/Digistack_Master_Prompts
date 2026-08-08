ID: P02
Version: 1.0
Status: Active

Title: WebSphere / Enterprise Middleware Integration

Imports:
IDX
STD
ARCH02
STDGAP01


Exports:
Versions 15-22
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
- Customer — minimal: the existing users row (from v2) gets a customer_id,
  name, and the ability to hold more than one account. No KYC, no
  verification workflow — just enough to say "a customer can have multiple
  accounts."
- Account (multi-account) — the existing accounts table (v3) gains a
  customer_id foreign key, so one customer can have 2+ accounts.
- Beneficiary — minimal: register another account (own or a test
  "external" account) you're allowed to transfer to. One table, no
  approval workflow.
- Fund Transfer — the actual new transaction: move money from one of your
  accounts to a registered Beneficiary account. This is the feature that
  gets processed asynchronously.

How It's Wired
Fund Transfer returns an immediate "accepted" response to the customer,
then the actual balance update happens via an MDB consuming from a JMS
Queue on SIBus. A deliberately-failing transfer (e.g., insufficient funds
discovered only during async processing) lands in the DLQ and is inspected.

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

Rapid Transfers
│
▼
Security Audit Event


Topics Covered: Global Security, Administrative Security, Application
Security, LDAP, LTPA, SSL, JAAS, Role Mapping, Secure Cookies, CSRF & XSS
Protection.

Sprint Deliverable: MFA/OTP enforced on login; account locks after N failed
attempts; LTPA token validated across the cluster; v16's REST/SOAP
endpoints reject unauthenticated calls; a rapid-repeated-transfer test
triggers a security audit log entry.

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

---

Version 19 — IBM MQ Integration
--------------------------------------
WebSphere Topic: IBM MQ Queue Manager, local/remote/transmission queues,
channels, MQ JMS Connection Factory, MQ-level DLQ.

Minimum App Needed
No new banking feature — extends v15's Fund Transfer with an external leg:
- A Fund Transfer whose Beneficiary is flagged "external bank" now routes
  its message through IBM MQ instead of (or in addition to) the internal
  SIBus queue from v15, simulating a Payment Request sent to an external
  banking system and a Payment Response received back.

Topics Covered: IBM MQ, Queue Manager, Local Queue, Remote Queue,
Transmission Queue, Channels, Listener, Triggering, MQ JMS, MQ Connection
Factory, Dead Letter Queue, MQ Monitoring.

Sprint Deliverable: IBM MQ Queue Manager created and connected via MQ JMS
Connection Factory; an "external" Fund Transfer sends a Payment Request
message to an external banking system simulator and receives a Payment
Response back through a dedicated response queue.

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

Completion Checklist
------------------------
□ Customer/Account model supports multiple accounts per customer
  (introduced v15)
□ Beneficiary registration and Fund Transfer (internal via SIBus, external
  via IBM MQ) working, with DLQ handling proven on both paths
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

Carried Forward to P03
---------------------------
This is the exact starting point P03 picks up from — where the real
Portal/CBS application split (v23), the two Tomcat-based channel simulators
(Mobile v26, ATM v27), the WAS-hosted Card Portal (v28), and Branch Portal
(v29) begin.