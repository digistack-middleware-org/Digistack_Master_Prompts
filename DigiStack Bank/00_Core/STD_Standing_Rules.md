ID: STD
Version: 1.10
Status: Active

Title: Standing Rules

Imports:
IDX

Exports:
Metadata Block Standard
Database
Application Server
OS
MQ
Git
Applications
Deployables
Environments
Naming
Ports
Security
Backup

Used By:
ARCH01
ARCH02
P01
P02
P03
P03.1
P03.2
P04
P05
P06
P07
P07.1
P08
P08.1
P09
P09.1
P10
P10.1

Next:
ARCH01

---

Metadata Block Standard
------------------------
Every file in this project opens with:

ID: <file ID>
Version: <x.y>
Status: <Active | Draft | Superseded>

Title: <full title>

Imports:
<file IDs this file depends on>

Exports:
<what this file defines/provides>

Used By:
<file IDs that depend on this file>

Next:
<file ID that logically follows>

Rule: no prose in this block. IDs only, one per line.

Import Rule (Mandatory vs Conditional)
-----------------------------------------
Mandatory Imports
-----------------
IDX  (Master Index)
STD  (Standing Rules)

Conditional Imports
-------------------
ARCH01   → Only if the file needs enterprise/business architecture.
ARCH02   → Only if the file needs deployment/solution architecture.
STDGAP01 → Only if the file cites doc 03/04/05/06/07's content directly
           (in practice: every Part from P01 onward, since SetupDoc/
           TestCases/Promotion checklists are used throughout).
SOE01    → Only if the file provisions or configures a VM/node directly
           (P01, P04's new monitoring/ELK/tracing VMs, P08's Ansible
           playbooks, P09's EC2 sizing, P10's container resource limits).
CAP01    → Only if the file sizes JVM heap, thread pools, JDBC pools, MQ
           depth, or cluster scale-out (P01, P04, P05's DR site sizing,
           P06's regional VM sizing, P09, P10).
RACI01   → Only if the file exercises incident response, escalation, or
           ownership during a failure/drill (P03.1, P04 v35, P05 v36-38,
           P06 v43's capstone incident response).

Version 1.5 change note (2026-07-28): Reverted the 2026-07-27 WebSphere
ND version-pin “correction” (9.0.5.28) back to the 9.0.3 placeholder —
that edit cited P01 v1's SetupDoc-v1.md, which does not exist yet (P01
is still “Not Started” per Progress_Log.md and its own README). Also
added port 22 (SSH) to the Ports table below, since SOE01's firewall
table (§8) already opens it and SOE01's own rule requires every port it
opens to already exist here.

Version 1.6 change note (2026-07-28, same day, later edit): Project
owner explicitly requested 9.0.5.28 be adopted as the intended *target*
package standard ahead of Sprint 1 — distinct in provenance from the
2026-07-27 edit reverted above. That prior edit was reverted because it
falsely cited an already-completed install (SetupDoc-v1.md) that did not
exist. This edit makes no such claim: it is a forward-looking planning
decision, made explicitly and knowingly before any VM has been built,
and the pin below remains marked "placeholder, unconfirmed" exactly as
before. It is only promoted to "confirmed" once P01 v1 is actually
installed and SetupDoc-v1.md §4.1 records the real result — if the
actual install ever differs from 9.0.5.28, this document is corrected to
match reality, not the other way around. Also added explicit target
pins for IHS/Web Server Plug-ins version-match and IBM Installation
Manager (see SOE01 §9, updated in the same pass) — these were
previously "latest at install time" with no concrete target number.

Version 1.7 change note (2026-07-29): Added NDS01 — Non-Developer
Delivery Standard. Project owner has confirmed they are not a developer
and has no prior coding/database/WebSphere admin background. Every
future Sprint, Version, and ad hoc task must be delivered as complete,
runnable artifacts with full step-by-step instructions — never partial
snippets, "you'll need to..." summaries, or content that assumes the
reader can fill in gaps independently. This governs Claude's own
behavior when assisting on this project, not a technical/architectural
change — no other section of this document is affected.

Version 1.7 change note (2026-07-29): Added "Build Tool" cross-reference
section pointing to ARCH02 §2a (new Maven Project Structure convention).
STD names the tool; ARCH02 owns the actual module layout — consistent
with this document's existing Import Rule split (ARCH02 = deployment/
solution structure, STD = naming/tooling conventions).

Version 1.8 change note (2026-07-30): P01 Version 1 signed off — all 6
Sprints complete, TestCases-v1.md's 13 test cases all Pass (Critical and
High), backupConfig baseline captured. Per this document's own
promotion rule (see the 2026-07-28 change notes above), the WebSphere ND
version pin is now promoted from "target/placeholder, unconfirmed" to
CONFIRMED — 9.0.5.28, installed on dsb-dmgr, source record is
SetupDoc-v1.md §4.1/§8. The Java SDK pin (bundled/certified alongside
WAS ND) is confirmed in the same pass. IHS, Web Server Plug-ins, and IBM
Installation Manager's exact version remain target/unconfirmed — those
are validated at P01 v8 (IHS install) and P07 (migration tooling), not
this version. PostgreSQL's exact installed version (13.x) should be
confirmed against `psql --version` on dsb-db and this document corrected
if it differs from the "13+" target.

Version 1.9 change note (2026-08-05): Added PIS01 (Production Incident
Simulation Standard) and FIS01 (Fault Injection Standard) as new
addenda, mirroring NDS01's placement/format. Effective P02 onward
(v15-v78) — NOT retroactive to P01 (v1-v14), which remains signed off
under its original 6-sprint structure per version. From v15 onward,
every version's sprint count changes from 6 to 8: Sprint 7 (Incident
Simulation, per PIS01) and Sprint 8 (Fault Injection Lab, per FIS01) are
appended after the existing Sprint 6 (smoke test/sign-off). This is a
project-owner-directed structural change, not a judgment call — see full
addenda at the bottom of this document.

Version 1.10 change note (2026-08-06): Project owner defined a new
profile/cell/node/cluster-naming convention, replacing the earlier
<env><region><appcode><tier><seq> pattern (e.g. dindsbap01) originally
drafted in the Naming section. New locked pattern, effective
immediately, applied project-wide from P01 v1 forward (no exceptions
carried):

    <env><appcode><region><role><seq>

Elements:
  <env>     = dev / test / uat / prod / dr (per STD's Environments list)
  <appcode> = dsb (DigiStack Bank)
  <region>  = in (India) — extends to sg (Singapore) / ae (Dubai/UAE)
              once P06 multi-region work begins; pattern requires no
              redesign at that point, just a new region code
  <role>    = cell / appserver / dmgr / node / appcluster / corecluster
              (spelled out, not abbreviated — deliberate readability
              choice)
  <seq>     = 01, 02... sequential

This convention now covers CLUSTER names as well, superseding the
plain "AppCluster"/"CoreCluster" names in this document's Clusters
section below.

P01 v1 profile (recreated 2026-08-06 to match this convention, after an
initial profile using different ad hoc names was deleted before any
app was deployed):
  Cell               = devdsbincell01
  AppServer profile  = devdsbinappserver01
  Server name        = server1 (unchanged — no env/region/appcode
                        prefix applied to server name, kept simple)

Future names (v5 DMgr/Node federation/clustering onward):
  DMgr profile  = devdsbindmgr01
  Node1         = devdsbinnode01
  Node2         = devdsbinnode02
  AppCluster    = devdsbinappcluster01
  CoreCluster   = devdsbincorecluster01 (reserved, unbuilt)

Worked examples at other env/region combinations (illustrative, not
yet built): PROD India AppCluster = proddsbinappcluster01; DEV
Singapore AppCluster = devdsbsgappcluster01; PROD Dubai AppCluster =
proddsbaeappcluster01.

This supersedes the profile-naming example previously given in this
document's Naming section (dindsbap01/dindsbdm01) — that example is
now historical/superseded, not the active convention. VM hostnames
(STD's existing dsb-dmgr/dsb-node01/etc. table) are UNCHANGED and
remain a separate, still-open decision (see Master Index Open
Decisions: "should VM hostnames encode region before P06") — this
naming change applies to WebSphere profile/cell/node/cluster names
only, not VM hostnames.

Dependency Matrix
-----------------
(Full 8-column copy, kept identical to IDX's own copy of this matrix —
this is not an independent source of truth; if the two ever disagree,
IDX governs and this table must be corrected to match. Previously this
table only carried the first 4 columns while IDX's carried 8 — corrected
2026-07-24 to restore the "kept in sync" claim to actual fact; values for
the 4 original columns were unchanged, the 4 added columns (STDGAP01,
SOE01, CAP01, RACI01) are copied verbatim from IDX and cross-checked
against each of those documents' own "Used By" lists. Corrected again
2026-07-25: this table was still missing 5 rows (P03.2, P07.1, P08.1,
P09.1, P10.1) that IDX's copy already carried -- added below, verbatim
from IDX, restoring the "identical copy" claim to actual fact a second
time. See also the corresponding "Used By" corrections in STDGAP01,
SOE01, CAP01, and RACI01 themselves, made in the same pass.)

Part  | IDX | STD | ARCH01 | ARCH02 | STDGAP01 | SOE01 | CAP01 | RACI01
P01   |  ✅ |  ✅ |   ❌    |   ❌    |    ✅     |   ✅   |   ✅   |   ❌
P02   |  ✅ |  ✅ |   ❌    |   ✅    |    ✅     |   ❌   |   ❌   |   ✅  ← changed
P03   |  ✅ |  ✅ |   ✅    |   ✅    |    ✅     |   ❌   |   ❌   |   ✅  ← changed
P03.1 |  ✅ |  ✅ |   ✅    |   ✅    |    ✅     |   ❌   |   ❌   |   ✅
P03.2 |  ✅ |  ✅ |   ✅    |   ✅    |    ✅     |   ❌   |   ❌   |   ✅
P04   |  ✅ |  ✅ |   ❌    |   ✅    |    ✅     |   ✅   |   ✅   |   ✅
P05   |  ✅ |  ✅ |   ❌    |   ✅    |    ✅     |   ❌   |   ✅   |   ✅
P06   |  ✅ |  ✅ |   ❌    |   ✅    |    ✅     |   ❌   |   ✅   |   ✅
P07   |  ✅ |  ✅ |   ✅    |   ✅    |    ✅     |   ❌   |   ❌   |   ✅  ← changed
P07.1 |  ✅ |  ✅ |   ✅    |   ✅    |    ✅     |   ❌   |   ❌   |   ✅
P08   |  ✅ |  ✅ |   ❌    |   ✅    |    ✅     |   ✅   |   ❌   |   ✅  ← changed
P08.1 |  ✅ |  ✅ |   ❌    |   ✅    |    ✅     |   ❌   |   ❌   |   ✅
P09   |  ✅ |  ✅ |   ✅    |   ✅    |    ✅     |   ✅   |   ✅   |   ✅  ← changed
P09.1 |  ✅ |  ✅ |   ✅    |   ✅    |    ✅     |   ❌   |   ✅   |   ✅
P10   |  ✅ |  ✅ |   ✅    |   ✅    |    ✅     |   ✅   |   ✅   |   ✅  ← changed
P10.1 |  ✅ |  ✅ |   ✅    |   ✅    |    ✅     |   ✅   |   ✅   |   ✅

Dependency Matrix correction (2026-08-05): RACI01 column flipped to ✅
for P02, P03, P07, P08, P09, P10. Trigger: STD's own Import Rule marks
RACI01 conditional on "the file exercises incident response, escalation,
or ownership during a failure/drill." As of STD v1.9's PIS01 addendum,
every version from P02 onward (v15-v78) runs a Sprint 7 incident-
response drill — the condition is now met project-wide for these Parts,
matching the standard their `.1` interview-book companions already met.
P01 is unaffected (PIS01 doesn't apply retroactively to v1-v14).

Database
--------
PostgreSQL
digistack_cbs (single dedicated DB, sole writer: CBS)

Schemas (within digistack_cbs)
--------------------------------
banking
audit
reporting

Application Server
------------------
WebSphere ND 9.0.5.28 (target — not yet installed; P01 v1 reset to
not-started 2026-08-04, see v1.10 change note above)

Secondary App Server
---------------------
Apache Tomcat (Mobile, ATM only)

OS
--
Rocky Linux 8.x / RHEL 8.x

Version Pins (target/placeholder, unconfirmed — P01 v1 reset to
not-started 2026-08-04; promote to CONFIRMED only once actually
installed and recorded in SetupDoc-v1.md)
----------------------------------------------------------------
WebSphere ND: 9.0.5.28 (target — not yet installed, unconfirmed; P01 v1
reset to not-started 2026-08-04)
Java SDK: IBM Java 8 (SDK 8.0), bundled/certified with WAS ND 9.0.5.28
(target — not yet installed, unconfirmed)
IBM HTTP Server (IHS): 9.0.5.28 (target — not yet installed; IHS is
P01 v8, still a forward-looking pin until then)
Web Server Plug-ins: 9.0.5.28 (target — same as above, pinned for real
at P01 v8)
IBM Installation Manager: 1.9.x (target — confirm the exact version
actually used for the WAS ND install against your install logs, and
correct this line if it differs)
PostgreSQL: 16 (target — project-wide standard, adopted 2026-08-04.
Not yet installed anywhere — P01 v1, which would have provisioned
dsb-db, has been reset to not-started per v1.10's change note.)
IBM MQ: Advanced for Developers, 9.3.x/9.4.x (target — not yet
installed, MQ is P02 v19)

Note: these were previously only "assumed pins" flagged in the Phase-1
Design Document as unconfirmed. Promoted to a project-wide standard
2026-07-25. A 2026-07-27 edit incorrectly "corrected" the WebSphere ND
pin to 9.0.5.28, citing P01 v1's SetupDoc-v1.md as the source of an
actual install — but P01 v1 had not been built at that time
(Progress_Log.md's Folder Tracker and 01_Application_Development's own
README both confirmed 0/14 versions complete, no SetupDoc-v1.md existed,
no Pause/Resume or Deviation entries logged). That edit was reverted on
2026-07-28 per this project's documentation discipline: a pin is only
promoted to "confirmed" once the version that produces it is actually
signed off, never before.

Later the same day (2026-07-28), the project owner explicitly requested
9.0.5.28 be re-adopted — this time as a knowing, forward-looking *target*
choice ahead of Sprint 1, not a claim that it's already installed. The
distinction matters: this edit makes no claim about SetupDoc-v1.md, which
still does not exist. The pin remains "placeholder, unconfirmed." It is
promoted to "confirmed" only once P01 v1 is actually built and
SetupDoc-v1.md §4.1 records the real installed version — re-validate at
that point, and correct this document if reality differs from 9.0.5.28.

MQ
--
IBM MQ
BANK.REQUEST.Q
BANK.RESPONSE.Q
BANK.NOTIFICATION.Q
BANK.AUDIT.Q

Git
---
main
develop
feature/*
hotfix/*
release/*

Build Tool
----------
Maven (multi-module per application — one Maven project per app, always)

Full module structure convention owned by ARCH02 §2a — not restated
here, per this project's "cross-reference, don't duplicate" discipline
(same pattern STD already uses for SQL Migration naming vs. DBS01's
Connection & Credentials content).

Commit Format
-------------
feat:
fix:
docs:
refactor:
test:
chore:

Applications
------------
InternetBanking      (WAS)    - customer web channel
CBS                  (WAS)    - core banking, sole writer of digistack_cbs
PaymentHub           (WAS)    - payment routing/coordination, never writes balances
NotificationService  (WAS)    - event consumer, alerts
ReportingService     (WAS)    - read-only reporting
BranchPortal         (WAS)    - teller/branch operations
CardPortal           (WAS)    - card lifecycle (issue/activate/block/PIN/hotlist)
MobileBanking        (Tomcat) - mobile.digistack.cloud
ATMSimulator         (Tomcat) - atm.digistack.cloud

Deployables
-----------
InternetBanking.ear
CBS.ear
PaymentHub.ear
NotificationService.ear
ReportingService.ear
BranchPortal.ear
CardPortal.ear
MobileBanking.war
ATMSimulator.war

Governing Rule
--------------
CBS is sole writer of digistack_cbs.
All other apps: read via REST/SOAP/EJB or consume events only.
No exceptions.

Architecture (reference — full detail in ARCH01)
----------------------------------------------------
                    Customer Channels
     ┌──────────────┬──────────────┬─────────────┬─────────────┐
     ▼              ▼              ▼             ▼
Internet Banking  Mobile Banking  ATM/POS    Card Portal / Branch Portal
(WAS/IHS)          (Tomcat)       (Tomcat)    (WAS/IHS)
     │              │              │             │
     └──────────────┴──────────────┴─────────────┘
                     │
                     ▼
              DigiStack CBS
         (sole writer of digistack_cbs)
                     │
     ┌─────────────┬──────────────┬──────────────┐
     ▼             ▼              ▼              ▼
 Customer      Accounts      Transactions    Products
                     │
                     ▼
              digistack_cbs Database

Clusters
--------
AppCluster    → superseded 2026-08-06, see v1.10: now devdsbinappcluster01
CoreCluster   → superseded 2026-08-06, see v1.10: now devdsbincorecluster01
              (reserved, unbuilt)

Nodes
-----
Node01
Node02

JDBC
----
BankDS          (pre-CBS-split builds)
CBSDataSource   (from CBS split onward)

Environments
------------
DEV
TEST
UAT
PROD
DR

Promotion Path
--------------
DEV → TEST → UAT → PROD → DR

Primary Key
-----------
id

Foreign Key
-----------
<table>_id

Index
-----
idx_<table>_<column>

Constraint
----------
chk_<table>_<rule>
fk_<table>_<ref_table>
uq_<table>_<column>

SQL Migration
-------------
V<N>__<description>.sql
Rollback: /db/rollback/

Backup
------
pg_dump weekly
Restore test every 15 days
Retain last 4 dumps, last 2 restore tests

VM Hostnames
------------
dsb-dmgr
dsb-node01
dsb-node02
dsb-ihs
dsb-db
dsb-mq
dsb-monitor
dsb-elk
dsb-tomcat        (Mobile/ATM host)

IP/VLAN Addressing + Resource Allocation (Lab Scheme)
-------------------------------------------------------
Domain: digistack.cloud
Subnet: 192.168.10.0/24
Zones: DMZ(VLAN10) → App(VLAN20) → Data(VLAN30); Mgmt separate.
Default-deny, explicit-allow firewall.
Host resource figures per SOE01 §1a / CAP01 §1a (lab-adjusted, not
generic enterprise sizing).

Host                     Zone   IP        vCPU   RAM     Disk (thin)   Power State
dsb-dmgr (+ Node1)       App    .10.10    2      3 GB    40 GB         Almost always on
dsb-node02               App    .10.11    2      2 GB    40 GB         On-demand (clustering/HA/failover topics only)
dsb-tomcat               App    .10.12    1      1 GB    20 GB         On when Mobile/ATM topics active
dsb-ihs                  DMZ    .10.20    1      1 GB    20 GB         On when web-tier routing exercised
dsb-db                   Data   .10.30    2      2 GB    40 GB         Almost always on
dsb-mq                   Data   .10.31    1      1.5 GB  20 GB         On-demand, P02 v19 onward
dsb-monitor              Mgmt   .10.40    1      1.5 GB  30 GB         On-demand, P04 v31 onward only
dsb-elk                  Mgmt   .10.41    1      1.5 GB  40 GB         On-demand, P04 v32 onward only

Totals if every VM ran simultaneously: 11 vCPU / ~13.5 GB RAM / 250 GB
disk — exceeds this host's realistic ceiling (16 GB RAM, 14.8 GB usable,
~234 GB free). Per SOE01 §1a's concurrent-VM budget: never run more than
4-5 VMs at once. A typical P01/P02-era session (dmgr+Node1, Node2, IHS,
PostgreSQL) totals ~7-8 GB RAM / ~4 vCPU — leaves headroom for host OS +
VMware Workstation overhead.

Storage discipline: thin-provisioned VMDKs only (never thick — 234 GB
free budget cannot absorb thick disks across 6+ VMs); consolidate/delete
VMware snapshots immediately after each version's sign-off; budget ~40 GB
per VM disk, not enterprise-scale 50 GB+.

Note: this addressing scheme was originally introduced only in the
Phase-1 Design Document as project-specific detail; the vCPU/RAM/Storage
columns are cross-referenced from SOE01 §1a (VM role table) and CAP01
§1a (Lab Hardware Sizing) — not new numbers, just merged into one table
here so IP allocation and resource sizing live together instead of
across three separate files.

Ports
-----
22    SSH (admin/bastion subnet only)
80    IHS HTTP
443   IHS HTTPS
9060  Admin Console
9043  Admin Console (secure)
9080  App (WAS)
9443  App (WAS secure)
8080  Tomcat HTTP
1414  MQ Listener
5432  PostgreSQL
9090  Prometheus
3000  Grafana
9200  OpenSearch
5601  OpenSearch Dashboards

Deployment Dependency Order
----------------------------
1. Infrastructure (VMs, network, IHS, Tomcat host)
2. Database (PostgreSQL)
3. MQ
4. CBS
5. PaymentHub
6. NotificationService
7. ReportingService
8. InternetBanking
9. BranchPortal
10. CardPortal
11. MobileBanking (Tomcat)
12. ATMSimulator (Tomcat)

Security
--------
HTTPS mandatory
SSL mandatory
LDAP
LTPA
RBAC
Least Privilege
MFA where applicable

Logging
-------
SystemOut.log
SystemErr.log
access_log
error_log
No manual deletion — rotation only

Config
------
No hardcoded values
.properties per environment
JNDI-bound resources only

Rollback
--------
Required before every promotion
backupConfig before every WAS change

Golden Rules
------------
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

===============================================================================
NDS01 — Non-Developer Delivery Standard
===============================================================================

Applies to: every Sprint, every Version, every ad hoc request across all
Parts (P01-P10) and all companion documents, for the life of the project.

Why This Exists
----------------
The project owner is not a software developer and has no assumed prior
knowledge of Java, SQL, WebSphere administration, Linux administration,
or Git. Every prior convention in this document (SQL Migration format,
wsadmin scripting, EAR/WAR packaging, etc.) still applies in full — this
section governs *how completely* that content gets delivered, not what
gets built.

Rule 1 — Full Code, Not Snippets
----------------------------------
Any code deliverable (Java class, JSP, servlet, XML config, SQL migration
script, wsadmin/Jython script, shell script, Ansible playbook, etc.) is
delivered as a complete, runnable file — every import, every closing
brace, every full file path — never a fragment the reader is expected to
complete, merge, or infer around.

Rule 2 — Step-by-Step, No Assumed Prior Knowledge
----------------------------------------------------
Every technical instruction is broken into ordered, numbered steps, each
naming: the exact tool/screen/command to use, the exact input/value to
enter, and the expected visible result. A step is not written as "install
X" or "configure Y" without also stating *how* — the specific
click-path, command, or file edit required. This applies to every
category already touched on in this project: WebSphere Admin Console
navigation, wsadmin/Jython commands, SQL/DDL, Linux/VM setup, Git
commands, and any future language/tool introduced in P04-P10 (Ansible,
Terraform-as-concept, AWS Console steps, etc.).

Rule 3 — Concepts Explained Inline, Not Assumed
--------------------------------------------------
Any term likely unfamiliar to a non-developer (EAR vs WAR, JNDI,
classloader, connection pool, DDL vs DML, mTLS, etc.) gets a short plain-
language explanation the first time it's used in a given
Sprint/response — consistent with how Sprint 2's walkthrough already
modeled this (concept table before the steps). Later reuse of an
already-explained term within the same session doesn't need to repeat
the explanation, but a new session should not assume it carries over
silently — a brief reminder is preferred over silent assumption.

Rule 4 — Database Work Is Never "Left as an Exercise"
----------------------------------------------------------
Every SQL migration script (`V<N>__<description>.sql`, per STD's existing
SQL Migration convention) is written out in full — complete `CREATE
TABLE`/`ALTER TABLE`/seed-data statements — never described only in
prose ("add a users table with username and password"). The corresponding
rollback script is written out in full as well, per STD's existing
Rollback convention.

Rule 5 — Verification Steps Are Concrete
---------------------------------------------
"Expected result" fields (per STDGAP01's SDD01 SetupDoc template, already
in use) are not satisfied by a vague description — they name the exact
screen, log line, HTTP status, or console output the owner should see,
so they can confirm success without needing to already know what
"success" looks like.

Rule 6 — No Regression on This Rule
----------------------------------------
This standard applies retroactively to how remaining Sprint 1-14 work in
P01, and all of P02-P10, is delivered going forward. It does not require
re-doing already-completed work, since no Version has been signed off
yet (Progress_Log.md confirms 0/14 P01 versions complete as of this
addendum's date).

Cross-Reference
-----------------
This standard sits alongside STDGAP01's SDD01 (SetupDoc template) and
TCS01 (TestCase template) — NDS01 governs the *completeness and
accessibility* of what goes into those templates' sections, it does not
replace or duplicate their structure.

Phase Boundary Rule
-------------------
Application (banking feature) development ends at P03. P04-P09 are
operational in nature only: observability, HA/DR, multi-region
architecture, platform migration, automation, and cloud migration against
a functionally frozen application set. Any exception must be documented
inline in the relevant Part file, not assumed.

===============================================================================
PIS01 — Production Incident Simulation Standard
===============================================================================

Applies to: Sprint 7 of every Version from P02 onward (v15-v78). NOT
retroactive to P01 (v1-v14) — those versions keep their original 6-sprint
structure exactly as signed off.

Why This Exists
----------------
Reading logs and fixing a known bug is not the same skill as walking into
a live incident with no idea what's wrong. Every real WebSphere admin job
starts exactly the way Sprint 7 is structured: a ticket, angry users, an
alert, and a system that won't say what's wrong until you go find out.
This standard exists to force that exact experience, every version, so
the muscle gets built repeatedly rather than practiced once and assumed.

Sprint 7 Structure — Two Phases, Strictly Separated
---------------------------------------------------------

### Phase 1 — Incident Presented (no solution)
Every Sprint 7 opens with an incident package containing ALL of the
following, and nothing beyond them:

- **Business Impact** — what's breaking for the bank/customers right now
- **Incident Ticket** — ticket ID, severity, reported time, reporter
- **User Complaints** — realistic, as if forwarded from a support queue
- **Monitoring Alerts** — whatever alerting exists at this point in the
  roadmap (pre-P04: a plausible alert description; P04 v34 onward: tied
  to the real Alertmanager rules built by then)
- **Symptoms Only** — observable behavior, never the cause
- **Environment Details** — which VM(s)/app(s)/component(s) are in play
- **Available Logs** — named and excerpted (SystemOut.log, SystemErr.log,
  FFDC, IHS access_log/error_log, plugin log, GC log, etc. — whichever
  are relevant to this incident)
- **Access Granted** — explicitly states what's available to investigate
  with: WAS Admin Console, wsadmin, Linux shell, IHS (when applicable),
  Database (when applicable)

**Hard rule:** Phase 1 never contains the root cause, the fix, or a hint
strong enough to shortcut genuine diagnosis. If asked "is it X?" mid-
investigation, a specific yes/no on that exact guess is fair (a senior
admin would confirm/deny a hypothesis) — but nothing is volunteered
unprompted. If the trainee asks for a hint outright, redirect back to
"what would you check next" rather than answering directly.

### Phase 2 — Solution (only after the trainee responds)
Phase 2 triggers only when the trainee submits their diagnosis/fix
attempt, or explicitly asks to see the solution. It then contains:

- **Root Cause Analysis (RCA)**
- **Correct Fix** — complete, runnable, per NDS01 Rule 1 (no fragments)
- **Verification Steps** — concrete, per NDS01 Rule 5 (exact screen/log
  line/output to confirm)
- **Preventive Actions**
- **Best Practices**
- **Production Recommendations**

Rules
-----
1. Every incident ties to that version's actual WebSphere topic (per
   that version's roadmap "WebSphere Topic" field) — e.g. a JDBC-topic
   version gets a connection-pool-exhaustion-style incident, not an
   unrelated one.
2. Phase 1 and Phase 2 are delivered as separate responses/turns, never
   in the same message — Phase 2 must wait for the trainee's attempt.
3. Documented as `IncidentSim-v<N>.md`, written using the template below,
   committed to `/docs/incidents/`.
4. Difficulty may scale up as the roadmap progresses (later versions can
   assume familiarity with earlier troubleshooting patterns) but every
   incident must still be genuinely solvable using only what that
   version's Access Granted list provides.
5. **Non-gating (project owner decision, 2026-08-05):** Sprint 7 is
   supplementary training practice. It does NOT block a version's
   sign-off, and is NOT part of TCS01 §2.7's Sign-off Rubric or EPS01
   §3.2/3.3's promotion checklists. A version signs off exactly as
   before — Sprint 6's existing Critical/High test-case pass + no open
   Critical/High defects — regardless of whether Sprint 7 has been
   completed at that point. Sprint 7 may be completed after a version is
   already signed off without reopening or blocking anything.

Standard Template — IncidentSim-v<N>.md
-------------------------------------------
```markdown
# Production Incident Simulation — Version <N>: <Title>

**Part:** <Part number and title>
**Sprint:** 7
**Topic tie-in:** <which WebSphere topic this incident exercises>

## Phase 1 — Incident

### Business Impact
### Incident Ticket
### User Complaints
### Monitoring Alerts
### Symptoms
### Environment Details
### Available Logs
### Access Granted

---
*(Trainee investigates here — this section stays blank until Phase 2 is requested)*
---

## Phase 2 — Solution
### Root Cause Analysis
### Correct Fix
### Verification Steps
### Preventive Actions
### Best Practices / Production Recommendations
```

===============================================================================
FIS01 — Fault Injection Standard
===============================================================================

Applies to: Sprint 8 of every Version from P02 onward (v15-v78). NOT
retroactive to P01.

Why This Exists
----------------
PIS01's incidents are narrated. FIS01 is the hands-on counterpart — a
real fault, actually introduced into the real lab environment, that the
trainee diagnoses and fixes themselves using real tools: Admin Console,
wsadmin, Linux commands, JVM/SystemOut/SystemErr/FFDC log analysis,
plugin log checks, JDBC/JMS/cluster/node-sync verification, component
restarts. This is a live lab, not a reading exercise — the trainee is
expected to actually touch the environment, not describe what they would
do.

Sprint 8 Structure — Two Phases, Strictly Separated
---------------------------------------------------------

### Phase 1 — Fault Injected (no reveal)
- The fault is real: either the trainee is given exact instructions to
  make a specific change (introduced "blind" — they follow steps without
  being told what failure it causes), or Claude directs a change to be
  made without stating its effect up front.
- Once injected, the trainee is told only that "something in <this
  version's component scope> is now broken" — no further hint.
- The trainee then investigates using real tools: Admin Console, wsadmin,
  Linux shell commands, log analysis (SystemOut.log, SystemErr.log,
  FFDC, plugin logs, GC logs as relevant), JDBC/JMS/cluster/Node Sync
  verification, and component restarts as they judge necessary.
- Claude may answer factual questions about what the trainee observes
  (e.g. "what does this specific log line mean") without revealing the
  root cause itself.

### Phase 2 — Solution (only after the trainee fixes it or asks to see the solution)
- Confirms whether the trainee's fix actually resolved it (or explains
  why it didn't, if they got it wrong)
- Full Root Cause Analysis
- The exact fault that was injected (what changed, and why it broke
  what it broke)
- Correct Fix (complete steps, per NDS01 Rule 1)
- Verification Steps
- Preventive Actions / Best Practices

Rules
-----
1. The fault must be realistic and tied to that version's actual
   WebSphere topic — never an arbitrary/unrelated break.
2. The fault must be genuinely diagnosable using only the tools STD/
   SOE01 already establish as available in this environment (Admin
   Console, wsadmin, Linux shell, logs per SOE01 §12's standard paths).
3. Every fault injection step and its later reveal are both given as
   complete, exact instructions per NDS01 Rule 1/2 — never "just break
   something," always a specific, reproducible change.
4. Phase 2 never triggers automatically after injection — it waits for
   the trainee's fix attempt or an explicit request to see the solution.
5. Once revealed, the trainee restores the environment to a known-good
   state (either by applying the correct fix for real, or by reverting
   via VM snapshot per SOE01's snapshot discipline) before Sprint 8 is
   considered closed — a fault is never left live in the environment
   past its own drill.
6. Documented as `FaultDrill-v<N>.md`, using the template below,
   committed to `/docs/faultdrills/`.
7. **Non-gating (project owner decision, 2026-08-05):** Same as PIS01
   Rule 5 — Sprint 8 is supplementary and does not block sign-off. A
   version's Sign-off Rubric (TCS01 §2.7) and promotion checklist
   (EPS01 §3.2/3.3) are unaffected by Sprint 8's completion status.

Standard Template — FaultDrill-v<N>.md
-------------------------------------------
```markdown
# Fault Injection Lab — Version <N>: <Title>

**Part:** <Part number and title>
**Sprint:** 8
**Topic tie-in:** <which WebSphere topic this fault exercises>

## Phase 1 — Fault Injected
### Injection Steps (exact, followed by the trainee or Claude-directed)
### What the Trainee Is Told
### Tools Available for Diagnosis

---
*(Trainee's troubleshooting log goes here — commands run, logs checked,
findings — filled in live during the drill)*
---

## Phase 2 — Solution
### What Was Actually Broken
### Root Cause Analysis
### Correct Fix (complete steps)
### Verification Steps
### Preventive Actions / Best Practices
### Environment Restored? (Y/N, method used)
```

Cross-Reference
-----------------
PIS01 and FIS01 sit alongside NDS01 (delivery completeness) and SDD01/
TCS01 (SetupDoc/TestCase templates) — they govern two new *kinds* of
Sprint deliverable, using the same completeness discipline NDS01 already
requires (full steps, no fragments, concrete verification), applied to
incident/fault scenarios instead of build-out work.

Version Numbering Impact
----------------------------
No version numbers change. Only each version's internal sprint count
changes, from 6 to 8, starting at P02 v15. P01 (v1-v14) is unaffected and
stays a 6-sprint structure per its own already-signed-off pattern.