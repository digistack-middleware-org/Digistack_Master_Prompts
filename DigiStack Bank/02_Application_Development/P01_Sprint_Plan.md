# P01 — Foundation: Consolidated Sprint Plan (Versions 1–14)

**Part:** P01 — Foundation
**Status:** ⏳ Not Started
**Deployment Model:** ONE deployable EAR for this entire Part — `digistack-bank-vN.ear`. No Portal/CBS split (that happens in P03).

---

# Version 1 — Project Setup & Enterprise Architecture

## Version Overview
**Objective:** Stand up the first deployable EAR on a standalone WebSphere ND AppServer and prove basic PostgreSQL connectivity.
**Business Scope:** Static Home page + one live DB read (`app_config` table). No transactions, no login yet.
**WebSphere Focus:** EAR/WAR structure, deployment layout, context root, virtual host, first EAR deployment via Admin Console.
**Expected Outcome:** `digistack-bank-v1.ear` deployed to WAS, reachable via context root/virtual host, Home page renders and confirms a live PostgreSQL read.
**Prerequisites:** WebSphere ND = 9.0.5.28 (target/placeholder pin per STD, to be confirmed against the actual install during this Sprint), Rocky Linux 8.x VM (`dsb-dmgr`), PostgreSQL 13 installed, SOE01 Golden Image checklist passed.

### Sprint 1
**Goal:** Provision the base VM and validate the WebSphere ND install.
**Learning Objective:** EAR/WAR structure and profile creation.
**Business Features:** None (infra only).
**WebSphere Admin:** Create standalone AppServer profile; validate against SOE01 checklist; confirm Admin Console reachable (9060/9043).
**Dependencies:** SOE01 §1/§9.
**Deliverables:** Standalone profile created.
**Acceptance Criteria:** Admin Console loads; profile status = Started.
**Enterprise Outcome:** Baseline AppServer ready.

### Sprint 2
**Goal:** Scaffold the EAR/WAR project structure.
**Learning Objective:** EAR/WAR packaging, context root, virtual host.
**Business Features:** Static Home page (skeleton).
**App Dev:** UI: Home.jsp skeleton.
**WebSphere Admin:** Define context root (`/digistack-bank`); define virtual host mapping.
**Dependencies:** Sprint 1.
**Deliverables:** EAR/WAR skeleton in Git (`feature/v1-setup`).
**Acceptance Criteria:** Project builds locally into a valid EAR.
**Enterprise Outcome:** Deployment-ready packaging structure established.

### Sprint 3
**Goal:** Configure PostgreSQL connectivity (direct JDBC — JNDI comes in v7).
**Learning Objective:** Basic JDBC connectivity.
**Business Features:** `app_config` table read.
**App Dev:** Backend: simple servlet reading one row. DB: `app_config` table (`V1__create_app_config.sql`).
**WebSphere Admin:** Confirm PostgreSQL JDBC driver placed in WAS shared library path.
**Dependencies:** Sprint 2, PostgreSQL running.
**Deliverables:** Migration script; servlet class.
**Acceptance Criteria:** Servlet returns row value in server log.
**Enterprise Outcome:** DB connectivity path proven.

### Sprint 4
**Goal:** Wire Home page to display live DB read.
**Learning Objective:** Basic logging framework setup.
**Business Features:** Home page renders `app_config` value.
**App Dev:** UI: Home.jsp displays DB value. Backend: servlet → JSP forward.
**WebSphere Admin:** Configure SystemOut/SystemErr logging levels.
**Dependencies:** Sprint 3.
**Deliverables:** Working Home.jsp.
**Acceptance Criteria:** Browser hit shows live value, not hardcoded.
**Enterprise Outcome:** First end-to-end request path proven.

### Sprint 5
**Goal:** Package and deploy `digistack-bank-v1.ear` via Admin Console.
**Learning Objective:** First EAR Deployment via Admin Console.
**WebSphere Admin:** Deploy EAR via Admin Console (manual); verify status = Started.
**Dependencies:** Sprints 2–4.
**Deliverables:** `digistack-bank-v1.ear` deployed.
**Acceptance Criteria:** App reachable via context root/virtual host.
**Enterprise Outcome:** First real WAS deployment completed.

### Sprint 6
**Goal:** Smoke test, document, sign off Version 1.
**Learning Objective:** SetupDoc/TestCase discipline (SDD01/TCS01).
**WebSphere Admin:** Confirm app status; capture backupConfig baseline.
**Deliverables:** SetupDoc-v1.md, TestCases-v1.md.
**Acceptance Criteria:** All Critical/High test cases pass.
**Enterprise Outcome:** Version 1 signed off.

**Version 1 Deliverables:** `digistack-bank-v1.ear`, `V1__create_app_config.sql`, SetupDoc-v1.md, TestCases-v1.md.
**Exit Criteria (target, not yet verified):** Home + DB read functional; DB validated; Deployment successful; Smoke passed.
**Lessons Learned:** EAR/WAR/context-root/virtual-host mechanics; direct JDBC deliberately precedes JNDI (deferred to v7).
**Technical Debt:** Direct JDBC (no pooling) — resolved at Version 7.


---

# Version 2 — Login & Session

## Version Overview
**Objective:** Introduce authentication and HTTP session management, observing JVM/application startup behavior via redeploy over v1.
**Business Scope:** `users` table (username, password_hash). Login/Logout only. "Last login" session-proof content.
**WebSphere Focus:** JVM Startup, Application Startup, Session Creation, Logs, EAR Redeploy (v2 over v1).
**Expected Outcome:** Login/logout works against PostgreSQL; session attribute set at login, read correctly on next request; v2 redeployed cleanly over v1.
**Prerequisites:** P01 v1 signed off.

### Sprint 1
**Goal:** Design and create the `users` table.
**App Dev:** DB: `users` table (`V2__create_users.sql`).
**WebSphere Admin:** Confirm migration runs cleanly against existing v1 DB.
**Deliverables:** `V2__create_users.sql`.
**Acceptance Criteria:** Table created, seed user inserted.

### Sprint 2
**Goal:** Build the Login servlet and password validation logic.
**Business Features:** Login (username + password_hash check).
**App Dev:** UI: Login.jsp. Backend: LoginServlet.
**Deliverables:** Login.jsp, LoginServlet.
**Acceptance Criteria:** Valid credentials succeed; invalid rejected with error.

### Sprint 3
**Goal:** Implement HTTP session creation on successful login.
**Business Features:** Session established post-login.
**App Dev:** Backend: LoginServlet sets `HttpSession` attribute (`lastLogin`).
**WebSphere Admin:** Review default session timeout config (tuning deferred to v9).
**Acceptance Criteria:** Session attribute present immediately after login.

### Sprint 4
**Goal:** Display "Last login" and implement Logout.
**Business Features:** "Last login: <timestamp>" display; Logout.
**App Dev:** UI: landing page shows `lastLogin`; Logout link. Backend: LogoutServlet invalidates session.
**Acceptance Criteria:** Last-login renders correctly; logout clears session, redirects to Login.

### Sprint 5
**Goal:** Package and redeploy `digistack-bank-v2.ear` over running v1.
**Learning Objective:** EAR Redeploy mechanics, JVM/Application startup during redeploy.
**WebSphere Admin:** Redeploy v2 over v1 (same context root/virtual host); observe/record startup log sequence.
**Acceptance Criteria:** Clean redeploy, zero manual profile changes; startup logs captured.

### Sprint 6
**Goal:** Smoke test, document, sign off Version 2.
**WebSphere Admin:** Confirm app status post-redeploy; capture backupConfig baseline (v2).
**Deliverables:** SetupDoc-v2.md, TestCases-v2.md.
**Acceptance Criteria:** Critical/High pass; v1 regression passes.

**Version 2 Deliverables:** `digistack-bank-v2.ear`, `V2__create_users.sql`, SetupDoc-v2.md, TestCases-v2.md.
**Exit Criteria (target, not yet verified):** Home + DB read functional; DB validated; Deployment successful; Smoke passed.
**Lessons Learned:** HTTP session lifecycle; redeploy-over-running-app mechanics.
**Technical Debt:** App-layer auth only (no WAS security roles) — deferred to Version 10.

---

# Version 3 — Basic Transaction (Deposit & Withdraw)

## Version Overview
**Objective:** Implement the first real business transaction, exercising Controller → Service → DAO → DB layering and ClassLoader basics.
**Business Scope:** `accounts` table (account_id, balance). Balance/Deposit/Withdraw. No Customer, Beneficiary, Transfer, History yet.
**WebSphere Focus:** Enterprise Application Layering, ClassLoader Basics, Application Packaging, EAR Redeploy (v3).
**Expected Outcome:** Balance/Deposit/Withdraw work end-to-end, deployed as v3, layering explained class-by-class.
**Prerequisites:** P01 v2 signed off.

### Sprint 1
**Goal:** Design and create the `accounts` table.
**App Dev:** DB: `accounts` table (`V3__create_accounts.sql`).
**Deliverables:** `V3__create_accounts.sql`.
**Acceptance Criteria:** Table created; seed account linked to seed user with starting balance.

### Sprint 2
**Goal:** Build the DAO layer for account balance operations.
**Learning Objective:** DAO pattern.
**App Dev:** Backend: `AccountDao` (getBalance, updateBalance).
**Acceptance Criteria:** DAO methods verified against seed account.

### Sprint 3
**Goal:** Build the Service layer with Deposit/Withdraw business rules.
**Business Features:** Deposit, Withdraw (withdrawal cannot exceed balance).
**App Dev:** Backend: `AccountService` (deposit(), withdraw()).
**Acceptance Criteria:** Deposit increases balance; withdraw decreases; over-withdrawal rejected.

### Sprint 4
**Goal:** Build Controller and UI (Balance view + Deposit/Withdraw form).
**Business Features:** View Balance; submit Deposit/Withdraw.
**App Dev:** UI: Account.jsp. Backend: `AccountController` servlet.
**Acceptance Criteria:** Balance displays; Deposit/Withdraw submit and redisplay updated balance.
**Enterprise Outcome:** First full 4-layer enterprise request path proven.

### Sprint 5
**Goal:** Package/redeploy `digistack-bank-v3.ear`; walk through ClassLoader behavior.
**Learning Objective:** ClassLoader Basics.
**WebSphere Admin:** Redeploy v3 over v2; review/document ClassLoader policy.
**Acceptance Criteria:** Clean redeploy; ClassLoader policy documented with concrete example.

### Sprint 6
**Goal:** Smoke test, document, sign off Version 3.
**WebSphere Admin:** Confirm app status; capture backupConfig baseline (v3).
**Deliverables:** SetupDoc-v3.md, TestCases-v3.md.
**Acceptance Criteria:** Critical/High pass; v1/v2 regression passes.

**Version 3 Deliverables:** `digistack-bank-v3.ear`, `V3__create_accounts.sql`, SetupDoc-v3.md, TestCases-v3.md.
**Exit Criteria (target, not yet verified):** Home + DB read functional; DB validated; Deployment successful; Smoke passed.
**Lessons Learned:** Controller→Service→DAO→DB layering; ClassLoader behavior.
**Technical Debt:** No concurrency/locking on balance updates — acceptable pre-clustering; revisited at v5 and P05 v38.

---

# Version 4 — EAR Update, Rollback & Application Lifecycle

## Version Overview
**Objective:** Practice update/rollback/lifecycle workflow reused by every later version — 100% admin-practice, zero new functionality.
**Business Scope:** Trivial visible UI change ("v4" label) to observe redeploy taking effect.
**WebSphere Focus:** Update Application, Application Lifecycle (start/stop/restart), Rollback, Deployment Targets.
**Cross-reference:** This version's EAR rollback discipline (Update Application → Rollback) is the application-layer counterpart to STD's DB-side rollback convention (`/db/rollback/`, per STD §SQL Migration) — no DB rollback is exercised here since v4 introduces no schema change, but the same "always test rollback" Golden Rule applies to both paths going forward.
**Expected Outcome:** v4 deployed; real rollback to v3 performed and verified; v4 redeployed — proving the reusable workflow.
**Prerequisites:** P01 v3 signed off.

### Sprint 1
**Goal:** Add the trivial version-label change to Home page.
**App Dev:** UI: Home.jsp — add "v4" label.
**Acceptance Criteria:** Label builds cleanly, no other files touched.

### Sprint 2
**Goal:** Package v4 EAR; study Application Lifecycle states.
**Learning Objective:** Application Lifecycle — Start/Stop/Restart.
**WebSphere Admin:** Stop running v3 app; restart without redeploying; confirm state transitions in logs.
**Deliverables:** Packaged `digistack-bank-v4.ear`; lifecycle state notes.

### Sprint 3
**Goal:** Deploy v4 over v3 using Update Application.
**Learning Objective:** Update Application mechanics.
**WebSphere Admin:** Use Admin Console's "Update" (not Uninstall+Install); confirm Deployment Targets unchanged.
**Acceptance Criteria:** Browser shows "v4" label; targets unchanged from v3.

### Sprint 4
**Goal:** Perform a deliberate rollback to v3.
**Learning Objective:** Real Rollback procedure.
**WebSphere Admin:** Roll back to previously packaged `digistack-bank-v3.ear`; verify old state visible.
**Acceptance Criteria:** Browser confirms v3 state restored; no data loss.

### Sprint 5
**Goal:** Redeploy v4 again, closing the loop.
**WebSphere Admin:** Redeploy v4 (second time); confirm status = Started, label = "v4".
**Acceptance Criteria:** Browser shows "v4"; no manual config changes needed.

### Sprint 6
**Goal:** Smoke test, document, sign off Version 4.
**WebSphere Admin:** Confirm app status; capture backupConfig baseline (v4).
**Deliverables:** SetupDoc-v4.md, TestCases-v4.md (rollback/update cases only).
**Acceptance Criteria:** Rollback test passes; v1–v3 regression passes.

**Version 4 Deliverables:** `digistack-bank-v4.ear`, SetupDoc-v4.md, TestCases-v4.md (no schema change).
**Exit Criteria (target, not yet verified):** Home + DB read functional; DB validated; Deployment successful; Smoke passed.
**Lessons Learned:** Update Application vs. Uninstall+Install; rollback is real and testable.
**Technical Debt:** None introduced.

---

# Version 5 — WAS Clustering

## Version Overview
**Objective:** Stand up a 2-member WebSphere cluster and prove horizontal scaling, session replication, and failover.
**Business Scope:** Zero new functionality — reuse v3's Deposit/Withdraw and v2's session/login as test subjects.
**WebSphere Focus:** DMgr Profile Creation & Node Federation (operational only — deep dive is v6), Cluster Creation, Horizontal/Vertical Scaling, Plugin Routing prep, Failover, Session Replication, Cluster Members.
**Expected Outcome:** 2-member cluster runs v5; killing one member mid-session proves session and transaction survive via replication/failover.
**Prerequisites:** P01 v4 signed off.
**Note:** A cluster needs a DMgr + federated node(s) — cell-level construct. Bare setup here; v6 is the deep dive.
**VM Note (per SOE01 §1a):** Node2 is powered on for this Version only (clustering/failover topics) — not left running alongside every other VM. A typical session here (DMgr+Node1, Node2, IHS, PostgreSQL) totals ~7–8 GB RAM / ~4 vCPU, within this host's realistic budget.

### Sprint 1
**Goal:** Create the DMgr profile.
**WebSphere Admin:** Create DMgr profile on `dsb-dmgr`; start DMgr; confirm Admin Console.
**Acceptance Criteria:** DMgr Admin Console reachable; cell name visible.

### Sprint 2
**Goal:** Federate the existing node into the DMgr's cell.
**Learning Objective:** Node federation.
**WebSphere Admin:** Federate `dsb-node01`; confirm "Synchronized" status.
**Acceptance Criteria:** Node status = Synchronized.

### Sprint 3
**Goal:** Create a 2-member WebSphere cluster.
**WebSphere Admin:** Create `AppCluster` with 2 members; start both, confirm "Started".
**Acceptance Criteria:** Both members reachable individually.

### Sprint 4
**Goal:** Deploy `digistack-bank-v5.ear` (unchanged app) to the cluster.
**WebSphere Admin:** Deploy to both members; note Plugin Routing prep (full IHS is v8).
**Acceptance Criteria:** App reachable via both members' ports.

### Sprint 5
**Goal:** Prove session replication and failover.
**WebSphere Admin:** Login + Deposit on Member 1; kill Member 1 mid-session; confirm session/balance survive on Member 2.
**Acceptance Criteria:** Session attribute intact after failover; no duplicate/lost transaction.
**Enterprise Outcome:** Core HA guarantee proven for the first time.

### Sprint 6
**Goal:** Smoke test, document, sign off Version 5.
**WebSphere Admin:** Confirm cluster member status; capture backupConfig baseline (v5, cell-level).
**Deliverables:** SetupDoc-v5.md, TestCases-v5.md (includes failover test).
**Acceptance Criteria:** Failover test passes; v1–v4 regression passes on cluster.

**Version 5 Deliverables:** `digistack-bank-v5.ear`, SetupDoc-v5.md, TestCases-v5.md (no schema change).
**Exit Criteria (target, not yet verified):** Home + DB read functional; DB validated; Deployment successful; Smoke passed.
**Lessons Learned:** DMgr/federation is a clustering prerequisite; session replication protects in-flight state.
**Technical Debt:** Federation/wsadmin at "bare operational" depth — full deep dive deferred to v6.

---

# Version 6 — Application Administration

## Version Overview
**Objective:** Deep-dive DMgr/federation plumbing from v5; introduce wsadmin scripting alongside Freeze/Unfreeze.
**Business Scope:** One admin action — Freeze/Unfreeze an account (blocks Deposit/Withdraw when frozen). No dashboard/approval workflow/audit UI.
**WebSphere Focus:** DMgr, Node Federation/Synchronization, wsadmin Scripting, Application/Server Lifecycle Management.
**Expected Outcome:** DMgr manages federated nodes; Freeze/Unfreeze toggled via app and verified; at least one action performed via wsadmin script.
**Prerequisites:** P01 v5 signed off.

### Sprint 1
**Goal:** Deep-dive Node Synchronization mechanics.
**Learning Objective:** Node Sync vs. Full Resynchronization.
**WebSphere Admin:** Trigger manual Node Sync; deliberately drift node repo, then Full Resync to correct.
**Acceptance Criteria:** Drift detected and corrected; repos confirmed in agreement.

### Sprint 2
**Goal:** Add the `is_frozen` flag to `accounts`.
**App Dev:** DB: `accounts.is_frozen` column (`V4__add_frozen_flag.sql`).
**Acceptance Criteria:** Column added; defaults unfrozen.

### Sprint 3
**Goal:** Build Freeze/Unfreeze logic; gate Deposit/Withdraw against it.
**Business Features:** Freeze/Unfreeze; Deposit/Withdraw blocked when frozen.
**App Dev:** UI: Freeze/Unfreeze toggle. Backend: `AccountService.freeze()`/`unfreeze()`.
**Acceptance Criteria:** Frozen account rejects Deposit/Withdraw; unfreeze restores operation.

### Sprint 4
**Goal:** Write a wsadmin script to perform Freeze/Unfreeze outside the UI.
**Learning Objective:** wsadmin (Jython) scripting fundamentals.
**WebSphere Admin:** Write and run wsadmin script against the live cluster on the seed account.
**Acceptance Criteria:** Script successfully freezes/unfreezes, verified via UI.

### Sprint 5
**Goal:** Package/deploy `digistack-bank-v6.ear` to the cluster.
**Learning Objective:** Application/Server Lifecycle Management on a clustered deployment.
**WebSphere Admin:** Deploy v6 to both members; confirm synchronized status.
**Acceptance Criteria:** Both members serve v6 identically, no drift.

### Sprint 6
**Goal:** Smoke test, document, sign off Version 6.
**WebSphere Admin:** Confirm both members' status; capture backupConfig baseline (v6).
**Deliverables:** SetupDoc-v6.md, TestCases-v6.md (includes wsadmin-scripted test).
**Acceptance Criteria:** Freeze/Unfreeze (UI + wsadmin) pass; v1–v5 regression passes on cluster.

**Version 6 Deliverables:** `digistack-bank-v6.ear`, `V4__add_frozen_flag.sql`, SetupDoc-v6.md, TestCases-v6.md, wsadmin script.
**Exit Criteria (target, not yet verified):** Home + DB read functional; DB validated; Deployment successful; Smoke passed.
**Lessons Learned:** Node Sync vs. Full Resync; wsadmin as a genuine operational path; admin features layer cleanly onto Service-layer code.
**Technical Debt:** Freeze/Unfreeze open to any logged-in user — deferred to Version 10 (role gating).

---

# Version 7 — WAS JDBC

## Version Overview
**Objective:** Migrate direct-JDBC (since v1) to a WAS-managed JDBC DataSource via JNDI, pooling, and JAAS auth alias.
**Business Scope:** Zero new functionality — all existing features now read/write via a JNDI-managed pool.
**WebSphere Focus:** JDBC Providers, DataSources, JNDI, Connection Pool, Validation, Transactions.
**Expected Outcome:** All existing features use the pooled JNDI DataSource; no hardcoded JDBC URL/credentials remain.
**Prerequisites:** P01 v6 signed off.

### Sprint 1
**Goal:** Create the JDBC Provider for PostgreSQL.
**WebSphere Admin:** Create PostgreSQL JDBC Provider at cell/node scope; confirm driver classpath on both nodes.
**Acceptance Criteria:** No classpath errors on either node.

### Sprint 2
**Goal:** Create the JNDI DataSource and JAAS Auth Alias.
**WebSphere Admin:** Create JAAS Auth Alias (credentials, no hardcoding); create `jdbc/BankDS` DataSource bound to it.
**Acceptance Criteria:** Test Connection succeeds via JAAS alias, no plaintext credentials.

### Sprint 3
**Goal:** Size the connection pool using CAP01 §4's lab-adjusted worked example.
**Learning Objective:** Connection Pool Sizing math, applied to this project's actual 2-member topology (not the generic 3-member enterprise illustration).
**WebSphere Admin:** Apply CAP01 §4's lab-adjusted formula: 2 members × 20-connection pool each = 40 connections required at peak, comfortably under `max_connections=100` on the 2 GB PostgreSQL VM (per SOE01 §1a). Configure pool min/max accordingly on `jdbc/BankDS`.
**Acceptance Criteria:** Configured pool size matches CAP01 §4's 40-connection figure; documented headroom against `max_connections=100`.

### Sprint 4
**Goal:** Migrate all DAO code from direct JDBC to JNDI lookup.
**App Dev:** Backend: `AccountDao`, LoginServlet, Freeze/Unfreeze logic all updated to use `jdbc/BankDS` via JNDI.
**Acceptance Criteria:** No `DriverManager`/hardcoded JDBC URL remains (grep-verified).
**Enterprise Outcome:** Direct-JDBC debt from v1 fully closed.

### Sprint 5
**Goal:** Package/deploy `digistack-bank-v7.ear`; validate transactional behavior.
**Learning Objective:** WAS-managed Transactions (commit/rollback via pooled connection).
**WebSphere Admin:** Deploy to cluster; trigger a deliberate failed Withdraw, confirm clean rollback.
**Acceptance Criteria:** All features function as v6; deliberate failure rolls back correctly.

### Sprint 6
**Goal:** Smoke test, document, sign off Version 7.
**WebSphere Admin:** Confirm pool metrics; capture backupConfig baseline (v7).
**Deliverables:** SetupDoc-v7.md, TestCases-v7.md (pool-exhaustion negative test, rollback test).
**Acceptance Criteria:** No hardcoded credentials (negative test); v1–v6 regression passes on pooled DS.

**Version 7 Deliverables:** `digistack-bank-v7.ear`, SetupDoc-v7.md, TestCases-v7.md, JDBC Provider/DataSource/JAAS Alias config.
**Exit Criteria (target, not yet verified):** Home + DB read functional; DB validated; Deployment successful; Smoke passed.
**Lessons Learned:** Pool sizing math prevents DB exhaustion at scale; JAAS Auth Alias is the correct credential-externalization point.
**Technical Debt:** None — closes prior debt.

---

# Version 8 — IBM HTTP Server (IHS)

## Version Overview
**Objective:** Install IHS as front door to the WAS cluster; generate/propagate plugin-cfg.xml; configure custom error pages.
**Business Scope:** One static asset (logo/CSS) served by IHS. Custom 404/500 error pages.
**WebSphere Focus:** IBM HTTP Server, Web Server Definition, Plugin Generation, Plugin Propagation, Reverse Proxy, Virtual Hosts, Custom Error Documents.
**Expected Outcome:** IHS installed; plugin-cfg.xml generated/propagated; static asset served by IHS; custom 404/500 served by IHS.
**Prerequisites:** P01 v7 signed off.

### Sprint 1
**Goal:** Install IBM HTTP Server on `dsb-ihs`.
**WebSphere Admin:** Install IHS; confirm default page on port 80.
**Acceptance Criteria:** Default IHS page reachable.

### Sprint 2
**Goal:** Define the Web Server in WAS Admin Console; generate plugin-cfg.xml.
**WebSphere Admin:** Define `dsb-ihs` as Web Server object; generate plugin-cfg.xml against cluster topology.
**Acceptance Criteria:** Plugin file lists both cluster members' endpoints.

### Sprint 3
**Goal:** Propagate plugin-cfg.xml to IHS; confirm reverse-proxy routing.
**WebSphere Admin:** Propagate plugin config; restart IHS; confirm reverse-proxy to cluster.
**Acceptance Criteria:** Hitting IHS port 80 serves the banking app.

### Sprint 4
**Goal:** Add and verify a static asset served directly by IHS.
**Business Features:** Static logo/CSS visible on Home/Login pages.
**App Dev:** UI: logo image + CSS.
**WebSphere Admin:** Place asset under IHS document root (not in EAR); confirm loads even with cluster stopped.
**Acceptance Criteria:** Asset loads with both cluster members stopped (proving IHS-served).

### Sprint 5
**Goal:** Configure custom 404 and 500 error pages at IHS layer.
**Business Features:** Branded error pages.
**App Dev:** UI: 404.html, 500.html.
**WebSphere Admin:** Configure IHS `ErrorDocument` directives; force a 500, confirm IHS page appears.
**Acceptance Criteria:** Broken URL → custom 404; forced outage → custom 500, both via IHS.

### Sprint 6
**Goal:** Package/deploy `digistack-bank-v8.ear`; smoke test; sign off Version 8.
**WebSphere Admin:** Deploy v8; confirm plugin routing/static assets/error pages end-to-end.
**Deliverables:** SetupDoc-v8.md, TestCases-v8.md (404/500 and static-asset-during-outage tests).
**Acceptance Criteria:** v1–v7 regression passes via IHS routing (not direct WAS ports); 404/500 tests pass.

**Version 8 Deliverables:** `digistack-bank-v8.ear`, SetupDoc-v8.md, TestCases-v8.md, Web Server definition/plugin-cfg.xml/ErrorDocument config.
**Exit Criteria (target, not yet verified):** Home + DB read functional; DB validated; Deployment successful; Smoke passed.
**Lessons Learned:** Plugin generation/propagation is explicit, not automatic; static content belongs at the web tier.
**Technical Debt:** Single IHS instance (no LB/HA yet) — enterprise load balancing is P02 v21.

---

# Version 9 — Session Management

## Version Overview
**Objective:** Harden session behavior across the cluster — sticky sessions, persistence/failover, memory-to-memory replication tuning.
**Business Scope:** Zero new functionality. Session Timeout (auto-logout after N idle minutes).
**WebSphere Focus:** HTTP Sessions, Sticky Sessions, Session Persistence, Session Failover, Memory-to-Memory Replication.
**Expected Outcome:** Session timeout enforced; session survives cluster member restart; sticky-session routing confirmed via logs.
**Prerequisites:** P01 v8 signed off.

### Sprint 1
**Goal:** Configure sticky session routing at the plugin layer.
**WebSphere Admin:** Confirm plugin `CloneID`-based affinity active; enable plugin request logging.
**Acceptance Criteria:** Plugin log shows a session's requests consistently routed to the same member.

### Sprint 2
**Goal:** Enable memory-to-memory session replication across the cluster.
**WebSphere Admin:** Enable replication domain for `AppCluster`; confirm both members registered as partners.
**Acceptance Criteria:** Admin Console shows both members actively replicating.

### Sprint 3
**Goal:** Implement Session Timeout (auto-logout after N minutes idle).
**Business Features:** Session Timeout.
**App Dev:** UI: "Session expired" redirect. Backend: configure timeout in `web.xml`/session config.
**Acceptance Criteria:** Session expires after configured idle period; redirected to Login with message.

### Sprint 4
**Goal:** Prove session failover via a cluster member restart.
**WebSphere Admin:** Log in, hold active session on Member 1; restart Member 1 (graceful, distinct from v5's kill test); confirm session survives on Member 2.
**Acceptance Criteria:** No forced re-login during restart window.

### Sprint 5
**Goal:** Package/deploy `digistack-bank-v9.ear`; tune replication behavior.
**WebSphere Admin:** Deploy v9; review/document replication tuning parameters at baseline setting.
**Acceptance Criteria:** App functions as v8; tuning documented with rationale.

### Sprint 6
**Goal:** Smoke test, document, sign off Version 9.
**WebSphere Admin:** Confirm replication domain health; capture backupConfig baseline (v9).
**Deliverables:** SetupDoc-v9.md, TestCases-v9.md (timeout, sticky-routing, restart-failover tests).
**Acceptance Criteria:** All three tests pass; v1–v8 regression passes on replicated cluster.

**Version 9 Deliverables:** `digistack-bank-v9.ear`, SetupDoc-v9.md, TestCases-v9.md, replication domain/session timeout config.
**Exit Criteria (target, not yet verified):** Home + DB read functional; DB validated; Deployment successful; Smoke passed.
**Lessons Learned:** Sticky sessions (routing) and replication (data protection) are distinct mechanisms.
**Technical Debt:** None introduced.

---

# Version 10 — Users & Groups

## Version Overview
**Objective:** Introduce administrative security — real user registry, roles, groups — closing v6's Freeze/Unfreeze open-access debt.
**Business Scope:** Zero new functionality beyond role enforcement. Freeze/Unfreeze gated to Administrator; Customer role limited to Deposit/Withdraw.
**WebSphere Focus:** Administrative Security, File Registry (or LDAP), Users, Groups, Roles, Authorization.
**Expected Outcome:** File-based registry configured; Customer/Administrator roles defined; Freeze/Unfreeze unreachable by Customer role.
**Prerequisites:** P01 v9 signed off.
**Clarification:** Only Customer and Administrator roles are built anywhere in this roadmap. Branch Operator/Auditor are not built.

### Sprint 1
**Goal:** Configure a file-based user registry in WAS.
**WebSphere Admin:** Enable Administrative Security (Global Security); configure File-based Federated Repository.
**Acceptance Criteria:** DMgr Admin Console requires login; registry test succeeds.

### Sprint 2
**Goal:** Define Customer and Administrator groups; assign test users.
**WebSphere Admin:** Create `Customer`/`Administrator` groups; assign seed users to each.
**Acceptance Criteria:** Both users authenticate against the new registry.

### Sprint 3
**Goal:** Define security roles and map them to groups.
**App Dev:** Backend: declare `Customer`/`Administrator` roles in `web.xml`.
**WebSphere Admin:** Map roles → groups via Admin Console.
**Acceptance Criteria:** Role mapping visible and correct.

### Sprint 4
**Goal:** Gate Freeze/Unfreeze behind the Administrator role.
**Business Features:** Freeze/Unfreeze restricted to Administrator.
**App Dev:** Backend: `<security-constraint>` in `web.xml`.
**Acceptance Criteria:** Customer-role direct URL access to Freeze/Unfreeze rejected (403).

### Sprint 5
**Goal:** Package/deploy `digistack-bank-v10.ear`; validate both role paths.
**WebSphere Admin:** Deploy v10; log in as each role and confirm access boundaries.
**Acceptance Criteria:** Customer can Deposit/Withdraw but not Freeze/Unfreeze; Administrator can do both.

### Sprint 6
**Goal:** Smoke test, document, sign off Version 10.
**WebSphere Admin:** Confirm Global Security status; capture backupConfig baseline (v10).
**Deliverables:** SetupDoc-v10.md, TestCases-v10.md (Customer-denied negative test).
**Acceptance Criteria:** Negative test passes; v1–v9 regression passes with security enabled.

**Version 10 Deliverables:** `digistack-bank-v10.ear`, SetupDoc-v10.md, TestCases-v10.md, File Registry/groups/role mapping/web.xml constraints.
**Exit Criteria (target, not yet verified):** Home + DB read functional; DB validated; Deployment successful; Smoke passed.
**Lessons Learned:** Container-managed security constraints enforce authorization independent of UI.
**Technical Debt:** File registry only — LDAP federation is a deliberate future step (P06 v42).

---

# Version 11 — SSL (HTTPS at the Web Tier)

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
**Goal:** Smoke test, document, sign off Version 11.
**WebSphere Admin:** Confirm HTTPS/redirect status; capture backupConfig baseline (v11).
**Deliverables:** SetupDoc-v11.md, TestCases-v11.md (redirect + mixed-content tests).
**Acceptance Criteria:** New tests pass; v1–v10 regression passes over HTTPS.

**Version 11 Deliverables:** `digistack-bank-v11.ear`, SetupDoc-v11.md, TestCases-v11.md, IHS SSL config.
**Exit Criteria (target, not yet verified):** Home + DB read functional; DB validated; Deployment successful; Smoke passed.
**Lessons Learned:** Web-tier SSL is distinct from end-to-end SSL (deferred to v12).
**Technical Debt:** Self-signed cert only; internal hops beyond IHS unencrypted until v12.

---

# Version 12 — WAS SSL Configuration (End-to-End)

## Version Overview
**Objective:** Extend v11's SSL to the full hop chain (IHS↔plugin↔AppServer↔DB) with mTLS on ≥1 internal hop.
**Business Scope:** Zero new functionality.
**WebSphere Focus:** SSL Repertoires, NodeDefaultSSLSettings, CellDefaultSSLSettings, Mutual SSL (mTLS), Plugin SSL, Certificate Renewal, SSL Troubleshooting.
**Expected Outcome:** SSL end-to-end; mTLS on ≥1 internal hop; cert expiry/renewal process documented and tested.
**Prerequisites:** P01 v11 signed off.

### Sprint 1
**Goal:** Configure the IHS plugin↔AppServer SSL hop.
**WebSphere Admin:** Configure plugin SSL settings for HTTPS transport; regenerate/propagate plugin-cfg.xml.
**Acceptance Criteria:** Plugin logs confirm HTTPS used for IHS→AppServer hop.

### Sprint 2
**Goal:** Configure SSL Repertoires and Cell/Node Default SSL Settings.
**WebSphere Admin:** Configure `NodeDefaultSSLSettings`/`CellDefaultSSLSettings`; create dedicated SSL Repertoire for internal traffic.
**Acceptance Criteria:** Repertoire correctly referenced by both members.

### Sprint 3
**Goal:** Enable mutual TLS (mTLS) on the AppServer↔DB hop.
**WebSphere Admin:** Configure PostgreSQL to require client cert auth; configure `jdbc/BankDS` to present a client cert.
**Acceptance Criteria:** Connection succeeds only with correct cert; wrong/missing cert rejected.

### Sprint 4
**Goal:** Validate the full end-to-end SSL chain; re-test all features.
**WebSphere Admin:** Trace request end-to-end confirming SSL/mTLS at every hop; deliberately break one hop, diagnose via logs.
**Acceptance Criteria:** All features function over full SSL chain; deliberate break correctly diagnosed.

### Sprint 5
**Goal:** Package/deploy `digistack-bank-v12.ear`; document/test certificate renewal.
**WebSphere Admin:** Deploy v12; perform deliberate cert renewal (internal-hop cert), confirm zero downtime.
**Acceptance Criteria:** Renewal completes with no interruption; CI01 updated (`digistack-mtls-internal-hop.crt`).

### Sprint 6
**Goal:** Smoke test, document, sign off Version 12.
**WebSphere Admin:** Confirm SSL status at every hop; capture backupConfig baseline (v12).
**Deliverables:** SetupDoc-v12.md, TestCases-v12.md (mTLS-rejection + cert-renewal tests).
**Acceptance Criteria:** New tests pass; v1–v11 regression passes over full SSL/mTLS chain.

**Version 12 Deliverables:** `digistack-bank-v12.ear`, SetupDoc-v12.md, TestCases-v12.md, plugin SSL/SSL Repertoires/mTLS config.
**Exit Criteria (target, not yet verified):** Home + DB read functional; DB validated; Deployment successful; Smoke passed.
**Lessons Learned:** SSL Repertoires provide explicit, reusable scoping; mTLS requires client authentication too.
**Technical Debt:** Only one internal hop carries mTLS, per NFR matrix's "≥1 internal hop" requirement — intentional scope.

---

# Version 13 — Notifications (JavaMail / JNDI Mail Session)

## Version Overview
**Objective:** Configure JavaMail via JNDI Mail Session; trigger a real email on Withdraw.
**Business Scope:** One trigger point — successful Withdraw sends one email. No SMS/push/multi-channel matrix.
**WebSphere Focus:** JavaMail, SMTP Configuration, Resource Environment Entries, JNDI Mail Session, External Resource Configuration, Logging/Troubleshooting Mail Delivery.
**Expected Outcome:** Mail Session configured; Withdraw triggers real delivered email; delivery failure visible in logs when deliberately misconfigured.
**Prerequisites:** P01 v12 signed off.
**Note:** Fund Transfer doesn't exist yet (deferred to P02) — Withdraw is the trigger point here.

### Sprint 1
**Goal:** Configure an SMTP resource and JNDI Mail Session.
**WebSphere Admin:** Configure Mail Provider/SMTP host; create `mail/BankMailSession`. SMTP credentials externalized the same way as v7's JAAS Auth Alias — never hardcoded in config or code, per STD's Golden Rule and DBS01 §4.2's Connection & Credentials Standard.
**Acceptance Criteria:** Test Connection confirms SMTP reachability; no plaintext SMTP credential found anywhere in config (grep-verified, same discipline as v7's negative test).

### Sprint 2
**Goal:** Configure a Resource Environment Entry for sender/recipient defaults.
**WebSphere Admin:** Create Resource Environment Entry for sender address (`noreply@digistack.cloud`).
**Acceptance Criteria:** Value resolves correctly via JNDI lookup.

### Sprint 3
**Goal:** Build email-sending logic; wire it to successful Withdraw.
**Business Features:** Withdraw triggers confirmation email.
**App Dev:** Backend: `NotificationService.sendWithdrawEmail()` via JavaMail/JNDI, called from `AccountService.withdraw()`.
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
**Goal:** Smoke test, document, sign off Version 13.
**WebSphere Admin:** Confirm Mail Session status; capture backupConfig baseline (v13).
**Deliverables:** SetupDoc-v13.md, TestCases-v13.md (successful-delivery + misconfigured-failure tests).
**Acceptance Criteria:** Both tests pass; v1–v12 regression passes with SMTP correctly configured.

**Version 13 Deliverables:** `digistack-bank-v13.ear`, SetupDoc-v13.md, TestCases-v13.md, SMTP Mail Provider/JNDI Mail Session/Resource Environment Entry.
**Exit Criteria (target, not yet verified):** Home + DB read functional; DB validated; Deployment successful; Smoke passed.
**Lessons Learned:** JNDI Mail Session mirrors the DataSource externalization pattern; breaking a working integration is the fastest way to learn its failure mode.
**Technical Debt:** Single channel (email only) — SMS/push explicitly out of scope for this Part.

---

# Version 14 — Reports & JVM Heap Tuning

## Version Overview
**Objective:** Generate a large Transaction Report to stress the JVM heap; tune and prove improvement via PMI/GC monitoring.
**Business Scope:** One Transaction Report — multi-thousand synthetic rows, PDF/CSV dump. No Daily/Customer/Audit/Login variants.
**WebSphere Focus:** JVM Heap Management, Heap Sizing, Large Report Generation, Thread Pool Tuning, Performance Monitoring, GC, Memory Analysis.
**Expected Outcome:** Report generates without OOM on a large dataset; JVM heap tuned, improvement verified via before/after PMI/GC logs.
**Prerequisites:** P01 v13 signed off.
**Note:** Final version of P01 — completion triggers consolidation.

### Sprint 1
**Goal:** Generate synthetic transaction data at scale.
**App Dev:** DB: bulk-insert multi-thousand synthetic transaction rows.
**Acceptance Criteria:** Multi-thousand rows confirmed present, documented row count/rationale.

### Sprint 2
**Goal:** Build the Transaction Report generator (unoptimized first pass).
**Business Features:** Transaction Report (PDF/CSV dump).
**App Dev:** UI: "Generate Report" button. Backend: `ReportService` (naive, loads all rows into memory).
**Acceptance Criteria:** Report generates on a small dataset.

### Sprint 3
**Goal:** Run the report against the full dataset; capture OOM/heap-pressure behavior.
**WebSphere Admin:** Run at default heap settings; capture GC logs and PMI metrics (deliberately allowing OOM as the baseline).
**Acceptance Criteria:** Heap pressure/OOM clearly captured — the "before" evidence.

### Sprint 4
**Goal:** Tune JVM heap sizing per CAP01 §2.1's lab-adjusted table.
**WebSphere Admin:** Apply CAP01 §2.1's lab-adjusted values (Xms=Xmx, fixed heap): Node1/App member (combined DMgr+Node1 VM) = 1 GB within its 3 GB VM; Node2/CBS-equivalent member = 1.25 GB within its 2 GB VM. Re-run report, capture GC/PMI logs again.
**Acceptance Criteria:** Report completes with no OOM; GC behavior measurably improved vs. Sprint 3 baseline.

### Sprint 5
**Goal:** Package/deploy `digistack-bank-v14.ear`; tune thread pools for report load.
**WebSphere Admin:** Deploy v14; review Web Container thread pool sizing under report load, adjust if needed.
**Acceptance Criteria:** Report generation no longer degrades concurrent Login/Deposit/Withdraw responsiveness.

### Sprint 6
**Goal:** Smoke test, document, sign off Version 14 — completing P01.
**WebSphere Admin:** Confirm final heap/thread pool settings; capture backupConfig baseline (v14, final P01 baseline).
**Deliverables:** SetupDoc-v14.md, TestCases-v14.md (large-dataset report test with before/after GC comparison).
**Acceptance Criteria:** Report test passes with no OOM; v1–v13 regression passes; before/after GC improvement documented.

**Version 14 Deliverables:** `digistack-bank-v14.ear`, SetupDoc-v14.md, TestCases-v14.md, tuned JVM heap/thread pool config.
**Exit Criteria (target, not yet verified):** Home + DB read functional; DB validated; Deployment successful; Smoke passed.
**Lessons Learned:** Reproducing a real performance problem before tuning validates the fix against evidence, not a guess.
**Technical Debt:** None — P01 closes clean.

---

## P01 — Foundation: Planned Application State (once complete)

**Modules:** Home, Login/Logout, Balance, Deposit, Withdraw, Freeze/Unfreeze, one Transaction Report, one Withdraw email.

**Infrastructure:** DMGR, Node, Cluster, DataSource, JNDI, IHS (incl. custom 404/500), SSL (end-to-end, mTLS on one hop), Security (roles/registry), JVM (heap-tuned), Mail (JNDI Mail Session), Reports.

**Technical Debt Plan (introduced and resolved within P01, per schedule):**
| Debt Introduced At | Planned Resolution At | Item |
|---|---|---|
| v1 | v7 | Direct JDBC → JNDI DataSource/pooling |
| v6 | v10 | Freeze/Unfreeze open access → role-gated |
| v11 | v12 | Web-tier-only SSL → end-to-end SSL/mTLS |

**Planned Hand-off to P02:** Once complete, this same small app + cluster + IHS + SSL + security domain + DataSource + mail session becomes the foundation P02 will build JMS, Web Services, deeper security, monitoring, IBM MQ, and load balancing on top of — still one EAR until the CBS split in P03.

---

*This is the consolidated P01_Sprint_Plan.md — the planning document for P01, not a completion record. No versions have been built or signed off yet. This file is the single source of truth for P01's plan going forward; actual progress is tracked in Progress_Log.md.*

*Correction (2026-07-28 cross-file audit): Version 1's Prerequisites line previously listed "WebSphere ND = 9.0.5.28 installed (per STD)" — this matched a since-reverted STD version-pin edit that had prematurely claimed an actual install before any Sprint work existed. Updated to reference STD's then-current placeholder pin (9.0.3, unconfirmed), consistent with Sprint 1's own goal of provisioning and validating that install for the first time.*

*Second update (2026-07-28, same day, later): the project owner has now explicitly chosen 9.0.5.28 as the intended target package (STD v1.6, SOE01 v1.5, same day) ahead of Sprint 1 — a deliberate forward-looking decision, distinct from the earlier premature edit above (which falsely implied an install had already happened). The value above is updated to match, still explicitly marked "target/placeholder... to be confirmed during this Sprint" — Sprint 1's job is unchanged: provision the VM and validate the real install against this target.*
