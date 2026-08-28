# DigiStack Bank — Context Pack v1.0
# Replaces: STD, STDGAP01, SOE01, CAP01 per-session uploads
# Authority: IDX → STD → STDGAP01 → Part files (higher wins on conflict)

---

## NDS01 — Non-Developer Delivery Standard
Applies to every response on this project. No exceptions ever.

Rule 1 — Full code, not snippets.
Every Java class, JSP, SQL script, wsadmin/Jython script, shell script
is a complete runnable file — every import, every closing brace, every
full file path. Never a fragment the reader must complete.

Rule 2 — Step-by-step, no assumed knowledge.
Every instruction is numbered and states: the exact tool/screen/command,
the exact input/value to enter, the expected visible result. Never
"install X" or "configure Y" without stating exactly how — specific
click-path, command, or file edit required. Applies to: WebSphere Admin
Console navigation, wsadmin/Jython, SQL/DDL, Linux/VM setup, Git.

Rule 3 — Concepts explained inline.
Any unfamiliar term (EAR, WAR, JNDI, JDBC, classloader, connection pool,
DataSource, DDL, DML, mTLS, DLQ, SIBus, MDB, JAAS, LTPA, FFDC, PMI,
JMX, etc.) gets a plain-language explanation the first time it appears
in a sprint or response. A new session does not assume prior explanations
carried over silently — brief reminder preferred.

Rule 4 — Database work never left as an exercise.
Every SQL migration script (V<N>__description.sql) is written in full:
complete CREATE TABLE / ALTER TABLE / seed-data statements. The
corresponding rollback script (/db/rollback/) is also written in full.

Rule 5 — Verification steps are concrete.
Expected result = the exact screen, log line, HTTP status code, or
console output to look for. Never vague ("it should work").

Rule 6 — No regression. Applies to every sprint in P01–P10 forever.

Rule 7 — Both Admin Console AND wsadmin steps, always.
For every WebSphere configuration task in every sprint, deliver:
  (a) Admin Console (GUI) steps — exact click path, field values,
      expected screen result after each step
  (b) wsadmin (Jython) steps — exact script or command, expected
      output shown inline
Never deliver only one method. Both paths are required every time,
no exceptions, for every version in P01–P10.

---

## Sprint Structure — 8 Sprints Per Version (ALL versions, v1–v78)
No exceptions. Source: STDGAP01 TCS02.3 + STD v1.14.

Sprint 1–4   Build the feature / infra
Sprint 5     Package and Deploy → produces digistack-<app>-v<N>.ear
Sprint 6     Test Cases → produces TestCases-v<N>.md
Sprint 7     Sign-off → produces SetupDoc-v<N>.md + backupConfig + smoke
Sprint 8     Fault Injection + Incident → produces FaultDrill-v<N>.md
             NON-GATING: does not block sign-off at Sprint 7

Sprint 8 — Three Phases, Strictly Separated (PIS01/FIS01)

Phase 1 — Fault Injection
Real, specific fault injected into the live environment. Exact steps
given per NDS01 Rules 1 and 2. The fault must be realistic and tied to
that version's WebSphere/application topic. Must be diagnosable using
only tools that version's build has established (Admin Console, wsadmin,
logs, DB/MQ tools, Linux commands per SOE01 §12 standard paths).
Nothing about the fault's effect, symptom, or cause is stated yet.

Phase 2 — Incident Ticket (triggered by "continue sprint")
Generated only from the real fault injected in Phase 1. Uses this exact
format:

  INCIDENT ID:
  SEVERITY:
  TIME:
  APPLICATION/SERVICE:
  BUSINESS IMPACT:
  CUSTOMER/BUSINESS SYMPTOM:
  INITIAL ALERT/TICKET:
  OBSERVED ERROR:
  SCOPE:
  NOT AFFECTED:
  RECENT CHANGE: (only if appropriate — never states the cause)
  STARTING EVIDENCE:
  STOP HERE.

Hard rules: NO root cause, NO solution, NO troubleshooting steps, NO
commands, NO hints anywhere in this ticket. If asked mid-investigation
"is it X?" — confirm/deny that exact hypothesis only, nothing more.

Phase 3 — Investigation (person-driven)
Trainee investigates live using real tools. Claude answers factual
questions about observations without revealing root cause. Proportional
hints if explicitly requested. RCA only on explicit request, as a
separate message. Environment restored to known-good state before
Sprint 8 is considered closed.

FaultDrill-v<N>.md location: /docs/faultdrills/

---

## Sign-off Rubric (STDGAP01 TCS01 §2.7)
A version is signed off only when ALL of these are true:
1. Every Critical test case: Pass
2. Every High test case: Pass
3. No open Critical or High defect remaining
4. Regression Pack (all prior versions' Critical + High cases) re-run
   and passing on current build
5. Reviewer name and approved date recorded in sign-off table
6. SetupDoc-v<N>.md complete and followed start-to-finish (not skimmed)
7. backupConfig baseline captured
8. Smoke test passes

Sprint 8 / FaultDrill does NOT gate sign-off.

---

## SetupDoc Required Sections (STDGAP01 SDD01)
Every SetupDoc-v<N>.md must contain all of these:

§1 Overview — 2–3 sentences on what this version adds
§2 VM Setup — reference SOE01, call out version-specific items only
§3 Pre-Deployment Checklist — first item is ALWAYS the 01_Architecture
   diagram check (open that folder's README, check Populate At triggers,
   update anything now due, tick the box)
   Also: previous SetupDoc verified, VM snapshot taken, Git branch created
§4 Step-by-Step Configuration
   4.1 WebSphere Admin Console Steps — numbered, exact clicks/fields,
       expected result shown inline after each step
   4.2 wsadmin / Command-Line Steps — exact commands, expected output
       shown inline. BOTH paths (GUI AND wsadmin) required every version.
   4.3 Database Changes — exact migration script command + expected output
   4.4 Application Deployment — Maven build command + Admin Console deploy
§5 Verification Steps — cross-references TestCases-v<N>.md by filename
§6 Rollback Procedure — exact steps (VM snapshot restore OR manual undo)
§7 Known Issues / Troubleshooting — populated as issues are found
§8 Sign-off table — Setup completed / Verification passed /
   Documentation reviewed / Reviewed by / Approved date

---

## Test Case Format (STDGAP01 TCS01)
ID format: TC-v<N>-<seq>   e.g. TC-v1-03

Columns: ID | Description | Type | Priority | Steps | Expected Result |
         Actual Result | Status | Notes

Priority model:
  Critical  Core money-movement or CBS single-writer-rule integrity.
            Must pass — zero exceptions — at every promotion gate.
  High      Primary feature path for the version.
            Must pass before UAT promotion.
  Medium    Secondary path, edge case, non-blocking UX.
            Documented if failed; does not block promotion.
  Low       Cosmetic. Documented if failed; never blocks promotion.

7 test types (TCS02.1):
  Functional   Proves a feature works as specified
  Negative     Proves something is correctly rejected/blocked
  Regression   Re-run of a prior version's Critical/High case
  Smoke        Minimal go/no-go subset (login, balance, Fund Transfer)
  Integration  Code connects correctly to DB/WAS/JNDI after deployment
  System/SIT   Entire flow works end-to-end, not just one class
  Performance  Only when CAP01 gives a numeric target for this version

Regression Pack = every prior version's Critical + High cases re-run.

---

## Naming Conventions (STD v1.10 — LOCKED, no exceptions)

WAS profile/cell/node/cluster pattern:
  <env><appcode><region><role><seq>

  env      = dev / test / uat / prod / dr
  appcode  = dsb
  region   = in (India); sg/ae added at P06
  role     = cell / appserver / dmgr / node / appcluster / corecluster
             (spelled out fully — no abbreviations)
  seq      = 01, 02…

This lab:
  Cell                devdsbincell01
  AppServer profile   devdsbinappserver01
  Server name         server1   (no prefix on server name — exception)
  DMgr profile        devdsbindmgr01          (v5+)
  Node1               devdsbinnode01          (v5+)
  Node2               devdsbinnode02          (v5+)
  AppCluster          devdsbinappcluster01    (v5+)

VM hostnames (unchanged, separate from WAS naming):
  dsb-dmgr   dsb-node01   dsb-node02   dsb-ihs    dsb-db
  dsb-mq     dsb-monitor  dsb-elk      dsb-tracing dsb-tomcat

IP addresses:
  dsb-dmgr  192.168.10.10   dsb-node02  192.168.10.11
  dsb-ihs   192.168.10.20   dsb-db      192.168.10.30
  dsb-mq    192.168.10.31   dsb-monitor 192.168.10.40
  dsb-elk   192.168.10.41   dsb-tracing 192.168.10.42
  dsb-oracle 192.168.10.32  dsb-tomcat  192.168.10.12
EAR/WAR naming:   digistack-<app>-v<N>.ear   (.war for Tomcat)
SQL migration:    V<N>__<description>.sql
Rollback:         /db/rollback/
Git branches:     main / develop / feature/* / hotfix/* / release/*
Commit format:    feat: / fix: / docs: / refactor: / test: / chore:
DB constraints:   pk=id / fk=<table>_id / idx=idx_<table>_<col>
                  chk=chk_<table>_<rule> / uq=uq_<table>_<col>

JNDI resources:
  DataSource    jdbc/BankDS            (P01 v7 through P02 v22 —
                                        PostgreSQL, decommissioned
                                        at P03 v23 Sprint 4)
                jdbc/OracleDS          (P02 v22.5 migration window
                                        only — migration utility
                                        DataSource, retired at v23)
                jdbc/CBSDataSource     (P03 v23 onward — Oracle
                                        DIGISTACK_CBS, sole DB
                                        DataSource from v23)
  Mail Session  mail/BankMailSession   (P01 v13)
  MQ resources  JNDI-bound — never hardcoded (P02 v19+)

Credentials: NEVER hardcoded. Always externalized via JAAS Auth Alias.
No plaintext passwords in config, code, SetupDocs, or Git.
Per-environment config: application-<env>-<region>.properties files.

---

## Version Pins (target/placeholder — NOT CONFIRMED after reset #2)
All reverted 2026-08-25. Promote to CONFIRMED only once that version's
SetupDoc-v<N>.md §4.1 records the actual installed version.

  WebSphere ND          9.0.5.28   (confirms at P01 v1 sign-off)
  IBM HTTP Server       9.0.5.28   (confirms at P01 v8)
  Web Server Plug-ins   9.0.5.28   (confirms at P01 v8)
  IBM Install Manager   1.9.x      (confirms at P01 v1)
  Java SDK              IBM Java 8 (SDK 8.0), bundled with WAS ND
  PostgreSQL            16         (confirms at P01 v1 sign-off;
                                    decommissioned at P03 v23)
  Oracle Database       21c XE     (confirms at v22.5 sign-off)
  Oracle JDBC Driver    ojdbc8.jar (IBM Java 8 compatible)
  IBM MQ                Advanced for Developers 9.3.x/9.4.x (P02 v19)
  OS                    RHEL 8.x (confirmed on dsb-dmgr at P01 v1 Sprint 2)

---

## Lab Hardware (SOE01 §1a + CAP01 §1a — use these, not enterprise figures)
Host: VMware Workstation, AMD Ryzen 5 7530U (6c/12t, 2.0 GHz),
      16 GB RAM (14.8 GB usable), ~234 GB free storage.

VM inventory and power-on schedule:
  VM            Zone  IP       vCPU  RAM     Disk   When On
  dsb-dmgr+N1   App   .10.10   2     3 GB    40 GB  Almost always
  dsb-node02    App   .10.11   2     2 GB    40 GB  Clustering/HA only
  dsb-ihs       DMZ   .10.20   1     1 GB    20 GB  Web-tier topics
  dsb-db        Data  .10.30   2     2 GB    40 GB  Almost always
  dsb-mq        Data  .10.31   1     1.5 GB  20 GB  P02 v19 onward
  dsb-monitor   Mgmt  .10.40   1     1.5 GB  30 GB  P04 v31 onward
  dsb-elk       Mgmt  .10.41   1     1.5 GB  40 GB  P04 v32 onward
  dsb-tracing   Mgmt  .10.42   1     1 GB    10 GB  P04 v33+, co-locate
  dsb-oracle    Data  .10.32   2     4 GB    60 GB  P02 v22.5 onward (Oracle 21c XE dedicated VM, never shared with dsb-db)
  dsb-tomcat    App   .10.12   1     1 GB    20 GB  P03 v26+ only

HARD RULE: Never run more than 4–5 VMs simultaneously.
Typical P01 session (dsb-dmgr, dsb-node02, dsb-ihs, dsb-db):
~7–8 GB RAM / ~6 vCPU — within budget.

Storage: thin-provisioned VMDKs ONLY (never thick). Delete/consolidate
snapshots immediately after each version sign-off.

Lab-adjusted JVM heap (Xms = Xmx — fixed, avoids resize pauses):
  Node1/App member (on DMgr+Node1 VM)   1 GB    within 3 GB VM
  DMgr (sharing same VM as Node1)       0.75 GB within 3 GB VM
  Node2/CBS-equivalent member           1.25 GB within 2 GB VM
Standard JVM args (all nodes):
  -Xms<above> -Xmx<above>
  -XX:+HeapDumpOnOutOfMemoryError
  -XX:HeapDumpPath=/var/log/was/heapdumps/
  -Xverbosegclog:/var/log/was/gc/gc_%Y%m%d.log
  -Dcom.ibm.websphere.security.util.authCacheTimeout=600

Lab-adjusted connection pool (P01 v7 worked example):
  2 members × 20 connections each = 40 at peak
  max_connections=100 on 2 GB PostgreSQL VM → comfortable headroom

Lab log retention:
  Prometheus metrics   14 days   OpenSearch logs  30 days
  Jaeger traces        7 days    SystemOut local  3 days rotation
  pg_dump              last 2    Restore tests    last 2

---

## Key Ports (STD — every port here must exist in STD before use)
  22    SSH (admin subnet only)
  80    IHS HTTP
  443   IHS HTTPS
  9060  WAS Admin Console
  9043  WAS Admin Console secure
  9080  WAS App HTTP
  9443  WAS App HTTPS
  8080  Tomcat HTTP
  1414  MQ Listener
  5432  PostgreSQL (P01–v22 only; closed at P03 v23 Sprint 4)
  1521  Oracle 21c XE (v22.5 onward)
  9090  Prometheus
  3000  Grafana
  9200  OpenSearch
  5601  OpenSearch Dashboards

Network: 192.168.10.0/24, domain digistack.cloud, default-deny.
Zones: DMZ (VLAN10) → App (VLAN20) → Data (VLAN30); Mgmt separate.

---

## Databases
  digistack_bank    Early-build shared DB (P01–v22)
                    Engine: PostgreSQL 16
                    Host: dsb-db, port 5432
                    Decommissioned at P03 v23 Sprint 4

  digistack_cbs     CBS-dedicated DB from v22.5 onward
                    Engine: Oracle 21c XE
                    PDB: DIGISTACK_CBS
                    Host: dsb-oracle (192.168.10.32), port 1521
                    Schema owner: DIGISTACK_APP
                    Note: Oracle NEVER installed on dsb-db.
                    dsb-db (PostgreSQL) is decommissioned at P03 v23 Sprint 4.

Backup discipline:
  PostgreSQL (P01–v22):  pg_dump weekly, restore test every
    15 days, retain last 4 dumps and last 2 restore tests.
  Oracle (v22.5 onward): expdp weekly, restore test every
    15 days (impdp to scratch schema), retain last 4 dump
    files and last 2 restore-test results.

---

## Architecture Rules (non-negotiable for the whole roadmap)

1. CBS sole writer of digistack_cbs from P03 v23 onward. No exceptions.
   All other apps call CBS via REST/SOAP/EJB or consume events only.

2. Presentation-only channels never touch the database directly.
   Portal, Mobile, ATM, Card Portal, Branch Portal — all call CBS only.

3. CBS stays a single EAR for the entire roadmap. Never decomposed.

4. No distributed XA transactions across EARs. Payment Hub uses
   Saga/compensating-transaction pattern only.

5. WebSphere practice first. Every choice defaults to more WAS practice
   over more banking realism.

6. Nothing silently retired. Every superseded component gets an explicit
   decommission step in the relevant SetupDoc.

7. Version Numbering Freeze. Once a version is signed off, its number
   never changes. Renumbering only before implementation.

8. Write-as-you-go. SetupDoc and TestCases written during the sprint,
   not reconstructed from memory after.

---

## 01_Architecture Diagram Rule
Nine diagram scaffold files exist. Rule: populate only AFTER the
Part/Version that builds the depicted component. Never ahead of the build.
At the start of every version, check each diagram's "Populate At" field
in 01_Architecture/README.md and update anything now due BEFORE Sprint work.

## 01_Architecture Diagram Rule (addendum — Part-Start Whole-Picture View)
At the start of every Part (P01, P02, P03, etc.), before Sprint work
begins: read through ALL versions listed in that Part's roadmap file
(e.g. P01_Foundation.md's full v1–v14 sequence, not just the next
version due) to determine what the Part will build end-to-end. Then
produce ONE whole-picture diagram for the Part using that full read:

                 DIGISTACK BANK
                       |
       +---------------+---------------+
       |               |               |
       v               v               v
   NETWORK           SERVERS        DEPLOYMENT
       |               |               |
   Network.md      VM_Layout.md   Deployment.md
       |
       +-------------------------------+
                                       |
                                       v
                              WebSphere ND
                                       |
                       +---------------+---------------+
                       |                               |
                       v                               v
                   REQUESTS                         CLUSTER
                       |                               |
                 Request_Flows.md              Cluster_Architecture.md
                       |
                       v
                 APPLICATION
                       |
             +---------+---------+
             |                   |
             v                   v
         PostgreSQL            IBM MQ
             |                   |
             v                   v
          DB ER.md            MQ.md
        SECURITY
           |
           v
     Security.md
        FUTURE
           |
           v
       DR.md

Mark each diagram node populated (✅) or not-yet-populated (❌) as of
the Part's START — i.e. only nodes already built by a PRIOR Part are
✅; everything this Part itself will build across its own version range
is ❌ until each version is actually signed off (per the base Diagram
Rule's "never populate ahead of the build"). This one diagram is the
Part's whole-picture reference for clarity going in — updated
incrementally node-by-node as each version inside the Part completes,
not regenerated from scratch each time.
---

## P01 Technical Debt Schedule
  v1  → v7    Direct JDBC replaced by JNDI DataSource/connection pooling
  v4.5 → v8   plugin-cfg.xml standalone era → regenerated for cluster
  v4.5 → v8   No custom error pages at IHS → added
  v4.5 → v8   No static assets served at IHS → added
  v4.5 → v11  HTTP only at IHS → HTTPS enforced
  v6  → v10   Freeze/Unfreeze open to all → gated behind Administrator role
  v11 → v12   Web-tier SSL only → end-to-end SSL/mTLS on ≥1 internal hop

---

## P01 Version Sequence (quick reference)
  v1   First EAR deployment              Static Home page + app_config DB read
  v2   JVM startup, sessions             Login/Logout
  v3   Enterprise layering, ClassLoader  Deposit/Withdraw
  v4   Update/Rollback/Lifecycle         Trivial UI change (v4 label only)
  v4.5 IHS standalone era                Wire IHS to standalone AppServer
  v5   Clustering, session replication   None (reuses v3 app)
  v6   DMgr/federation/wsadmin           Freeze/Unfreeze
  v7   JDBC/DataSource/JNDI              Migrate to JNDI connection pool
  v8   IHS cluster era                   Regenerate plugin; static assets; 404/500
  v9   Session management                Session timeout
  v10  Users/Groups/Roles                Gate Freeze/Unfreeze → Administrator only
  v11  SSL at web tier                   All pages → HTTPS
  v12  SSL end-to-end + mTLS             Full hop chain; mTLS on ≥1 internal hop
  v13  JavaMail/JNDI Mail Session        Withdraw triggers email notification
  v14  JVM heap tuning                   Transaction Report (heap stress)

---

## Golden Rules (STD)
Never skip documentation
Never deploy without backup
Never hardcode credentials
Always validate deployments
Always test rollback
Keep Git history clean
Follow naming standards
Maintain audit trails
Keep environments consistent
Document every change