ID: P01
Version: 1.0
Status: Active

Title: Foundation

Imports:
IDX
CONTEXT_PACK
ARCH01


Exports:
Versions 1-4, 4.5, 5-14 (15 versions total; v4.5 = Basic IHS Standalone Era)
First EAR deployment
Login/session
Basic transactions
Clustering
Application administration
JDBC
IHS
Session management
Users/groups/security
SSL end-to-end
Notifications
Reports/JVM tuning

Used By:
P02
P03
P04

Next:
P02

Used By Note (added 2026-08-25)
--------------------------------
P04 added to Used By: P04 v31 formally retires P02 v18's dashboard,
whose underlying JVM/heap data traces back to this Part's v14 (JVM Heap
Tuning) and v7 (Connection Pool Sizing) work; P04 v33 also names this
Part's v14 thread-pool tuning topic explicitly when introducing
WebSphere's distinct thread pools. Both are direct content
dependencies on this Part, not just indirect ones through P02/P03.

---

Purpose
-------
Practice WebSphere ND administration fundamentals. The WebSphere topic is the
deliverable of each version — the banking feature is only the minimum vehicle
needed to exercise it. No banking realism, extra fields, screens, or "nice to
have" modules beyond what a topic requires.

Deployment Model
------------------
ONE deployable EAR for this entire Part: digistack-bank-vN.ear.
No Portal/CBS split yet (that happens in P03).
Small codebase: a few servlets/JSPs, one or two DAOs, one or two tables —
growing only when a topic requires it.

Continuity Rule
------------------
Each version reuses the same tiny app and either:
(a) adds the smallest possible new screen/field a topic needs, or
(b) touches zero new banking functionality — pure infra/admin exercise.

---
Part-Start Architecture Diagram (generate first, before Version 1 work begins)
--------------------------------------------------------------------------------
Per 01_Architecture/README.md's Version-Start Diagram Check, generated once
at the start of this Part — pruned to only the diagram files P01's versions
(v1-v14) actually populate or extend. Files outside this tree stay
untouched until a later Part's own start-of-Part diagram unlocks them.

                 DIGISTACK BANK — P01 (v1-v14)
                       |
       +---------------+---------------+
       |               |               |
       v               v               v
   NETWORK           SERVERS        DEPLOYMENT
       |               |               |
 01_Network_       02_VM_          08_Deployment_
 Diagram.md         Layout.md       Architecture.md
 (v1 basic,        (v1 dsb-dmgr,   (v1 first EAR,
  v8 IHS,           v5 Node2,       extended every
  v11/v12 SSL)       v8 IHS)        packaging change)
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
 (v1 basic path,               (v5 cluster stood up,
  v7 JNDI, v8 IHS,               v6 DMgr/federation
  v12 mTLS)                      deep dive)
       |
       v
   APPLICATION
       |
       v
   PostgreSQL
       |
       v
 06_Database_ER_Diagram.md
 (v1 app_config, v2 users,
  v3 accounts, v6 is_frozen —
  redrawn every schema change)

   SECURITY
       |
       v
 07_Security_Architecture.md
 (v10 roles/registry,
  v11/v12 SSL/mTLS)

Not in scope this Part: 05_MQ_Architecture.md (MQ doesn't exist until P02
v19), 09_DR_Architecture.md (DR is P05). Both stay Not Populated for the
duration of P01.

Version 1 — Project Setup & Enterprise Architecture
------------------------------------------------------
WebSphere Topic: First EAR deployment to a standalone AppServer — EAR/WAR
structure, deployment layout, context root, virtual host.

Minimum App: Static Home page. One PostgreSQL connectivity test (single
table app_config, read on page load). Basic logging framework.

Topics Covered: Enterprise Architecture, EAR/WAR Structure, Deployment Layout,
Context Root, Virtual Host, First EAR Deployment via Admin Console.

Sprint Deliverable: digistack-bank-v1.ear deployed to WAS, reachable via
context root/virtual host, Home page renders and confirms a live PostgreSQL
read. Confirmed via Admin Console app status + browser hit.

Public Landing Page UI Note (added 2026-08-24)
------------------------------------------------
This version's "Static Home page" is the public/pre-login landing page —
hero banner, marketing copy, feature-teaser tiles (Accounts/Transfer/
Transactions, linking to Login — no live functionality), "Login" and
"Open an Account" buttons. The "Open an Account" button is rendered
disabled/"Coming soon" — self-service account opening is NOT scoped
anywhere in P01-P10 yet (see Progress_Log.md Open Questions,
2026-08-24 entry). A "Personal | Business" toggle in the header is
likewise disabled/"Coming soon" — Business banking is out of scope
project-wide (ARCH01 covers CIF/Accounts/Transactions/Products/Loans,
retail only; no corporate-banking module exists in this roadmap).
Both remain visually present but non-functional until explicitly scoped.

Version 2 — Login & Session
------------------------------
WebSphere Topic: JVM/Application startup behavior, HTTP session creation,
session-scoped logs.

Minimum App: users table (username, password_hash). Login + Logout only.
"Last login: <timestamp>" shown post-login as the only session-proof content.

Topics Covered: JVM Startup, Application Startup, Session Creation, Logs,
EAR Redeploy (v2 over v1, same context root/virtual host).

Sprint Deliverable: Login/logout works against PostgreSQL; session attribute
(last login) set at login, read correctly on next request; v2 redeployed
cleanly over v1.

Dashboard UI Note (added 2026-08-24)
--------------------------------------
"Last login: <timestamp>" — already the session-proof content this
version builds — is retrofitted onto Dashboard.jsp (P01 v3 Sprint 4) as
a small, persistent header element, not just a one-time post-login flash.
Shown on every Dashboard visit within the session, framed as a basic
security signal ("was this you?"), consistent with real banking UX.

Login Page UI Note — Login Field Label (added 2026-08-25)
-------------------------------------------------------------
The Login.jsp field is labeled "Username" from this version through P03
v23. At P03 v24 (CIF & Account Lifecycle), the customer_id already present
on the users row since P02 v15 is formalized as a foreign key into the
new CIF customer table — from that version onward
the login field label changes to "Customer ID" on Login.jsp, matching
the UI mockup. No backend auth change — just a label update on the JSP,
noted in SetupDoc-v24.md.

Dashboard UI Note — Greeting and Display Name (added 2026-08-25)
-------------------------------------------------------------------
The Dashboard header shows "Good Morning / Afternoon / Evening,
<name>" using the name column added to the users row at P02 v15
(where the existing users row gains customer_id and name). Before v15
the greeting reads "Good Morning, <username>" using the session's
username attribute — a one-line JSP fallback, no extra DB call. From
P02 v15 onward the name field is used instead. The time-of-day greeting
("Good Morning" / "Good Afternoon" / "Good Evening") is determined by
server-side time in the Servlet — no JS date logic needed.

Login Page UI Note — Forgot Password (added 2026-08-24)
-------------------------------------------------------------
The Login.jsp mockup includes a "Forgot Password?" link. This is rendered
disabled/"Coming soon" — no password-reset flow is scoped anywhere in
P01 v2 (Login) or v10 (Administrative Security). Deferred until
explicitly scoped (see Progress_Log.md Open Questions, 2026-08-24 entry).

Login Page UI Note — Unlock User (added 2026-08-25)
-------------------------------------------------------------
The Login.jsp mockup also includes an "Unlock User" link, alongside
"Forgot Password?". Rendered disabled/"Coming soon — v29" from this
version onward. Account lockout itself is introduced at P02 v17
(lockout after N failed attempts), but the unlock action is deliberately
not self-service — it's a Teller-performed operation, added at P03 v29
(Branch Portal / Enterprise Banking Operations) once that portal exists.
See P03 v29's UI note for the actual Unlock User feature.

Dashboard UI Note — "Coming Soon" Convention (added 2026-08-25)
-------------------------------------------------------------------
Standing convention for the whole roadmap: any Dashboard sidebar item or
tile whose backing feature isn't built yet is rendered visible but
disabled, labeled "Coming soon — vNN" (pointing at the version that
activates it), rather than omitted or rendered as if live. This extends
the same treatment already used for "Forgot Password?" above to every
placeholder tile introduced at P01 v3 (Cards, Payments/Transfers,
Statements) and applies to every later placeholder noted in P02/P03 (see
P02 v15/v16, P03 v28/v29 UI notes).

"Security" and "Profile" sidebar items are removed from the Dashboard
entirely — no customer-facing Security settings screen or Profile
management module is scoped anywhere in P01–P03, and no future Part has
been assigned these features. They do not appear as placeholders or
"Coming soon" items; they are simply absent from the sidebar.

Version 3 — Basic Transaction (Deposit & Withdraw)
------------------------------------------------------
WebSphere Topic: Enterprise application layering (Controller → Service →
DAO → DB), ClassLoader basics.

Minimum App: accounts table (account_id, balance). One screen: view Balance,
then Deposit or Withdraw, submit — balance updates and redisplays. No
Customer, Beneficiary, Transfer, or History modules yet.

Topics Covered: Enterprise Application Architecture, ClassLoader Basics,
Application Packaging, EAR Redeploy (v3).

Sprint Deliverable: Balance/Deposit/Withdraw work end-to-end through
Controller → Service → DAO → DB, deployed as v3, layering explained
class-by-class.

Dashboard UI Note (added 2026-08-24, applies at Sprint 4 retrofit)
-------------------------------------------------------------------
Per the project's standing Dashboard-first UI standard, this version's
Balance screen is delivered as `Dashboard.jsp` (not a bare `Account.jsp`),
with balance hidden by default behind a "View Balance" toggle (JS reveal,
no page reload) — matching a Kotak-style reference layout (structure only,
no reused account data). This Dashboard becomes the permanent Home page
shell for the rest of the roadmap; later Parts activate additional tiles
on it (see P02 v15/v16, P03 v28/v29 notes) rather than replacing it.
Sidebar placeholders introduced here (Cards, Payments/Transfers,
Statements) render as "Coming soon — vNN" per the convention in this
version's Login Page UI notes above, not as live/enabled nav items.

Dashboard UI Note — Quick Actions Row (added 2026-08-25)
-------------------------------------------------------------
A "Quick Actions" row sits below the account tile(s) on the Dashboard,
showing shortcut buttons. The row is present from v3 onward; individual
buttons activate exactly when their backing feature does:
- "Transfer Money" — disabled/"Coming soon — v15" until Fund Transfer
  exists (P02 v15), then live.
- "Download Statement" — disabled/"Coming soon — v16" until the SOAP
  Account Statement service exists (P02 v16), then live.
- "Pay Bill" — dropped entirely; not shown even as a placeholder
  (see Pay Bill note below).
No new backend work per this note — the row is a JSP layout element
whose buttons toggle enabled/disabled state as versions activate.

Dashboard UI Note — Pay Bill (dropped, added 2026-08-25)
-------------------------------------------------------------
An earlier UI mockup included a "Pay Bill" quick action on the Dashboard.
This is confirmed out of scope for the whole roadmap — no bill-payment
module (biller registry, biller payment processing) is defined anywhere
in P01, P02, or P03. Not included as a placeholder either; left out of
the Dashboard entirely rather than shown as "Coming soon."

Version 4 — EAR Update, Rollback & Application Lifecycle
-------------------------------------------------------------
WebSphere Topic: Update Application (redeploy over running app), Application
Lifecycle (start/stop/restart), Rollback.

Minimum App: Zero new functionality — trivial visible UI change (version
label "v4" on Home page) purely to observe redeploy taking effect.

Topics Covered: Update Application, Application Lifecycle, Deployment
Targets, Rollback (deliberate rollback to v3, confirm old label, redeploy v4).

Sprint Deliverable: v4 deployed; real rollback to v3 performed and verified
(old label visible), then v4 redeployed — proving update/rollback workflow
reused by every later version. 100% admin-practice sprint, no feature work.

Version 4.5 — Basic IHS Standalone Era
-----------------------------------------
(Suffix-slot version per the project's convention — does not renumber
v5-v14. Deliberately placed between v4 and v5: v5 stands up the DMgr +
cluster, and IHS is practiced once against the standalone AppServer
first, so the plugin's standalone-mode behavior is observed before
cluster-mode routing replaces it.)

WebSphere Topic: First IBM HTTP Server install against the standalone
AppServer — web server definition on a single non-federated node,
plugin-cfg.xml generation/propagation, basic reverse proxy. (The cluster-
scoped plugin behavior, custom error pages, and virtual-host refinement
are v8's territory; this is the standalone-era first pass.)

Minimum App: Zero new functionality — same v4 app. One static asset and
the existing Home page routed through IHS instead of hitting the
AppServer directly.

Topics Covered: IBM HTTP Server, Web Server Definition (standalone),
Plugin Generation, Plugin Propagation, Reverse Proxy (basic).

Sprint Deliverable: IHS installed and defined in the cell; plugin-cfg.xml
generated and propagated for the standalone AppServer; Home page and one
static asset confirmed served via IHS, with AppServer-direct access
compared against IHS-proxied access in the SetupDoc.

Continuity note: v8 supersedes this setup once the cluster exists — the
web server definition is re-pointed at the cluster, and v8's deliverable
(static asset proof + custom 404/500) builds on this foundation rather
than replacing it silently. Documented in SetupDoc-v4.5.md and revisited
in SetupDoc-v8.md.


Version 5 — WAS Clustering
------------------------------
Prerequisite Note: A cluster needs a DMgr + federated node(s) — cell-level
construct, not something a standalone AppServer profile can do alone. This
version includes minimum operational steps to stand up DMgr + federate the
node (bare setup, not deeply explained). Version 6 goes back over this same
foundation as its own dedicated topic (DMgr/federation internals, wsadmin).

WebSphere Topic: Cluster creation, horizontal scaling, session replication,
failover.

Minimum App: Zero new functionality — reuse v3's Deposit/Withdraw and v2's
session/login exactly as-is as test subjects.

Topics Covered: DMgr Profile Creation & Node Federation (operational only —
deep dive is v6), Cluster Creation, Horizontal/Vertical Scaling, Plugin
Routing (prep only — IHS itself is v8), Failover, Session Replication,
Cluster Members.

Sprint Deliverable: 2-member cluster runs v5 (same app as v4, unchanged);
logging in, then killing one cluster member mid-session, proves session and
a Deposit/Withdraw both survive via replication/failover.

Version 6 — Application Administration
-------------------------------------------
(Renamed from "WAS Administration" — DMgr/Node/wsadmin are genuine WebSphere
Admin topics, but Freeze/Unfreeze is an application-level admin action.)

WebSphere Topic: DMgr, node federation/synchronization, wsadmin scripting,
application/server lifecycle management. This is the deep-dive on the
DMgr/federation plumbing v5 had to stand up operationally.

Minimum App: One admin action — Freeze/Unfreeze an account (toggle a status
flag on accounts table, block Deposit/Withdraw when frozen). No full Admin
Dashboard, no Approval workflows, no Employee Management, no Audit Log UI.

Topics Covered: DMGR, Node Federation, Node Synchronization, JVM Management,
Application Management, Server Lifecycle, wsadmin.

Sprint Deliverable: DMgr manages federated nodes; Freeze/Unfreeze toggled via
app and verified to block/allow Deposit/Withdraw; at least one freeze/unfreeze
action performed via a wsadmin script instead of the UI.

Dashboard UI Note (added 2026-08-24)
--------------------------------------
When an account's `is_frozen` flag is true, the Dashboard shows a clear
banner ("Your account is frozen — contact support") instead of silently
rejecting Deposit/Withdraw with no explanation. Retrofitted onto
Dashboard.jsp; no new backend logic — reads the same `is_frozen` column
this version already introduces.

Version 7 — WAS JDBC
------------------------
WebSphere Topic: JDBC Providers, DataSources, JNDI, connection pooling,
transactions.

Minimum App: Zero new functionality — migrate the existing DB connection
(used since v1) to a proper WAS-managed JDBC DataSource via JNDI, connection
pool, JAAS auth alias.

Topics Covered: JDBC Providers, DataSources, JNDI, Connection Pool,
Validation, Transactions.

Connection Pool Sizing — Worked Example
------------------------------------------
Rule: (cluster members × max pool size per member) + admin/replication
headroom ≤ PostgreSQL max_connections.

VM Note (added 2026-08-25): PostgreSQL 16 runs on its own dedicated VM
(dsb-db) for the whole of P01. At P02 v22.5, Oracle 21c XE is installed on
a SEPARATE VM (dsb-oracle) — never on dsb-db — so both database engines
never share a host. dsb-db (PostgreSQL) is fully decommissioned and
deleted at P03 v23 Sprint 4 (see P03).

Generic enterprise example: 3 members × 50-connection pool each = 150
connections required at peak, against PostgreSQL default
max_connections=100 — exhausts the DB connection limit before any app
server's pool is full.

Lab-adjusted worked example (this project's actual topology, per
CAP01 §4 and P01_Sprint_Plan v7 Sprint 3): 2 cluster members ×
20-connection pool each = 40 connections at peak, comfortably under
max_connections=100 on the 2 GB dsb-db VM. Revisit any time cluster
membership changes; actual numbers recorded in SetupDoc-v7.md.


Sprint Deliverable: All existing features (login, deposit/withdraw, freeze)
read/write exclusively through a JNDI-looked-up, WAS-managed connection
pool — no hardcoded JDBC URL/credentials remain.

Version 8 — IBM HTTP Server (IHS)
--------------------------------------
WebSphere Topic: IHS install, web server definition, plugin-cfg.xml
generation/propagation, reverse proxy, virtual hosts.

Minimum App: One static asset (logo/CSS) to prove it's served by IHS, not
the AppServer. Custom error pages for 404 and 500 at the IHS layer.

Topics Covered: IBM HTTP Server, Web Server Definition, Plugin Generation,
Plugin Propagation, Reverse Proxy, Virtual Hosts, Custom Error Document
config (404, 500).

Sprint Deliverable: IHS installed as front door to WAS cluster; plugin-cfg.xml
generated/propagated; static asset confirmed served by IHS; broken URL
returns custom 404, forced server error returns custom 500 — both served by
IHS, not WAS default output.

Test Pipeline Note (added 2026-08-25)
---------------------------------------
From this version (v8) onward, every version's Sprint 6 additionally
executes the TP01_Test_Pipeline.md multi-environment pipeline
(DEV → SIT → UAT → PRE-PROD → PROD) and records results in the
"TP01 Pipeline Results" section of TestCases-v<N>.md. Versions v1–v7
are unaffected (pipeline applies from v8 onward).

Version 9 — Session Management
-----------------------------------
WebSphere Topic: Sticky sessions, session persistence/failover across the
cluster, memory-to-memory replication tuning.

Minimum App: Zero new functionality. Session Timeout (auto-logout after N
minutes idle) — smallest feature that lets you observe session replication/
timeout behavior across cluster members.

Topics Covered: HTTP Sessions, Sticky Sessions, Session Persistence, Session
Failover, Memory-to-Memory Replication.

Sprint Deliverable: Session timeout enforced correctly; session survives a
cluster member restart (memory-to-memory replication proven); sticky-session
routing confirmed via IHS/plugin logs.

Version 10 — Users & Groups
--------------------------------
WebSphere Topic: Administrative security, file registry (or LDAP), users,
groups, roles, authorization.

Minimum App: Zero new functionality. Gate v6's Freeze/Unfreeze behind a real
"Administrator" role instead of open to any logged-in user; regular users
get a "Customer" role limited to Deposit/Withdraw.

Topics Covered: Administrative Security, File Registry, LDAP, Users, Groups,
Roles, Authorization.

Sprint Deliverable: File-based (or LDAP) user registry configured; Customer
and Administrator roles/groups defined; Freeze/Unfreeze unreachable by a
Customer-role user, proving role enforcement (not just UI hiding).

Roles Actually Built (clarification)
-------------------------------------
Only two roles are built in this roadmap: Customer and Administrator
(this version). Auditor is not built anywhere in P01–P10. Branch Operator is not built
in P01–P02 — P03 v29's Branch Portal (Teller Login) introduces a Teller
role there. Until that version, only Customer and Administrator exist;
no role is assumed to already exist.

Version 11 — SSL (HTTPS at the Web Tier)
-----------------------------------------------
WebSphere Topic: SSL basics, certificates, keystore/truststore, HTTPS,
certificate chains.

Minimum App: Zero new functionality. All existing pages move to HTTPS.

Topics Covered: SSL Basics, Certificates, KeyStore, TrustStore, HTTPS,
Certificate Chains.

Sprint Deliverable: Self-signed certificate generated/imported; HTTPS
enforced on IHS for all existing pages; HTTP requests redirect to HTTPS.

Version 12 — WAS SSL Configuration (End-to-End)
-----------------------------------------------------
WebSphere Topic: SSL repertoires, NodeDefaultSSLSettings/
CellDefaultSSLSettings, mutual TLS, certificate renewal.

Minimum App: Zero new functionality. Extend v11's SSL to the full hop chain:
IHS↔plugin↔AppServer↔DB, mTLS on at least one internal hop.

Topics Covered: SSL Repertoires, NodeDefaultSSLSettings,
CellDefaultSSLSettings, Mutual SSL (mTLS), Plugin SSL, Certificate Renewal,
SSL Troubleshooting.

Sprint Deliverable: SSL enabled end-to-end (browser→IHS→plugin→AppServer→DB);
mTLS configured on at least one internal hop; cert expiry/renewal process
documented and tested once.

Version 13 — Notifications (JavaMail / JNDI Mail Session)
------------------------------------------------------------
WebSphere Topic: JavaMail, SMTP configuration, JNDI Mail Session, external
resource configuration.

Minimum App: One trigger point — a successful Withdraw sends one real email
via a WAS Mail Session. No SMS, no push, no OTP, no multi-channel matrix.

Note on Withdraw vs Fund Transfer: Fund Transfer doesn't exist yet in this
Part (deferred to P02's business-module buildout) — Withdraw is the
higher-risk/higher-value operation available now, so the trigger stays here.
Once Fund Transfer exists (P02), that's the natural point for a second,
more realistic transfer-notification email.

Topics Covered: JavaMail, SMTP Configuration, Resource Environment Entries,
JNDI Mail Session, External Resource Configuration, Logging/Troubleshooting
Mail Delivery.

Sprint Deliverable: WAS Mail Session configured via JNDI; a Withdraw
triggers a real email delivered through configured SMTP; delivery failure
visible in logs when deliberately misconfigured once, to prove
troubleshooting.

Dashboard UI Note (added 2026-08-24, activates fully at P02 v15)
------------------------------------------------------------------
A notification/alert bell icon is added to the Dashboard header here,
initially showing a count for Withdraw email events (this version). It's
a lightweight in-app list backed by the same trigger this version already
fires — not a new notification engine. Extended at P02 v15 to include
Fund Transfer confirmations once that feature exists.

Version 14 — Reports & JVM Heap Tuning
-------------------------------------------
WebSphere Topic: JVM heap management, large-object generation, thread pool
tuning, GC/PMI monitoring, OutOfMemory prevention.

Minimum App: One Transaction Report — dump all rows from the transaction log
(from v3's Deposit/Withdraw activity) as a large PDF/CSV, multi-thousand
synthetic rows, big enough to stress the heap. No Daily/Customer/Audit/Login
report variants.

Topics Covered: JVM Heap Management, Heap Sizing, Large Report Generation,
Thread Pool Tuning, Performance Monitoring, GC, Memory Analysis.

Sprint Deliverable: Report generates without OutOfMemoryError on a large
synthetic dataset; JVM heap tuned and improvement verified via PMI/GC logs
before/after.

---

Completion Checklist
------------------------
□ digistack-bank-v14.ear running as a single EAR on WAS ND (no split yet)
□ PostgreSQL connected via managed JNDI DataSource, no hardcoded credentials
□ 2-member cluster operational, session replication/failover tested
□ IHS installed, fronting cluster via plugin-cfg.xml
□ SSL/HTTPS end-to-end, mTLS on at least one hop
□ Administrative security enabled — Customer/Administrator roles enforced
□ DMgr + federated nodes operational, wsadmin fluency demonstrated
□ Email notification working via WAS Mail Session/JNDI
□ Large report generates under tuned JVM heap without OOM
□ App itself stayed intentionally tiny — every other topic practiced on
  infrastructure around this same small app

Application State After P01
-------------------------------
Modules: Home, Login/Logout, Balance, Deposit, Withdraw, Freeze/Unfreeze,
one Transaction Report, one Withdraw email.

Infrastructure: DMGR, Node, Cluster, DataSource, JNDI, IHS (incl. custom
404/500), SSL (end-to-end, mTLS on one hop), Security (roles/registry), JVM
(heap-tuned), Mail (JNDI Mail Session), Reports.

Carried Forward to P02
---------------------------
Same small app + cluster + IHS + SSL + security domain + DataSource + mail
session becomes the foundation P02 builds JMS, Web Services, deeper
security, monitoring, IBM MQ, and load balancing on top of — still one EAR
until the CBS split in P03.