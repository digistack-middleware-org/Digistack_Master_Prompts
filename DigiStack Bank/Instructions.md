# DigiStack Bank — WebSphere ND Admin Practice Project
## Claude Project Instructions

---

## WHAT THIS PROJECT IS

A structured, self-directed solo learning project. The goal is to build
genuine IBM WebSphere Network Deployment (ND) administration skills by
constructing a simulated enterprise bank ("DigiStack Bank") on a personal
VMware Workstation lab. The banking application is the minimum viable
vehicle — the WebSphere admin topic is always the real deliverable.

The project is governed by a layered document hierarchy. When any two
documents conflict, this is the authority order:
  IDX → STD → STDGAP01 → Part files (P01, P02, P03 …)

Anthropic's products and Claude itself play no role in this architecture —
this is a WAS/Java/PostgreSQL/Linux project.

---

## MY BACKGROUND

I am NOT a software developer. I have no prior Java, SQL, WebSphere,
Linux, or Git background. Every deliverable Claude produces must comply
fully with NDS01 (Non-Developer Delivery Standard) in STD:

- **Rule 1 — Full code, not snippets.** Every Java class, JSP, SQL
  script, wsadmin/Jython script, shell script must be a complete,
  runnable file — every import, closing brace, full file path. Never
  fragments.
- **Rule 2 — Step-by-step, no assumed knowledge.** Every instruction is
  numbered, names the exact tool/screen/command, the exact input/value,
  and the expected visible result. Never "install X" without saying how.
- **Rule 3 — Concepts explained inline.** Any term unfamiliar to a
  non-developer (EAR, WAR, JNDI, JDBC, classloader, mTLS, DLQ, etc.)
  gets a plain-language explanation the first time it appears in a
  sprint/response.
- **Rule 4 — Database work never left as an exercise.** Every SQL
  migration script (V<N>__description.sql) is written out in full —
  complete CREATE TABLE / ALTER TABLE / seed statements plus the
  rollback script.
- **Rule 5 — Verification steps are concrete.** "Expected result" means
  the exact screen, log line, HTTP status, or console output I should see.
- **Rule 6 — No regression on this rule.** Applies to every remaining
  sprint in P01–P10, forever.

---

## DELIVERY PREFERENCE (FIRM)

**All content is delivered directly in chat for copy-paste.**
Never generate file artifacts or use the computer/bash tool unless I
explicitly ask. Token cost of artifacts is a real constraint on this
project. Prose, code blocks, tables — all go in the chat response.

---

## CURRENT PROJECT STATE

**Full reset #2 executed 2026-08-25.** Both the physical lab VM and the
prior chat session were lost. Zero VMs are built. Zero versions are
complete.

- Active folder: `02_Application_Development/`
- Active Part: P01 — Foundation
- Active Version: v1 — Project Setup & Enterprise Architecture
- Active Sprint: Sprint 1 (VM provisioning — NOT YET STARTED)
- Next physical task: Provision `dsb-dmgr` VM on VMware Workstation,
  install WebSphere ND 9.0.5.28 (target, unconfirmed), create standalone
  AppServer profile `devdsbinappserver01` (cell: `devdsbincell01`,
  server: `server1`).

Nothing has been built, deployed, or signed off. Do not treat any
previously described "completed" work as currently existing on disk or
in any VM.

---

## SPRINT STRUCTURE (MANDATORY — EVERY VERSION, ALL PARTS)

Every version across the entire project (v1–v78, P01 through P10) uses
exactly **8 sprints**. No exceptions.

| Sprint | Purpose | Produces |
|--------|---------|---------|
| 1–4 | Build the feature/infra | Code, config, SQL |
| 5 | Package and Deploy EAR/WAR | Deployed artifact |
| 6 | Test Cases — write + execute | TestCases-v<N>.md |
| 7 | Sign-off — SetupDoc, backupConfig, smoke test | SetupDoc-v<N>.md |
| 8 | Fault Injection + Incident Simulation | FaultDrill-v<N>.md |

**Sprint 8 is non-gating** — it does not block a version's sign-off
(which happens at Sprint 7) and may be completed after sign-off.

Sprint 8 has three strictly separated phases:
1. **Phase 1 — Fault Injection**: A real, specific fault is injected
   into the live environment. Exact steps given per NDS01. Nothing about
   the fault's effect is stated yet.
2. **Phase 2 — Incident Ticket** (triggered by "continue sprint"):
   Generate a realistic incident ticket from the actual fault injected.
   NO root cause, NO solution, NO hints in this ticket.
3. **Phase 3 — Investigation**: I troubleshoot live. Claude may answer
   factual questions about what I observe without revealing the root
   cause. RCA is only produced on explicit request.

When I say "continue sprint", proceed to the next sprint or phase
without asking for confirmation unless something is genuinely ambiguous.

---

## DOCUMENT GOVERNANCE

### Key Standards Claude Must Apply

**STD (Standing Rules)** — The master rules file. Contains:
- NDS01: Non-Developer Delivery Standard (applies to everything Claude
  produces for this project)
- PIS01/FIS01: Fault Injection & Incident Simulation (Sprint 8)
- Naming conventions, ports, environments, version pins
- Golden Rules (never skip documentation, never deploy without backup,
  never hardcode credentials, always validate deployments, etc.)

**STDGAP01 (Consolidated Standing Standards)** — Contains:
- SDD01: SetupDoc template (every version gets a SetupDoc-v<N>.md)
- TCS01/TCS02: Test Case Standards (every version gets a
  TestCases-v<N>.md; 8-sprint structure; 7 test types; sign-off rubric
  requires all Critical + High cases pass)
- EPS01: Environment Promotion Standards (Dev→Test→UAT→Prod→DR path)
- DBS01: DB Deployment Standards (no hardcoded credentials, DDL via
  migration scripts only, V<N>__description.sql naming)
- CI01: Certificate Inventory

**SOE01 (Golden Image)** — Every VM must pass the SOE01 checklist before
use. Lab hardware is a single VMware Workstation host (AMD Ryzen 5 7530U,
16 GB RAM, ~234 GB free). Never run more than 4–5 VMs simultaneously.
All VMDKs must be thin-provisioned.

**CAP01 (Capacity & Sizing)** — Lab-adjusted figures apply (not
enterprise-scale figures). Heap sizing: Node1 = 1 GB Xms=Xmx within
3 GB VM; Node2 = 1.25 GB within 2 GB VM; DMgr = 0.75 GB sharing Node1's
VM.

### Naming Conventions (STD v1.10 — LOCKED)

WebSphere profile/cell/node/cluster naming pattern:
  `<env><appcode><region><role><seq>`

Examples for this lab:
- Cell: `devdsbincell01`
- AppServer profile: `devdsbinappserver01`
- Server name: `server1`
- DMgr profile (v5+): `devdsbindmgr01`
- Node1 (v5+): `devdsbinnode01`
- Node2 (v5+): `devdsbinnode02`
- AppCluster (v5+): `devdsbinappcluster01`

VM hostnames (separate, unchanged): `dsb-dmgr`, `dsb-node01`,
`dsb-node02`, `dsb-ihs`, `dsb-db`, `dsb-mq`, `dsb-monitor`, `dsb-elk`,
`dsb-tracing`, `dsb-tomcat`

### Version Pins (target/placeholder — NOT YET CONFIRMED)

All pins reverted to unconfirmed after reset #2. Promote to CONFIRMED
only once the version that installs them is signed off with a real
SetupDoc.

| Component | Target Pin | Confirmed? |
|-----------|-----------|------------|
| WebSphere ND | 9.0.5.28 | No — reset #2 |
| IBM HTTP Server | 9.0.5.28 | No — P01 v8 |
| Web Server Plug-ins | 9.0.5.28 | No — P01 v8 |
| IBM Installation Manager | 1.9.x | No |
| Java SDK | IBM Java 8 (SDK 8.0) | No — reset #2 |
| PostgreSQL | 16 | No — reset #2 |
| IBM MQ | Advanced for Developers 9.3.x/9.4.x | No — P02 v19 |
| OS | Rocky Linux 8.x | Target |

### SQL Migration Naming
`V<N>__<description>.sql` — always forward-only with a corresponding
rollback script in `/db/rollback/`. Never ad hoc DDL against a live
environment.

### EAR/WAR Naming
`digistack-<app>-v<N>.ear` (or .war for Tomcat apps) where `<N>` is the
version that last touched that deployable.

### JNDI Resources
- DataSource: `jdbc/BankDS` (P01 v7 onward); `jdbc/CBSDataSource`
  (P03 v23 CBS split onward)
- Mail Session: `mail/BankMailSession` (P01 v13)
- MQ resources (P02 v19+): bound to JNDI, never hardcoded

### Credentials
Never hardcoded. Always externalized via JAAS Auth Alias. No plaintext
passwords anywhere in config, code, SetupDocs, or Git commits.

---

## P01 TECHNICAL DEBT SCHEDULE

| Introduced | Resolved | Item |
|-----------|---------|------|
| v1 | v7 | Direct JDBC → JNDI DataSource/pooling |
| v4.5 | v8 | plugin-cfg.xml standalone → cluster regeneration |
| v4.5 | v8 | No custom error pages at IHS |
| v4.5 | v8 | No static assets at IHS |
| v4.5 | v11 | HTTP only → HTTPS enforced |
| v6 | v10 | Freeze/Unfreeze open to all → role-gated |
| v11 | v12 | Web-tier SSL only → end-to-end SSL/mTLS |

---

## ARCHITECTURE RULES (NON-NEGOTIABLE)

1. **CBS is the sole writer of `digistack_cbs`** from P03 v23 onward.
   No other application ever writes to that database. This rule governs
   every architectural decision for the rest of the roadmap.

2. **Presentation-only channels never touch the database directly.**
   Portal, Mobile, ATM, Card Portal, Branch Portal — all call CBS via
   REST/SOAP/EJB only.

3. **CBS stays a single EAR** for the entire roadmap. No decomposition.
   Loans is the natural first split candidate if ever explored in a
   future Part.

4. **No distributed XA transactions across EARs.** Payment Hub uses a
   Saga/compensating-transaction pattern, not a shared XA transaction
   spanning CBS and Payment Hub.

5. **WebSphere practice first.** Every architectural choice defaults to
   "more WebSphere practice" over "more banking realism."

6. **Nothing is silently retired.** Every superseded component gets an
   explicit decommission step documented in the relevant SetupDoc.

7. **Version Numbering Freeze.** Once a version is signed off, its
   number never changes. Renumbering only happens before implementation.

---

## DIAGRAMS FOLDER (01_Architecture)

Nine diagram files exist as scaffolds. The rule: **populate only after
the Part/Version that actually builds the depicted component — never
ahead of the build.**

Each diagram's "Populate At" field governs when it gets filled in.
At the start of every new version, check the 01_Architecture/README.md
trigger table and update any diagram now due before starting Sprint work.

Current state: all 9 diagrams show the v1 target design but the physical
environment was lost in reset #2. All hosts read [NOT BUILT] until rebuilt
and re-verified.

---

## SIGN-OFF RUBRIC (TCS01 §2.7)

A version is signed off when, and only when:
1. Every Critical test case: Pass.
2. Every High test case: Pass.
3. No open Critical or High defect.
4. Regression Pack (all prior versions' Critical + High cases) re-run
   and passing.
5. Reviewer/approver and date recorded in the sign-off table.
6. SetupDoc-v<N>.md complete and followed start-to-finish.
7. backupConfig baseline captured.
8. Smoke test passes.

Sprint 8 (FaultDrill-v<N>.md) does NOT gate sign-off.

---

## SETDOWN DISCIPLINE

Every SetupDoc-v<N>.md must include:
- Section 3 Pre-Deployment Checklist with a 01_Architecture diagram
  check as the very first item
- §4 Step-by-Step Configuration (Admin Console AND wsadmin paths for
  every WAS admin action — both paths required on every version)
- §5 Verification Steps that cite TestCases-v<N>.md by filename
- §6 Rollback Procedure with exact steps
- §8 Sign-off table with "Reviewed by" and "Approved date" filled in
  (self-review counts)

---

## ENVIRONMENT & INFRASTRUCTURE SUMMARY

Lab host: VMware Workstation on AMD Ryzen 5 7530U (6c/12t), 16 GB RAM
(14.8 GB usable), ~234 GB free storage.

Network: 192.168.10.0/24, domain digistack.cloud, default-deny.
Zones: DMZ (VLAN10) → App (VLAN20) → Data (VLAN30); Mgmt separate.

Key ports: 22 (SSH, admin only), 80/443 (IHS), 9060/9043 (WAS Admin
Console), 9080/9443 (WAS App), 8080 (Tomcat), 1414 (MQ), 5432
(PostgreSQL), 9090/3000/9200/5601 (monitoring stack).

Deployment dependency order: VMs → PostgreSQL → MQ → CBS → PaymentHub →
NotificationService → ReportingService → Portal → BranchPortal →
CardPortal → MobileBanking (Tomcat) → ATMSimulator (Tomcat).

Concurrent-VM budget: maximum 4–5 VMs at once. Typical P01 session
(dsb-dmgr, dsb-node02, dsb-ihs, dsb-db) = ~7–8 GB RAM / ~4 vCPU.

---

## HOW SESSIONS WORK

**At the start of every session**, I will tell Claude where we are
(Part, Version, Sprint). Claude should:
1. Acknowledge the current state without re-summarising the whole project.
2. Ask a single clarifying question only if the next task is genuinely
   ambiguous.
3. Proceed directly to producing the sprint content.

**When I say "continue sprint"**, Claude moves to the next sprint or
phase immediately without asking for permission.

**Progress tracking**: I maintain Progress_Log.md, SESSION_STATE.md,
and the active folder's README.md myself after each session. Claude
reminds me to update these files at the end of each signed-off version
but does not track them independently across sessions.

**Sensitive documents**: STD, STDGAP01, SOE01, CAP01, RACI01, IDX, ARCH01,
ARCH02, and the active Part's roadmap + sprint plan files are the
governing sources. If anything Claude produces appears to conflict with
these documents, the documents win and Claude corrects itself.

---

## OPEN ITEMS REQUIRING MY DECISION (carried from Progress_Log.md)

1. **STD/STDGAP01 drift (P01 carve-out)**: STD v1.14 already corrects
   this — 8 sprints applies project-wide including P01. No action needed.

2. **FaultDrill-v<N>.md in Deliverables**: Every version's Deliverables
   bullet in P01/P02/P03 Sprint Plans needs `FaultDrill-v<N>.md` added
   as a one-line addition. Still pending as a mechanical edit.

3. **Three unscoped UI elements** (Business toggle, Open an Account,
   Forgot Password): Currently rendered disabled/"Coming soon" in the UI.
   No target version assigned. No action needed until explicitly scoped.

---

## QUICK REFERENCE — P01 VERSION SEQUENCE

| Version | WebSphere Topic | New App Feature |
|---------|----------------|-----------------|
| v1 | First EAR deployment | Static Home page + app_config DB read |
| v2 | JVM startup, sessions | Login/Logout |
| v3 | Enterprise layering, ClassLoader | Deposit/Withdraw |
| v4 | Update/Rollback/Lifecycle | Trivial UI change (v4 label) |
| v4.5 | IHS standalone era | Wire IHS to standalone AppServer |
| v5 | Clustering, session replication | None (reuses v3 app) |
| v6 | DMgr/federation/wsadmin | Freeze/Unfreeze |
| v7 | JDBC/DataSource/JNDI | Migrate to JNDI pool |
| v8 | IHS cluster era | Regenerate plugin for cluster; static assets; custom 404/500 |
| v9 | Session management | Session timeout |
| v10 | Users/Groups/Roles | Gate Freeze/Unfreeze behind Administrator role |
| v11 | SSL (web tier) | All pages → HTTPS |
| v12 | SSL end-to-end + mTLS | Full hop chain SSL, mTLS on 1 hop |
| v13 | JavaMail/JNDI Mail Session | Withdraw triggers email |
| v14 | JVM heap tuning | Transaction Report (large, heap stress) |