ID: ARCH02
Version: 1.1
Status: Active

Title: Solution Architecture

Imports:
IDX
STD
ARCH01

Exports:
Technology stack
Internal application structure
Integration patterns
NFR matrix
Technical standards

Used By:
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
P01

---

Purpose
-------
Describes how the solution is actually built, structured, and wired together —
one level below ARCH01's principles/views. Answers "how," not "what/why."

Version 1.1 change note (2026-07-29): Formalized §2a, Maven Project
Structure, and added "Maven (build)" to §1's Technology Stack CI/CD row
— both already reflected the project's actual multi-module build
convention but had not been captured with a version bump/change note
until this entry. Documents an existing convention; no
architectural/behavioral change.

1. Technology Stack
----------------------

Application Tier
Layer            | Technology
Presentation      | JSP, Servlets, HTML5, CSS3, Bootstrap 5, vanilla JS
Business Logic    | EJB (session beans, MDBs, Timer Service) — lives in CBS only
Integration       | JAX-RS (REST), JAX-WS (SOAP), JMS (SIBus + IBM MQ)
Data Access       | Plain JDBC via WAS-managed DataSource, no ORM
App Server        | WebSphere ND (per STD)
Web Server        | IBM HTTP Server (IHS)
Secondary Server  | Apache Tomcat — Mobile/ATM only
Load Balancer     | NGINX/HAProxy (on-prem), AWS ALB (cloud phase)
Messaging         | SIBus, IBM MQ, Amazon SQS/SNS (selective, cloud phase)
Database          | PostgreSQL → Amazon RDS for PostgreSQL (cloud phase)

Infrastructure Tier
Layer             | Technology
Virtualization     | VMware VMs, Rocky Linux/RHEL (per STD)
Cloud              | AWS: EC2, VPC, RDS, S3, SQS/SNS, IAM, KMS, ACM, Route 53, ALB
Observability       | Prometheus, Grafana, Node/JMX/PostgreSQL Exporters,
                     OpenSearch, Jaeger, Alertmanager
CI/CD               | Maven (build), Git, Jenkins, Nexus, Ansible, wsadmin (Jython)
Load Testing        | Apache JMeter
Security            | Self-signed certs (on-prem) → AWS ACM (cloud);
                     JAAS Auth Alias → Secrets Manager (backing store only)
Concept-only tools  | Instana, Dynatrace, AppDynamics, Datadog, ServiceNow,
                     HashiCorp Vault, Terraform, AWS WAF/Shield/GuardDuty

2. Internal Application Structure
-------------------------------------

CBS Internal Layering (single EAR, modular not distributed — per ARCH01 P-3)

CBS.ear
└── CBS.war / .jar (EJB module)
├── controller/ (REST/SOAP endpoints)
├── service/ (session beans)
│ ├── CustomerService
│ ├── AccountService
│ ├── TransactionService
│ ├── FundTransferService
│ ├── CardService
│ ├── LoanService
│ └── OperationsService (Branch/BOD-EOD)
├── mdb/ (SIBus/MQ consumers)
├── dao/ (JDBC data access)
├── model/ (POJOs)
├── event/ (published events — Notification/Reporting consume)
└── config/ (JNDI references, no hardcoded values)

Rule: service classes call each other directly within the same JVM/transaction.
No CBS service ever makes a network call to reach another CBS service.

Satellite App Layering (InternetBanking, PaymentHub, NotificationService,
ReportingService, BranchPortal, CardPortal, MobileBanking, ATMSimulator)

<app>.ear / .war
├── controller/servlet/
├── client/ (REST/SOAP client stubs calling CBS — ONLY path to business data)
├── view/ (JSP, Bootstrap 5)
└── config/ (CBS base URL, externalized)

Rule: if a satellite ever needs a dao/ package or direct JDBC to digistack_cbs,
that is an architecture violation (ARCH01 P-2/P-5), not a shortcut.

Package Naming

com.digistack.cbs.controller.FundTransferController
com.digistack.cbs.service.FundTransferService
com.digistack.cbs.dao.AccountDao
com.digistack.cbs.mdb.FundTransferMDB
com.digistack.internetbanking.controller.LoginController
com.digistack.paymenthub.service.SettlementService
com.digistack.notificationservice.mdb.TransactionEventMDB

Base package always com.digistack.<app-short-name>.
2a. Maven Project Structure (Build Convention)
------------------------------------------------

Every application in this project (CBS and all satellites) is built as a
multi-module Maven project — never a hand-packaged EAR/WAR. One Maven
multi-module project per application, following this fixed module
layout:

<app-short-name>-parent/          (packaging: pom — parent POM only,
                                    declares child modules + shared
                                    dependency versions)
├── pom.xml
├── <app-short-name>-ejb/          (packaging: ejb — CBS ONLY; session
│   └── pom.xml                    beans, MDBs, Timer Service, per
│                                  §2's CBS internal layering above.
│                                  Satellite apps have no ejb module.)
├── <app-short-name>-web/          (packaging: war — JSP/Servlet/
│   └── pom.xml                    controller/client code, per §2's
│                                  layering for whichever app this is)
└── <app-short-name>-ear/          (packaging: ear — assembles ejb + web
    └── pom.xml                    modules above into the final
                                   deployable digistack-<app>-vN.ear,
                                   per STD's Deployables naming)

Example — CBS:              cbs-parent/ { cbs-ejb/, cbs-web/, cbs-ear/ }
Example — InternetBanking:  internetbanking-parent/
                             { internetbanking-web/, internetbanking-ear/ }
                             (no ejb/ module — InternetBanking has no
                             session beans of its own, per §2)

Rule: Tomcat-hosted apps (MobileBanking, ATMSimulator) use ONLY the
<app>-web/ module (packaging: war) — no ear/ module, since Tomcat
deploys WARs directly (per ARCH01's Application/Component Architecture
table).

Package naming inside each module follows §2's existing convention
(com.digistack.<app-short-name>.controller/service/dao/...) — this
section defines the Maven module boundary that convention lives inside;
it does not introduce a new naming scheme.

Build command convention: `mvn clean package` run at the
`<app>-parent/` level builds and assembles the full EAR (or WAR, for
Tomcat apps) in one step. From this point forward, every
SetupDoc-v<N>.md's §4.4 "Application Deployment" section means this
Maven build when it says "build the artifact" — this replaces any
earlier manual zip-based packaging description (e.g. P01 v1/v2's
Sprint Plan wording, written before this convention was formalized).

3. Integration Architecture
------------------------------

Synchronous (REST/SOAP)
Contract                          | Provider | Consumers                | Style
Balance Inquiry                    | CBS      | InternetBanking, Mobile, | REST
                                    |          | ATM                      |
Fund Transfer                      | CBS      | InternetBanking, Mobile  | REST
Account Statement / Txn History    | CBS      | InternetBanking,         | SOAP
                                    |          | ReportingService (data)  |
Card Service (Issue/Activate/etc)  | CBS      | CardPortal               | REST/SOAP
Operations (Teller/BOD/EOD)        | CBS      | BranchPortal             | REST/SOAP

API versioning: not introduced as its own scheme. Endpoint contracts stable across
versions; breaking change requires an explicit, newly-scoped decision.
Contract stability rule: relocating a service (e.g. endpoint moves between apps)
never changes the contract itself — same path, same WSDL, same shapes.

Asynchronous (JMS/MQ/SQS)
Flow                              | Mechanism        | Modernization path
Internal Fund Transfer            | SIBus JMS + MDB  | Stays on SIBus (XA-integrated)
External Fund Transfer leg        | IBM MQ           | Stays on IBM MQ (XA-integrated)
CBS event publish (Notification/  | IBM MQ           | May migrate to SQS/SNS
Reporting consumption)            |                  | (fire-and-forget, no XA)
NEFT/IMPS settlement coordination | JMS/MQ, Saga     | Unchanged — permanent pattern

Decision rule (permanent): a flow migrates to SQS/SNS only if fire-and-forget/
fan-out and NOT participating in an XA transaction with CBS's transaction manager.
Anything transactionally coupled to CBS stays on IBM MQ/SIBus permanently.

Event-Driven Consumption Pattern

CBS (publisher only, never a subscriber)
│
▼
IBM MQ / SQS-SNS (per decision rule above)
│
├──────────────► NotificationService (consumes only)
└──────────────► ReportingService (consumes only)

Both obey ARCH01 P-2: read events/data, never write back to digistack_cbs.

Security of Integration Channels
- Internal service-to-service: mTLS (single-site → extended cross-region)
- MQ channels: CHLAUTH + TLS
- External-facing endpoints: token/API-key authentication enforced

4. Non-Functional Requirements (NFR) Matrix
-----------------------------------------------
NFR Category         | Requirement                              | Verified In
Availability          | Cluster survives node failure            | P02, P07
Session Continuity     | Session survives node/platform/region    | P01, P07
                        | failure                                  |
Disaster Recovery      | RPO 0 / RTO 15 min for Fund Transfer     | P07
Security — Transport    | End-to-end SSL, mTLS on ≥1 internal hop | P01, P07
Security — AuthN/Z      | RBAC, MFA/OTP, LTPA SSO                 | P01, P07
Performance             | JVM heap tuned, no OOM on large report   | P01, P05
Scalability             | Horizontal scaling via clustering        | P02, P07
Observability            | /health + metrics + logs + traces        | P05
Data Integrity           | XA correctness, idempotent Fund Transfer | P03, P07
Backup/Recoverability    | DB-native backup + restore test          | STD, P07
Compliance               | PII masking in logs                      | P05, P08
Deployability            | Zero-downtime redeploy/rollback           | P06

5. Solution-Level Component Diagram — Steady State
------------------------------------------------------
                IBM HTTP Server (IHS)
    Virtual-host routed: WAS plugin | Tomcat
                │             │
    ┌───────────┘             └───────────┐
    ▼                                     ▼

WebSphere ND Cluster Apache Tomcat
┌───────┬───────┬───────┬───────┐ ┌───────┬───────┐
▼ ▼ ▼ ▼ ▼ ▼ ▼
InternetBanking CardPortal BranchPortal PaymentHub MobileBanking ATMSimulator
NotificationService
ReportingService
│ │ │ │ │ │
└───┬───┴───┬───┴───┬───┴─────────────┴───┬───┘
│ REST/SOAP/EJB (sync) │
│ IBM MQ / SIBus (async) │
▼ │
DigiStack CBS ◄─────────────────┘
│
▼
digistack_cbs (PostgreSQL)


6. Technical Standards Not Covered Elsewhere
------------------------------------------------
Standard              | Rule
Exception handling      | CBS exceptions checked, mapped to fault/error-code
                         taxonomy at controller layer — never a raw stack trace
                         returned to a channel app
Logging format           | Structured (JSON-line), one schema across all 9 apps,
                         PII masked at emission
Correlation ID            | Assigned at every channel entry point, propagated
                         through REST headers and JMS message properties
Idempotency key format     | UUID-based, on Fund Transfer and any future write op
Timeouts                   | Every satellite→CBS client call has an explicit,
                         externalized timeout — never infinite wait
Retry policy                | Exponential backoff, max-attempt ceiling, shared
                         constants reused everywhere retry logic appears

7. Assumptions (Solution Level)
------------------------------------
A7  API contracts: REST=JSON, SOAP=standard WSDL/XSD. No GraphQL/gRPC.
A8  No API gateway product — security/throttling at IHS + app layer.
A9  No ORM — plain JDBC/DAO, per teaching style and topic visibility.
A10 Correlation ID / structured logging introduced as a convention at the
    Observability phase (P05) — not retrofitted onto earlier builds.

Visual Companion
--------------------
This document's §3 Integration Architecture, §5 Solution-Level Component
Diagram, and §2a Maven Project Structure are visualized incrementally in
01_Architecture/ — specifically 03_Request_Flows.md,
04_Cluster_Architecture.md, 05_MQ_Architecture.md, and
08_Deployment_Architecture.md. Those diagrams populate only as each
depicted piece is actually built (per SDD01's "write it as you go"
discipline); this document remains authoritative for the target-state
view regardless of build progress.

Summary
-------
Translates ARCH01's principles into concrete tech choices, internal structure,
integration patterns, and NFRs. Introduces no new banking features; formalizes
structure and conventions only.