ID: ARCH01
Version: 1.0
Status: Active

Title: Enterprise Architecture

Imports:
IDX
STD

Exports:
Architectural principles
Logical/application/data/deployment views
Cross-cutting concerns
Assumptions
Accepted tradeoffs

Used By:
ARCH02
P03
P03.1
P03.2
P07
P07.1
P09
P09.1
P10
P10.1

Next:
ARCH02

---

Purpose
-------
Defines the program's governing structure and principles — what gets built and why,
sitting above P01-P09's version-by-version implementation detail.

Guiding Filter — WebSphere-Practice-First Test
------------------------------------------------
Applied to every architectural element, in order:
1. Does this exercise a specific WebSphere ND admin/deployment/clustering/security/
   monitoring/DR capability? → In scope, name the capability.
2. If not, is it minimum plumbing so (1) has something to run on? → In scope, minimal.
3. Neither? → Out of scope, even if "a real bank would have it."

Primary Objective
-----------------
Teach WebSphere ND administration. Banking app = minimum viable vehicle, not a product
with its own roadmap.

Architectural Priority Rule
----------------------------
Where a choice could go toward "more realistic banking" or "more WebSphere practice,"
this architecture always chooses the latter, provided the former stays plausible.
Example: CBS stays a single EAR for the life of the project — decomposing it would
teach microservices, not WAS administration.

Architectural Principles
-------------------------
P-1  Topic drives feature, not the reverse.
P-2  One authoritative writer per datastore — only CBS writes to digistack_cbs.
P-3  CBS stays a single deployable (CIF, Accounts, Transactions, Products, Loans).
P-4  Cross-app coordination is a Saga, not a distributed XA transaction.
P-5  Presentation-only channels never touch the database directly.
P-6  Infrastructure evolves; WebSphere administration remains the constant.
P-7  Nothing is silently retired — every superseded component gets an explicit
     decommission step.
P-8  Configuration is externalized, never hardcoded, never duplicated by rebuild.
P-9  Observability is additive across phases, not replaced.
P-10 Every renumbering/architectural decision is documented, never silent.
P-11 Realism is scoped to lab scale, honestly stated.
P-12 Security/compliance postures are stated, not assumed.

Architectural Views
--------------------

1. Logical Architecture (End-State)
------------------------------------
                    Customers
        (Internet / Mobile / ATM / Branch / Cards)
                        │
              Global DNS / GSLB / Route 53
                        │
    ┌───────────────────┼───────────────────┐
    ▼                   ▼                   ▼
 Region A            Region B            Region C
    │                   │                   │
 Enterprise LB    Enterprise LB       Enterprise LB
    │                   │                   │
   IHS                 IHS                 IHS
    │                   │                   │
 WebSphere ND Cell  WebSphere ND Cell  WebSphere ND Cell
    │
    ├── InternetBanking (WAS)
    ├── CardPortal (WAS)
    ├── BranchPortal (WAS)
    ├── PaymentHub / NotificationService / ReportingService (WAS)
    ├── MobileBanking (Tomcat)
    └── ATMSimulator (Tomcat)
                        │
              REST / SOAP / EJB
                        │
                 DigiStack CBS (single EAR)
        (CIF, Accounts, Transactions, Products, Loans
              — sole writer of digistack_cbs)
                        │
                 digistack_cbs Database
              (PostgreSQL, Primary/Standby, DR cross-region)

Global Shared Services (cross-cutting layer):
LDAP · PKI · Git · CI/CD · Prometheus/Grafana · ELK · MQ Hub ·
SMTP/SMS · NTP · DNS

2. Application/Component Architecture
---------------------------------------
Application          | Type              | Deployment | Writes digistack_cbs?
CBS                   | Core banking      | WAS EAR    | Yes — sole writer
InternetBanking       | Presentation       | WAS EAR    | No
PaymentHub            | Payment routing    | WAS EAR    | No (routes only)
NotificationService   | Event consumer     | WAS EAR    | No
ReportingService      | Read-only reports  | WAS EAR    | No (read-only, accepted risk)
BranchPortal          | Teller ops         | WAS EAR    | No
CardPortal            | Card lifecycle     | WAS EAR    | No
MobileBanking         | Channel            | Tomcat WAR | No
ATMSimulator          | Channel            | Tomcat WAR | No

Component boundary rule (P-2, P-5): every arrow into digistack_cbs terminates at CBS.
No exception without an explicit, documented architectural decision.

3. Data Architecture
----------------------
- Single engine: PostgreSQL end-to-end. No other engine ever introduced.
- Database boundary shift: shared DB (early build) → dedicated digistack_cbs
  (from CBS-split version onward), one-time verified migration, legacy DB
  decommissioned.
- Data residency (multi-region): each region's CIF is authoritative for its own
  customers. Cross-region lookup = live mTLS query into foreign region's CBS,
  never a replicated global index.
- Backup: pg_dump weekly, 15-day restore-test cadence, from day one — not deferred.
- PII: account numbers, Aadhaar/PAN, card numbers masked/tokenized at point of
  log emission.

4. Deployment/Physical Architecture (Phase Summary)
------------------------------------------------------
Phase              | Substrate
Early build         | Single-site on-prem VMs
HA/DR               | Primary + DR site pairing
Multi-region        | 3 on-prem regions + shared global services layer
Platform migration  | Same footprint, WAS version migrated underneath
Automation          | Same footprint, pipeline-driven deployment
Cloud               | Progressive move to AWS: hybrid → lift-and-shift →
                      managed services → full cutover

Cross-Cutting Concerns
------------------------
Concern                  | Governing Standard | Summary
Naming                    | STD                | App/EAR/VM/branch/tag conventions
Environment promotion     | STD §Promotion     | DEV→TEST→UAT→PROD→DR
DB migration discipline   | STD §SQL Migration | V<N>__description.sql, forward-only + rollback
Config/port/cert inventory| STD §Ports/Certs   | Per-environment config, standing port matrix
Transaction integrity     | ARCH02             | Single-writer rule, Saga pattern
Messaging HA              | ARCH02             | ME failover policy, file vs DB store
Observability              | P05                | Prometheus/Grafana/ELK/Jaeger, additive
Identity/security realm    | P07                | File/LDAP → federated LDAP, explicit migration path
Load-testing tooling       | P05                | Apache JMeter, standing choice

Assumptions
-----------
A1 WAS ND edition/version chosen at implementation time, not pre-selected here.
A2 OS distribution: Rocky Linux/RHEL, held consistent once chosen.
A3 Single-VM vs multi-VM at start: single-VM lab-scale start assumed, split only
   when a topic requires it.
A4 Multi-account AWS Landing Zone may be a documented single-account simulation
   if true multi-account isn't feasible at lab scale.
A5 Fixed/Recurring Deposits remain unscoped, deferred until explicitly requested.
A6 Containerization not yet roadmapped; AWS-native estate is the baseline until
   explicitly requested.

Risks and Accepted Tradeoffs
-------------------------------
Tradeoff                                  | Why accepted                          | Closed when
CBS never decomposed internally           | Simple local transactions; not the    | Flagged as future,
                                            | program's learning goal               | out-of-scope option
ReportingService reads OLTP directly      | Avoids new infra scope mid-build       | Closed at RDS read
                                            |                                        | replica phase
No distributed XA across EARs (PaymentHub)| Avoids coupling independent failure    | Permanent — Saga is
                                            | domains                                | the permanent design
Lab-scale load-test figures                | Small-VM baseline ≠ production scale  | Never "closed" —
                                            |                                        | always framed as
                                            |                                        | mechanism validation
Trial/developer licenses (MQ, WAS trial)   | Assumed usable for project duration    | Re-verify each phase
                                            |                                        | boundary

Future Scalability Considerations
------------------------------------
- CBS decomposition — Loans is the natural first split candidate if ever pursued.
- Containerization — natural next step after full cloud-native estate.
- Configuration sprawl governance — periodic golden-template audits recommended
  as the platform ages.

Visual Companion
--------------------
This document's Logical Architecture (§1) and Deployment/Physical
Architecture (§4) are visualized incrementally in 01_Architecture/ —
specifically 01_Network_Diagram.md, 02_VM_Layout.md, and
09_DR_Architecture.md. Those diagrams populate only as each depicted
piece is actually built; this document remains authoritative for the
target-state end-to-end view regardless of build progress.