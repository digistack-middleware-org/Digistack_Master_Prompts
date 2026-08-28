ID: P07
Version: 1.1
Status: Active

Title: Enterprise WebSphere Migration & Modernization

Imports: IDX STD ARCH01 ARCH02 STDGAP01

Exports: Versions 44-48
Migration Strategy Options (In-Place / Side-by-Side / Clone)
WebSphere Version Migration & Upgrade Administration
Application & Resource Migration Validation
Infrastructure & Security Migration
Zero-Downtime Migration & Cutover
Enterprise Migration Capstone

Used By: P08 P09

Next: P08

Purpose
Learn how real organizations migrate and upgrade IBM WebSphere
environments — version upgrades, application compatibility,
infrastructure migration, and zero-downtime cutover — with minimal
downtime and a fully documented, reversible process.

Scope Decision — Single Region Only (India/Hyderabad + its Bangalore DR
pair)
This Part deliberately migrates **India only** (Hyderabad, plus its
Bangalore DR pair from P05) — not all three P06 regions. Reasoning:
every migration skill this Part teaches — WASPreUpgrade/WASPostUpgrade,
managesdk, Installation Manager upgrades, cell/node/DMgr migration,
plugin regeneration, JNDI/JDBC/JMS revalidation, SSL/LTPA migration,
canary cutover with connection draining — is fully teachable and
interview-relevant against one WAS cell (and its DR pair). Extending
this to Singapore and Dubai would add multi-region migration
*coordination* (which region migrates first, cross-region version-skew
handling, golden-template forking) without deepening the core migration
skill itself — a different, later exercise, not a missing piece of this
one.

Consequence: Singapore and Dubai remain on the pre-migration WAS version
throughout this Part and are not touched by any version below. This is
explicit scope, not an oversight — restated below wherever it would
otherwise be ambiguous (Support Matrix Validation, Cross-Region
Compatibility, Global Shared Services, Golden Cell Template).

Prerequisite
P06 Completion Checkpoint satisfied — three-region topology (India/
Singapore/Dubai) operational, Global Shared Services layer standing,
golden cell configuration template established. Only India's portion of
that topology is migrated in this Part.

Deployment Model
No new banking functionality is introduced anywhere in this Part. The
existing DigiStack Bank applications (the same 9 applications from P03,
running in India since P06 — with Singapore/Dubai's copies untouched) are
migrated, upgraded, and validated on a new WebSphere platform version —
not extended with new features. Every version instruments, migrates, or
cuts over infrastructure/applications already built, in India only.

Process for every version: Requirements → Development (migration
scripting/config, beginner-level explanation) → Deployment & Admin
across old + new WAS environments (Hyderabad + Bangalore) → Testing
(compatibility/regression/failover) → Documentation → Pause for approval
before the next version.

Per-version deliverables (per Master Index standing standards): VM Setup
section (doc 01), Git-committed migration scripts/config (doc 02),
TestCases-v.md (doc 03), SetupDoc-v.md (doc 06), SQL migration script(s)
only if a migration-tracking table is added (doc 05), config changes per
doc 07.

Version Numbering Correction (resolved before this file is marked ready)
The source material this Part was drafted from numbered its versions
43–47. That collides with P06, which already owns and has frozen
Versions 39–43 (per Engineering Standards §7 — Version Numbering
Freeze: v43 is already "Enterprise Middleware Architect Capstone"). Per
the freeze rule, P07 is renumbered to start immediately after P06 ends.

Renumbering table (required by Engineering Standards §7 whenever a
renumbering happens):

| Old # (draft) | Old Title | New # | New Title |
|---|---|---|---|
| 43 | WebSphere Version Migration | 44 | WebSphere Version Migration & Upgrade Administration |
| 44 | Application Migration | 45 | Application & Resource Migration Validation |
| 45 | Infrastructure Migration | 46 | Infrastructure & Security Migration |
| 46 | Zero Downtime Migration | 47 | Zero-Downtime Migration & Cutover |
| 47 | Enterprise Migration Capstone | 48 | Enterprise Migration Capstone |

No version has been implemented yet, so this is a clean
pre-implementation renumbering — a uniform +1 shift with no internal
reordering — same precedent as P06's own offset from its 38–42 draft.

Consolidation note. The review notes accompanying the original draft
raised five categories of gaps (Migration Assessment, Configuration
Migration, Automation, Migration Testing, Documentation) plus a long list
of per-version admin tasks (Installation Manager rollback, managesdk,
WASPreUpgrade/WASPostUpgrade, JNDI/JDBC/JMS validation, LTPA/SSL/cert
migration, session continuity, connection draining, incremental traffic
shifting, etc.). Rather than leaving these as a floating checklist, each
is folded into whichever version below it most naturally extends —
consistent with how P04 absorbed its "doc 14" gap analysis and P05
absorbed its 12-item gap review.

A second gap-analysis pass (post-freeze, post-scope-decision) identified
6 additional gaps specific to migrating inside a multi-region-aware
platform without migrating every region: MQ's absence from the Support
Matrix gate, the unstated fate of India's Bangalore DR pair, whether
Global Shared Services itself gets migrated, cross-region version-skew
between migrated India and unmigrated Singapore/Dubai, the golden cell
template going stale for India specifically, and cross-region rollback
dependency. These are resolved below — primarily in v44 (Support Matrix,
DR-pair handling), v46 (Global Shared Services boundary), and v47/v48
(cross-region skew validation, golden template update).

Standing Principle — Migrate, Don't Rebuild
Every version in this Part operates on the same DigiStack Bank
application set built through P06 (7 WAS EARs + 2 Tomcat apps, in
India). Nothing here changes what the bank does — only the platform it
runs on. Any test failure that would require an actual code/feature
change is logged as a migration blocker, not silently worked around by
adding scope.

Version 44 — WebSphere Version Migration & Upgrade Administration
Objective: Upgrade DigiStack Bank's WebSphere platform in India
(Hyderabad primary + Bangalore DR pair) from the version used through P06
to a newer WAS ND version (e.g., WAS 8.5.5 → WAS 9.0), including the
underlying Java SDK, using the correct enterprise upgrade tooling rather
than a fresh reinstall.

Banking Features Added: None. This version upgrades the platform beneath
the existing applications; the applications themselves are not touched
until Version 45.

Migration Strategy Options (context before implementation)
Before any tooling is touched, the strategy itself is a decision to
document in SetupDoc-v44.md:

- In-Place Migration — upgrade the existing profile/cell directly;
  fastest, but the old state is only recoverable via backupConfig/
  WASPreUpgrade, not a live fallback environment. This is the approach
  used for the node-by-node Rolling Upgrade below.
- Side-by-Side Migration — new WAS version installed alongside the old
  on the same or different hardware, old environment stays live and
  untouched until cutover; this is what makes Version 47's Parallel
  Environment / canary cutover possible.
- Clone Migration — the existing profile is cloned, then the clone is
  upgraded — a middle ground giving a fallback clone without needing
  fully separate new hardware.

This Part uses Side-by-Side as the primary strategy (it's the only one
of the three that supports Version 47's zero-downtime canary cutover),
with In-Place used only for the lower-risk, easily-reversible steps
within Version 44 itself (e.g., upgrading a single already-redundant
cluster member).

Clarification for this project's scale (added 2026-07-19 cross-file
audit): "Side-by-Side" and "Parallel Environment" (v47) are realized here
as mixed-version cluster membership on the existing VM inventory — during
the rolling window, some cluster members run the old WAS version and
some run the new, within the same cluster and the same VMs already
listed in doc 01 — not as a second, wholly separate set of hardware
standing up a duplicate environment. This is a deliberate scope decision
consistent with this project's lab scale, not an oversight; a real
enterprise migration at larger scale might well provision genuinely
separate hardware for the "new" side, but no version in this Part
introduces a new VM for that purpose (see each version's own "VM Setup
Note" below), and none should be assumed to exist that isn't in doc 01.

India's DR Pair (Hyderabad ↔ Bangalore) — Migration Sequencing (added;
previously unstated)
Since Bangalore's entire purpose (P05 v37) is to be Hyderabad's failover
target, it cannot be silently left on the old platform while Hyderabad
moves to the new one — a real Site Failure during that window would fail
over production traffic onto a WAS version that was never validated
against the migrated applications. This Part's sequencing is:
1. Hyderabad (Primary) is migrated first, node-by-node, per the Rolling
   Upgrade below, while Bangalore (still on the old platform) remains
   available as the real DR fallback throughout — this is the actual
   safety net for Hyderabad's own migration risk, not just
   backupConfig/WASPreUpgrade.
2. Once Hyderabad's migration is validated (v45's regression pack passes,
   v47's cutover is stable, per the Migration Success Criteria in v48),
   Bangalore is migrated using the identical, now-proven procedure.
3. Only after both sites run the same new WAS version is India's DR pair
   considered "migration complete" — a mid-state where Hyderabad is
   upgraded and Bangalore is not is an explicitly acknowledged,
   time-boxed risk window, not a stable end state, and P05's DR drills
   should not be run for real during that window.

IBM Support Matrix Validation (planning gate, before any upgrade step)
Checked and documented in SetupDoc-v44.md before Installation Manager is
even opened — an unsupported combination here isn't a "try it and see,"
it's a hard planning blocker:

- WebSphere version ↔ target Java SDK version compatibility
- Java SDK ↔ Operating System version compatibility
- WebSphere version ↔ IBM HTTP Server version compatibility (mismatched
  IHS/plugin and WAS versions is a common real-world migration failure
  mode)
- **WebSphere version ↔ IBM MQ / Queue Manager version compatibility**
  (added — previously missing from this gate despite v46 assuming it
  downstream; an unsupported WAS/MQ pairing must be caught here, before
  any upgrade step, not discovered mid-migration when v46's MQ Migration
  step begins)
- IBM Installation Manager version itself ↔ target WAS version (IM
  sometimes needs its own upgrade before it can install a newer WAS
  version)

Topics Covered: WebSphere Version Lifecycle, End-of-Support Planning,
Migration Strategy Selection (In-Place / Side-by-Side / Clone), IBM
Support Matrix Validation (including MQ); IBM Installation Manager —
Upgrade and Rollback (a failed upgrade must be reversible, not just
forward-only); Repository Configuration (local/remote IM repositories)
and Response File-driven silent upgrades (needed for repeatable,
scriptable upgrades — ties into P06 v43's golden-cell automation, though
only India's fork of that template is touched here — see v46);
Fix Pack Installation; Java SDK Upgrade, including Java SDK Coexistence
(old and new SDKs installed side-by-side during transition) and
managesdk command usage to switch a profile's active SDK without
breaking other profiles on the same node; Profile Compatibility,
WASPreUpgrade (captures the existing profile's config/apps before
upgrade) and WASPostUpgrade (re-applies that captured state onto the new
install); backupConfig / restoreConfig as the safety net before and
after every upgrade step; Cell Migration, Rolling Upgrades (node-by-node,
not a full-cell outage); Cell synchronization validation post-upgrade —
confirming DMgr and every node agree on cell state, not just that each
node individually reports "started"; Rollback Strategy (mirrors the
Installation Manager rollback above, at the cell level); DR-Pair
Migration Sequencing (above).

Migration Flow
```
Current WAS Cell — Hyderabad (India Primary, per P06/P05)
        │
   backupConfig (per node)
        │
   WASPreUpgrade (capture profile state)
        │
   Install Fix Packs / New WAS Version (Installation Manager, response
   file) — IBM Support Matrix already validated, including MQ
        │
   managesdk (switch active Java SDK per profile)
        │
   WASPostUpgrade (restore profile state onto new install)
        │
   Cell Synchronization Validation
        │
   Rolling Upgrade (one node at a time, traffic continues on remaining
   nodes; Bangalore DR pair still on old platform, serving as real DR
   fallback throughout)
        │
   New WAS Cell — Hyderabad confirmed healthy via P04 observability
   stack
        │
   (Once validated per v45/v47/v48) — repeat identical procedure against
   Bangalore
        │
   India DR pair — both sites confirmed on new WAS version
```

VM Setup Note: No new VM introduced. Existing India WAS nodes (Hyderabad,
then Bangalore — per doc 01's regional inventory) are upgraded in place,
one node at a time, per the Rolling Upgrade pattern above. Singapore and
Dubai's VMs are untouched by this version — see Scope Decision above.
SetupDoc-v44.md must capture exact Installation Manager response-file
paths and managesdk commands used.

WebSphere Topics Covered: Installation Manager, Fix Pack Management, Java
SDK Coexistence, managesdk, WASPreUpgrade/WASPostUpgrade, backupConfig/
restoreConfig, Cell Synchronization, Rolling Upgrade, Rollback.

Enterprise Learning: Platform Lifecycle Management, Upgrade Planning,
Reversible Change Management, DR-Aware Migration Sequencing.

Sprint Deliverable: Hyderabad's WAS cell is upgraded to the new WAS
version (WAS 8.5.5 → WAS 9.0) and Java SDK, node-by-node, with zero
full-cell outage, while Bangalore remains on the old platform as a real
DR fallback throughout; a deliberate rollback is performed on one node
using the backupConfig/WASPreUpgrade capture, proving the reverse path
works before Bangalore is touched; post-upgrade cell synchronization is
confirmed cell-wide, not just per-node; the IBM Support Matrix gate
(including the newly-added MQ compatibility check) is confirmed passed
before any install step, documented in SetupDoc-v44.md.

Version 45 — Application & Resource Migration Validation
Objective: Migrate DigiStack Bank's applications (all 7 EARs + 2 WARs)
in Hyderabad onto the upgraded WAS platform from Version 44, and
validate that every JNDI-bound resource, deprecated API usage, and
shared library still functions correctly — not just that the EAR
installs without error.

Banking Features Added: None.

Cross-Region Compatibility During Transition (added; previously
unaddressed) While Hyderabad runs the new platform and Singapore/Dubai
remain on the old platform (per the Scope Decision), P06's cross-region
features stay live and must keep working across that version skew:
- mTLS REST cross-region calls (P06 v41) between migrated Hyderabad and
  unmigrated Singapore/Dubai — validated here as a compatibility check,
  not assumed
- MQ Hub channels (P06 v41) between Hyderabad's (now upgraded) queue
  manager and Singapore/Dubai's (still old-version) queue managers —
  this is the direct, practical reason the MQ compatibility gate was
  added to v44's Support Matrix Validation: a queue-manager version
  mismatch across the Hub would surface here, during real cross-region
  traffic, not just in a lab check
- GSLB (P06 v40) continuing to route users correctly to India even
  though India's platform version now differs from Singapore/Dubai's —
  confirmed as a non-issue since GSLB routes on the /health signal (P04
  v31), which is platform-version-agnostic by design

Topics Covered: EAR Compatibility, WAR Compatibility, Shared Libraries,
Class Loading (parent-first/parent-last behavior can change between WAS
versions); Deprecated API identification (via the WebSphere Migration
Toolkit / Application Migration Toolkit); Testing Strategy (see
validation list below); Java EE compatibility — confirming the Java EE
spec level targeted by each of the 9 applications is still supported on
the new platform; JNDI resource validation — every DataSource, Mail
Session, and Connection Factory JNDI name (per doc 07 §4) resolves
correctly post-migration; JDBC validation — connection pool behavior,
JAAS auth alias resolution, and pool sizing carry over correctly (P01 v7
foundation); JMS resource validation — SIBus queues, MDBs, and IBM MQ
connection factories (P02 v15/v19) still bind and deliver messages, both
within Hyderabad and across the MQ Hub to Singapore/Dubai (above);
Session persistence validation — confirming P01 v9/P05 v36's session
replication still functions on the new platform, not assumed; Plugin
regeneration — plugin-cfg.xml regenerated against the new cell topology;
Performance comparison — a baseline (response time, throughput) captured
before migration and compared after, using the existing P04 Prometheus/
Grafana stack rather than a new tool; Cross-Region Compatibility
Validation (above).

Migration Flow
```
Old WAS Cell (Hyderabad, applications running)
        │
   Migration Toolkit scan (deprecated APIs, EAR/WAR compatibility
   report)
        │
   Fix flagged compatibility issues (code/config only — no new features)
        │
   Deploy all 9 applications to New WAS Cell (Hyderabad, from Version
   44)
        │
   JNDI / JDBC / JMS Resource Validation
        │
   Cross-Region Compatibility Validation (mTLS to Singapore/Dubai, MQ
   Hub channels, GSLB routing — all against the now-mixed-version
   topology)
        │
   Session Persistence Validation
        │
   Plugin Regeneration
        │
   Performance Comparison (Grafana, before vs. after)
        │
   Regression Test Pack (re-run TestCases-v1..v43, per Environment
   Promotion Standards)
```

WebSphere Topics Covered: EAR/WAR Compatibility, Class Loading, Migration
Toolkit, JNDI Validation, JDBC Validation, JMS Validation, Session
Persistence Validation, Plugin Regeneration, Performance Baselining,
Cross-Region Version-Skew Validation.

Enterprise Learning: Application Portability, Compatibility Testing,
Regression Discipline, Performance Regression Detection.

Sprint Deliverable: All 7 EARs and 2 WARs are redeployed to Hyderabad's
new WAS cell; the Migration Toolkit's compatibility report is reviewed
and every flagged deprecated API is resolved or explicitly documented as
a non-blocking warning; every JNDI-bound resource (DataSource, Mail
Session, SIBus/MQ connection factories) resolves correctly; cross-region
calls to Singapore and Dubai (mTLS REST, MQ Hub messaging) are confirmed
still functioning across the version skew; a full regression pass (all
prior TestCases-v.md files, per doc 03/04) passes against the new
platform; a before/after performance comparison shows no unexplained
regression.

Version 46 — Infrastructure & Security Migration
Objective: Migrate the remaining infrastructure tier — Nodes, DMgr, IHS,
IBM MQ, SSL/LTPA — in Hyderabad onto the new platform version,
completing what Version 44 started at the WAS-core level.

Banking Features Added: None.

Global Shared Services Boundary (added; previously unaddressed) P06's
Global Shared Services layer (Central LDAP, Jenkins, Git, Nexus,
Prometheus/Grafana, ELK, the MQ Hub, ServiceNow concepts, SMTP/SMS, NTP,
DNS) is **not migrated in this Part** — it remains shared, standing
infrastructure consumed by all three regions, and India's migration must
work *against* the existing Global Shared Services layer as-is, not
assume it also upgrades in lockstep. Concretely: India's regional LDAP
config (below) is migrated, but Central LDAP itself, Jenkins, Git, and
Nexus are unchanged by this Part. This is a deliberate boundary — since
Singapore and Dubai still depend on these shared services at their
current versions, upgrading the shared layer here could break the two
regions this Part isn't touching. If Global Shared Services itself ever
needs a platform upgrade, that is a separate, explicitly-scoped future
Part affecting all three regions at once, not a side effect of P07.

Topics Covered: Node Migration, DMGR Migration; IHS Migration, including
plugin-cfg regeneration (reused from v45, re-verified here against the
fully migrated topology) and node federation validation — every migrated
node re-federates cleanly to the (possibly upgraded) DMgr; IBM MQ
Migration (Queue Manager version compatibility — validated at the v44
gate, per the Support Matrix fix above — channel definitions carried
over, including the Hyderabad-side channel to the still-shared MQ Hub);
SSL Migration — SSL certificate migration (existing certs from doc 07
§7's Certificate Inventory imported into the new keystores, not
regenerated from scratch unless expired); LTPA key migration — critical
given P06 v42's cross-cell LTPA synchronization; Hyderabad's migrated
cell must still trust the same LTPA keys as Bangalore (not yet migrated,
per v44's sequencing) and Singapore/Dubai (never migrated in this Part)
during the entire transition; LDAP Migration — India's regional
federated-repository config carried over, per P06 v42, against the
unchanged Central LDAP (per the Global Shared Services Boundary above);
Security configuration migration (JAAS auth aliases, role mappings —
doc 07 §4's config-per-environment discipline extended to
config-per-migration-phase); Custom properties migration (WAS custom
properties/JVM args that aren't part of the standard profile export);
Scheduler migration (if EJB Timer Service jobs — e.g., P03 v30's EMI
auto-debit — are in flight during migration, they must resume correctly
on the new platform, not silently drop).

Migration Flow
```
Old Infrastructure Tier (Hyderabad)   New Infrastructure Tier (Hyderabad)
───────────────────────────────────  ───────────────────────────────────
DMgr (old)                  ──migrate──►  DMgr (new)
Nodes (old)                 ──migrate──►  Nodes (new, federated +
                                            validated)
IHS (old)                   ──migrate──►  IHS (new) + plugin-cfg
                                            regenerated
IBM MQ (old)                ──migrate──►  IBM MQ (new, channels/queues
                                            carried over, incl. MQ Hub
                                            channel)
SSL Certs (old)             ──imported──►  New Keystores (same certs,
                                            not regenerated)
LTPA Keys (old)             ──synchronized──►  New Cell (trusts same
                keys as Bangalore/Singapore/Dubai, none of which are
                migrated yet)
LDAP Config (old, regional) ──carried over──►  New Cell (federated repos
                unchanged; Central LDAP itself untouched — see Global
                Shared Services Boundary)
Custom Properties           ──migrated──►  New Profiles
EJB Timers (in-flight)      ──resumed──►  New Cell (no dropped scheduled
                                            jobs)
```

WebSphere Topics Covered: Node/DMgr Migration, Plugin Regeneration, Node
Federation Validation, IBM MQ Migration, SSL Certificate Migration, LTPA
Key Migration, LDAP Migration, Security Configuration Migration, Custom
Properties Migration, Scheduler Migration, Global Shared Services
Boundary.

Enterprise Learning: Infrastructure Portability, Security Continuity
During Migration, Zero-Trust-Gap Migration Discipline, Shared-Services
Scope Discipline.

Sprint Deliverable: Hyderabad's DMgr and all nodes are migrated and
re-federated, confirmed via node federation validation; IHS fronts the
new topology with a freshly regenerated plugin-cfg.xml; IBM MQ Queue
Manager and channel definitions (including the channel to the still-
shared MQ Hub) are confirmed intact post-migration; existing SSL
certificates are imported (not regenerated) into new keystores and
validated; LTPA keys are confirmed synchronized between Hyderabad's
migrated cell and Bangalore/Singapore/Dubai's unmigrated cells so SSO
doesn't silently break; at least one in-flight EJB Timer job (e.g., an
EMI schedule) is proven to resume correctly on the new platform; Central
LDAP/Jenkins/Git/Nexus are confirmed unchanged and still functioning
against Hyderabad's new platform, per the Global Shared Services
Boundary.

Version 47 — Zero-Downtime Migration & Cutover
Objective: Perform the actual production cutover from old to new
platform in Hyderabad with zero customer-visible downtime, using the
same discipline established for zero-downtime maintenance in P05 v38,
now applied to a full platform migration rather than a routine rolling
restart.

Banking Features Added: None.

Topics Covered: Blue-Green Migration, Canary Migration, Rolling Upgrade,
Parallel Environment (old + new run simultaneously during transition —
see v44's clarification above: realized as mixed-version cluster
membership on the existing VM inventory, not separate new hardware);
Traffic Switching, Rollback; Session continuity — a customer mid-session
during cutover is not logged out (reuses P01 v9/P05 v36 session
replication, now proven across old→new platform boundary); Connection
draining — in-flight requests on the old platform are allowed to
complete before that node is removed from rotation, rather than being
cut off mid-request; Health-check validation — the new platform's
/health endpoints (P04 v31) must report healthy before GSLB/LB (P06 v40)
sends it live traffic; Incremental traffic shifting — canary starts at a
small percentage (e.g., 5%) and increases only after each step is
confirmed healthy, not an all-or-nothing switch; Post-cutover
verification — full smoke test + a defined observation window before the
old platform is decommissioned; Cross-Region Traffic During Cutover —
GSLB (P06 v40) continues to send some users to Singapore/Dubai
throughout Hyderabad's canary shift; this version confirms that traffic
split is unaffected by India's internal old→new cutover, since GSLB's
per-region routing decision and India's internal canary percentage are
independent layers.

Migration Flow
```
Parallel Environment (Old + New platforms both live, Hyderabad)
        │
   Canary: 5% traffic → New Platform
        │
   Health-Check Validation (P04 v31 /health, P06 v40 GSLB signals)
        │
   Incremental Traffic Shift: 5% → 25% → 50% → 100%
        │
   At each step: Session Continuity confirmed, Connection Draining on
   Old Platform, Singapore/Dubai traffic share confirmed unaffected
        │
   Full Cutover (100% on New Platform, Hyderabad)
        │
   Post-Cutover Verification (smoke test + observation window)
        │
   Old Platform — retained as rollback target, then decommissioned
        │
   (Once stable — per v48's Migration Success Criteria) — Bangalore
   migration begins, using v44–v47's now-proven procedure
```

WebSphere Topics Covered: Blue-Green Deployment (reused/extended from P02
v21), Canary Deployment, Connection Draining, Health Policy-Driven
Traffic Shifting, Rollback, Cross-Region Traffic Independence.

Enterprise Learning: Zero-Downtime Operations at Platform Scale,
Progressive Delivery, Cutover Risk Management.

Sprint Deliverable: A full production cutover from old to new WAS
platform is performed in Hyderabad via incremental canary traffic
shifting (5%→25%→50%→100%), with zero customer-visible downtime; a
mid-cutover session is proven to survive the platform switch; connection
draining is confirmed on the old platform (no abruptly terminated
in-flight requests); a deliberate rollback is exercised at the 25%
traffic stage, proving the old platform can still take 100% of traffic
back cleanly; GSLB's routing of Singapore/Dubai-bound traffic is
confirmed unaffected throughout.

Version 48 — Enterprise Migration Capstone
Objective: Run the complete migration lifecycle end-to-end for India
(Hyderabad, then Bangalore), treating Versions 44–47 as the technical
building blocks and this version as the full planning-through-operate
exercise a lead WebSphere migration architect would actually own —
including the DR-pair migration and golden-template update this Part's
Scope Decision requires.

Banking Features Added: None.

Migration Lifecycle

Planning
- Inventory (every EAR/WAR, DataSource, queue, cert, LTPA key set — per
  doc 07's standing tables, India only)
- IBM Support Matrix re-validation (consolidates v44's checks — including
  the MQ compatibility gate — into one final go/no-go, applied first to
  Hyderabad and then, identically, to Bangalore)
- Risk Assessment (what breaks if X migration step fails, per component)
- Dependency Analysis (reuses doc 04 §"Deployment Dependency /
  Startup-Order Matrix" — migration order must respect the same
  startup-order dependencies)
- Rollback Planning (one rollback plan per phase below, not just one
  global "undo everything")
- Migration Freeze Window — standard enterprise change-management
  concepts, applied here rather than left implicit:
  - Code Freeze — no new commits to develop for the applications being
    migrated, for the duration of the migration window
  - Change Freeze — no unrelated infrastructure changes (patching,
    config tweaks) permitted on any environment touched by the migration
  - Migration Window — an explicitly scheduled start/end time,
    communicated in advance; scoped to India only (Hyderabad, then
    Bangalore) in this Part, distinct from P06 v43's "all three regions
    at once" framing, which does not apply here per the Scope Decision
  - CAB Approval — Change Advisory Board sign-off (simulated, per doc
    04's "simulate the artifact even solo" pattern) required before the
    window opens, mirroring the Emergency CAB concept already introduced
    in P05 v38

Build
- New infrastructure (from v44/v46, Hyderabad)
- New cell (Hyderabad; Bangalore follows using the identical procedure
  once Hyderabad is validated, per v44's DR-Pair Migration Sequencing)
- Automation — this is where migration explicitly reuses P05/P06's
  existing CI/CD (Jenkins, Git, Nexus, wsadmin, response files) rather
  than introducing a parallel toolchain, consistent with the Global
  Shared Services principle (P06) and this Part's Global Shared Services
  Boundary (v46) — the toolchain is reused, not migrated
- Security (carried over from v46, re-verified)

Migrate
- DMGR, Nodes (v46)
- Applications (v45)
- Databases (Oracle 21c XE version/compatibility check, if the DB engine
  itself is also being upgraded — otherwise confirm no forced DB
  migration was silently assumed)
- MQ (v46)
- IHS (v46)

Validate
- Smoke Testing (critical paths only — login, balance, Fund Transfer, per
  Environment Promotion Standards' Prod Promotion Checklist pattern)
- Functional Testing (full regression, per doc 03/04)
- Performance Testing (v45's before/after baseline)
- Security Testing (penetration-style validation of the migrated SSL/
  LTPA/LDAP config from v46 — at minimum, confirm no cert/key was left on
  default/self-signed-and-forgotten)
- Cross-Region Compatibility Testing (v45/v47's mTLS, MQ Hub, and GSLB
  checks, re-confirmed at capstone level)

Cutover (executes v47's mechanics)
- DNS / GSLB (P06 v40) — India's entry unaffected in routing logic;
  Singapore/Dubai untouched
- Plugin (regenerated, per v45/v46)
- Load Balancer (traffic shifting, per v47)

Operate
- Monitoring (P04 stack, confirmed still multi-region-aware per P06 v43
  even though only India's data now reflects the new platform)
- Incident Handling (P04 v35 runbook discipline, applied to a
  migration-specific incident type)
- Rollback (the actual, tested rollback — not just a documented plan —
  executed at least once during this capstone, per the Production
  Exercises discipline established in P05 v38)

DR-Pair Completion & Golden Template Update (added; closes the two
remaining gaps from the pre-scope-decision review)
- Once Hyderabad's migration is fully validated (Success Criteria below
  all true), Bangalore is migrated using the identical v44–v47 procedure.
  India's DR pair is not considered migration-complete until both sites
  run the new WAS version — a real P05-style DR drill should not be
  executed for real against a mismatched-version pair.
- India's fork of the golden cell template (P06 v43) is updated in Git
  to reflect the new WAS version, new Java SDK, and any config changes
  from v44–v46, immediately once Hyderabad+Bangalore are both migrated —
  otherwise P06's configuration-drift detection would begin incorrectly
  flagging India's correctly-migrated cells as drifted from a now-stale
  template. Singapore and Dubai's forks of the template remain
  unchanged, since those regions are not migrated in this Part.

Migration Artifacts (documentation deliverables for this version)
- Migration Runbook (in the same shape as P04 v35 / P05 v38's runbooks:
  Trigger → Steps → Validation → Rollback Trigger → Closure)
- Rollback Runbook (a distinct document, not a subsection — rollback
  under migration pressure needs its own clear, standalone reference)
- Validation Checklist (derived from the Validate phase above)
- Downtime Plan (even if the target is zero customer-visible downtime,
  the plan documents the worst-case fallback window and who approves
  exceeding it)
- Sign-off Checklist (mirrors doc 03/04's sign-off pattern: all
  High-priority cases passed, no open Critical/High defects, Migration
  Runbook demonstrably followed, ready to decommission old platform)

Migration Success Criteria (what "done" actually means)
The capstone — and by extension the whole Part — is not complete until
every item below is true simultaneously, not just individually achieved
at some point during the process, for **both** Hyderabad and Bangalore:

- No open Critical defects
- No open High defects
- All 9 applications available and passing health checks (India only)
- Performance within the acceptable range established by Version 45's
  before/after baseline (no unexplained regression)
- Security validated — SSL/LTPA/LDAP migration from Version 46 confirmed,
  no cert or key left on a stale/default configuration
- Monitoring operational — P04 observability stack confirmed reporting
  correctly against the new platform, for India
- Backup completed — full backup inventory (per P05 v38's expanded
  inventory, doc 07 §7's Certificate Inventory) taken against the new
  platform
- Rollback tested — not just planned; the Rollback Runbook has been
  executed at least once as a real drill (per Version 47/48 above)
- **India's DR pair confirmed matched** — Hyderabad and Bangalore both
  running the same new WAS version, not a mixed-version pair
- **Golden cell template updated** — India's fork reflects the new
  platform; Singapore/Dubai's forks confirmed still correctly describing
  their (unmigrated) current state

If any one of these is false, the migration is not signed off — it stays
in-flight, parallel environment retained, old platform not
decommissioned.

Migration Assessment (pre-work, folded in here rather than as a separate
version)
- Current-state inventory (component/version list, India only)
- Compatibility analysis (Migration Toolkit output from v45)
- Unsupported features / deprecated APIs (final consolidated list, with
  resolution status)
- Third-party library review (any library incompatible with the new
  Java SDK from v44)

WebSphere Topics Covered: Everything from Versions 44–47, exercised
together as one governed lifecycle, for both sites of India's DR pair;
Migration Governance, Cutover Risk Management, Post-Migration
Decommissioning, DR-Pair Completion, Golden Template Maintenance.

Enterprise Learning: Enterprise Migration Program Management, Risk-Based
Planning, Full Lifecycle Change Governance, DR-Aware Migration
Discipline.

Sprint Deliverable: India completes the full Plan → Build → Migrate →
Validate → Cutover → Operate lifecycle for the platform migration, first
at Hyderabad and then, using the identical proven procedure, at
Bangalore; a Migration Runbook and separate Rollback Runbook are both
executed at least once (the rollback runbook exercised as a deliberate
drill, not just written); the full Validation Checklist and Sign-off
Checklist are completed with real results, not placeholders; India's DR
pair is confirmed running the same new WAS version at both sites; the
golden cell template's India fork is updated in Git to match; the old
platform is formally decommissioned only after a defined post-cutover
observation window closes cleanly with no rollback triggered.

Completion Checklist
□ IBM Support Matrix Validation includes and passes the MQ version
  compatibility check, alongside WAS/Java SDK/OS/IHS/IM (v44)
□ WAS platform and Java SDK upgraded in Hyderabad first, with
  backupConfig/WASPreUpgrade/WASPostUpgrade discipline followed and at
  least one deliberate node-level rollback proven, while Bangalore
  remains available as the real DR fallback throughout (v44)
□ All 7 EARs + 2 WARs redeployed and validated on Hyderabad's new
  platform — JNDI/JDBC/JMS resources, session persistence, and plugin
  regeneration all confirmed, full regression pack passed (v45)
□ Cross-region compatibility (mTLS to Singapore/Dubai, MQ Hub channels,
  GSLB routing) confirmed functioning across the India/Singapore/Dubai
  version skew (v45/v47)
□ Nodes, DMgr, IHS, IBM MQ, SSL certs, LTPA keys, and India's regional
  LDAP config migrated in Hyderabad; node federation validated; LTPA
  keys confirmed synchronized between Hyderabad and all not-yet-migrated
  cells (Bangalore, Singapore, Dubai); at least one in-flight EJB Timer
  job proven to resume correctly (v46)
□ Global Shared Services (Central LDAP, Jenkins, Git, Nexus, MQ Hub,
  Prometheus/Grafana/ELK) confirmed unchanged and still functioning
  against Hyderabad's new platform (v46)
□ A full zero-downtime cutover executed in Hyderabad via incremental
  canary traffic shifting, with session continuity and connection
  draining both proven, a rollback exercised mid-cutover, and
  Singapore/Dubai's GSLB traffic share confirmed unaffected (v47)
□ Full migration lifecycle (Plan → Build → Migrate → Validate → Cutover
  → Operate) executed for Hyderabad, then repeated for Bangalore;
  Migration Runbook and Rollback Runbook both executed at least once;
  Validation and Sign-off Checklists completed with real data (v48)
□ India's DR pair (Hyderabad + Bangalore) confirmed running the same new
  WAS version — no mixed-version DR pair left as an end state (v48)
□ India's golden cell template fork updated in Git to reflect the new
  platform; Singapore/Dubai's forks confirmed unchanged and still
  accurate for their current (unmigrated) state (v48)
□ Every migration step verified through the existing P04 observability
  stack (Grafana/Prometheus/Jaeger/ELK) — not manual inspection alone
□ All five versions' TestCases-v44.md–v48.md signed off per Test Case
  Standards
□ VM inventory (doc 01) and Certificate Inventory (doc 07 §7) updated to
  reflect India's new platform version and any regenerated/imported
  certs
□ Old platform (India only) formally decommissioned only after Version
  48's observation window closes with no rollback
□ Part promoted Dev → UAT → Prod per Environment Promotion Standards,
  part7-release tag applied (India only; Singapore/Dubai unaffected)

Application State After This Part
Application code: unchanged from P06 (digistack-bank family of 7 EARs +
Mobile/ATM Tomcat WARs) — zero new banking functionality was added in
this Part.

Platform Changes (India only — Hyderabad and Bangalore)
- WAS ND upgraded to new version (e.g., 8.5.5 → 9.0) in India (v44)
- Java SDK upgraded, managesdk-managed coexistence retired once
  migration confirmed stable (v44)
- All 9 applications validated and redeployed on India's new platform
  (v45)
- Infrastructure tier (DMgr, Nodes, IHS, IBM MQ, SSL, LTPA, India's
  regional LDAP config) migrated and re-validated in India (v46)
- Zero-downtime cutover mechanics (canary, incremental traffic shift,
  connection draining) now a reusable pattern for future migrations (v47)
- Full migration governance artifacts (Migration Runbook, Rollback
  Runbook, Validation/Sign-off Checklists) established as the template
  for any future platform migration, including Singapore/Dubai's
  eventual migration in a later, explicitly-scoped Part (v48)
- India's golden cell template fork updated to the new platform;
  Singapore/Dubai's forks unchanged (v48)

Unchanged (explicit — Scope Decision)
- Singapore and Dubai remain on the pre-migration WAS version, pre-
  migration Java SDK, and their original golden cell template fork,
  throughout this entire Part
- Global Shared Services (Central LDAP, Jenkins, Git, Nexus, MQ Hub,
  Prometheus/Grafana/ELK) unchanged

Explicit transition rule: every Part from this point forward assumes the
migrated platform (new WAS version, new Java SDK) as the current state
**for India**. Singapore and Dubai continue to be referenced at their
pre-migration platform version until a future, explicitly-scoped Part
migrates them. The pre-migration Hyderabad/Bangalore platform is retained
only as historical record — it is not referenced as "current,"
"existing," or a fallback for India in any future Part's roadmap text,
request-flow diagrams, or VM inventory, except where a future chat
explicitly needs to discuss migration history for context.

Carried Forward to P08
The migrated, upgraded India WebSphere platform (new WAS version, new
Java SDK) becomes the baseline for India in all future work. Singapore
and Dubai continue on their P06 baseline until their own migration is
explicitly scoped in a future Part — no version after this Part should
assume Singapore/Dubai are already migrated.
