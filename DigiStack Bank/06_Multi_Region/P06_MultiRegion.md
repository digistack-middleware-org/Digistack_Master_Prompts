ID: P06
Version: 1.1
Status: Active

Title: Multi-Region Enterprise Banking & Middleware Architecture

Imports: IDX STD ARCH02 STDGAP01 CAP01 RACI01

Exports: Versions 39-43
Global Shared Services (standing layer)
Data Residency / Multi-Region Data Model decision
Multi-Region Banking Architecture
Global Traffic Management
Cross-Region Integration
Global Security & Identity Management
Enterprise Middleware Architect Capstone

Used By: P07 P08

Next: P07

Purpose
Build a globally distributed DigiStack Bank spanning multiple countries
and data centers, learning the architecture and operational practices
used by multinational banks — global traffic management, cross-region
integration, federated identity, and the standing shared-services layer
every region depends on.

Prerequisite
P05 Completion Checkpoint satisfied — HA (v36), DR (v37), and Business
Continuity (v38) all operational at a single DR-paired site (Hyderabad ↔
Bangalore).

Deployment Model
No new banking features are added in this Part. Every version extends
the existing 9-application topology (P03) and its observability (P04)
and HA/DR discipline (P05) from a single-site/single-DR-site model into a
true multi-region model. The banking application itself is the same one
built through P05 — this Part is entirely architecture, routing,
integration, identity, and operations.

Process for every version: Requirements → Development/Configuration
(beginner-level explanation) → Deployment & Admin across regional WAS
cells → Testing → Documentation → Pause for approval before the next
version.

Per-version deliverables (per Master Index standing standards): VM Setup
section, Git-committed config/scripts, TestCases-v.md, SetupDoc-v.md, SQL
migration script(s) only if a region-tracking table is added.

Version Numbering Correction (resolved before this file is marked ready)
The draft material this Part was written from numbered its versions
38–42. That collides with P05, which already owns and has frozen
Versions 36–38 (per Engineering Standards §7 — Version Numbering Freeze:
v38 is already "Business Continuity & Application Resilience"). Per the
freeze rule, P06 is shifted forward so it starts immediately after P05
ends.

Because no version in the P06 draft had been implemented, and the only
change is a uniform +1 shift with no internal reordering, this is a
straightforward offset, not a historical renumbering of frozen content —
a full mapping table (as required for P03/P04/P05's own renumbering
passes, which did reorder or split versions) isn't necessary here. The
offset is simply:

| Draft # | New # | Title |
|---|---|---|
| 38 | 39 | Multi-Region Banking Architecture |
| 39 | 40 | Global Traffic Management |
| 40 | 41 | Cross-Region Integration |
| 41 | 42 | Global Security & Identity Management |
| 42 | 43 | Enterprise Middleware Architect Capstone |

P06 title, confirmed: Multi-Region Enterprise Banking & Middleware
Architecture is P06 (Option A from the MASTER INDEX's Open Decisions).
This matches P05's existing "Carried forward into P06" note, which
already assumes P06 is the multi-region Part — no edit needed there.
WebSphere Migration is deferred to a later Part (P07), to be scoped when
reached.

DR Scope Decision (stated explicitly, not left implicit)
Only India retains a DR pair (Hyderabad ↔ Bangalore, per P05). Singapore
and Dubai are each stood up as single-site regional deployments with no
DR counterpart of their own. This is an intentional lab scope boundary —
building three fully DR-paired regions (six sites total) is out of scope
for this project's lab environment — not an oversight. Consequently,
v43's "DR failover (a full region acts as DR for another)" simulation is
a **conceptual/tabletop exercise only**: it discusses how a real
multi-region bank might use one healthy region as a coarse-grained
fallback for another, without a technical DR relationship actually being
built between Singapore/Dubai and any other region. Do not confuse this
with India's real, technically-implemented DR pairing from P05 — they
are different in kind, not just scale.

Standing Architectural Layer — Global Shared Services
Introduced here as a persistent layer referenced in every diagram in
this Part, not as its own version. Three regions each running
independent LDAP, CI/CD, and monitoring stacks would be duplicated
infrastructure, not real multi-region architecture — real banks
centralize exactly these services and let every region consume them.

Global Shared Services
────────────────────────────────────────────────────
Enterprise LDAP
Enterprise PKI / CA
Git
Jenkins
Nexus (Artifact Repo)
Enterprise Monitoring (Prometheus/Grafana)
Enterprise Logging (ELK)
ServiceNow (concepts)
SMTP / SMS Gateway
NTP
DNS
IBM MQ Hub (cross-region messaging backbone)
────────────────────────────────────────────────────
consumed by every region ↓

Rule: every region-specific diagram from Version 39 onward sits
underneath this layer, not beside it. A regional CBS talks to its own
regional database, but authenticates against the same federated identity
layer (v42), ships logs/metrics to the same monitoring stack (P04,
extended here to be multi-region-aware), and is built/deployed through
the same Git/Jenkins/Nexus pipeline — regional independence is at the
data and traffic-serving layer, not at the tooling layer.

This layer is referenced by filename cross-reference in each version
below rather than repeated per-version — consistent with how doc 07
(Configuration & Cross-Cutting Standards) is referenced rather than
duplicated elsewhere in the roadmap.

Regional MQ vs. the Global MQ Hub — Explicit Relationship (added; this
was previously implied but never drawn)
Each region keeps its own local Messaging Engine/queue manager (the same
SIBus infrastructure carried forward from P02/P05) for in-region async
processing — a Singapore Fund Transfer's internal messaging never leaves
Singapore. The Global Shared Services "IBM MQ Hub" is a **separate,
central queue manager network** that regional queue managers connect
*out* to via MQ's own inter-queue-manager channels (sender/receiver
channel pairs), specifically for the events that must cross regions —
today, that's the "Central notification service" event stream (v41).
This is a hub-and-spoke topology, not one shared queue manager across
three regions and not direct region-to-region MQ channels:

```
India QM ──channel──┐
                     │
Singapore QM ──channel──►  MQ Hub (Global Shared Services)  ──► Central Notification Service
                     │
Dubai QM ──channel───┘
```

v41's "IBM MQ channels secured with TLS for the cross-region messaging
backbone" refers specifically to the channels between each regional QM
and the Hub shown above, not a direct India↔Singapore↔Dubai mesh.

Data Residency / Multi-Region Data Model — Explicit Decision (added per
Senior Architect Review, Finding #9)
Version 39 stands up each region with its own independent PostgreSQL
Primary/Standby cluster, implying regional data silos. Version 41 then
adds cross-region customer lookup and account verification, which only
makes sense if a customer's data can legitimately be found from another
region — implying the regions are not fully independent after all. This
ambiguity is resolved here, before either version is built, since it
materially changes what v41's deliverable actually does:

Resolution: data-residency model. Each region's CIF (Customer
Information File, P03 v24) is authoritative for its own region's
customers — this is not one global customer table sharded by region, and
there is no replicated global customer index anywhere in this design.
"Cross-region customer lookup" (v41) means a live query into a foreign
region's CBS, over the v41 mTLS-secured REST channel, at the moment it's
needed — not a query against a local, eventually-consistent copy of
another region's data. This mirrors how real multi-country banks
commonly operate under data-residency regulation: a customer's data
legitimately lives in one region, and other regions reach it live, on
demand, rather than replicating it wholesale. If a future version needs
different semantics (e.g., a genuinely global customer able to transact
identically from any region), that is a new, explicitly-scoped data-model
change — not something to assume silently follows from v41's lookup
feature as written.

Regulatory caveat (added): the resolution above settles the *technical*
question only. It deliberately does not resolve the real-world regulatory
question — India (RBI), Singapore (MAS, PDPA), and Dubai (CBUAE, PDPL)
each have distinct, genuinely different data-localization and
cross-border data-transfer rules, and a live cross-region query of the
kind v41 implements would, in a real multinational bank, require
per-region legal/compliance sign-off before being enabled in production.
This project implements the technical mechanism only, as a WebSphere/
architecture learning exercise; SetupDoc-v41.md should state plainly that
the regulatory clearance step is out of scope and assumed, not solved.

Currency / Locale Scope — Explicit Exception (added; otherwise a silent
assumption)
DigiStack CBS was built (P01–P05) against a single-currency, India/RBI/
NEFT-style domestic banking model. Deploying the same CBS to Singapore
and Dubai in this Part does **not** add SGD/AED currency support, local
payment-rail integration (e.g., PayNow, UAEFTS), or local regulatory
logic — doing so would be new banking functionality, which the Phase-2
Application-Freeze Rule (carried forward from P05) does not permit
without an explicit, justified exception, and no such exception is being
made here. Singapore and Dubai deployments in this Part should be
understood as **the same INR-denominated CBS application running on
infrastructure located in a different country**, purely to exercise
multi-region WebSphere architecture, traffic management, and identity —
not as a localized banking product. If a future Part adds real
multi-currency support, it must do so explicitly and be called out the
same way P05 v38 called out its one narrow idempotency exception.

Version 39 — Multi-Region Banking Architecture
Objective: Expand DigiStack Bank from a single-country (Hyderabad +
Bangalore DR) deployment into a true multi-region platform, with
independent, fully-functioning regional deployments.

Banking Features Added: None. This version replicates the existing
9-application topology (P03) into two additional regions — it is an
infrastructure/architecture exercise, not a new feature set. Per the
Currency/Locale Scope note above, all three regions run the same
INR-denominated CBS; no localization is added.

Regions
- 🇮🇳 India (existing Hyderabad site becomes the India region; Bangalore
  remains its DR pair per P05)
- 🇸🇬 Singapore (new region — single-site, no DR pair; see DR Scope
  Decision above)
- 🇦🇪 Dubai (new region — single-site, no DR pair; see DR Scope Decision
  above)

Regional Components (per region)
- Enterprise Load Balancer
- IBM HTTP Server
- WebSphere ND Cell (Portal, CBS, Payment Hub, Notification Service,
  Reporting Service, Branch Portal, Card Portal — same 7 EARs as P03,
  plus Mobile/ATM Tomcat apps)
- DigiStack CBS
- Regional Messaging Engine / queue manager (local SIBus, connected
  outward to the Global MQ Hub — see Regional MQ vs. Global MQ Hub above)
- Regional PostgreSQL Cluster — Primary + Standby (streaming
  replication), not a single database instance. This preserves
  continuity with the Primary/Standby pattern already established for DR
  in P05 v37, rather than introducing a second, inconsistent replication
  model for regional data. (Note: for Singapore/Dubai, this Primary/
  Standby pair is *intra-region* resilience only, not a DR site — per the
  DR Scope Decision, there is no second site for either region.)

Architecture
```
                          Global Users
                               │
                        Global DNS / GSLB
                               │
        ┌──────────────────────┼──────────────────────┐
        ▼                      ▼                      ▼
   India Region          Singapore Region         Dubai Region
        │                      │                      │
   Enterprise LB          Enterprise LB          Enterprise LB
        │                      │                      │
IBM HTTP Server Cluster  IBM HTTP Server Cluster  IBM HTTP Server Cluster
        │                      │                      │
  WebSphere ND Cell       WebSphere ND Cell       WebSphere ND Cell
        │                      │                      │
   DigiStack CBS           DigiStack CBS           DigiStack CBS
        │                      │                      │
Regional PostgreSQL      Regional PostgreSQL      Regional PostgreSQL
Cluster (Primary/Standby) Cluster (Primary/Standby) Cluster (Primary/Standby)

──────────────────────────────────────────────────────────────────────────
                 Global Shared Services (see standing layer above)
──────────────────────────────────────────────────────────────────────────
```

WebSphere Topics Covered: Multi-Cell Architecture, Cell Isolation,
Regional Deployments, Environment Management.

Enterprise Learning: Global Architecture, Multi-Region Deployment,
Regional Operations.

Sprint Deliverable: Three independent regional deployments (India,
Singapore, Dubai) are each fully operational — own LB, IHS, WAS cell,
CBS, local Messaging Engine, and Primary/Standby PostgreSQL cluster —
each capable of serving banking traffic in complete isolation from the
other two, with no cross-region dependency yet (that's v41).

Version 40 — Global Traffic Management
Objective: Route users to the nearest healthy region and fail over
cleanly when a region becomes unhealthy.

Banking Features Added: None.

Enterprise Components
- Global DNS
- Global Server Load Balancing (GSLB)
- Health Checks (reused, not reinvented)
- Regional Failover
- User Routing

Health Check Design — Reuses Existing Mechanisms
Rather than introducing a new, parallel health-check scheme, GSLB routing
decisions are driven by the exact health signals already built earlier
in the roadmap:

| Layer | Mechanism | Source |
|---|---|---|
| Application Health | /health liveness/readiness endpoint | P04, Version 31 |
| WebSphere Health | Cluster member status | P01 v5 clustering, P05 v36 HA |
| Database Health | PostgreSQL connectivity + replication lag | P05 v37/v38 streaming replication |

GSLB polls these same endpoints per region rather than a bespoke probe —
a region is only considered healthy if all three layers report healthy,
consistent with the existing observability stack rather than a duplicate
one.

Example Routing
```
User from India     → India DC
User from Singapore → Singapore DC
User from UAE       → Dubai DC
India DC unavailable → routes to Singapore DC (nearest healthy region)
```
Note: since Singapore/Dubai have no DR pair (per the DR Scope Decision
above), "India DC unavailable" here means GSLB routes India's *traffic*
to Singapore as the nearest healthy region — it does not mean Singapore
becomes India's DR site in the P05 v37 sense (no data failover, no
Fund-Transfer RPO/RTO guarantee, since Singapore's CBS holds Singapore's
own customers, not India's). This is a traffic-continuity measure, not a
data-recovery one; the distinction matters and should not be conflated
in SetupDoc-v40.md.

Architecture
```
                       Users
                         │
                    Global DNS
                         │
          Global Server Load Balancer (GSLB)
                         │
    ├── Health Probe: /health (v31)        ──► Application Health
    ├── Health Probe: Cluster Status       ──► WebSphere Health
    └── Health Probe: Replication Lag      ──► Database Health
                         │
             ┌───────────┼───────────┐
             ▼           ▼           ▼
           India     Singapore     Dubai
```

WebSphere Topics Covered: Health Policies (extended from P04 v31 and P05
v36), Cluster Health Reporting.

Enterprise Learning: Geo Routing, Regional Failover, Traffic Engineering.

Sprint Deliverable: GSLB correctly routes a simulated user in each region
to their own regional DC under normal conditions; a deliberate
India-region health-check failure (across all three layers) causes GSLB
to fail traffic over to Singapore within the defined detection window,
verified through the same Grafana/Prometheus dashboards used in P04/
P05 — not a separate monitoring view.

Version 41 — Cross-Region Integration
Objective: Allow secure communication between regional banking systems
for the specific cases where a customer or operation legitimately spans
regions.

Banking Features Added: None (integration only — no new banking
transaction types). Per the Currency/Locale Scope note above, all
cross-region lookups operate on the same INR-denominated CIF format in
every region — no currency-conversion logic is introduced here.

Features
- Cross-region customer lookup
- Cross-region account verification
- Cross-region reporting
- Regional service APIs
- Central notification service (consumes events from all three regions
  via the Global Shared Services' MQ Hub — see Regional MQ vs. Global MQ
  Hub above for the exact topology)

Security Model — Explicit, Not Generic
Cross-region traffic is never sent in the clear or over unauthenticated
channels. Building directly on P01 v12's end-to-end mTLS work (rather
than introducing a new trust model for inter-region calls):
- Mutual TLS (mTLS) between regions for all service-to-service calls
- REST APIs over HTTPS exclusively — no plain HTTP cross-region path,
  ever
- IBM MQ channels secured with TLS for the cross-region messaging
  backbone, specifically the regional-QM-to-Hub channels defined above
- Certificate-based trust between regional Certificate Authorities,
  tying into doc 07 §7's Certificate Inventory (extended here with
  cross-region entries)

Data Residency reminder: see the Data Residency / Multi-Region Data Model
decision and its Regulatory caveat above — this version implements the
live-query technical mechanism only; real-world regulatory clearance for
cross-border customer data access is explicitly out of scope and assumed.

Architecture
```
India CBS
   │  REST (HTTPS + mTLS) / IBM MQ (TLS, via MQ Hub)
   ▼
Singapore CBS
   │  REST (HTTPS + mTLS) / IBM MQ (TLS, via MQ Hub)
   ▼
Dubai CBS
```

WebSphere Topics Covered: Cross-Cell Communication, IBM MQ Across
Regions, Secure REST APIs (mTLS), Service Federation.

Enterprise Learning: Enterprise Integration, Cross-Region Services,
Distributed Systems.

Sprint Deliverable: A cross-region customer lookup (e.g., an India
customer verified against Singapore's CIF) succeeds only over an
mTLS-authenticated REST call; a cross-region MQ message (e.g., a central
notification triggered by a Dubai transaction) is confirmed encrypted in
transit and shown traversing the Hub topology (regional QM → MQ Hub →
Central Notification Service), not a direct region-to-region channel; a
deliberately mismatched/expired cross-region certificate is proven to
fail the connection, not silently degrade to an insecure fallback.

Version 42 — Global Security & Identity Management
Objective: Provide centralized authentication and consistent security
across all regions, so a single identity works everywhere rather than
each region maintaining its own user store.

Banking Features Added: None.

Features
- Central LDAP (concepts, part of Global Shared Services)
- Single Sign-On (SSO)
- LTPA Token Sharing across regional cells
- LTPA Key Synchronization — called out explicitly as its own subsection
  below, not folded silently into "SSL Trust"
- Trust Association Interceptor (TAI) — concept-level
- Certificate Management (cross-references v41's cross-region
  certificate trust)
- Regional Role Mapping
- Audit Consolidation

LTPA Key Synchronization — Why It Gets Its Own Subsection
Single Sign-On across India/Singapore/Dubai cells depends entirely on
every cell trusting the same LTPA keys. If keys are generated
independently per cell (the default), a token issued by India is
rejected by Singapore, and SSO silently fails — often the least obvious
failure mode to debug in a federated WebSphere environment, and a
recurring real-world interview topic. This version's deliverable
explicitly proves keys are exported from one cell and imported into the
other two, not merely that each cell has a working LTPA configuration in
isolation.

Registry Migration Note (added per Senior Architect Review, Finding #7)
P01 v10 introduced "File Registry (or LDAP)" for Customer/Administrator
roles, without committing to which was actually built. This version
introduces Central LDAP as part of Global Shared Services. If v10 was
built as a file-based registry, this version is a clean cutover — there
are no pre-existing user IDs/group mappings that need to survive the
move, and that should be stated plainly in SetupDoc-v42.md rather than
left ambiguous. If v10 was instead built directly against LDAP already,
then this version's job is federating that existing LDAP into the new
central/multi-region identity layer, and SetupDoc-v42.md must document
how existing users/groups/role mappings carry over — UID/DN mapping
mismatches between a standalone registry and a federated repository are a
common real-world WebSphere security-realm migration failure mode, and
this project should name that risk explicitly rather than silently
assume a smooth cutover, consistent with how P07 treats the WAS platform
migration.

Trust Association Interceptor (TAI) — Concept
Covered at concept level only (no dedicated external SSO product
installed, consistent with how P04 treats Dynatrace/AppDynamics/Instana
as concepts-only): TAI is how WebSphere delegates authentication
decisions to an external reverse-proxy or SSO product sitting in front of
the application server, rather than performing authentication itself.
Understanding where TAI fits relative to LTPA/SSO is the expected
takeaway, not a working TAI deployment.

Architecture
```
                        Users
                          │
        Identity Provider (Central LDAP + LTPA,
             part of Global Shared Services)
                          │
             ┌────────────┼────────────┐
             ▼            ▼            ▼
           India      Singapore      Dubai
     (each cell trusts the same synchronized LTPA keys)
```

WebSphere Topics Covered: Federated Repositories, LDAP, LTPA Across
Cells (incl. Key Synchronization), SSL Trust Between Regions, Trust
Association Interceptor (concept), Administrative Security.

Enterprise Learning: Identity Federation, Global Authentication,
Enterprise Security.

Sprint Deliverable: A user authenticates once against the central
identity provider and moves between India/Singapore/Dubai-hosted
services without re-authenticating, proven by exporting/importing
synchronized LTPA keys across all three cells (not just configuring each
independently); a deliberately unsynchronized fourth cell is shown to
reject the shared token, demonstrating why synchronization — not mere
LTPA presence — is the actual requirement.

Version 43 — Enterprise Middleware Architect Capstone
Objective: Operate DigiStack Bank as if acting as the lead middleware
architect for a global enterprise — build, operate, simulate failures
across, and validate the full multi-region platform. This version also
absorbs global configuration-management discipline, folded in here
rather than as a separate fractional version.

Banking Features Added: None.

Global Configuration Management (folded in from the original "41A"
recommendation)
Rather than a separate version, configuration consistency across three
regional cells is treated as part of what "operating" a global platform
means — the natural home for it is this capstone, where build/operate/
simulate/validate all come together.
- wsadmin automation across all three cells
- Configuration consistency — property files, environment variables
- Cell templates (a "golden" cell configuration each region's cell is
  built from) — the golden template itself is version-controlled in Git
  (Global Shared Services) and is included in the Backup & Configuration
  Inventory below, since losing the template with no regional cell left
  to reverse-engineer it from would be a real operational gap
- Standard JVM settings, shared SSL configuration, shared security
  policies (regional role mapping from v42 layered on top)
- Plugin standardization, DataSource standardization across regions
- Configuration drift detection — proving a manually-modified regional
  cell is caught and reconciled against the golden template, not just
  documented as a risk

Global Failure Taxonomy (extends P05 v36's Failure Type Taxonomy to
multi-region scope; given the same formal treatment here rather than
left as prose, since it's referenced by name in the Simulate section
below)

| Failure Type | Scope | First introduced |
|---|---|---|
| Server / Application / Database / Storage / Network / DNS / LB Failure | Single region, single site | P05 v36 |
| Site Failure | Single region's DR pair (India only, per DR Scope Decision) | P05 v37 |
| **Regional Failure** | An entire region (all sites within it) goes dark | P06 v43 |
| **Network Partition** | Connectivity between two or more regions is lost, while each region individually stays healthy — distinct from a Regional Failure, since here every region is up but can't reach the others (this breaks v41's cross-region calls and the v42 LTPA/SSO trust chain, even though no single region is "down") | P06 v43 |

Multi-Region Incident Governance (added; P05 resolved this for a
single-site Site Failure via RACI01's Incident Commander — a Regional
Failure or cross-region Network Partition raises the same question at
higher stakes: different time zones, potentially different regulators
per region, and no existing single-region Incident Commander has natural
authority across all three). Per RACI01 §4, a Regional Failure or
Network Partition event escalates to a **Global Incident Commander**
role (RACI01 §4's cross-region extension of the same Incident Commander
concept already used in P04 v35 and P05 v37/v38) — this is a
documentation cross-reference only; no new technical scope is added by
naming this role, consistent with how P05 handled the same question for
its own Site Failure governance note.

Activities

Build
- Multi-cell WebSphere topology
- Multi-region deployment
- Automated deployments (Jenkins/Git/Nexus, per Global Shared Services)
- Standardized configurations (golden cell template, above)

Operate
- Monitoring (multi-region-aware Grafana/Prometheus, extending P04)
- Logging (multi-region ELK, extending P04 v32)
- Incident response (extending P04 v35's runbook discipline to a
  regional/global scope, under the Global Incident Commander above for
  Regional Failure/Network Partition events specifically)
- Change management
- Capacity planning (per-region, extending P04 v35)

Simulate
- Regional outage (an entire region goes dark — GSLB from v40 must route
  around it) — classified as Regional Failure in the Global Failure
  Taxonomy above
- Node failure (reuses P01 v5/P05 v36 patterns, at regional scale)
- Database outage (reuses P05 v38's DB continuity patterns, per region)
- Certificate expiry (cross-region, from v41's trust model)
- IBM HTTP Server failure
- Cluster member failure
- Network partition (between regions) — classified as Network Partition
  in the Global Failure Taxonomy above; verify specifically that v41's
  cross-region calls fail closed (mTLS/REST timeouts, not silent
  fallback) and that v42's SSO degrades to region-local authentication
  only, rather than an undefined state, while the partition is active
- DR failover (a full region acting as DR for another) — per the DR
  Scope Decision above, this is a **conceptual/tabletop walkthrough only**
  for Singapore/Dubai; only India's actual Hyderabad↔Bangalore pairing
  (P05 v37) is technically exercised here

Validate
- Zero-downtime deployment (extends P05 v38's rolling restart to a
  multi-region rollout)
- Rollback
- DR testing (India's real DR pair only, per the DR Scope Decision — see
  Simulate above)
- Backup & Configuration Inventory validation (extended from P05 v38's
  single-site inventory to multi-region scope; see table below)
- Monitoring verification (per the standing Monitoring Verification
  requirement established in P05 v36–v38)

Backup & Configuration Inventory — Multi-Region Extension (added; P05
v38 built this for one site, it is restated here at ×3-region scope
rather than assumed to carry over silently)

| Item | Scope in P06 |
|---|---|
| WAS profile / IHS / plugin / SSL-keystore / DB / EAR-WAR / MQ / scripts / Git / Grafana / Prometheus backups (P05 v38 baseline) | Performed independently per region (India, Singapore, Dubai) — three separate backup sets, not one |
| Golden cell configuration template | Version-controlled in Git (Global Shared Services); this is new in P06 and not part of P05's original inventory |
| Central LDAP / LTPA key material | Backed up centrally (Global Shared Services), not per-region, since it's shared infrastructure |
| Cross-region certificate trust store (v41) | Backed up centrally alongside the Enterprise PKI/CA in Global Shared Services |

Final Enterprise Architecture
```
                          Global Users
                               │
                        Global DNS / GSLB
                               │
        ┌──────────────────────┼──────────────────────┐
        ▼                      ▼                      ▼
   India Region          Singapore Region         Dubai Region
        │                      │                      │
   Enterprise LB          Enterprise LB          Enterprise LB
        │                      │                      │
IBM HTTP Server Cluster  IBM HTTP Server Cluster  IBM HTTP Server Cluster
        │                      │                      │
  WebSphere ND Cell       WebSphere ND Cell       WebSphere ND Cell
        │                      │                      │
  ┌─────┴─────┐          (same pattern in each region)
  ▼           ▼
Internet    CBS  ATM / Card / Branch / Payment Hub /
Banking          Notification / Reporting
        │
Regional PostgreSQL Cluster (Primary/Standby)

──────────────────────────────────────────────────────────────────────────
                        Global Shared Services
──────────────────────────────────────────────────────────────────────────
Enterprise LDAP | PKI | Git | Jenkins | Nexus | IBM MQ Hub
Prometheus | Grafana | ELK | ServiceNow (concepts) | SMTP/SMS | NTP | DNS
──────────────────────────────────────────────────────────────────────────
```

WebSphere Topics Covered: Everything from Versions 39–42, exercised
together under simulated failure conditions (per the Global Failure
Taxonomy above); Configuration Drift, Configuration Compliance, Golden
Cell Templates, Global Incident Governance.

Enterprise Learning: Enterprise Middleware Architecture, Global
Operations, Multi-Region Incident Management, Configuration Governance
at Scale.

Sprint Deliverable: All Build/Operate/Simulate/Validate activities above
are performed at least once; a Regional Failure and a Network Partition
are each simulated and classified correctly against the Global Failure
Taxonomy, with cross-region calls (v41) and SSO (v42) shown failing
closed/degrading gracefully rather than silently misbehaving during the
partition; the golden cell template, central LDAP/LTPA material, and
cross-region certificate trust store are each confirmed restorable from
backup, alongside each region's independent P05-style backup set;
India's real DR pair is tested per P05 v37, while Singapore/Dubai's
"regional DR" is documented as a tabletop exercise only, per the DR
Scope Decision.

Completion Checklist
□ Three independent regional deployments (India, Singapore, Dubai) each
  fully operational in isolation (v39)
□ DR Scope Decision (India-only DR pairing) documented in SetupDoc-v39.md
  so it isn't mistaken for an oversight (v39)
□ Regional MQ ↔ Global MQ Hub topology documented and at least one
  cross-region event shown traversing it correctly (v39/v41)
□ GSLB correctly routes normal traffic per-region and fails over India's
  traffic to Singapore under a simulated India health-check failure,
  across all three health layers (v40)
□ Cross-region customer lookup succeeds only over mTLS; mismatched/
  expired certificate fails closed, not silently (v41)
□ Data-residency technical model documented, with the regulatory caveat
  (RBI/MAS/CBUAE differences) explicitly stated as out of scope (v41)
□ Currency/locale scope exception (same INR CBS in all three regions, no
  localization) stated explicitly, not left implicit (v39/v41)
□ SSO works across all three cells via synchronized LTPA keys; an
  unsynchronized fourth cell is shown rejecting the shared token (v42)
□ Registry migration path (file-registry cutover vs. LDAP federation)
  documented per the Registry Migration Note (v42)
□ Global Failure Taxonomy (Regional Failure, Network Partition) defined
  and both types simulated at least once (v43)
□ Global Incident Commander role documented for Regional Failure/Network
  Partition governance (v43)
□ Golden cell template version-controlled in Git and confirmed
  restorable from backup (v43)
□ Central LDAP/LTPA key material and cross-region certificate trust
  store confirmed restorable from backup, separately from each region's
  own P05-style backup set (v43)
□ Configuration drift detected and reconciled against the golden
  template at least once (v43)
□ All five versions' TestCases-v39.md–v43.md signed off per Test Case
  Standards
□ Promoted Dev → UAT → Prod per Environment Promotion Standards,
  part6-release tag applied

Application State After This Part
Application code: unchanged from P05 (same INR-denominated digistack-bank
family of EARs + Mobile/ATM Tomcat apps, including P05 v38's idempotency
addition). No new banking modules, currencies, or localized features were
added in this Part — see Currency/Locale Scope Exception above.

New Infrastructure
- Two new regions (Singapore, Dubai), each a full single-site regional
  deployment (LB, IHS, WAS cell, CBS, local MQ, Primary/Standby
  PostgreSQL) — no DR pairing for either, per the DR Scope Decision (v39)
- Global DNS / GSLB with three-layer health-based routing (v40)
- Cross-region mTLS REST channels and MQ Hub connectivity between all
  three regional queue managers (v41)
- Central LDAP, synchronized LTPA keys across all three cells, TAI
  understood at concept level (v42)
- Golden cell configuration template (Git-versioned), drift detection,
  Global Failure Taxonomy, Global Incident Commander governance role
  (v43)

Carried Forward to P07
The multi-region topology, Global Shared Services layer, and golden cell
template built here become the baseline P07 migrates/upgrades from, one
region and one cell at a time, rather than as a single big-bang platform
change.
