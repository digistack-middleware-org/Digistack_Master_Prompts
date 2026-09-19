# P01 — Foundation: Consolidated Sprint Plan (Versions 1-4, 4.5, 5-8, 8.5, 9, 9.5, 10-14)

**Part:** P01 — Foundation
**Status:** ⏳ Not Started
**Deployment Model:** ONE deployable EAR for this entire Part — `digistack-bank-vN.ear`. No Portal/CBS split (that happens in P03).
**Sprint Structure:** 8 sprints per version — Sprint 1–4 Build, Sprint 5 Package and Deploy, Sprint 6 Test Cases, Sprint 7 Sign-off, Sprint 8 Fault Injection + Incident.

---

# Version 1 — Project Setup & Enterprise Architecture

## Version Overview
**Objective:** Stand up the first deployable EAR on a standalone WebSphere ND AppServer and prove basic PostgreSQL connectivity.
**Business Scope:** Static Home page + one live DB read (`app_config` table). No transactions, no login yet.
**WebSphere Focus:** EAR/WAR structure, deployment layout, context root, virtual host, first EAR deployment via Admin Console.
**Expected Outcome:** `digistack-bank-v1.ear` deployed to WAS, reachable via context root/virtual host, Home page renders and confirms a live PostgreSQL read.
**Prerequisites:** WebSphere ND = 9.0.5.28 (target/placeholder pin per STD, to be confirmed against the actual install during this Sprint), Rocky Linux 8.x VM (`dsb-dmgr`), PostgreSQL 16 (target pin per STD §Version Pins and SOE01 §9 — confirm actual installed version during this Sprint and promote pin to CONFIRMED in STD/SOE01 once verified), SOE01 Golden Image checklist passed.

### Sprint 1
**Goal:** Provision the base VM and validate the WebSphere ND install.
**Learning Objective:** EAR/WAR structure and profile creation.
**Business Features:** None (infra only).
**WebSphere Admin:** Create standalone AppServer profile `devdsbinappserver01` with cell name `devdsbincell01` and server name `server1` (per STD v1.10 naming convention); validate against SOE01 checklist; confirm Admin Console reachable (9060/9043).
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
**Goal:** Write and execute test cases for Version 1.
**Learning Objective:** Test Case discipline (TCS01/TCS02).
**WebSphere Admin:** Confirm app status for test execution.
**Deliverables:** TestCases-v1.md.
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7.
**Enterprise Outcome:** Version 1 test coverage complete.

### Sprint 7
**Goal:** Sign off Version 1.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Admin:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v1.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 1 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 1.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Admin:** Phase 1 — inject a realistic fault tied to this version's topic. Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v1.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 1 fault drill complete. Non-gating — does not block sign-off.

**Version 1 Deliverables:** `digistack-bank-v1.ear`, `V1__create_app_config.sql`, SetupDoc-v1.md, TestCases-v1.md, FaultDrill-v1.md.
**Exit Criteria (target, not yet verified):** Home + DB read functional; DB validated; Deployment successful; Smoke passed; Fault drill complete (Sprint 8, non-gating).
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
**Goal:** Write and execute test cases for Version 2.
**Learning Objective:** Test Case discipline (TCS01/TCS02).
**WebSphere Admin:** Confirm app status for test execution.
**Deliverables:** TestCases-v2.md.
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7.
**Enterprise Outcome:** Version 2 test coverage complete.

### Sprint 7
**Goal:** Sign off Version 2.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 2 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 2.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Admin:** Phase 1 — inject a realistic fault tied to this version's topic. Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v2.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 2 fault drill complete. Non-gating — does not block sign-off.

**Version 2 Deliverables:** `digistack-bank-v2.ear`, `V2__create_users.sql`, SetupDoc-v2.md, TestCases-v2.md, FaultDrill-v2.md.
**Exit Criteria (target, not yet verified):** Login and Logout functional against PostgreSQL; session created on successful login; lastLogin session attribute displayed correctly; v2 EAR redeployed cleanly over v1 using the same context root/virtual host; Smoke passed; Fault drill complete (Sprint 8, non-gating).
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
**App Dev:** UI: Dashboard.jsp (balance hidden by default behind a "View Balance" toggle per P01_Foundation.md v3 UI note). Backend: `AccountController` servlet.
**Acceptance Criteria:** Balance displays; Deposit/Withdraw submit and redisplay updated balance.
**Enterprise Outcome:** First full 4-layer enterprise request path proven.

### Sprint 5
**Goal:** Package/redeploy `digistack-bank-v3.ear`; walk through ClassLoader behavior.
**Learning Objective:** ClassLoader Basics.
**WebSphere Admin:** Redeploy v3 over v2; review/document ClassLoader policy.
**Acceptance Criteria:** Clean redeploy; ClassLoader policy documented with concrete example.

### Sprint 6
**Goal:** Write and execute test cases for Version 3.
**Learning Objective:** Test Case discipline (TCS01/TCS02).
**WebSphere Admin:** Confirm app status for test execution.
**Deliverables:** TestCases-v3.md.
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7.
**Enterprise Outcome:** Version 3 test coverage complete.

### Sprint 7
**Goal:** Sign off Version 3.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Admin:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v3.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 3 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 3.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Admin:** Phase 1 — inject a realistic fault tied to this version's topic. Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v3.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 3 fault drill complete. Non-gating — does not block sign-off.

**Version 3 Deliverables:** `digistack-bank-v3.ear`, `V3__create_accounts.sql`, SetupDoc-v3.md, TestCases-v3.md, FaultDrill-v3.md.
**Exit Criteria (target, not yet verified):** Balance Inquiry, Deposit and Withdraw functional end-to-end; accounts schema validated; Controller → Service → DAO → DB layering verified; v3 EAR deployed successfully; Smoke passed; Fault drill complete (Sprint 8, non-gating).
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
**Goal:** Write and execute test cases for Version 4.
**Learning Objective:** Test Case discipline (TCS01/TCS02).
**WebSphere Admin:** Confirm app status for test execution.
**Deliverables:** TestCases-v4.md.
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7.
**Enterprise Outcome:** Version 4 test coverage complete.

### Sprint 7
**Goal:** Sign off Version 4.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Admin:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v4.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 4 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 4.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Admin:** Phase 1 — inject a realistic fault tied to this version's topic. Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v4.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 4 fault drill complete. Non-gating — does not block sign-off.

**Version 4 Deliverables:** `digistack-bank-v4.ear`, SetupDoc-v4.md, TestCases-v4.md, FaultDrill-v4.md (no schema change).
**Exit Criteria (target, not yet verified):** v4 EAR deployed successfully; application lifecycle Start/Stop/Restart behavior verified; rollback from v4 to v3 completed successfully; v4 redeployed and verified; Smoke passed; Fault drill complete (Sprint 8, non-gating).
**Lessons Learned:** Update Application vs. Uninstall+Install; rollback is real and testable.
**Technical Debt:** None introduced.

---
# Version 4.5 — Basic IHS (Standalone Era)

## Version Overview
**Objective:** Introduce IBM HTTP Server as the front door to the standalone
AppServer — proving the Browser→IHS→WAS→DB flow before clustering exists.
This is the first of two IHS milestones: v4.5 wires IHS to the standalone
server; v8 migrates the same `dsb-ihs` VM to front the cluster.
**Business Scope:** Zero new functionality — existing Home page + DB read
(v1) is the test subject. No SSL yet (HTTP only), no custom error pages
(deferred to v8), no static assets (deferred to v8).
**WebSphere Focus:** IHS install, Web Server Definition creation, basic
plugin-cfg.xml generation and propagation against a Standalone AppServer,
reverse-proxy routing verification.
**Flow Proven:**
Browser (port 80)
│
▼
dsb-ihs — IBM HTTP Server
│ plugin-cfg.xml → dsb-dmgr:9080 (standalone server1)
│
▼
dsb-dmgr — Standalone WAS AppServer (server1)
│
▼
dsb-db — PostgreSQL (port 5432)
**Expected Outcome:** IHS installed on `dsb-ihs`; Web Server object defined
in WAS Admin Console; plugin-cfg.xml generated and propagated pointing at
the standalone AppServer; Home page loads via IHS port 80 and DB read
confirms the full path is live.
**Prerequisites:** P01 v4 signed off. `dsb-ihs` VM powered on (1 vCPU,
1 GB RAM, per SOE01 §1a).
**VM Note (per SOE01 §1a):** `dsb-ihs` is powered on from this version
onward and remains on for the rest of P01. A typical session here
(dsb-dmgr, dsb-ihs, dsb-db) totals ~6 GB RAM / ~5 vCPU — within this
host's budget.

### Sprint 1
**Goal:** Install IBM HTTP Server on the `dsb-ihs` VM.
**Learning Objective:** IHS install process; confirming the default IHS
page proves the web server binary is running before any WAS integration
is attempted.
**Business Features:** None (infra only).
**WebSphere Admin (GUI):** SSH into `dsb-ihs`; run IBM Installation Manager
to install IHS 9.0.5.28 (target, matches WAS ND pin per SOE01 §9);
start IHS (`./apachectl start`); confirm default IHS page reachable from
Windows host browser on `http://dsb-ihs-ip:80`.
**WebSphere Admin (wsadmin):** Not applicable for binary install — IHS
install is OS-level only. wsadmin steps begin at Sprint 2 (Web Server
Definition).
**Dependencies:** SOE01 §1a (dsb-ihs VM spec), SOE01 §9 (IHS version pin).
**Deliverables:** IHS installed and running on `dsb-ihs`.
**Acceptance Criteria:** Default IHS "It works!" page reachable on port 80
from Windows host browser. IHS process visible in `ps aux | grep httpd`.
**Enterprise Outcome:** Web tier VM operational — ready to be wired into
WAS.

### Sprint 2
**Goal:** Define the Web Server object in WAS Admin Console and via
wsadmin — both paths required per standing project rule.
**Learning Objective:** Web Server Definition — what it is (a WAS-side
object that represents IHS and tells WAS how to generate plugin config
for it), and why it must exist before a plugin can be generated.
**Business Features:** None.
**WebSphere Admin (GUI):**
Servers → Server Types → Web Servers → New.
  - Server name: `webserver1`
  - Host: `dsb-ihs` hostname/IP
  - Installation path: IHS install root (e.g. `/apps/IBM/HTTPServer`)
  - WAS install path: WAS install root on `dsb-dmgr`
  - Port: 80
Save → confirm `webserver1` listed with status Stopped (expected —
plugin not yet propagated).
**WebSphere Admin (wsadmin Jython):**
```python
# Connect to standalone AppServer (not DMgr — v5 is where DMgr arrives)
# Run from dsb-dmgr:
# /apps/IBM/WebSphere/AppServer/bin/wsadmin.sh -lang jython

import AdminConfig
cell = AdminConfig.list('Cell').split('\r\n')[0]
node = AdminConfig.list('Node').split('\r\n')[0]

# Create Web Server definition
AdminTask.createWebServer(
    node,
    ['-name', 'webserver1',
     '-templateName', 'IHS',
     '-serverConfig',
     ['-webPort', '80',
      '-webInstallRoot', '/apps/IBM/HTTPServer',
      '-webProtocol', 'HTTP',
      '-configurationFile', '',
      '-adminPort', '8008',
      '-adminUserID', '',
      '-adminPasswd', ''
     ],
     '-primaryServerConfig',
     ['-webHostName', 'dsb-ihs-hostname-or-ip']
    ]
)
AdminConfig.save()
print "Web Server definition created."
```
**Acceptance Criteria:** `webserver1` visible in Admin Console →
Web Servers list. wsadmin script completes without error.
**Enterprise Outcome:** WAS now knows IHS exists — prerequisite for plugin
generation.

### Sprint 3
**Goal:** Generate plugin-cfg.xml against the standalone AppServer —
both Admin Console and wsadmin paths.
**Learning Objective:** What plugin-cfg.xml actually is — a routing table
that tells IHS "for this URL pattern, forward to this WAS server on
this port." Generated by WAS, consumed by IHS. Without it, IHS cannot
forward any request to WAS.
**Business Features:** None.
**WebSphere Admin (GUI):**
Servers → Server Types → Web Servers → select `webserver1` →
Generate Plug-in.
Confirm success message: "Plugin configuration file generated."
**WebSphere Admin (wsadmin Jython):**
```python
# Generate plugin for webserver1
AdminTask.generatePluginCfg(
    ['-serverName', 'webserver1',
     '-nodeName', AdminConfig.list('Node').split('\r\n')[0],
     '-options', ''
    ]
)
print "plugin-cfg.xml generated."
```
**Acceptance Criteria:** `plugin-cfg.xml` file exists on `dsb-dmgr` under
the WAS profile's `config/cells/<cell>/nodes/<node>/servers/webserver1/`
directory. File contents reference `dsb-dmgr` hostname and port 9080
(standalone AppServer HTTP port).
**Enterprise Outcome:** Routing table created — IHS can now be told where
to send traffic.

### Sprint 4
**Goal:** Propagate plugin-cfg.xml to `dsb-ihs`; configure IHS to load
the plugin; restart IHS; verify reverse-proxy routing is active.
**Learning Objective:** Propagation — why the file generated on WAS
(dsb-dmgr) must be physically copied to IHS (dsb-ihs). They are different
VMs. Without propagation, IHS has no routing table and forwards nothing.
**Business Features:** None.
**WebSphere Admin (GUI):**
Servers → Server Types → Web Servers → select `webserver1` →
Propagate Plug-in.
Confirm success message: "Plugin configuration file propagated."
Then on `dsb-ihs`: confirm `plugin-cfg.xml` exists at the IHS plugin
install path (e.g. `/apps/IBM/HTTPServer/Plugins/config/webserver1/`).
**WebSphere Admin (wsadmin Jython):**
```python
AdminTask.propagatePluginCfg(
    ['-serverName', 'webserver1',
     '-nodeName', AdminConfig.list('Node').split('\r\n')[0]
    ]
)
print "plugin-cfg.xml propagated to dsb-ihs."
```
**IHS httpd.conf — add these two lines** (if not auto-added by IHS
plugin install):

LoadModule was_ap22_module /apps/IBM/HTTPServer/Plugins/bin/64bits/mod_was_ap22_http.so
WebSpherePluginConfig /apps/IBM/HTTPServer/Plugins/config/webserver1/plugin-cfg.xml

Restart IHS: `./apachectl restart`
**Acceptance Criteria:** IHS restarts without error. `dsb-ihs` IHS error
log shows plugin loaded: `[notice] mod_was_ap22_http: plugin loaded`.
**Enterprise Outcome:** IHS is now a live reverse proxy in front of WAS.

### Sprint 5
**Goal:** End-to-end flow verification — Browser→IHS→WAS→DB.
**Learning Objective:** Proving the full path works before signing off.
This is what a real WAS admin does after every IHS config change — hit
the app through IHS, not directly, and confirm the DB read is live.
**Business Features:** None — uses existing Home page + DB read from v1.
**WebSphere Admin (GUI):** From Windows host browser, navigate to
`http://dsb-ihs-ip:80/digistack-bank` (IHS port, not WAS port 9080).
Confirm: Home page renders AND the `app_config` value from PostgreSQL
is displayed (proving the full Browser→IHS→WAS→DB path is live).
Then confirm IHS access_log shows the request was received.
Then confirm WAS SystemOut.log shows the request was forwarded from IHS
(look for the `X-Forwarded-For` header or plugin-generated log entries).
**WebSphere Admin (wsadmin Jython):**
```python
# Check webserver1 status via wsadmin
import AdminControl
print AdminControl.getAttribute(
    AdminControl.queryNames('type=WebServer,name=webserver1,*'),
    'state'
)
```
**Acceptance Criteria:**
1. Home page loads via `http://dsb-ihs-ip:80/digistack-bank` — NOT via
   `dsb-dmgr:9080` (direct WAS port).
2. `app_config` value visible on page — proves DB read through the full
   chain.
3. IHS `access_log` shows the GET request.
4. WAS `SystemOut.log` shows the request handled.
5. Deliberately stop the IHS web server (`./apachectl stop`) — confirm
   browser gets a connection error (not a WAS error page), proving IHS
   is the real front door.
**Enterprise Outcome:** Browser→IHS→WAS→DB path proven end-to-end.

### Sprint 6
**Goal:** Write and execute test cases for Version 4.5.
**Learning Objective:** Test Case discipline (TCS01/TCS02).
**WebSphere Admin:** Confirm app status for test execution.
**Deliverables:** TestCases-v4.5.md.
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7.
**Enterprise Outcome:** Version 4.5 test coverage complete.

### Sprint 7
**Goal:** Sign off Version 4.5.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Admin:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v4.5.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 4.5 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 4.5.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Admin:** Phase 1 — inject a realistic fault tied to this version's topic. Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v4.5.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 4.5 fault drill complete. Non-gating — does not block sign-off.
**Version 4.5 Deliverables:** SetupDoc-v4.5.md, TestCases-v4.5.md, FaultDrill-v4.5.md,
`webserver1` Web Server Definition, plugin-cfg.xml (standalone era),
IHS httpd.conf with plugin directives.
**Exit Criteria (target, not yet verified):** Browser→IHS→WAS→DB path
proven; IHS log confirms traffic; regression passes; plugin debt logged; Fault drill complete (Sprint 8, non-gating).
**Lessons Learned:** plugin-cfg.xml is a routing table — generated on WAS,
consumed by IHS, must be physically propagated between two different VMs.
Regeneration (v8) is a normal, expected admin operation when topology
changes (standalone → cluster).
**Technical Debt:**
- plugin-cfg.xml points at standalone AppServer only — must be
  regenerated at v8 to point at cluster members.
- HTTP only (no SSL) — enforced at v11.
- No custom error pages — added at v8.
- No static assets at IHS layer — added at v8.

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
**WebSphere Admin:** Create `devdsbinappcluster01` with 2 members (per STD v1.10 naming convention); start both, confirm "Started".
**Acceptance Criteria:** Both members reachable individually.

### Sprint 4
**Goal:** Deploy `digistack-bank-v5.ear` (unchanged app) to the cluster.
**WebSphere Admin:** Deploy to both members; note Plugin Routing prep (full IHS is v8).
**Acceptance Criteria:** App reachable via both members' ports.

### Sprint 5
**Goal:** Prove session replication and failover.
**Learning Objective:** Memory-to-memory session replication and cluster failover.
**Acceptance Criteria:** Session attribute intact after failover; no duplicate/lost transaction; Deposit/Withdraw mid-failover survives (per P01_Foundation.md v5 Sprint Deliverable).
**Enterprise Outcome:** Core HA guarantee proven for the first time — kill one member mid-session, login state and a completed Deposit both survive via replication/failover.

### Sprint 6
**Goal:** Write and execute test cases for Version 5.
**Learning Objective:** Test Case discipline (TCS01/TCS02).
**WebSphere Admin:** Confirm app status for test execution.
**Deliverables:** TestCases-v5.md.
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7.
**Enterprise Outcome:** Version 5 test coverage complete.

### Sprint 7
**Goal:** Sign off Version 5.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Admin:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v5.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 5 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 5.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Admin:** Phase 1 — inject a realistic fault tied to this version's topic. Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v5.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 5 fault drill complete. Non-gating — does not block sign-off.

**Version 5 Deliverables:** `digistack-bank-v5.ear`, SetupDoc-v5.md, TestCases-v5.md, FaultDrill-v5.md (no schema change).
**Exit Criteria (target, not yet verified):** 2-member cluster runs v5; session replication + failover proven; Home + DB read functional as regression; Smoke passed; Fault drill complete (Sprint 8, non-gating).
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
**App Dev:** DB: `accounts.is_frozen` column (`V4__add_frozen_flag.sql`) — V4 in migration numbering because Version 4 introduced no schema change (see v4 "no schema change" deliverables).
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
**Goal:** Write and execute test cases for Version 6.
**Learning Objective:** Test Case discipline (TCS01/TCS02).
**WebSphere Admin:** Confirm app status for test execution.
**Deliverables:** TestCases-v6.md.
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7.
**Enterprise Outcome:** Version 6 test coverage complete.

### Sprint 7
**Goal:** Sign off Version 6.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Admin:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v6.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 6 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 6.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Admin:** Phase 1 — inject a realistic fault tied to this version's topic. Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v6.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 6 fault drill complete. Non-gating — does not block sign-off.

**Version 6 Deliverables:** `digistack-bank-v6.ear`, `V4__add_frozen_flag.sql`, SetupDoc-v6.md, TestCases-v6.md, FaultDrill-v6.md, wsadmin script.
**Exit Criteria (target, not yet verified):** DMgr/node federation and synchronization verified; deliberate repository drift corrected through Full Resynchronization; Freeze/Unfreeze functional and role-independent at this stage; at least one Freeze/Unfreeze action executed successfully through wsadmin; Smoke passed; Fault drill complete (Sprint 8, non-gating).
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
**WebSphere Admin:** Apply CAP01 §4's lab-adjusted formula: 2 members × 20-connection pool each = 40 connections required at peak, comfortably under `max_connections=100` on the 2 GB dsb-db VM (dedicated PostgreSQL 16 VM, per SOE01 §1a and P01_Foundation.md v7 VM Note — dsb-db hosts PostgreSQL alone for the whole of P01; Oracle 21c XE gets its own separate VM, dsb-oracle, at P02 v22.5 and never shares dsb-db; dsb-db is fully decommissioned at P03 v23 Sprint 4). Configure pool min/max accordingly on `jdbc/BankDS`.
**WebSphere Admin:** Also configure pool validation on `jdbc/BankDS` (pre-test connection / validation query) so stale connections are detected and evicted — per P01_Foundation.md v7 Topics Covered ("Validation").
**Acceptance Criteria:** Configured pool size matches CAP01 §4's 40-connection figure; documented headroom against `max_connections=100`; pool validation configured and proven (deliberately kill a backend PostgreSQL connection, confirm the pool detects/evicts it and the next request succeeds without app error).

### Sprint 4
**Goal:** Migrate all DAO code from direct JDBC to JNDI lookup.
**App Dev:** Backend: `AccountDao`, LoginServlet, Freeze/Unfreeze logic all updated to use `jdbc/BankDS` via JNDI.
**Acceptance Criteria:** No `DriverManager`/hardcoded JDBC URL remains (grep-verified).
**Enterprise Outcome:** Direct-JDBC debt from v1 fully closed.

### Sprint 5
**Goal:** Package/deploy `digistack-bank-v7.ear`; validate transactional behavior; record the transaction-boundary traceability note.
**Learning Objective:** WAS-managed Transactions (commit/rollback via pooled connection). Transaction boundaries first become a WebSphere topic here (per P01_Foundation.md v7 Transaction Boundary Note, added 2026-08-25): all balance writes to date are single-table, single-DataSource local transactions inside one EAR. Explicit boundary design is only needed at P02 v15 (Fund Transfer writes TWO balances from ONE action); 2PC/XA reasoning is addressed at P02 v15 (local boundary) and P03 v23/v25 (deliberate avoidance of distributed XA in favor of single-writer CBS + Saga patterns).
**WebSphere Admin:** Deploy to cluster; trigger a deliberate failed Withdraw, confirm clean rollback. Record the transaction-boundary traceability in SetupDoc-v7.md so the local-transaction story is traceable from this version.
**Acceptance Criteria:** All features function as v6; deliberate failure rolls back correctly; SetupDoc-v7.md includes the transaction-boundary note (current state = local transactions; future boundary design points: P02 v15, P03 v23/v25).

### Sprint 6
**Goal:** Write and execute test cases for Version 7.
**Learning Objective:** Test Case discipline (TCS01/TCS02).
**WebSphere Admin:** Confirm app status for test execution.
**Deliverables:** TestCases-v7.md.
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7.
**Enterprise Outcome:** Version 7 test coverage complete.

### Sprint 7
**Goal:** Sign off Version 7.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Admin:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v7.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 7 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 7.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Admin:** Phase 1 — inject a realistic fault tied to this version's topic. Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v7.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 7 fault drill complete. Non-gating — does not block sign-off.

**Version 7 Deliverables:** `digistack-bank-v7.ear`, SetupDoc-v7.md, TestCases-v7.md, FaultDrill-v7.md, JDBC Provider/DataSource/JAAS Alias config.
**Exit Criteria (target, not yet verified):** PostgreSQL JDBC Provider and jdbc/BankDS operational; all existing application database operations use the JNDI DataSource and connection pool; JAAS Auth Alias works; no hardcoded JDBC credentials remain; Smoke passed; Fault drill complete (Sprint 8, non-gating).
**Lessons Learned:** Pool sizing math prevents DB exhaustion at scale; JAAS Auth Alias is the correct credential-externalization point.
**Technical Debt:** None — closes prior debt.

---

# Version 8 — IBM HTTP Server (IHS, Cluster Era)

## Version Overview
**Objective:** Migrate the existing `dsb-ihs` IBM HTTP Server (installed
at v4.5 for the standalone AppServer) to front the WAS cluster. IHS is
NOT reinstalled — it already exists. This version regenerates
plugin-cfg.xml against the cluster topology, adds static assets and
custom error pages, and proves the full cluster-aware routing path.
This closes the technical debt introduced at v4.5.
**Business Scope:** One static asset (logo/CSS) served directly by IHS.
Custom 404/500 error pages at the IHS layer.
**WebSphere Focus:** Plugin regeneration against cluster topology, Web
Server Definition update, static asset serving, custom error pages
(ErrorDocument), virtual host confirmation, plugin propagation to cluster.
**Migration Action (what changes on dsb-ihs at this version):**

BEFORE (v4.5 state — still live entering this version):
plugin-cfg.xml → dsb-dmgr:9080 (standalone server1)

AFTER (v8 state):
plugin-cfg.xml → ClusterMember1:9080
ClusterMember2:9081
(load balancing + failover now active)

**Full Flow After v8:**
Browser (port 80/443)
│
▼
dsb-ihs — IBM HTTP Server
│ plugin-cfg.xml now points at BOTH cluster members
│
├─────────────────────┐
▼ ▼
ClusterMember1 ClusterMember2
│ │
└──────────┬──────────┘
▼
dsb-db PostgreSQL

**Expected Outcome:** plugin-cfg.xml regenerated and propagated against
cluster; static asset confirmed IHS-served; custom 404/500 confirmed
IHS-served; all existing features work via IHS cluster routing.
**Prerequisites:** P01 v7 signed off. `dsb-ihs` already running (from
v4.5). Cluster already running (from v5).

### Sprint 1
**Goal:** Regenerate plugin-cfg.xml against the cluster topology —
both Admin Console and wsadmin paths. This is the core migration action
that replaces the standalone-era plugin with a cluster-aware one.
**Learning Objective:** Why regeneration is needed — the plugin file
generated at v4.5 lists only one server (standalone AppServer port 9080).
After clustering (v5), the topology changed: two cluster members now
exist. IHS cannot route to members it doesn't know about. Regeneration
reads the current WAS topology and writes a new plugin file that lists
both members, their ports, and their weights for load balancing.
**Business Features:** None.
**WebSphere Admin (GUI):**
Servers → Server Types → Web Servers → select `webserver1` →
Generate Plug-in.
Open the generated `plugin-cfg.xml` and confirm it now lists BOTH
cluster member hostnames and ports (not just the old standalone
server1 entry).
**WebSphere Admin (wsadmin Jython):**
```python
# Regenerate plugin against cluster topology
AdminTask.generatePluginCfg(
    ['-serverName', 'webserver1',
     '-nodeName', AdminConfig.list('Node').split('\r\n')[0],
     '-options', ''
    ]
)
print "plugin-cfg.xml regenerated against cluster."
```
**Acceptance Criteria:** Generated `plugin-cfg.xml` contains entries for
both ClusterMember1 (port 9080) and ClusterMember2 (port 9081). Old
standalone server1 entry is no longer the sole endpoint. File timestamp
confirms this is a fresh generation, not the v4.5 file.
**Enterprise Outcome:** Plugin routing table now reflects real cluster
topology — foundation for all subsequent sprints in this version.

### Sprint 2
**Goal:** Update the existing `webserver1` Web Server Definition (created
at v4.5) for the cluster era — not a new definition.
**Learning Objective:** Web Server Definition update — how the WAS-side
object is re-pointed at the cluster topology (server weight/routing
metadata) so plugin regeneration reflects the cluster.
**WebSphere Admin (GUI):** Verify `webserver1` (from v4.5) still listed;
update host/port metadata if cluster ports changed; confirm the
definition references the cluster, not standalone server1.
**Dependencies:** Sprint 1.
**Acceptance Criteria:** `webserver1` definition updated (not duplicated);
plugin generation from Sprint 1 confirmed against both cluster members.

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
**Goal:** Write and execute test cases for Version 8.
**Learning Objective:** Test Case discipline (TCS01/TCS02).
**Deliverables:** TestCases-v8.md (including TP01 Pipeline Results section).
**WebSphere Admin:** Execute TP01_Test_Pipeline.md stages 1–5 (DEV → SIT → UAT → PRE-PROD → PROD) and record every stage in the "TP01 Pipeline Results — v8" table.
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7; all TP01 pipeline stages Pass (Critical/High) per TP01 R3.
**Enterprise Outcome:** Version 8 test coverage complete.

### Sprint 7
**Goal:** Sign off Version 8.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Admin:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v8.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 8 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 8.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Admin:** Phase 1 — inject a realistic fault tied to this version's topic. Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v8.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 8 fault drill complete. Non-gating — does not block sign-off.

**Version 8 Deliverables:** `digistack-bank-v8.ear`, SetupDoc-v8.md, TestCases-v8.md, FaultDrill-v8.md, Web Server definition/plugin-cfg.xml/ErrorDocument config.
**Exit Criteria (target, not yet verified):** Home + DB read functional; DB validated; Deployment successful; Smoke passed; Fault drill complete (Sprint 8, non-gating).
**Lessons Learned:** Plugin generation/propagation is explicit, not automatic; static content belongs at the web tier.
**Technical Debt:** Single IHS instance (no LB/HA yet) — enterprise load balancing is P02 v21.

---

# Version 8.5 — Transaction Service / XA Recovery

## Version Overview
**Objective:** Prove atomic, recoverable distributed transactions against
the existing DigiStack app using a "FundsTransfer" flow (Servlet → EJB
CMT → two XA DataSources: DEBIT_DS, CREDIT_DS).
**Business Scope:** No new customer-facing feature — validates
correctness of the existing Deposit/Withdraw path under two-resource
transactions.
**WebSphere Focus:** XA vs non-XA DataSource selection, 2PC, transaction/
recovery logs, transaction timeouts, heuristic outcomes, WTRN/WSVR error
codes.
**Expected Outcome:** Rollback proven on partial failure; XA recovery
proven after a mid-2PC server kill; heuristic outcome forced and safely
resolved; `wtrn-error-cheatsheet.md` produced.
**Prerequisites:** P01 v7 signed off (DataSource/pooling in place).

### Sprint 1
**Goal:** Stand up DEBIT_DS and CREDIT_DS as XA DataSources; build the
FundsTransfer Servlet → EJB (CMT) skeleton.
**WebSphere Admin:** Create both DataSources as XA-capable; verify Test
Connection on each.
**Acceptance Criteria:** Both DS connect; EJB CMT deploys and calls both.

### Sprint 2
**Goal:** Prove ACID via forced partial failure.
**Business Features:** None.
**WebSphere Admin:** Force bad SQL on CREDIT_DS after DEBIT_DS succeeds;
verify rollback leaves both unchanged. Repeat with non-XA + autocommit to
show an orphaned debit as the negative control.
**Acceptance Criteria:** XA path rolls back both; non-XA control leaves an
orphaned debit, confirming why XA matters.

### Sprint 3
**Goal:** Capture 1PC vs 2PC behavior and locate/size the transaction log.
**WebSphere Admin:** Enable `Transaction=all` trace; run one successful
transfer; identify prepare → decision log → commit sequence in trace.
Locate `<profile>/tranlog/`; document sizing rationale.
**Acceptance Criteria:** Trace clearly shows 2PC sequence; tranlog
location and sizing documented.

### Sprint 4
**Goal:** Configure and test transaction timeouts; force and resolve a
heuristic outcome.
**WebSphere Admin:** Set total transaction lifetime, client inactivity,
and max in-process timeouts (console + wsadmin). Simulate a timeout via a
sleep servlet, capture the exact WTRN error. Force a heuristic outcome by
manually committing one DB while the transaction is in-doubt; resolve via
AdminControl (retry/commit/forget).
**Acceptance Criteria:** Timeout reproduced with captured WTRN code;
heuristic outcome forced, correctly diagnosed, and resolved.

### Sprint 5
**Goal:** XA recovery — the money lab. Kill the server mid-2PC
(in-doubt: prepared, not committed); restart; verify recovery replay.
**WebSphere Admin:** `kill -9` the member mid-transfer at the prepared
stage; restart; watch WTRN recovery messages in SystemOut; verify DB
state matches expected outcome (zero lost/duplicated funds).
**Acceptance Criteria:** Recovery log replay confirmed; funds neither lost
nor duplicated.

### Sprint 6
**Goal:** Write and execute test cases for Version 8.5.
**Deliverables:** TestCases-v8.5.md (including TP01 Pipeline Results
section).
**WebSphere Admin:** Execute TP01_Test_Pipeline.md stages 1–5.
**Acceptance Criteria:** All Critical/High test cases pass; all TP01
stages Pass.
**Enterprise Outcome:** Version 8.5 test coverage complete.

### Sprint 7
**Goal:** Sign off Version 8.5.
**WebSphere Admin:** Capture backupConfig baseline (including tranlog
backup strategy); final smoke test.
**Deliverables:** SetupDoc-v8.5.md, `wtrn-error-cheatsheet.md`.
**Acceptance Criteria:** SetupDoc complete; backupConfig + tranlog backup
captured; smoke test passes.
**Enterprise Outcome:** Version 8.5 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation — full war-game.
**WebSphere Admin:** JMeter load on FundsTransfer → `kill -9` mid-flight →
verify in-doubt state in the DB → restart → recovery replay → zero lost
transfers → write post-mortem. Time yourself.
**Deliverables:** FaultDrill-v8.5.md (post-mortem format).
**Acceptance Criteria:** Fault injected, incident raised, RCA completed,
zero lost/duplicated funds confirmed, environment restored.
**Enterprise Outcome:** Version 8.5 fault drill complete. Non-gating.

**Version 8.5 Deliverables:** FundsTransfer app (Servlet/EJB/2 XA DS),
`wtrn-error-cheatsheet.md`, SetupDoc-v8.5.md, TestCases-v8.5.md,
FaultDrill-v8.5.md.
**Exit Criteria (target, not yet verified):** Rollback proven; 2PC traced;
timeouts configured and tested; heuristic outcome resolved; XA recovery
proven; fault drill complete (non-gating).
**Lessons Learned:** Recovery correctness depends entirely on tranlog
integrity — this is why Sprint 7's backup step is not optional.
**Technical Debt:** None new — this version exists purely to close a
correctness gap.

---

# Version 9 — Session Management

## Version Overview
**Objective:** Harden session behavior across the cluster — sticky sessions, persistence/failover, memory-to-memory replication tuning.
**Business Scope:** Zero new functionality. Session Timeout (auto-logout after N idle minutes).
**WebSphere Focus:** HTTP Sessions, Sticky Sessions, Session Persistence, Session Failover, Memory-to-Memory Replication, Database-Backed Session Persistence (session table in PostgreSQL, dedicated DataSource `jdbc/SessionDS` or reusing `jdbc/BankDS`), Session Persistence Frequency/tuning, Trade-off Analysis (performance vs. reliability vs. DB load).
**Expected Outcome:** Session timeout enforced; session survives cluster member restart; sticky-session routing confirmed via logs; all three persistence strategies ((c) sticky-only, (a) M2M, (b) DB-backed) configured, exercised, and compared in SetupDoc-v9.md.
**Prerequisites:** P01 v8 signed off.

### Sprint 1
**Goal:** Configure sticky session routing at the plugin layer.
**WebSphere Admin:** Confirm plugin `CloneID`-based affinity active; enable plugin request logging.
**Acceptance Criteria:** Plugin log shows a session's requests consistently routed to the same member.

### Sprint 2
**Goal:** Configure and compare the sticky-only control case and enable memory-to-memory session replication.
**Learning Objective:** The three persistence strategies (per P01_Foundation.md v9): (c) sticky-only (no replication) as the control case, and (a) memory-to-memory (default).
**WebSphere Admin:** First configure sticky-only (no replication) and exercise the control case — log in, kill a member, confirm session lost (baseline). Then enable replication domain for `devdsbinappcluster01`; confirm both members registered as partners; observe single-replica vs. multi-replica tuning.
**Acceptance Criteria:** Sticky-only baseline observed (session lost on member kill); Admin Console shows both members actively replicating; M2M kill-test shows session survives; replica-count tuning observed and documented.

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
**Goal:** Package/deploy `digistack-bank-v9.ear`; build and evaluate the DB-backed persistence case; record the three-way comparison.
**Learning Objective:** Database-backed session persistence (session table in PostgreSQL, dedicated DataSource `jdbc/SessionDS` or reusing `jdbc/BankDS`), persistence frequency tuning, and trade-off analysis (performance vs. reliability vs. DB load).
**WebSphere Admin:** Deploy v9; configure strategy (b) DB-backed session persistence; kill a member and confirm session survives; observe session table growth and timeout/purge behavior; measure latency difference between (a) M2M and (b) DB-backed under load; assess DB connection-pool impact of (b) reusing the P01 v7 pool-sizing math (per P01_Foundation.md v7 Connection Pool Sizing worked example).
**Acceptance Criteria:** All three strategies configured and exercised in turn ((c) sticky-only baseline, (a) M2M, (b) DB-backed); comparison recorded in SetupDoc-v9.md covering latency (a) vs (b), pool impact of (b), and a stated ship decision (memory-to-memory, with DB-backed documented as the fallback for non-replicable session state); interview-anchor three-way write-up (when each is appropriate, sizing implications, DR implications) included as a SetupDoc-v9.md section.

### Sprint 6
**Goal:** Write and execute test cases for Version 9.
**Learning Objective:** Test Case discipline (TCS01/TCS02).
**Deliverables:** TestCases-v9.md (including TP01 Pipeline Results section).
**WebSphere Admin:** Execute TP01_Test_Pipeline.md stages 1–5 (DEV → SIT → UAT → PRE-PROD → PROD) and record every stage in the "TP01 Pipeline Results — v9" table.
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7; all TP01 pipeline stages Pass (Critical/High) per TP01 R3.
**Enterprise Outcome:** Version 9 test coverage complete.

### Sprint 7
**Goal:** Sign off Version 9.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Admin:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v9.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 9 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 9.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Admin:** Phase 1 — inject a realistic fault tied to this version's topic. Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v9.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 9 fault drill complete. Non-gating — does not block sign-off.

**Version 9 Deliverables:** `digistack-bank-v9.ear`, SetupDoc-v9.md (including three-way persistence comparison + interview-anchor write-up), TestCases-v9.md, FaultDrill-v9.md, replication domain/session timeout config, DB-backed session persistence config (session table + DataSource).
**Exit Criteria (target, not yet verified):** Home + DB read functional; DB validated; Deployment successful; Smoke passed; Fault drill complete (Sprint 8, non-gating).
**Lessons Learned:** Sticky sessions (routing) and replication (data protection) are distinct mechanisms.
**Technical Debt:** None introduced.

---

# Version 9.5 — wsadmin Jython Toolkit & Troubleshooting

## Version Overview
**Objective:** Build a reusable, properties-driven wsadmin Jython
automation toolkit and prove diagnostic fluency (thread/heap dumps, log/
trace, JVM tuning) against the existing v1–v9 deployment.
**Business Scope:** Zero new functionality — pure ops/automation surface
over the existing app.
**WebSphere Focus:** AdminControl/AdminConfig/AdminApp/AdminTask,
properties-file-driven scripting, thread dump and heap dump analysis
(Eclipse MAT), SystemOut/FFDC/HPEL log viewing, trace strings, GC policy
comparison, WebContainer thread pool tuning.
**Expected Outcome:** `wasOps.py` toolkit (start/stop, status, deploy/
undeploy, pool changes, transaction timeout changes) driven entirely by
an external properties file; one memory leak identified end-to-end; one
OOM/thread-exhaustion scenario captured and resolved.
**Prerequisites:** P01 v8.5 signed off (transaction timeout changes are
one of the toolkit's scripted actions).

### Sprint 1
**Goal:** Build the properties-file-driven `wasOps.py` skeleton
(AdminControl/AdminConfig/AdminApp/AdminTask wrappers).
**WebSphere Admin:** Write Jython functions for start/stop and status
listing, reading target env/server names from an external `.properties`
file — no hardcoded values.
**Acceptance Criteria:** Same script runs unmodified against a second
target by swapping only the properties file.

### Sprint 2
**Goal:** Extend the toolkit: deploy/undeploy, DataSource pool changes,
and transaction timeout changes (reusing v8.5's timeout settings).
**WebSphere Admin:** Add `AdminApp.install/update`, pool-size change
function, and a transaction-timeout-change function.
**Acceptance Criteria:** Toolkit deploys/undeploys the app and changes
pool size and timeout values, all properties-driven.

### Sprint 3
**Goal:** Thread dump analysis — capture and diagnose a stuck-thread
scenario.
**WebSphere Admin:** Force a stuck thread (blocking call); capture via
`kill -3` and the wsadmin equivalent; analyze for deadlock/stuck-thread
pattern.
**Acceptance Criteria:** Stuck thread identified from the dump with a
documented root cause.

### Sprint 4
**Goal:** Heap dump analysis — hunt a deliberately introduced memory
leak with Eclipse MAT.
**WebSphere Admin:** Deploy a build with an intentional leak; capture
heap dumps over time; identify the leaking class in MAT.
**Acceptance Criteria:** Leak class correctly identified from dump
comparison.

### Sprint 5
**Goal:** JVM tuning under OOM/thread-pool exhaustion.
**WebSphere Admin:** Run `-Xms`/`-Xmx` experiments; simulate OOM; compare
GC policies (gencon/optthruput/balanced) via GC log parsing; simulate
WebContainer thread pool exhaustion and resolve via sizing.
**Acceptance Criteria:** OOM reproduced and resolved via heap/pool
tuning; GC policy comparison documented.

### Sprint 6
**Goal:** Write and execute test cases for Version 9.5.
**Deliverables:** TestCases-v9.5.md (including TP01 Pipeline Results).
**WebSphere Admin:** Execute TP01_Test_Pipeline.md stages 1–5.
**Acceptance Criteria:** All Critical/High test cases pass; all TP01
stages Pass.
**Enterprise Outcome:** Version 9.5 test coverage complete.

### Sprint 7
**Goal:** Sign off Version 9.5.
**WebSphere Admin:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v9.5.md, `wasOps.py` (committed toolkit).
**Acceptance Criteria:** SetupDoc complete; toolkit runs clean against a
second environment; smoke test passes.
**Enterprise Outcome:** Version 9.5 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation.
**WebSphere Admin:** Phase 1 — inject an OOM or stuck-thread fault. Phase
2 — incident ticket raised from real symptoms. Phase 3 — diagnose live
using the toolkit and dump-analysis skills from Sprints 3–5, RCA, restore.
**Deliverables:** FaultDrill-v9.5.md.
**Acceptance Criteria:** Fault diagnosed using the toolkit itself (not ad
hoc commands); RCA completed; environment restored.
**Enterprise Outcome:** Version 9.5 fault drill complete. Non-gating.

**Version 9.5 Deliverables:** `wasOps.py`, SetupDoc-v9.5.md,
TestCases-v9.5.md, FaultDrill-v9.5.md.
**Exit Criteria (target, not yet verified):** Toolkit functional and
properties-driven; leak identified; OOM/thread exhaustion resolved; fault
drill complete (non-gating).
**Lessons Learned:** A dump is only useful if you already know what
"normal" looks like — baseline captured here feeds every later version's
troubleshooting.
**Technical Debt:** None new.

---

# Version 10 — Users & Groups

## Version Overview
**Objective:** Introduce administrative security — real user registry, roles, groups — closing v6's Freeze/Unfreeze open-access debt.
**Business Scope:** Zero new functionality beyond role enforcement. Freeze/Unfreeze gated to Administrator; Customer role limited to Deposit/Withdraw.
**WebSphere Focus:** Administrative Security, File Registry (or LDAP), Users, Groups, Roles, Authorization.
**Expected Outcome:** File-based registry configured; Customer/Administrator roles defined; Freeze/Unfreeze unreachable by Customer role.
**Prerequisites:** P01 v9.5 signed off.
**Clarification:** Only two roles are built in P01: Customer and Administrator (this version). Auditor is not built anywhere in P01–P10. Branch Operator is not built in P01–P02 — P03 v29's Branch Portal (Teller Login) introduces a Teller role there. Until that version, only Customer and Administrator exist; no role is assumed to already exist (per P01_Foundation.md v10 "Roles Actually Built").

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
**Goal:** Write and execute test cases for Version 10.
**Learning Objective:** Test Case discipline (TCS01/TCS02).
**Deliverables:** TestCases-v10.md (including TP01 Pipeline Results section).
**WebSphere Admin:** Execute TP01_Test_Pipeline.md stages 1–5 (DEV → SIT → UAT → PRE-PROD → PROD) and record every stage in the "TP01 Pipeline Results — v10" table.
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7; all TP01 pipeline stages Pass (Critical/High) per TP01 R3.
**Enterprise Outcome:** Version 10 test coverage complete.

### Sprint 7
**Goal:** Sign off Version 10.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Admin:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v10.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 10 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 10.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Admin:** Phase 1 — inject a realistic fault tied to this version's topic. Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v10.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 10 fault drill complete. Non-gating — does not block sign-off.

**Version 10 Deliverables:** `digistack-bank-v10.ear`, SetupDoc-v10.md, TestCases-v10.md, FaultDrill-v10.md, File Registry/groups/role mapping/web.xml constraints.
**Exit Criteria (target, not yet verified):** File-based Federated Repository operational; Customer and Administrator groups/users authenticate successfully; security roles mapped correctly; Customer cannot invoke Freeze/Unfreeze; Administrator can perform Freeze/Unfreeze; Smoke passed; Fault drill complete (Sprint 8, non-gating).
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
**Goal:** Write and execute test cases for Version 12.
**Learning Objective:** Test Case discipline (TCS01/TCS02).
**Deliverables:** TestCases-v12.md (including TP01 Pipeline Results section).
**WebSphere Admin:** Execute TP01_Test_Pipeline.md stages 1–5 (DEV → SIT → UAT → PRE-PROD → PROD) and record every stage in the "TP01 Pipeline Results — v12" table.
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7; all TP01 pipeline stages Pass (Critical/High) per TP01 R3.
**Enterprise Outcome:** Version 12 test coverage complete.

### Sprint 7
**Goal:** Sign off Version 12.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Admin:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v12.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 12 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 12.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Admin:** Phase 1 — inject a realistic fault tied to this version's topic. Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v12.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 12 fault drill complete. Non-gating — does not block sign-off.

**Version 12 Deliverables:** `digistack-bank-v12.ear`, SetupDoc-v12.md, TestCases-v12.md, FaultDrill-v12.md, plugin SSL/SSL Repertoires/mTLS config.
**Exit Criteria (target, not yet verified):** IHS → plugin → AppServer → DB SSL path verified; NodeDefaultSSLSettings and CellDefaultSSLSettings correctly configured; mTLS enforced on the selected internal hop; deliberate SSL failure diagnosed; certificate renewal tested; Smoke passed; Fault drill complete (Sprint 8, non-gating).
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
**Learning Objective:** JNDI Mail Session as external-resource configuration (mirrors v7's DataSource pattern).
**WebSphere Admin:** Configure Mail Provider/SMTP host; create `mail/BankMailSession`. SMTP credentials externalized the same way as v7's JAAS Auth Alias — never hardcoded in config or code, per STD's Golden Rule and DBS01 §4.2's Connection & Credentials Standard.
**Acceptance Criteria:** Test Connection confirms SMTP reachability; no plaintext SMTP credential found anywhere in config (grep-verified, same discipline as v7's negative test).

### Sprint 2
**Goal:** Configure a Resource Environment Entry for sender/recipient defaults.
**WebSphere Admin:** Create Resource Environment Entry for sender address (`noreply@digistack.cloud`).
**Acceptance Criteria:** Value resolves correctly via JNDI lookup.

### Sprint 3
**Goal:** Build email-sending logic; wire it to successful Withdraw.
**Business Features:** Withdraw triggers confirmation email.
**App Dev:** Backend: `NotificationService.sendWithdrawEmail()` via JavaMail/JNDI, called from `AccountService.withdraw()`. UI: notification/alert bell icon on Dashboard header showing Withdraw email event count (per P01_Foundation.md v13 UI note; extended at P02 v15).
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
**Goal:** Write and execute test cases for Version 13.
**Learning Objective:** Test Case discipline (TCS01/TCS02).
**Deliverables:** TestCases-v13.md (including TP01 Pipeline Results section).
**WebSphere Admin:** Execute TP01_Test_Pipeline.md stages 1–5 (DEV → SIT → UAT → PRE-PROD → PROD) and record every stage in the "TP01 Pipeline Results — v13" table.
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7; all TP01 pipeline stages Pass (Critical/High) per TP01 R3.
**Enterprise Outcome:** Version 13 test coverage complete.

### Sprint 7
**Goal:** Sign off Version 13.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Admin:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v13.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 13 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 13.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Admin:** Phase 1 — inject a realistic fault tied to this version's topic. Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v13.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 13 fault drill complete. Non-gating — does not block sign-off.

**Version 13 Deliverables:** `digistack-bank-v13.ear`, SetupDoc-v13.md, TestCases-v13.md, FaultDrill-v13.md, SMTP Mail Provider/JNDI Mail Session/Resource Environment Entry.
**Exit Criteria (target, not yet verified):** JNDI Mail Session operational; successful Withdraw delivers the expected email; deliberately broken SMTP configuration produces actionable logs; recovery verified; cluster-wide mail trigger tested; Smoke passed; Fault drill complete (Sprint 8, non-gating).
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
**WebSphere Admin:** Apply CAP01 §2.1's lab-adjusted values (Xms=Xmx, fixed heap): Node1/App member (combined DMgr+Node1 VM) = 1 GB within its 3 GB VM; Node2/App member = 1.25 GB within its 2 GB VM. Re-run report, capture GC/PMI logs again.
**Acceptance Criteria:** Report completes with no OOM; GC behavior measurably improved vs. Sprint 3 baseline.

### Sprint 5
**Goal:** Package/deploy `digistack-bank-v14.ear`; tune thread pools for report load.
**WebSphere Admin:** Deploy v14; review Web Container thread pool sizing under report load, adjust if needed.
**Acceptance Criteria:** Report generation no longer degrades concurrent Login/Deposit/Withdraw responsiveness.

### Sprint 6
**Goal:** Write and execute test cases for Version 14.
**Learning Objective:** Test Case discipline (TCS01/TCS02).
**Deliverables:** TestCases-v14.md (including TP01 Pipeline Results section).
**WebSphere Admin:** Execute TP01_Test_Pipeline.md stages 1–5 (DEV → SIT → UAT → PRE-PROD → PROD) and record every stage in the "TP01 Pipeline Results — v14" table.
**Acceptance Criteria:** All Critical/High test cases pass per TCS01 §2.7; all TP01 pipeline stages Pass (Critical/High) per TP01 R3.
**Enterprise Outcome:** Version 14 test coverage complete.

### Sprint 7
**Goal:** Sign off Version 14.
**Learning Objective:** SetupDoc discipline (SDD01).
**WebSphere Admin:** Capture backupConfig baseline; final smoke test.
**Deliverables:** SetupDoc-v14.md.
**Acceptance Criteria:** SetupDoc complete and followed start to finish; backupConfig captured; smoke test passes.
**Enterprise Outcome:** Version 14 signed off.

### Sprint 8
**Goal:** Fault Injection + Incident Simulation for Version 14.
**Learning Objective:** Real fault diagnosis against a live broken environment (PIS01/FIS01).
**WebSphere Admin:** Phase 1 — inject a realistic fault tied to this version's topic. Phase 2 — incident ticket raised from real symptoms. Phase 3 — investigate live, perform RCA, restore environment.
**Deliverables:** FaultDrill-v14.md.
**Acceptance Criteria:** Fault injected, incident raised, RCA completed, environment restored to known-good state.
**Enterprise Outcome:** Version 14 fault drill complete. Non-gating — does not block sign-off.

**Version 14 Deliverables:** `digistack-bank-v14.ear`, SetupDoc-v14.md, TestCases-v14.md, FaultDrill-v14.md, tuned JVM heap/thread pool config.
**Exit Criteria (target, not yet verified):** Multi-thousand-row Transaction Report generates successfully; baseline heap pressure/OOM captured; JVM Xms/Xmx tuning applied; GC/PMI comparison shows improvement; Web Container thread-pool behavior under report load validated; Smoke passed; Fault drill complete (Sprint 8, non-gating).
**Lessons Learned:** Reproducing a real performance problem before tuning validates the fix against evidence, not a guess.
**Technical Debt:** None — P01 closes clean.

---

## P01 — Foundation: Planned Application State (once complete)

**Modules:** Home, Login/Logout, Balance, Deposit, Withdraw, Freeze/Unfreeze, one Transaction Report, one Withdraw email.

**Infrastructure:** DMGR, Node, Cluster, DataSource, JNDI, IHS (incl. custom 404/500), SSL (end-to-end, mTLS on one hop), Security (roles/registry), JVM (heap-tuned), Mail (JNDI Mail Session), Reports, XA Transaction Service (2PC/recovery, tranlog — v8.5), wsadmin Jython Toolkit (wasOps.py, properties-driven — v9.5).

**Technical Debt Plan (introduced and resolved within P01, per schedule):**
| Debt Introduced At | Planned Resolution At | Item |
|---|---|---|
| v1 | v7 | Direct JDBC → JNDI DataSource/pooling |
| v4.5 | v8 | plugin-cfg.xml points at standalone AppServer — regenerated against cluster at v8 |
| v4.5 | v8 | No custom error pages at IHS layer — added at v8 |
| v4.5 | v8 | No static assets at IHS layer — added at v8 |
| v4.5 | v11 | HTTP only at IHS — SSL/HTTPS enforced at v11 |
| v6 | v10 | Freeze/Unfreeze open access → role-gated |
| v11 | v12 | Web-tier-only SSL → end-to-end SSL/mTLS |

**Planned Hand-off to P02:** Once complete, this same small app + cluster + IHS + SSL + security domain + DataSource + mail session becomes the foundation P02 will build JMS, Web Services, deeper security, monitoring, IBM MQ, and load balancing on top of — still one EAR until the CBS split in P03.

---

*This is the consolidated P01_Sprint_Plan.md — the planning document for P01, not a completion record. No versions have been built or signed off yet. This file is the single source of truth for P01's plan going forward; actual progress is tracked in Progress_Log.md.*

*Correction (2026-07-28 cross-file audit): Version 1's Prerequisites line previously listed "WebSphere ND = 9.0.5.28 installed (per STD)" — this matched a since-reverted STD version-pin edit that had prematurely claimed an actual install before any Sprint work existed. Updated to reference STD's then-current placeholder pin (9.0.3, unconfirmed), consistent with Sprint 1's own goal of provisioning and validating that install for the first time.*

*Second update (2026-07-28, same day, later): the project owner has now explicitly chosen 9.0.5.28 as the intended target package (STD v1.6, SOE01 v1.5, same day) ahead of Sprint 1 — a deliberate forward-looking decision, distinct from the earlier premature edit above (which falsely implied an install had already happened). The value above is updated to match, still explicitly marked "target/placeholder... to be confirmed during this Sprint" — Sprint 1's job is unchanged: provision the VM and validate the real install against this target.*
