ID: STD
Version: 1.14
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
addenda, mirroring NDS01's placement/format. Effective project-wide, P01 through the final Part (v1-v78) — no exceptions. P01's Versions 1-14 also use the 8-sprint structure (Sprint 7 Sign-off, Sprint 8 Fault Injection + Incident), consistent with every later Part. This corrects an earlier note that mistakenly exempted P01 from this structure — P01_Sprint_Plan.md already reflects the correct 8-sprint format and was never actually built under the old 6-sprint structure (P01 v1-v14 remain unstarted per the 2026-08-25 reset), so no retroactive rework is needed. From v15 onward,
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

Version 1.11 change note (2026-08-07): P01 Version 1 signed off (13/13
TestCases-v1.md cases pass, TCS01 §2.7 rubric satisfied). Per this
document's own promotion rule, WebSphere ND (9.0.5.28) and PostgreSQL
(16) version pins promoted from target/placeholder to CONFIRMED — source
record is SetupDoc-v1.md §4.1/§4.3. Also added digistack_bank as the
named early-build shared database (Database section), closing the
ARCH01 gap where the pre-CBS-split shared DB had no defined name.



Version 1.12 change note (2026-08-25): Header version bumped from 1.11
to 1.12 to match this change note, which had been added without the
metadata block being updated to match — same category of gap as the
SOE01 v1.9 Used-By correction below.

Version 1.13 change note (2026-08-25): Full reset #2 (VM + chat lost,
confirmed with project owner 2026-08-25, per SESSION_STATE v1.5).
WebSphere ND and PostgreSQL version pins reverted from CONFIRMED back
to target/placeholder, unconfirmed — mirroring the 2026-08-11 reset
precedent. This closes a real drift: Progress_Log.md's 2026-08-11
reset entry already claimed this revert had been done, but this
document's actual pins were never edited at that time and still read
CONFIRMED (dated 2026-08-07) until this entry. Re-promote to CONFIRMED
only once P01 v1 is rebuilt and signed off again, with a fresh
SetupDoc-v1.md as the source record.

This entry replaces an earlier, garbled version of this same change
note that named the same value ("Portal") on both sides of a claimed
correction, making it unreadable as written. Correcting to what is
actually true today, cross-checked directly against the files: this
document's Applications/Deployables tables, ARCH01, ARCH02, and both
P02_Middleware.md and P03_Banking_Systems.md (including their Sprint
Plans) all consistently name this application "Portal" (EAR:
digistack-portal-v<N>.ear) — no naming mismatch currently exists
anywhere in the project. If a real mismatch prompted the original
(unreadable) note, it has since been resolved; this entry is a
metadata correction only, per this project's standing "correct the
reference doc, not the built work" precedent (same as the 2026-07-28
STD/SOE01 port-table fix). No architectural/technical change.

Version 1.14 change note (2026-08-27): P01 (v1-v14) sprint structure
updated from 6 sprints to 8 sprints, matching P02-P10's existing
structure. Sprint 6 = Test Cases (TestCases-v<N>.md), Sprint 7 =
Sign-off (SetupDoc, backupConfig, smoke test), Sprint 8 = Fault
Injection + Incident (FaultDrill-v<N>.md). The prior PIS01/FIS01
exclusion of P01 is removed — project owner decision 2026-08-27.
Non-gating rule for Sprint 8 unchanged and applies to P01 as well.

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
digistack_bank (early-build shared DB, P01-P02, per ARCH01 Data Architecture — added at P01 v1 sign-off, 2026-08-07)
digistack_cbs (single dedicated DB from CBS split onward, sole writer: CBS)

Schemas (within digistack_cbs)
--------------------------------
banking
audit
reporting

Application Server
------------------
WebSphere ND: 9.0.5.28 (target/placeholder, unconfirmed — reverted here
per the 2026-08-25 full reset #2; not yet installed, promote back to
CONFIRMED only once actually installed and recorded in a fresh
SetupDoc-v1.md)

Secondary App Server
---------------------
Apache Tomcat (Mobile, ATM only)

OS
--
Rocky Linux 8.x / RHEL 8.x

Version Pins (target/placeholder, unconfirmed — P01 v1 reset to
not-started again 2026-08-25 (full reset #2); promote to CONFIRMED
only once actually installed and recorded in a fresh SetupDoc-v1.md)
----------------------------------------------------------------
WebSphere ND: 9.0.5.28 (target — not yet installed, unconfirmed;
reverted from a prior CONFIRMED state per the 2026-08-25 reset)
Java SDK: IBM Java 8 (SDK 8.0), bundled/certified with WAS ND 9.0.5.28
(target — not yet installed, unconfirmed)
IBM HTTP Server (IHS): 9.0.5.28 (target — not yet installed; IHS is
P01 v8, still a forward-looking pin until then)
Web Server Plug-ins: 9.0.5.28 (target — same as above, pinned for real
at P01 v8)
IBM Installation Manager: 1.9.x (target — confirm the exact version
actually used for the WAS ND install against your install logs, and
correct this line if it differs)
PostgreSQL: 16 (target — not yet installed, unconfirmed; reverted
from a prior CONFIRMED state per the 2026-08-25 reset)
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
Portal               (WAS)    - customer web channel
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
Portal.ear
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
dsb-tracing       (Jaeger — may co-locate on dsb-monitor; add as separate VM only if SOE01 §1a RAM budget permits)
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
dsb-tracing              Mgmt   .10.42    1      1 GB    10 GB         On-demand, P04 v33 onward; co-locate on dsb-monitor if RAM permits (per SOE01 §1a budget)

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
8. Portal
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
PIS01/FIS01 — Merged Fault Injection & Incident Simulation Standard
===============================================================================

Applies to: Sprint 8 of every Version in the project, P01 through the
final Part (v1-v78) — no exceptions.

Why This Exists
----------------
A narrated ticket disconnected from the real environment teaches
pattern-matching against a script, not real troubleshooting. A live
fault with no ticket skips the "how do I even know something's wrong"
step a real admin actually starts from. Merging them fixes both: a real
change is made to the actual build, and the incident ticket you receive
describes exactly what that real fault would look like from the outside —
so your investigation is against a genuinely broken system, not a
narrated hypothetical.

Sprint 8 Structure — Three Phases, Strictly Separated
---------------------------------------------------------

### Phase 1 — Fault Injection (real, into the actual environment)
- The fault is real: either the trainee is given exact instructions to
  make a specific change (introduced "blind" — they follow steps without
  being told what failure it causes), or Claude directs a change to be
  made without stating its effect up front.
- The fault must be realistic and tied to that version's actual
  WebSphere/application topic — never an arbitrary/unrelated break.
- The fault must be genuinely diagnosable using only the tools that
  version's own build has actually established (WAS Admin Console,
  wsadmin, IHS logs, WAS logs, application logs, DB/MQ tools, Linux
  commands per SOE01 §12's standard paths).
- The injection steps are given as complete, exact instructions per
  NDS01 Rule 1/2 — never "just break something," always a specific,
  reproducible change.
- Nothing about the fault's effect, symptom, or cause is stated at this
  point — that's Phase 2's job.

### Phase 2 — Incident Raised (triggered by "continue sprint" after injection)
Once the fault from Phase 1 is actually in place, "continue sprint"
produces an incident ticket in this exact format — describing the real
symptoms that specific injected fault would actually produce, not a
generic scripted incident:
INCIDENT ID:
SEVERITY:
TIME:
APPLICATION/SERVICE:

BUSINESS IMPACT:

CUSTOMER/BUSINESS SYMPTOM:
<realistic banking symptom, derived from the actual fault injected>

INITIAL ALERT/TICKET:
<short production ticket>

OBSERVED ERROR:
<realistic error message this specific fault would actually produce —
HTTP error, WAS error, application error, MQ/JMS/DB/IHS error, etc.>

SCOPE:
<what is affected>

NOT AFFECTED:
<what still works>

RECENT CHANGE:
<only if appropriate; never states the fault's exact nature/cause>

STARTING EVIDENCE:
<only the evidence normally available at incident creation — no
diagnostic conclusions>

STOP HERE.


Hard rules for Phase 2:
1. The ticket is generated only from the real fault actually injected in
   Phase 1 — never a stock/unrelated scenario.
2. No root cause, solution, troubleshooting steps, commands,
   configuration changes, hints, expected diagnosis, or RCA appear
   anywhere in this output.
3. If asked mid-investigation "is it X?" — a specific yes/no on that
   exact hypothesis is fair (a senior admin would confirm/deny a
   guess), but nothing beyond that exact question is volunteered.
4. If a hint is explicitly requested, redirect back to "what would you
   check next" rather than answering directly.

### Phase 3 — Investigation, RCA, and Help (person-driven)
- The person investigates using real tools against the real fault: Admin
  Console, wsadmin, Linux shell commands, log analysis (SystemOut.log,
  SystemErr.log, FFDC, plugin logs, GC logs as relevant), JDBC/JMS/
  cluster/Node Sync verification, and component restarts as judged
  necessary.
- Claude may answer factual questions about what the trainee observes
  (e.g. "what does this specific log line mean") without revealing the
  root cause itself, per Phase 2's hard rules.
- The person may explicitly ask for help at any point — Claude then
  gives proportional assistance (a nudge toward the next diagnostic
  step, not the answer outright) unless the person explicitly asks to
  see the full RCA/solution.
- RCA Review (root cause, correct fix, verification steps, preventive
  actions, best practices) is produced only on explicit request — as a
  separate, later message, never bundled into Phase 2's ticket or
  volunteered mid-investigation.
- Once revealed/fixed, the trainee restores the environment to a
  known-good state (real fix applied, or reverted via VM snapshot per
  SOE01's snapshot discipline) before Sprint 8 is considered closed — a
  fault is never left live past its own drill.

Documentation
-----------------
Documented as `FaultDrill-v<N>.md`, committed to `/docs/faultdrills/`,
using this structure:
```markdown
# Fault Injection & Incident — Version <N>: <Title>

**Part:** <Part number and title>
**Sprint:** 8
**Topic tie-in:** <which WebSphere/application topic this fault exercises>

## Phase 1 — Fault Injected
### Injection Steps (exact)
### What the Trainee Is Told

## Phase 2 — Incident Ticket
(exact PIS01/FIS01 format above, generated from the real fault)

---
*(Trainee's troubleshooting log goes here — commands run, logs checked,
findings — filled in live during investigation)*
---

## Phase 3 — RCA Review (only on explicit request)
### What Was Actually Broken
### Root Cause Analysis
### Correct Fix (complete steps, per NDS01 Rule 1)
### Verification Steps
### Preventive Actions / Best Practices
### Environment Restored? (Y/N, method used)
```

Non-Gating Rule
-------------------
(project owner decision, 2026-08-05, carried forward unchanged) — Sprint
8 is supplementary training practice. It does NOT block a version's
sign-off and is NOT part of TCS01 §2.7's rubric or EPS01 §3.2/3.3's
promotion checklists. A version signs off on its Sign-off sprint
(Sprint 7) exactly as before, regardless of whether Sprint 8 has been
completed. Sprint 8 may be completed after a version is already signed
off, without reopening anything.

Supersession Note
----------------------
This section replaces the prior separate PIS01 (narrated-ticket-only,
Sprint 7) and FIS01 (hands-on-fault-only, Sprint 8) standards in full.
Both are now this single merged Sprint 8 sequence, fault-first, applied
to every version in the project.

Version Numbering Impact
----------------------------
No version numbers change. Only each version's internal sprint count
changes, from 6 to 8. This applies to every version in the project,
P01 through the final Part (v1-v78) — no exceptions.