ID: P03
Version: 1.0
Status: Active

Title: Enterprise Banking Systems (CBS, Payments, Channel Simulators, Loans)

Imports:
IDX
ARCH01
ARCH02
RACI01
CONTEXT_PACK


Exports:
Versions 23-30 (incl. suffix-slot v30.5 Expert War-Game & Final Exam)
Core Banking System (CBS) — sole writer of digistack_cbs
CIF & Account Lifecycle
Payment Hub (NEFT/IMPS)
Mobile Banking Simulator (Tomcat)
ATM Simulator (Tomcat)
Card Portal (WAS)
Branch Portal / Enterprise Banking Operations
Loan Management
Governing Rule (single-writer discipline)

Used By:
P03.1
P03.2
P04
P05
P06
P07
P08

Next: P03.2

Sub-Parts Note (added 2026-08-25)
----------------------------------
This Part is followed by two sub-Parts, P03.2 (Enterprise Interview
Book — reference material) and P03.1 (Interview Preparation — drill
layer), which build on P03's nine-application topology (7 WAS EARs +
2 Tomcat apps) before P04 begins observability work. Reading order:
P03 → P03.2 → P03.1 → P04. Their scope is defined in their own Part
files; P03 imposes no additional prerequisites beyond its own Completion
Checklist.


---

Purpose
-------
Transform DigiStack Bank into a real enterprise banking platform where the
Core Banking System (CBS) is the heart of all banking operations, with
realistic external channel simulators (Mobile, ATM, Card Portal)
integrating against it.

Two-App Architecture (Evolves in This Part)
------------------------------------------------
- DigiStack Internet Banking Portal — presentation layer ONLY. Its canonical
  WebSphere application/artifact name is digistack-portal-v<N>.ear. From
  Version 23 onward, it no longer touches the database directly — it calls
  CBS services exclusively.
- DigiStack CBS (Core Banking System) — becomes the single system of
  record. All customer/account/transaction data is owned and updated only
  through CBS.
- NEW in this Part — Channel Simulators: Mobile Banking and ATM are built
  as small, separate applications deployed on Apache Tomcat (not
  WebSphere), each under its own subdomain, each calling CBS via REST/SOAP
  exactly like any external client would. Card Portal, by contrast, is
  deployed on WebSphere — see "Why Card Portal Belongs on WAS" below. See
  the Channel Simulator Standard for the two Tomcat apps.
- NEW in this Part — Satellite Services: Payment Hub, Notification
  Service, and Reporting Service each become their own independent
  WebSphere applications (own EARs), rather than modules bolted onto CBS
  or Portal. See the Governing Rule and Ownership Matrix below.

Prerequisite
------------------
P02 Completion Checkpoint satisfied (full middleware stack — LB, IHS, WAS
Cluster, SIBus JMS, IBM MQ, Web Services, Security Hardening, Monitoring —
all operational).

---
Part-Start Architecture Diagram (generate first, before Version 23 work begins)
----------------------------------------------------------------------------------
Pruned to what P03 (v23-v30) adds or extends. This is the Part with the
biggest topology change — the single P01/P02 EAR is progressively split
and extended into 9 deployable applications by the end of P03 — so more
of the architecture tree gets touched than in P02.

Topology progression:
v23 = 4 deployables,
v25 = 5,
v26 = 6,
v27 = 7,
v28 = 8,
v29 = 9;
v30 adds internal CBS functionality without creating another deployable
application.

                 DIGISTACK BANK — P03 (v23-v30)
                       |
       +---------------+---------------+
       |               |               |
       v               v               v
   NETWORK           SERVERS        DEPLOYMENT
       |               |               |
 01_Network_       02_VM_          08_Deployment_
 Diagram.md         Layout.md       Architecture.md
 (extended: v26/v27  (extended:      (extended: v23 CBS
  dsb-tomcat          dsb-tomcat      split into CBS/
  powers on)          powers on,      Portal/Notification/
                       Mobile+ATM)    Reporting EARs;
                                      v25 Payment Hub;
                                      v28 Card Portal;
                                      v29 Branch Portal)
       |
       v
              WebSphere ND
                       |
       +---------------+---------------+
       |                               |
       v                               v
   REQUESTS                         CLUSTER
       |                               |
 03_Request_Flows.md           04_Cluster_Architecture.md
 (extended: v23 Portal->CBS      (unchanged from P01/P02 —
  REST/SOAP pivot replaces        still the 2-member
  direct DB path; v26/v27          AppCluster; no new
  Tomcat->CBS REST paths           members this Part)
  added)
       |
       v
   APPLICATION
       |
       v
   Oracle 21c XE (DIGISTACK_CBS PDB)   ← migrated at P02 v22.5; lives on
       |                                  its dedicated VM dsb-oracle.
       v                                  PostgreSQL VM (dsb-db) fully
                                          deleted at v23 Sprint 4
 06_Database_ER_Diagram.md
 (extended: v23 digistack_cbs
  dedicated DataSource cutover;
  v24 cif table; v28 card table;
  v29 bod_eod_log; v30 loan tables)

   SECURITY
       |
       v
 07_Security_Architecture.md
 (extended: v26-v28 SSL cert
  provisioning per new subdomain,
  per CI01 §5.1)

Still not in scope this Part: 09_DR_Architecture.md (P05).

The One Governing Write Rule (Introduced at Version 23, Applies for the Rest of the Roadmap)
-------------------------------------------------------------------------------------------
Only CBS performs business-data writes to digistack_cbs. Every other application either invokes
CBS services (synchronously, via REST/SOAP/EJB), consumes CBS-published
events (asynchronously, via JMS/MQ), or has explicitly approved read-only
access where this roadmap states that direct reads are required. No
application other than CBS may perform a business-data write to
digistack_cbs.

Applied per service, from Version 23 onward:


|
 Service 
|
 Allowed 
|
 Never 
|
|
---
|
---
|
---
|
|
 Payment Hub 
|
 Routes payments, coordinates settlement 
|
 Never updates balances directly — settlement is confirmed back to CBS, which performs the actual write 
|
|
 Notification Service 
|
 Consumes events, sends SMS/Email 
|
 Never updates account balances 
|
|
|  Reporting Service 
|
|  Reads approved CBS data read-only and generates reports 
|
|  Never updates business tables and never performs business-data writes 
|
|
 Branch Portal 
|
 Invokes CBS services on the teller's behalf 
|
 Never touches digistack_cbs directly 
|
|
 Mobile / ATM (Tomcat) 
|
 REST/SOAP calls into CBS 
|
 Never touches digistack_cbs directly 
|
|
 Card Portal (WAS) 
|
 REST/SOAP calls into CBS's Card Service 
|
 Never touches digistack_cbs directly 
|

This rule is a standing footnote for every request-flow diagram in this
Part and every Part after it — it's the thing that keeps the topology
honest as more services are added.

---

Why Card Portal Belongs on WAS, Not Tomcat
------------------------------------------------
Unlike Mobile Banking and ATM — genuinely external, lower-risk customer
channels — the Card Portal is treated as a bank-owned enterprise
application in this roadmap. It handles sensitive card lifecycle
operations: Card Issue, Card Activation, Card Blocking, PIN Generation, PIN
Reset, Card Replacement, Card Status, and Hotlisting. These are commonly
hosted on enterprise middleware (WebSphere) rather than a lighter-weight
servlet container, reflecting how real banks segregate card-management
systems from consumer-facing channel apps. Accordingly, Card Portal is
deployed as its own WebSphere EAR (digistack-cardportal-vN.ear) on the same ND cluster as Internet Banking Portal, CBS, Payment Hub,
Notification Service, and Reporting Service (Branch Portal joins at v29) — not on Tomcat.


This raises the WAS EAR count to six (seven once Branch Portal joins at v29 — i.e., seven by end of this Part), giving practice
across: multiple EAR deployments, context roots, virtual hosts, classloader
isolation, shared libraries, security roles, independent deployment/rollback
per application, application startup order, cluster deployment, session
management, and cross-application troubleshooting — all genuinely valuable,
résumé-relevant WebSphere administration territory.

Channel Simulator Standard (Applies to Versions 26 and 27)
------------------------------------------------------------------
Mobile Banking and ATM are each built as a small standalone app with a neat
UI, deployed on Tomcat, under its own subdomain, rather than as modules
bolted onto the WAS-hosted CBS/Portal. Card Portal (Version 28) follows a
different deployment model — see above — but reuses the same
presentation-only guardrails.


|
 Application 
|
 Domain 
|
 Deployment 
|
 Version 
|
|
---
|
---
|
---
|
---
|
|
 Mobile Banking 
|
 mobile.digistack.cloud 
|
 Apache Tomcat 
|
 Version 26 
|
|
 ATM Simulator 
|
 atm.digistack.cloud 
|
 Apache Tomcat 
|
 Version 27 
|
|
 Card Portal 
|
 card.digistack.cloud 
|
 WebSphere (own EAR) 
|
 Version 28 
|

Why the Tomcat split is genuinely good WebSphere admin practice, not a
detour: Real banking estates are rarely single-vendor — WebSphere ND
commonly coexists with lighter Tomcat instances for newer/lower-risk
channels. The valuable admin skill here isn't Tomcat itself — it's
configuring IHS / the Enterprise Load Balancer to route distinct subdomains
to a completely different backend server type, alongside the existing WAS
cluster. That's real heterogeneous-topology routing work. Card Portal's
placement on WAS instead is the deliberate counterpoint: not every channel
goes to the lighter tier, and knowing which ones don't (and why) is itself
part of real architecture judgment.

Shared guardrails for Mobile and ATM simulators:
- Presentation-only, same rule as the main Portal — zero direct database
  access, all business logic and data access happens through CBS REST/SOAP
  calls (relocated to CBS in Version 23, originally built in Version 16,
  hardened in Version 17).
- "Neat UI" means clean, minimal HTML5/CSS3/Bootstrap 5 — no heavy JS
  framework (same standing tech constraint: no React/Angular/Vue). A neat
  UI is entirely achievable with well-structured Bootstrap 5 + a little
  vanilla JS.
- Tomcat installation/config is kept intentionally shallow — the learning
  focus stays on IHS/LB routing, virtual hosts, and cross-server topology,
  not deep Tomcat administration.
- Each simulator authenticates against CBS (reusing Version 17's MFA/OTP/
  session rules, now hosted by CBS per the Version 23 relocation) — none of
  them maintain their own separate user store.
- Each is its own Git repo or a clearly separated folder (digistack-mobile,
  digistack-atm-sim), per Git Standards. (Card Portal follows the same
  one-repo-per-application principle but as a WAS EAR project — see Version
  28.)

Shared request flow pattern (Mobile and ATM):

Customer / Teller / ATM User
│
▼
<subdomain>.digistack.cloud
│
▼
Enterprise Load Balancer
│
▼
IBM HTTP Server
│
Virtual Host Routing Rule
(<subdomain> → Tomcat, NOT the WAS plugin)
│
▼
Apache Tomcat
│
▼
Simulator App (JSP/Servlet, Bootstrap 5 UI)
│
REST/SOAP calls only
▼
DigiStack CBS (sole writer of digistack_cbs)
│
▼
digistack_cbs Database


Payment-routing exception (applies from v26 onward): account-data reads — login/auth, balance, mini statement, beneficiary list — go to CBS exclusively, exactly as the flow above shows. However, channel payment submissions (IMPS transfers, Quick Pay) go directly to Payment Hub's REST endpoint, not through CBS: payment routing is Payment Hub's role per v25, and Payment Hub still never writes balances — CBS alone performs the settlement write. This is the one deliberate exception to the "everything calls CBS" picture and does not weaken the Governing Rule (no channel ever touches digistack_cbs).

Shared WebSphere/Enterprise topics across Mobile and ATM (Tomcat):
- Heterogeneous backend routing (WAS + non-WAS Tomcat instances behind one
  IHS/LB tier)
- Virtual host configuration per subdomain, alongside existing WAS virtual
  hosts and Card Portal's own WAS-routed virtual host
- Reverse proxy rules distinguishing plugin-routed traffic (→ WAS,
  including Card Portal) vs. standard proxy-routed traffic (→ Tomcat)
- SSL/certificate handling for three additional subdomains on the same IHS
  instance (mobile, atm routed to Tomcat; card routed to the WAS plugin)
- Cross-server topology documentation and troubleshooting

---

Enterprise Architecture (Introduced at Start of This Part)
------------------------------------------------------------------
                DigiStack Enterprise Banking

                       Customer Channels
 ┌──────────────┬──────────────┬─────────────┬─────────────┐
 │              │              │             │
 ▼              ▼              ▼             ▼

Internet Banking Mobile Banking ATM/POS Card Portal / Branch Banking
(WAS/IHS) (Tomcat) (Tomcat) (WAS/IHS)
│ │ │ │
└──────────────┴──────────────┴─────────────┘
│
▼
DigiStack Core Banking System
(CBS)
(sole writer of digistack_cbs)
│
┌─────────────┬──────────────┬──────────────┐
▼ ▼ ▼ ▼
Customer Accounts Transactions Products
│
▼
digistack_cbs Database


---

Version 23 — Core Banking System (CBS)
---------------------------------------------
Objective: Develop DigiStack CBS, the central banking platform responsible
for executing every banking transaction. After this version, Internet
Banking will no longer access the database directly. Instead, it invokes
CBS services, and CBS becomes the only system that updates customer
accounts.

This version is the architectural pivot point of the entire project.
Everything built across P01–P02 lived inside a single EAR with reasonable
proximity to the database. From this version forward, ownership of data,
endpoints, and infrastructure is explicitly transferred to CBS — see the
Migration & Relocation Notes below. Nothing is silently dropped; every
P01/P02 capability is accounted for.

Architectural Decision: CBS Stays a Single Application (resolved)
A senior-architect review of this roadmap flagged that every satellite
service introduced from this version forward (Payment Hub, Notification
Service, Reporting Service, and later Branch Portal, Card Portal) gets
split into its own independently deployable EAR — but CBS itself remains
one independently deployable application.

Internally, CBS spans the CIF, Account, Transaction, Card, Operations,
and Loan service modules across the roadmap. These modules remain inside
the same CBS EAR rather than becoming independently deployable applications.
Products remain a business-domain concept within CBS rather than a separate
deployable application.

CBS never gets decomposed the same way for the life of this roadmap.

This is a deliberate decision, not an oversight: CBS remains a single
application throughout this roadmap. The reasons:
- It keeps transaction management simple — CIF, Accounts, and Transactions
  frequently need to participate in the same local (non-distributed)
  transaction, which is straightforward inside one EAR/one JVM and
  considerably harder once split across independently deployed
  applications (see Version 25's note on why Payment Hub deliberately
  avoids this problem rather than solving it).
- The roadmap's primary goal is WebSphere ND administration practice, not
  microservice decomposition practice — the satellite services already
  provide ample multi-EAR administration surface (7 WAS EARs + 2 Tomcat
  apps by end of this Part) without CBS itself needing to be split for that
  goal to be met.
- Splitting CBS's internal domains (e.g., separating Loans from core
  Deposits/Accounts, which have genuinely different lifecycle and
  regulatory profiles in a real bank) is a legitimate future direction, but
  is explicitly out of scope for this roadmap — it's called out here so the
  decision is visible rather than silently assumed. If a future Part wants
  to explore this, it should do so as new, clearly-scoped work, not a
  retrofit into Parts already built.

Banking Features Added
Customer: Customer Information, Customer Verification
Accounts: Open Account, Close Account
Transactions: Deposit, Withdrawal, Balance Inquiry, Fund Transfer, Mini
Statement
Products: Savings Account, Current Account

Account Schema Note — Branch + IFSC Fields (added 2026-08-25)
-------------------------------------------------------------------
The accounts table in digistack_cbs gains two new display-only fields
this version: branch_name (e.g. "Hyderabad Main") and ifsc_code (e.g.
"DIGI0001234"). These are reference data sourced from a branches lookup
table — not user-entered, assigned at account-open time. They are
read-only on every channel (Portal, Mobile, ATM, Card Portal) — only
CBS may set them, per the Governing Rule. These fields surface on the
Accounts screen (account detail card) and are used nowhere else in
P01–P03 except as display metadata.

Accounts Screen UI Note — Full Detail Card (added 2026-08-25)
-------------------------------------------------------------------
From this version onward the Accounts screen detail card displays the
complete set of fields the UI mockup shows — all now available in
digistack_cbs for the first time:
- Account Number (masked: XXXX XXXX XXXX 4521) — present since P01 v3
- Account Type (Savings / Current) — introduced this version
- Branch (branch_name, e.g. "Hyderabad Main") — introduced this version
- IFSC (ifsc_code, e.g. "DIGI0001234") — introduced this version
- Available Balance — present since P01 v3
- View Transactions button — live at P02 v16.5 (Transaction History
  pagination); links to Transaction History screen
- Download Statement button — live at P02 v16; calls SOAP Account
  Statement service
All fields read from CBS via REST/SOAP — the Portal never queries
digistack_cbs directly, per the Governing Rule established this version.

Multi-Account Dashboard Tile Labels (added 2026-08-25)
-------------------------------------------------------------------
The Dashboard account tiles introduced as a list/switcher at P02 v15
now show the account type label ("SAVINGS ACCOUNT" / "CURRENT ACCOUNT")
alongside the masked account number and balance — because account_type
is only available in digistack_cbs from this version onward. Before v23,
tiles show only the masked account number and balance (no type label).
The tile layout does not change; only the type label populates.

Request Flow

Customer
│
▼
Internet Banking
│
REST/SOAP
▼
DigiStack CBS
│
Business Validation
│
Transaction Manager
│
CBS Database
│
Response
│
Internet Banking
│
Customer


Database Architecture (New in This Version)
CBS uses the Oracle 21c XE DIGISTACK_CBS PDB migrated at v22.5 —
already populated with all existing data from the PostgreSQL
digistack_bank database. A new DataSource (jdbc/CBSDataSource) is configured in WAS pointing at
Oracle (jdbc:oracle:thin:@dsb-oracle:1521/DIGISTACK_CBS), using the Oracle
JDBC Provider and ojdbc8.jar shared library already placed at v22.5 —
no provider reinstallation required. jdbc/CBSDataSource gets its own
connection pool sizing and JAAS Auth Alias (CBSAlias) separate from the
P01/P02 PostgreSQL pool. The v22.5-era jdbc/OracleDS (migration
utility's DataSource) is retired at this version's cutover — its role
is assumed by jdbc/CBSDataSource; removal of jdbc/OracleDS is captured
in SetupDoc-v23.md alongside the jdbc/BankDS decommission. This mirrors real banking
architecture where the core banking database is isolated
from channel-layer databases for security, blast-radius,
and performance-tuning reasons.

Migration & Relocation Notes

1. Data Migration

Portal (Shared Database, P01/P02)
│
▼
Migration (V23__migrate_existing_data_to_cbs.sql)
│
▼
digistack_cbs
│
▼
Switch DataSource (jdbc/CBSDataSource)
│
▼
CBS Live

Data migration from PostgreSQL to Oracle was completed at v22.5
using a JDBC-based Java migration utility, with row-count
verification for all tables. The DIGISTACK_CBS Oracle PDB already
contains all migrated data at the start of this version. Version
23's migration script (V23__migrate_existing_data_to_cbs.sql) is
therefore an Oracle-dialect DDL extension script — adding any CBS-
specific new columns or tables to the already-populated Oracle
schema — rather than a full data migration. The Portal's PostgreSQL
DataSource (jdbc/BankDS) is decommissioned in Sprint 4 of this
version. In the same Sprint, the PostgreSQL VM (dsb-db) is fully
decommissioned: one final pg_dump archived to the backup location,
the VM shut down, a single retention snapshot taken, and the VM
deleted — PostgreSQL ceases to exist in the estate from this point
forward (matches the P02 v22.5 separate-VM design: Oracle never
shared a host with PostgreSQL). Once verified, the Portal's DataSource is
decommissioned entirely — the Portal retains no database connectivity of
its own from this point forward.

2. Service Relocation
The REST and SOAP services introduced in Version 16 relocate from the
Banking Portal application to the CBS application. Existing clients
continue using the same contracts — no breaking changes to endpoint paths,
WSDL, or request/response shapes.

3. Messaging Relocation
The SIBus, MDBs, JMS queues, and IBM MQ integrations introduced in
Versions 15 and 19 are now hosted by CBS, since they represent business
processing rather than presentation logic. The Portal no longer has any
direct SIBus/MQ configuration.

4. Notification & Reporting Relocation
The Withdraw-triggered email (v13) and Transaction Report (v14) — later
joined by v16's SOAP Account Statement — are extracted from the single EAR
into two new independent applications: Notification Service and Reporting
Service.

Notification Service consumes CBS-published transaction events and never
queries or writes the CBS database directly.

Accepted tradeoff: Reporting Service reads digistack_cbs directly,
read-only, from Version 23 until P09 v64 introduces the RDS read replica.
Report generation (especially the large Transaction Report from P01 v14,
deliberately designed to stress the heap with multi-thousand rows) running
against the same database instance serving live Fund Transfers is a genuine
OLTP/OLAP contention risk.

See "Satellite Services" note below; both services are built out fully
starting this version and referenced again through v25.

Ownership Matrix (before/after this version):

| Component | Before v23 | After v23 |
|---|---|---|
| Database Writes | Portal | CBS only |
| REST/SOAP Endpoints | Portal | CBS |
| SIBus / MDB | Portal | CBS |
| IBM MQ Business Processing (external/Customer-to-Customer leg) | Portal | CBS |
| Authentication | Portal (own session/MFA) | Internet Banking authenticates users (login/MFA) and propagates a trusted identity (LTPA/JWT) to CBS; CBS performs authorization for business services |
| Notifications | Portal module (v13) | Notification Service (own EAR) |
| Reporting | Portal module (v14, v16 SOAP) | Reporting Service (own EAR) |
| Payment Processing (from v25) | — | Payment Hub (own EAR), routes only — never writes balances |

EAR Naming Convention
---------------------
From Version 23 onward there are multiple independently deployable
applications. Every WebSphere EAR uses the following canonical format:

  digistack-<application>-v<N>.ear

where <N> is the roadmap version that last changed that application.
Version numbers are NOT independent per application.

Canonical application names:

  Internet Banking Portal  → digistack-portal-v<N>.ear
  Core Banking System      → digistack-cbs-v<N>.ear
  Payment Hub              → digistack-paymenthub-v<N>.ear
  Notification Service     → digistack-notification-v<N>.ear
  Reporting Service        → digistack-reporting-v<N>.ear
  Card Portal              → digistack-cardportal-v<N>.ear
  Branch Portal            → digistack-branch-v<N>.ear

Examples:

  v23:
    digistack-portal-v23.ear
    digistack-cbs-v23.ear
    digistack-notification-v23.ear
    digistack-reporting-v23.ear

  v25:
    digistack-paymenthub-v25.ear

  v28:
    digistack-cardportal-v28.ear

  v29:
    digistack-branch-v29.ear

The application name remains stable in WebSphere across updates; the EAR
artifact version changes according to the version that last modified that
application. Do not use vN in an executed deployment document — vN is only
a placeholder when defining the convention.

Tomcat Application Naming Convention
-------------------------------------
Tomcat channel simulators do not use EAR naming because they are not
deployed on WebSphere.

Canonical application/repository names:

  Mobile Banking → digistack-mobile
  ATM Simulator  → digistack-atm-sim

Canonical DNS names:

  Mobile Banking → mobile.digistack.cloud
  ATM Simulator  → atm.digistack.cloud

The repository/application name and DNS name are stable across versions;
the deployment version is tracked by the roadmap version and release tag,
not by changing the DNS name.

Database Migration Scope (cross-reference, updated)
Beginning Version 24, all schema changes occur only inside digistack_cbs.
The legacy Portal/shared PostgreSQL database (used by P01–P02) is frozen
at the moment of the v23 migration and is never targeted by any migration
script numbered V24 or higher — retained read-only only as long as needed
for verification/rollback, then formally decommissioned (capture the
decommission step in SetupDoc-v23.md, not silently assumed).

Backup discipline remains engine-specific from v22.5 onward:
PostgreSQL uses pg_dump through the v23 cutover, while Oracle uses expdp
(Oracle Data Pump) after the v22.5 migration. Each active database requires
a scheduled full backup and periodic restore verification according to its
engine-specific procedure; backup and restore evidence must be retained
for every version.

Satellite Services Introduced Here

Accepted tradeoff: Reporting Service reads digistack_cbs directly,
read-only, from this version through P08. Report generation (especially
the large Transaction Report from P01 v14, deliberately designed to stress
the heap with multi-thousand rows) running against the same database
instance serving live Fund Transfers is a genuine OLTP/OLAP contention
risk. This is a deliberately deferred, not solved-early, tradeoff — a
lightweight on-prem read replica isn't introduced here because it would
require inserting new infrastructure scope into an already-frozen Part
(per Engineering Standards §7's Version Numbering Freeze discipline), and
the roadmap's PostgreSQL streaming-replication skills aren't taught until
P05 v37/v38 anyway. Reporting Service's direct OLTP read is closed by P09
v64's RDS read replica — until then, it's a known, accepted risk, not a
silent gap.

Notification Service and Reporting Service become independent WebSphere
applications in this version — not modules inside CBS.

CBS publishes transaction events (directly or via IBM MQ), which
Notification Service consumes to deliver SMS/Email alerts.

Reporting Service generates operational and customer reports using its
approved read-only access to digistack_cbs from Version 23 until the P09
v64 read-replica migration closes the direct-read exception.
It does not perform core banking transactions or update account balances.

Neither satellite service performs business-data writes. CBS remains the
sole system of record, per the Governing Rule above.

This split is chosen deliberately for independent deployment, scaling, and
maintenance — and for the additional WAS administration practice of
managing two more distinct EARs with their own lifecycles.

CBS
│
├──────────────────────────────► IBM MQ / Events
│                                      │
│                                      ▼
│                              Notification Service
│                                  (own EAR)
│
└──────────────────────────────► Reporting Service
                                    (own EAR)
                                    │
                                    │ read-only
                                    ▼
                              digistack_cbs


Documentation Requirement for This Version
Because v23 changes data, endpoint, and infrastructure ownership all at
once, SetupDoc-v23.md must include a dedicated "Migration & Ownership
Transfer" section (in addition to the standard SetupDoc template) covering:
- Exact migration script execution steps and row-count verification (old
  DB vs. digistack_cbs) before the old DataSource is retired
- The Ownership Matrix above, expanded with exact JNDI names / config
  references
- Confirmation steps proving the Portal can no longer reach the database
  directly (e.g., DataSource unbound/removed from Portal's JNDI, verified
  via a deliberate failed lookup test)
- Confirmation steps proving Notification Service and Reporting Service
  cannot write to digistack_cbs:
    * Notification Service must have no direct business-data database
      write access and consumes CBS-published events only.
    * Reporting Service may have an explicitly approved read-only database
      identity for digistack_cbs during v23-P08, but must have no INSERT,
      UPDATE, DELETE, MERGE, DDL, or other business-data write privileges.
      Its read-only access must be documented and tested.
- A rollback note specific to this version: since this isn't just an EAR
  redeploy but a data + ownership migration, the rollback procedure must
  explicitly state whether rolling back means restoring the old shared-DB
  Portal and retaining the migrated CBS data, or a full revert — this is
  more complex than every prior version's rollback and deserves its own
  explicit call-out rather than the generic template line.
- Migration & Ownership Transfer — Section Requirement (cross-reference)
  This section requirement is formalized project-wide as STDGAP01's §3.8
  Change Request trigger (DB schema migration ⇒ CR required) plus the
  version-23-specific extra section named above — treat both together as
  what "Migration & Ownership Transfer" documentation means going forward,
  not just a one-off instruction for this version.

TestCases-v23.md must include a negative test proving the Portal's old
DataSource lookup now fails, and negative tests proving Notification/
Reporting Service cannot write to digistack_cbs.

VM/database provisioning detail (which VM hosts digistack_cbs, exact
DataSource configuration steps, updates to the standing VM inventory table
in doc 01) belongs in SetupDoc-v23.md — the roadmap states what changes;
the SetupDoc states how.

Topics Covered: Enterprise Service Layer, Shared Business Services, XA
Transactions, Clustered Business Services, Service Integration, JDBC
Optimization, Connection Pool Tuning, Enterprise Deployment, Application
Ownership Migration (data, endpoints, messaging, and satellite service
extraction — new to this version).

Transaction Boundary Note (added 2026-08-25)
------------------------------------------------
"XA Transactions" in this topic list means: the CBS decision documented
below (CBS stays a single application) is what makes distributed 2PC
unnecessary for core banking writes — CIF, Accounts, and Transactions
participate in one local transaction inside one EAR. The remaining
cross-EAR coordination (Payment Hub routing settlement back to CBS, v25)
is explicitly a Saga/compensating-transaction pattern, not XA — see v25's
resolved note. SetupDoc-v23.md includes a short "Transaction Boundaries"
section stating this per operation type: which writes are local (all CBS
balance writes), which are async-coordinated (Payment Hub, MQ leg from
P02 v19), and why 2PC spanning EARs was rejected. This closes the
"no visible transaction boundaries" gap with a documented decision
rather than an absent one.

Enterprise Learning: Core Banking Architecture, Banking Domain Model,
Shared Service Design, Enterprise Transaction Processing, Production
Banking Flow, Real-world migration/ownership-transfer discipline (no
breaking contracts, verified cutover, explicit rollback complexity).

Sprint Deliverable: Internet Banking Portal calls CBS exclusively via
REST/SOAP for every transaction (deposit, withdrawal, balance inquiry, fund
transfer) — direct Portal→DB access is removed entirely; CBS enforces
business validation and local-transaction integrity for all writes (per
the Transaction Boundary Note above — no distributed XA); existing
P01/P02 data is migrated into digistack_cbs and verified; Notification
Service and Reporting Service are stood up as independent applications
consuming CBS events/data.

Architectural note — this is the pivot point of the whole project: From
this version forward, CBS is the only system permitted to write to the CBS
Database. This is the single most important rule for the rest of this Part
onward — including the two Tomcat simulators, Card Portal, Payment Hub,
Notification Service, Reporting Service, and Branch Portal.

---

Version 24 — Customer Information File (CIF) & Account Lifecycle
------------------------------------------------------------------------
Objective: Introduce Customer Information File (CIF), the master customer
repository used by every banking channel. The CIF model supports multiple
independent customers, with one CIF representing one customer and one customer
owning multiple accounts. Each CIF is associated with the authenticated
customer identity used by the banking channels.

Banking Features Added: Create CIF, Modify CIF, Customer Search, Aadhaar
Verification, PAN Verification, Primary Holder, Nominee.

Request Flow

Customer
│
▼
Internet Banking
│
▼
CBS
│
▼
CIF Service
│
▼
Account Service
│
▼
CBS Database


Topics Covered: Multi-module EAR, Shared Libraries, Service-to-Service
Communication, JDBC Transactions, Data Integrity.

Enterprise Learning: CIF Architecture, Customer Master Data, Enterprise
Banking Relationships, Account Lifecycle Management.

Sprint Deliverable: Multiple independent CIF records can be created and
searched; each CIF supports multiple linked accounts; Aadhaar/PAN
verification (simulated) gates creation of each CIF; each CIF is associated
with its authenticated customer identity; CIF Service and Account Service
communicate as separate modules within CBS's multi-module EAR, not as one
monolithic class.

Note: "Enterprise Validation" appeared as a heading with no listed content
in the source material — folded into Enterprise Learning above (Aadhaar/PAN
verification is the validation being exercised in this version).

- The customer data migrated in P02 v22.5 lived as columns on `account`/`users`
  (no standalone `customer` table existed at v15). This migration creates the
  standalone CIF model and backfills `customer` rows from those columns.

---

Version 25 — Payment Systems (NEFT, IMPS)
------------------------------------------------
Objective: Develop DigiStack Payment Hub responsible for routing all
electronic payments.

Deployment Model
Payment Hub is deployed as its own EAR (digistack-paymenthub-v25.ear),
separate from CBS, communicating with CBS via internal REST/SOAP or EJB
calls. This gives an additional distinct deployable unit for WAS admin
practice — its own application lifecycle, classloader, and
startup-dependency ordering (Payment Hub must come up after CBS is
available) — rather than folding payment logic into CBS's multi-module
EAR.

Per the Governing Rule: Payment Hub routes payments and coordinates
settlement, but never updates balances directly. Settlement is confirmed
back to CBS, which alone performs the write to digistack_cbs.

Why This Isn't a Distributed (Cross-EAR) Transaction (resolved)
A senior-architect review flagged that this deployment model — Payment Hub
coordinates, CBS alone writes — is, functionally, a Saga /
compensating-transaction pattern: async coordination between two
independently deployed applications, instead of one shared distributed
(XA) transaction spanning both EARs. Worth stating explicitly, since the
why matters as much as the rule itself:

WebSphere's Transaction Manager technically supports a JTA/XA transaction
spanning multiple EARs on the same cell (Payment Hub could, in principle,
open an XA transaction directly against CBS's jdbc/CBSDataSource). This
roadmap deliberately avoids that. Reasons:
- A shared distributed transaction across two independently deployed
  applications couples their failure domains and deployment lifecycles
  tightly — a Payment Hub redeploy or crash mid-transaction now has to be
  reasoned about in terms of CBS's own transactional integrity, not just
  Payment Hub's.
- CBS remains the sole writer (per the Governing Rule) specifically so
  that the hard part — atomic balance updates — happens inside one
  application, in one local transaction, where it's straightforward.
  Payment Hub's job is routing and coordination, not participating in
  CBS's local transaction.
- This is why Payment Hub "coordinates settlement... never writes balances
  directly" — it's not just an ownership rule, it's the reason a
  distributed XA transaction was never needed here in the first place. The
  same reasoning applies everywhere this pattern recurs later in the
  roadmap: Notification Service consuming events, the IBM MQ external
  payment leg (P02 v19, reused here), and P09 v66's SQS/SNS migration
  decision.

Banking Features Added
Payment Systems: NEFT Transfer, IMPS Transfer
Beneficiary: Add Beneficiary, Modify Beneficiary, Delete Beneficiary
Processing: Payment Validation, Beneficiary Validation, Payment Routing,
Settlement, Failed Payment Handling

Request Flow

Customer
│
▼
Internet Banking
│
▼
Payment Hub (own EAR)
│
┌──┴──┐
▼ ▼
NEFT IMPS
│ │
└──┬──┘
▼
CBS (writes settlement to digistack_cbs)
│
▼
Settlement Confirmation → Payment Hub


Topics Covered: JMS, IBM MQ, Distributed Transactions, Asynchronous
Processing, Retry Mechanisms, Queue Monitoring, Transaction Recovery,
Multi-EAR startup dependency management (new to this version).

Enterprise Learning: Payment Gateway, Payment Switch, Settlement,
Reconciliation, Financial Messaging.

Sprint Deliverable: IMPS transfer completes in real time (synchronous
validation, async settlement via JMS/MQ); NEFT transfer is processed in a
batch window; a deliberately failed payment is retried per the configured
retry policy and, if still unresolved, lands in a reviewable failed-payment
queue; Payment Hub never writes directly to digistack_cbs — all confirmed
settlements are written by CBS.

Consistency note: NEFT/IMPS were originally scoped for this Part in the
Master Index under this exact heading — this version fulfills that scope
precisely, building on the JMS (P02 v15) and IBM MQ (P02 v19) foundations
rather than duplicating them.

Dashboard UI Note — Transfer Money Screen (added 2026-08-25)
-------------------------------------------------------------------
The Transfer Money screen's "Transfer Type" selector activates here with
exactly two options: IMPS and NEFT — matching this version's scope
precisely. RTGS is deliberately excluded and not planned for any later
version either; the UI never offers it.

---

Version 26 — Mobile Banking Simulator (Tomcat — mobile.digistack.cloud)
------------------------------------------------------------------------------
Objective: Build Mobile Banking as a small, standalone, neat-UI app on
Apache Tomcat, calling CBS exclusively via REST. See the Channel Simulator
Standard above for shared rules and request flow.

Banking Features Added: Mobile Login (delegates auth to CBS, same MFA/OTP
rules as Version 17), Balance Inquiry, Mini Statement, Fund Transfer (IMPS
only — mirrors real mobile banking's real-time bias), Quick Pay to Saved
Beneficiary.

Request Flow
Follows the shared Channel Simulator request flow (see standard above),
specifically:

Customer (Mobile Browser)
│
▼
mobile.digistack.cloud
│
▼
IHS Virtual Host Rule → Tomcat
│
▼
Mobile App (JSP/Servlet, Bootstrap 5)
│
REST calls only
▼
DigiStack CBS → digistack_cbs Database

Exception (payment submissions only, per v25): IMPS transfers and Quick Pay
go directly to Payment Hub's REST endpoint — payment routing is Payment
Hub's role. All account-data reads (auth, balance, statement, beneficiary
list) remain CBS-only. Mobile still never touches digistack_cbs directly.


Topics Covered (in addition to the shared Channel Simulator topics above):
API-first channel design (mobile as "just another CBS API consumer"),
Channel-specific rate limiting / throttling at the IHS layer (optional
stretch goal).

Enterprise Learning: Multi-vendor application server estates in real
banking IT, Channel isolation and blast-radius reasoning (why a bank might
deliberately keep mobile off the same cluster as core Internet Banking).

Sprint Deliverable: mobile.digistack.cloud resolves through the IHS/LB tier
and routes to Tomcat (not the WAS plugin); a customer can log in (CBS auth,
MFA intact), check balance, view mini statement, and complete an
IMPS Quick Pay — all data reads via REST calls to CBS, the IMPS submission
via Payment Hub's endpoint (v25 payment-routing exception), zero direct
database access from the Tomcat app, clean Bootstrap 5 UI.


---

Version 27 — ATM Simulator (Tomcat — atm.digistack.cloud)
------------------------------------------------------------
Objective: Build the ATM channel as a small, standalone, neat-UI app on
Apache Tomcat, simulating physical ATM interactions while calling CBS
exclusively via REST/SOAP.

Reference UI Flow
==========================
DIGISTACK ATM SIMULATOR
Balance Inquiry
Cash Withdrawal
Mini Statement
Change PIN
Exit

Banking Features Added: Card/PIN Entry (simulated card swipe → card number
+ PIN form), Balance Inquiry, Cash Withdrawal, Mini Statement, PIN Change.

Request Flow

ATM User (Browser simulating ATM screen)
│
▼
atm.digistack.cloud
│
▼
IHS Virtual Host Rule → Tomcat
│
▼
ATM Simulator App (JSP/Servlet, Bootstrap 5 "ATM screen" UI)
│
REST/SOAP calls only
▼
DigiStack CBS
│
Check PIN → Check Balance → Debit → Update Ledger
│
▼
digistack_cbs Database
│
▼
Return Success / Dispense Confirmation (simulated)


Topics Covered (in addition to the shared Channel Simulator topics above):
Connection Pools sized for high-frequency, low-latency ATM-style calls,
Thread Pool tuning for synchronous request patterns, High Availability
considerations specific to always-on ATM traffic.

Enterprise Learning: ATM Switch concepts, Card Authorization flow, External
Banking Integration patterns.

Sprint Deliverable: ATM Simulator UI (styled to resemble a real ATM screen)
performs Balance Inquiry, Cash Withdrawal, Mini Statement, and PIN Change
against CBS, entirely through REST/SOAP; PIN validation and a
blocked/incorrect-PIN negative case are both provable.

Scope note (per the "ATM & POS Integration {Minimal}" instruction): Stays
intentionally minimal — a functional web-based simulator proving the
request flow and relevant WebSphere admin topics, not a full ISO 8583
switch implementation. POS is treated as sharing this same simulator's
transaction path, distinguished by transaction type (purchase vs.
withdrawal) rather than as a separate app — kept out of scope as its own
build to avoid over-expanding this already-growing Part.

---

Version 28 — Card Portal (WebSphere — card.digistack.cloud)
------------------------------------------------------------------
Objective: Build the Card Management channel as a small, standalone,
neat-UI application, deployed as its own WebSphere EAR (not Tomcat — see
"Why Card Portal Belongs on WAS" above), giving customers (or bank staff,
depending on how you want to frame it) a portal for card lifecycle
operations — calling CBS exclusively via REST/SOAP.

Deployment Model
Card Portal is deployed as its own EAR (digistack-cardportal-v28.ear) on the
existing WAS ND cluster, fronted by IHS via a virtual host rule for
card.digistack.cloud that routes to the WAS plugin (not Tomcat, unlike
Mobile and ATM). It remains presentation-only: zero direct database
access, all card operations invoked through CBS's Card Service. Per the
Governing Rule, Card Portal never writes to digistack_cbs directly.

Banking Features Added: Issue Card, Activate Card, Block Card, Generate
PIN, Reset PIN, Card Status Lookup, Hotlisting.

Request Flow

Customer / Staff
│
▼
card.digistack.cloud
│
▼
IHS Virtual Host Rule → WAS Plugin
│
▼
Card Portal (own EAR, JSP/Servlet, Bootstrap 5)
│
REST/SOAP calls only
▼
DigiStack CBS (Card Service)
│
▼
digistack_cbs Database


Topics Covered: Deploying and administering a sixth independent WAS EAR
alongside Portal, CBS, Payment Hub, Notification Service, and Reporting
Service (Branch Portal follows at v29, becoming the seventh); Virtual host routing distinguishing Card
Portal's plugin-routed traffic from Mobile/ATM's Tomcat-routed traffic, on
the same IHS instance; Service-to-service call from Card Portal into CBS's
Card Service specifically (as opposed to CBS's core Account/Transaction
services) — a good example of routing to the correct internal service, not
just "CBS" as a monolith; IBM MQ / JMS touchpoint: card issuance triggers
an async "Card Issued" notification, published by CBS and consumed by
Notification Service (per the v23 satellite service split).

Enterprise Learning: Card Authorization, Card Lifecycle Management,
External Banking Integration.

Sprint Deliverable: A card can be issued, activated, blocked, and have its
PIN reset through the Card Portal UI; a blocked card correctly fails
authorization when tested against the ATM Simulator (Version 27) — proving
Card Portal (WAS), ATM Simulator (Tomcat), and CBS are properly integrated
across the heterogeneous topology, not siloed.

Dashboard UI Note (added 2026-08-24)
--------------------------------------------------------
The Dashboard's "Your Cards" section (placeholder since P01 v3, shown as
"Coming soon — v28" per P01 v2's UI convention) activates here as a
summary tile: masked card number + status, with a "Manage Card"
action that redirects the customer from the Internet Banking Portal's
Dashboard to card.digistack.cloud (Card Portal, its own WAS EAR). This is
a navigation hand-off, not an embedded iframe/API call from the Portal —
the Portal shows only the minimal card summary (via REST/SOAP read from CBS's Card Service, per this Part's Governing Rule — Card Portal is a presentation, not a data provider), full card management
(activate/block/PIN reset/hotlist) happens on Card Portal itself after
redirect.

Scope note: Card expiry/renewal is a deliberate, documented omission —
consistent with the "minimal" instruction for this area, not an oversight.

---

Version 29 — Enterprise Banking Operations
--------------------------------------------------
Objective: Implement day-to-day banking operations performed by branch
staff and operations teams.

Deployment Model
Branch Portal is a separate WebSphere application
(digistack-branch-v29.ear) deployed on the existing ND cluster and communicates only with CBS services — it does not access
digistack_cbs directly, following the same presentation-only rule as the
Internet Banking Portal, Card Portal, and the two Tomcat simulators.

Banking Features Added: Teller Login, Cash Deposit, Cash Withdrawal,
Beginning of Day (BOD), End of Day (EOD), EOD Reconciliation Report
(NEFT/IMPS settlement tie-out against CBS ledger).

Request Flow

Branch Teller
│
▼
Branch Portal (digistack-branch-v29.ear)
│
▼
Operations Service (within CBS)
│
▼
CBS
│
▼
Batch Service
│
▼
Reporting Service (v23) — EOD Reconciliation Report


Topics Covered: Batch Scheduling, JVM Monitoring, Thread Pools, JMS Batch
Queue, Log Analysis, Performance Tuning, Production Troubleshooting.

Enterprise Learning: Branch Banking, Batch Processing, EOD/BOD Operations,
Production Support, Banking Operations.

Sprint Deliverable: A Teller can log in to the Branch Portal and process
cash deposits/withdrawals against CBS; a scheduled BOD job opens the
banking day (e.g., interest accrual prep, NEFT batch window opens) and an
EOD job closes it — including a reconciliation report, generated by
Reporting Service, that ties out the day's NEFT/IMPS settlements (Version
25) against CBS's own ledger, surfacing any mismatch — both BOD/EOD run as
WAS-scheduled batch jobs, not manually triggered code.

Note: This is where "Reconciliation," originally only mentioned under
Version 25's Enterprise Learning with no actual feature, gets a concrete
implementation — EOD is when real banks reconcile, so it belongs here
rather than as a separate version.

Dashboard/Admin UI Note — Admin Portal Merged Into Branch Portal
(added 2026-08-25)
-------------------------------------------------------------------
An earlier UI mockup proposed a separate internal "Admin Portal" (System
Overview tiles for WAS Node/DB status, an Application Status table,
Customers/Accounts/Transactions/Alerts/Reports/Audit Logs/Configuration
menu, Recent Admin Events feed). That concept is merged into Branch
Portal rather than built as a distinct application — Branch Portal is
already the roadmap's one staff-facing, non-customer portal, and adding
a second, overlapping internal portal would duplicate deployment/routing
work without a new WebSphere topic to justify it. Consequences of the
merge:
- Branch Portal's Teller Login screen gains an "Operations" side menu
  alongside Teller functions, but only for the features this roadmap
  actually scopes: Cash Deposit/Withdrawal, BOD/EOD, EOD Reconciliation
  Report (all above), plus Unlock User (new, below).
- The System Overview-style live infrastructure view (WAS node status,
  app status per server, JVM/session/queue/DB pool health) is NOT
  rebuilt here — that's already P02 v18's Operations Dashboard (PMI/JMX),
  which stays its own screen; Branch Portal does not duplicate it.
- A general Customers/Accounts/Transactions/Reports/Configuration admin
  menu remains out of scope. The Audit Log UI is also out of scope — but
  note the distinction: the audit LOG itself exists and is live since
  P02 v17 (immutable `audit_log` table, INSERT-only, all balance-
  affecting operations). What is out of scope is only a UI to VIEW it —
  queried directly via SQL when needed. P01 v6's exclusion was about the
  UI, and this version keeps exactly that boundary.
  Only the specific operations named above (Teller cash ops, BOD/EOD,
  reconciliation, Unlock User) exist in Branch Portal.
- App/server naming from the mockup ("DigiBank-Web/API/Payments" on
  "server1/2/3") doesn't map to this project's real deployables and is
  dropped — the real units are the 7 WAS EARs + 2 Tomcat apps listed in
  this Part's architecture diagram.

Dashboard/Admin UI Note — Unlock User (added 2026-08-25)
-------------------------------------------------------------------
Unlock User — flagged as "Coming soon — v29" on the Login screen since
P01 v2 — is implemented here as a Teller-performed operation inside
Branch Portal's new Operations menu (above), not as customer
self-service. A Teller looks up a locked account (locked per P02 v17's
lockout-after-N-attempts rule) and clears the lock; the customer's own
Login screen never gains a working Unlock User control.

---

Version 30 — Loan Management
------------------------------------
Objective: Introduce lending as a core CBS product line — the module
referenced in P02's Capstone feature list but never actually built until
now. Closes the realism gap in an otherwise deposit/payments-only
simulation.

Banking Features Added
Loan Origination: Loan Application, Eligibility Check (simulated
credit/income rules), Loan Approval / Rejection, Loan Disbursement (credits
the linked CBS account)
Loan Servicing: EMI Schedule Generation, EMI Auto-Debit (scheduled), Loan
Statement, Foreclosure / Prepayment, Overdue / NPA Flagging (simulated)
Products: Personal Loan, Home Loan (simplified)

Request Flow

Customer
│
▼
Internet Banking
│
▼
Loan Service (within CBS)
│
Eligibility Check
│
▼
CBS (Account Service)
│
Disbursement
│
▼
CBS Database
│
▼
EMI Scheduler (EJB Timer / Batch)
│
▼
Auto-Debit on Due Date


Topics Covered: EJB Timer Service (EMI due-date scheduling), Batch Interest
Accrual, Long-Running Transaction Patterns, Scheduled Task Administration
in WAS, Transaction Isolation for Concurrent EMI Processing.

Enterprise Learning: Lending Domain Model, EMI Amortization Logic, Credit
Risk Simulation (simplified), Batch-Driven Financial Processing, NPA/
Overdue Handling Basics.

Sprint Deliverable: A customer can apply for a Personal Loan, pass a
simulated eligibility check, get approved, and have the loan amount
disbursed straight into their CBS savings account; an EMI schedule is
generated and at least one EMI auto-debits on its due date via EJB Timer
Service, correctly reducing the outstanding principal.

Why this version exists: Loan Management was referenced in the P02
Capstone (v22) feature list as something the platform "combines," but no
version anywhere actually built it. Given lending is core to any real bank
and adds genuinely new WebSphere topics (EJB Timer Service, batch accrual)
not exercised elsewhere in the roadmap, it's added here to complete this
Part.

Note on this module's home: Loan Management is added here, inside CBS,
consistent with the Version 23 decision that CBS stays a single application
throughout this roadmap (see that version's "Architectural Decision" note).
If a future Part ever explores decomposing CBS, Loans — with its genuinely
distinct lifecycle and regulatory profile versus core Deposits/Accounts —
is the most natural first candidate to split out; that's flagged there as
an option, not committed to here.

Dashboard UI Note (added 2026-08-24)
--------------------------------------
The Dashboard gets a new "Loans" section here — once a customer has an
active loan (origination through disbursement, this version), it appears
under this section showing loan type, outstanding principal, and next EMI
due date, sourced read-only from CBS's Loans module via REST/SOAP (same
Governing Rule as Cards — Portal never writes to digistack_cbs directly).
If the customer has no active loan, the section is omitted (not shown as
an empty placeholder), consistent with the Dashboard's existing
placeholder/omission discipline for not-yet-relevant sections.
---

Enterprise Architecture After This Part
--------------------------------------------
                           Customers
                               │
  ┌──────────────┬──────────────┬──────────────┬──────────────┐
  ▼              ▼              ▼              ▼

Internet Banking Mobile Banking ATM Sim Card Portal
(www.digistack.cloud)(mobile.digistack.cloud)(atm.digistack.cloud)(card.digistack.cloud)

                      Branch Employees
                               │
                               ▼
                        Branch Portal
                               │
  │              │              │              │              │
  │              ▼              ▼              │              │
  │         IBM HTTP Server / Enterprise LB                    │
  │      (single tier, virtual-host routed)                   │
  │              │              │              │              │
  │        ┌─────┴──────────────┴─────┐        │              │
  │        ▼                          ▼        │              │
  │   WAS Plugin                   Tomcat       │              │
  │   (→ cluster)              (Mobile, ATM)     │              │
  └──────────────┴──────────┬───────────────────┴──────────────┘
                             │ (all non-CBS apps call CBS via REST/SOAP/EJB only)
                             ▼
                 WebSphere ND Cluster (7 EARs)
                 │
                 ├── Internet Banking Portal
                 ├── CBS  ◄────────────────────────┐
                 ├── Payment Hub                   │
                 ├── Notification Service           │ (sole writer of
                 ├── Reporting Service               │  digistack_cbs)
                 ├── Branch Portal                   │
                 └── Card Portal                     │
                                                      │
  IBM MQ:  CBS → MQ → Notification Service ──────────┘
                → Payment Hub (external NEFT/IMPS leg)

                               │
                               ▼
                 DigiStack Core Banking System
                            (CBS)
  ┌────────────┬────────────┬────────────┬────────────┬────────────┐
  ▼            ▼            ▼            ▼            ▼
  CIF      Accounts    Transactions   Products       Loans
                               │
                               ▼
                        digistack_cbs Database
                (dedicated CBS database — CBS is the
                   ONLY application that writes here)

Total deployable applications by end of this Part: Internet Banking
Portal, CBS, Payment Hub, Notification Service, Reporting Service, Branch
Portal, Card Portal (7 WAS EARs) + Mobile Banking, ATM Simulator (2 Tomcat
apps) = 9 distinct deployable applications, all governed by a single rule
that Only CBS performs business-data writes to digistack_cbs.

---

Version 30.5 — Expert War-Game & Final Exam
------------------------------------------------
Objective: Close the roadmap with unscripted, timed failure drills across
the full 9-application estate, plus a from-bare-VMs final build. No new
banking features — this version validates and stress-tests everything
built across P01–P03.

Memory Leak Hunt: a deliberately leaky build (any one of the 9 apps) is
deployed; heap dumps captured over time and analyzed in Eclipse MAT to
identify the leaking class — reusing the V9.5 toolkit, now against a
multi-app, multi-cluster topology.

Outage War-Game (11 induced failures, timed, fix-and-document each):
expired cert; port conflict; DataSource down; corrupted config; dead node
agent; full log disk; stopped MQ channel; MQ queue full; hung thread;
wrong virtual host; in-doubt transaction requiring XA recovery (V8.5).
Each is broken deliberately across the real topology (not a toy app) and
resolved against the runbooks built in V19 and V22.

Core Groups / HA Manager: core group bridge configuration and DRS
(Data Replication Service) settings reviewed and tuned across the full
cluster topology carrying all 9 applications.

Final Exam — Build From Bare VMs (one day, fully scripted):
- WAS ND 2-node cluster
- IHS/NGINX front end with plugin routing (V20/V21)
- LDAP security + console role mapping (V10–V12)
- SSL/TLS end-to-end (V11/V12)
- Oracle XA DataSource (V22.5, V8.5)
- IBM MQ with the external payment leg live (V19)
- XA Transfer Test proof: kill server mid-2PC → recovery replay → zero
  lost/duplicated transactions (P01 v8.5)
- End-to-end message flow: Portal → WAS → JMS/MQ → CBS → reply
- JMeter capacity report with sizing justification (V14 methodology)

Sprint Deliverable: All 11 outage scenarios fixed and documented with
root-cause/fix/prevention writeups; leak identified and root-caused; the
one-day final build completed from bare VMs with every final-exam
checklist item verifiable live, not just documented.

Interview-anchor note: "Walk me through your worst production outage" is
answered directly from this version's 11 post-mortems plus the V8.5/T.8
war-game — this is the single version an interviewer's toughest scenario
question gets pulled from.

---

Completion Checklist
------------------------
□ Governing Rule enforced: only CBS writes to digistack_cbs; Payment Hub,
  Notification Service, Reporting Service, Branch Portal, Card Portal, and
  both Tomcat apps (Mobile, ATM) verified (via negative test) to have no
  direct write access
□ CBS is the sole system of record — Internet Banking Portal has zero
  direct DB access
□ CBS running on its own dedicated digistack_cbs database via a separate
  DataSource/connection pool
□ v22.5 data migration verified (row-count reconciliation between old
  shared DB and digistack_cbs); v23 Oracle-dialect DDL extension script
  (V23__migrate_existing_data_to_cbs.sql) applied; old Portal DataSource
  (jdbc/BankDS) decommissioned; final pg_dump archived; dsb-db VM shut
  down, snapshotted once, and deleted — no PostgreSQL anywhere in the estate
□ REST/SOAP endpoints (v16) confirmed relocated to CBS with unchanged
  contracts; SIBus/MDB/MQ (v15, v19) confirmed relocated to CBS
□ Notification Service and Reporting Service live as independent EARs,
  consuming CBS events/data only, with no direct database write access
□ CIF supports multiple accounts per customer, with Aadhaar/PAN
  verification gating creation
□ Payment Hub deployed as its own EAR; NEFT (batch) and IMPS (real-time)
  both functional, with retry/DLQ handling proven; Payment Hub confirmed
  never writing balances directly
□ mobile.digistack.cloud live on Tomcat, routed via IHS virtual host, calling
  CBS via REST only
□ atm.digistack.cloud live on Tomcat, ATM Simulator functional including a
  blocked/incorrect-PIN negative test
□ card.digistack.cloud live on WebSphere (own EAR, routed via IHS to the WAS
  plugin, not Tomcat), Card Portal functional; a card blocked here
  correctly fails at the ATM Simulator
□ Branch Portal deployed as its own EAR; Teller operations functional;
  BOD/EOD batch jobs run on a WAS schedule, including EOD reconciliation
  report generated by Reporting Service
□ Loan origination through disbursement functional; at least one EMI
  auto-debit proven via EJB Timer Service
□ All eight versions' TestCases-v23.md–v30.md signed off per Test Case
  Standards, including the new ownership/write-access negative tests
  introduced at v23
□ SetupDoc-v23.md's "Migration & Ownership Transfer" section completed,
  reviewed, and matches what was actually executed
□ Promoted Dev → UAT → Prod per Environment Promotion Standards,
  part3-release tag applied (all 9 applications — 7 WAS EARs + 2 Tomcat
  apps — promoted together, per environment)

Module Sufficiency Review — Resolved
-----------------------------------------
Versions 23–30 give nine distinct services/apps — seven independent
WebSphere EARs (Internet Banking Portal, CBS, Payment Hub, Notification
Service, Reporting Service, Branch Portal, Card Portal) plus two Tomcat
apps (Mobile Banking, ATM Simulator) — a genuinely heterogeneous topology
(WAS + Tomcat behind one IHS/LB tier), multiple queues (JMS + MQ), a
dedicated CBS database with a single-writer rule, and batch/scheduled
jobs. That heterogeneous routing and cross-server tracing work is
realistic, résumé-relevant WAS admin territory that a pure single-vendor
stack wouldn't have taught.

Decisions reflected in this version of the document:
1. Loan Management — Version 30. Closes the P02 Capstone reference gap.
2. Mobile Banking and ATM — rebuilt as separate small Tomcat apps under
   their own subdomains (Versions 26, 27).
3. Card Portal moved to WebSphere — Version 28 deploys Card Portal as its
   own WAS EAR (not Tomcat), reflecting that card lifecycle operations
   (issue, activate, block, PIN reset, hotlisting) are sensitive
   bank-owned enterprise functions, not an external customer channel like
   Mobile/ATM. This brings the WAS EAR count to seven and adds a
   deliberate architectural counterpoint to the Tomcat-split reasoning
   used for Mobile/ATM.
4. CBS database separation — CBS has its own dedicated digistack_cbs
   database and DataSource (Version 23), with an explicit one-time
   migration script and ownership matrix documenting the cutover from the
   shared P01/P02 database.
5. Service and messaging relocation — v16's REST/SOAP and v15/v19's SIBus/
   MDB/MQ explicitly relocate from Portal to CBS at v23, with existing
   contracts preserved.
6. Satellite services — Notification Service and Reporting Service become
   independent EARs at v23 (not folded into CBS), and Payment Hub becomes
   an independent EAR at v25 — all three governed by the single "only CBS
   writes" rule.
7. Branch Portal — explicitly scoped as its own separate WebSphere
   application at v29, presentation-only, no direct DB access.
8. Reconciliation — concrete feature inside Version 29's EOD, generated by
   Reporting Service.
9. Card expiry/renewal and POS as a separate app — both confirmed as
   deliberate, documented omissions to keep this Part from over-expanding
   further.
10. CBS stays a single application — an explicit, stated decision at
    Version 23, not a silent omission; Loan Management (v30) is flagged as
    the natural first candidate if CBS decomposition is ever explored in a
    future Part.
11. Payment Hub's coordination pattern named — Version 25 explicitly
    states this is a Saga/compensating-transaction pattern chosen to avoid
    a distributed XA transaction spanning independently deployed EARs, and
    why.

Application State After This Part
--------------------------------------
Total deployable applications: Internet Banking Portal, CBS, Payment Hub,
Notification Service, Reporting Service, Branch Portal, Card Portal (7 WAS
EARs) + Mobile Banking, ATM Simulator (2 Tomcat apps) = 9 distinct
deployable applications.

Governing Rule in force: only CBS writes to digistack_cbs — every other
application invokes CBS services or consumes CBS-published events.

Carried Forward to P04
---------------------------
CBS as system of record, Payment Hub, Notification Service, Reporting
Service, the two Tomcat-based channel simulators (Mobile/ATM), the
WAS-hosted Card Portal, Branch Portal, and Loan Servicing all become
subjects of observability instrumentation (APM, distributed tracing, chaos
testing) in P04. The heterogeneous WAS+Tomcat topology, combined with 9
independently deployed applications all communicating through CBS, is
especially valuable here — distributed tracing across this many services,
on two different application server products, is closer to real
enterprise observability work than a single-vendor, single-app stack
would be.