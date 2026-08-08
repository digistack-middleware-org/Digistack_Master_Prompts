# DigiStack Telecom Enterprise — Master Reference
*(Upload this once per new chat, alongside your Progress Log + current Phase file. Treat everything below as locked — flag before re-litigating.)*

## Learner Profile
- Venkatesh, returning to IT after a 10-year gap. Fresher-level: no coding/DB/WAS background.
- Learns best: concept → build → hands-on lab, always in that order — never build before explaining.
- Target role: WAS Administrator, banking/enterprise → now also telecom track.
- Working pattern: one sprint at a time, explicit go-ahead before advancing. Structural decisions get flagged and confirmed, never assumed.

## Project Concept
**DigiStack Telecom Enterprise** — fictional telecom platform (company: Digistack), a WAS ND admin training environment. Infrastructure-first: thin business logic (3–5 screens/app) + real enterprise middleware plumbing. Front end: JSP + Servlet + CSS/Bootstrap (not SPA).
**Focus split: 70% WebSphere Administration / 30% Application Development** — enforced structurally (thin logic everywhere + Phase 3 dedicated to pure admin).

## Locked Architectural Decisions (ADRs)
- **ADR-001 Monitoring:** none until Phase 6, then Prometheus/Grafana/Node Exporter on existing VMs
- **ADR-002 Database:** PostgreSQL 16.x, pinned
- **ADR-003 VM Topology:** 5 VMs (DMGR+Node1 combined, Node2, IHS, PostgreSQL, MQ) — VM1 known SPOF, fixed in Phase 6
- **ADR-004 Cluster Shape:** 3 clusters (Customer, Business, Integration), each spans both nodes
- **ADR-005 App Landscape:** 11 apps (below)

## Tech Stack
| Layer | Tech | Version |
|---|---|---|
| Backend | Java 8, Servlet, JDBC, DAO pattern | IBM Java 8 |
| Frontend | JSP + Servlet + CSS/Bootstrap | — |
| Build | Maven, multi-module per app | 3.8.x+ |
| Middleware | WebSphere ND | 9.0.5.21 (pinned) |
| Middleware | IBM HTTP Server | 9.0.5.21 |
| Middleware | IBM MQ | 9.3.x–9.4.x |
| Database | PostgreSQL | 16.x |
| DB versioning | Flyway | Community |
| Packaging | EAR per app, wrapping one WAR | — |

## App Landscape (11 apps)
Authentication Service, SelfCare Portal, CRM, SIM Service, Order Service, Provisioning Service, Billing Service, Payment Service, Notification Service, CDR Service, Admin Portal.
(SIM Service replaces "Network Inventory" — classloader isolation topic dropped.)

## Phase Structure (6 phases, consistent 6-sprint shape)
1. Foundation — 12 versions (platform infra)
2. Application Development — 11 versions (one per app)
3. WebSphere Administration — 7 versions (pipeline, tuning, security, wsadmin, logging, testing, packaging)
4. Operations — 8 versions (Jenkins automation, fleet-wide)
5. Production Support — 4 versions (incident response by failure category)
6. Enterprise Operations — 7 versions (DR, change mgmt, monitoring, capstone)

## Concept Index (quick lookup by phase/version)
**Phase 1:** V1 Git/ADRs · V2 VM networking/DNS/RHEL · V3 Maven modules · V4 PostgreSQL · V5 Flyway · V6 IBM MQ · V7 WAS ND profiles · V8 Cell/DMGR/Federation · V9 Clusters · V10 IHS/virtual hosts · V11 Plugin gen/propagation · V12 JDBC providers/datasources
**Phase 2:** V1 LTPA/JAAS/SSO (Auth) · V2 Session/IHS routing (SelfCare) · V3 Security roles (CRM) · V4 JDBC transactions/pooling (SIM) · V5 JMS producer (Order) · V6 MDB/activation spec (Provisioning) · V7 XA/2PC (Billing) · V8 XA across DB+MQ (Payment) · V9 Fan-out async (Notification) · V10 High-volume MDB/DLQ (CDR) · V11 PMI/health checks (Admin Portal)
**Phase 3:** V1 EAR versioning/rollback · V2 JVM/thread/ORB/pool/session/cache tuning · V3 Cell-wide SSL/LDAP/LTPA rotation · V4 wsadmin/Jython · V5 Centralized logging · V6 Testing strategy · V7 EAR packaging/handoff
**Phase 4:** Jenkins CI/CD, automated pipelines, fleet-wide rolling deploys, EAR version mgmt, backup/recovery, cert rotation, security audits, automated tuning
**Phase 5:** App/deployment failures · JVM/memory failures · Data/messaging failures · Network/security/session failures — full symptom→root cause→resolution cycles
**Phase 6:** DR planning/drills, ITIL change mgmt, capacity planning, synthetic monitoring, Prometheus/Grafana, runbooks
*(Cross-cutting diagrams live in a separate `01_Architecture/` folder — Enterprise Architecture, Cluster Topology, MQ, DB ER, Security, Deployment, DR — 7 pieces, not part of this consolidation. Also see `11_ENTERPRISE_ARCHITECTURE_AND_REQUEST_FLOWS.md`, the single-file version — upload either only for Phase 3/5/6 troubleshooting sprints.)*

## Coding Standards
- Package structure: `com.digistack.telecom.<app>.{controller,service,dao,model,filter,util}`
- Naming: classes PascalCase, methods/variables camelCase, constants UPPER_SNAKE_CASE
- DB schema changes: always via numbered Flyway migration (`V1__init.sql`) — never hand-edited SQL
- Logging: centralized framework (Phase 3 V5) — no raw `System.out.println`
- Exceptions: custom classes from shared framework (Phase 1 V3), never bare `Exception`
- Each app: independent Maven multi-module project, own EAR wrapping own WAR, own DB schema

## Maven/EAR Structure (every app follows this)
```
<app>-service/                    <- parent (packaging: pom)
├── pom.xml
├── <app>-service-web/            <- WAR module (packaging: war)
│   └── src/main/java/com/digistack/telecom/<app>/{controller,service,dao,model,filter,util}
│       + src/main/webapp/ (WEB-INF/web.xml, css, js, *.jsp)
└── <app>-service-ear/            <- EAR module (packaging: ear)
    └── src/main/application/META-INF/application.xml
```
Build chain: `mvn package` in WAR module → .war → `mvn package` in EAR module → .ear (deployed via Console or `AdminApp.install`).
Why: independent build/deploy/rollback per app, independent classloading, matches real enterprise packaging.

**Parent pom.xml** (groups modules, no code):
```xml
<project>
  <groupId>com.digistack.telecom</groupId>
  <artifactId><app>-service</artifactId>
  <packaging>pom</packaging>
  <modules>
    <module><app>-service-web</module>
    <module><app>-service-ear</module>
  </modules>
</project>
```

**WAR pom.xml:**
```xml
<project>
  <parent>
    <groupId>com.digistack.telecom</groupId>
    <artifactId><app>-service</artifactId>
  </parent>
  <artifactId><app>-service-web</artifactId>
  <packaging>war</packaging>
</project>
```

**EAR pom.xml:**
```xml
<project>
  <parent>
    <groupId>com.digistack.telecom</groupId>
    <artifactId><app>-service</artifactId>
  </parent>
  <artifactId><app>-service-ear</artifactId>
  <packaging>ear</packaging>
  <dependencies>
    <dependency>
      <groupId>com.digistack.telecom</groupId>
      <artifactId><app>-service-web</artifactId>
      <type>war</type>
    </dependency>
  </dependencies>
</project>
```

**application.xml** (tells WebSphere what's inside the EAR):
```xml
<application>
  <display-name><App>ServiceApp</display-name>
  <module>
    <web>
      <web-uri><app>-service-web.war</web-uri>
      <context-root>/<app></context-root>
    </web>
  </module>
</application>
```

## Realism Enhancements
- **Catalog-only apps (~20, referenced not built):** used in Phase 5 incidents for scale, e.g. Payment Gateway Adapter ("times out under load"), SMS Gateway ("cert expires"), Roaming Partner API ("connection refused during maintenance"). Full list drafted before Phase 5.
- **Incident lifecycle:** Symptoms → Investigation → Logs → Commands → Root Cause → Resolution → Prevention (every Phase 5 scenario).
- **ITSM ticket lifecycle:** RFC → SCTASK → INC → PROB (Phase 6 Change Management).
- **Load patterns:** month-end billing spike, promo traffic surge, CDR mediation backlog.
- **Accepted trade-offs (not silently fixed):** VM1 SPOF until Phase 6 DR audit; Admin Portal inside Integration Cluster (could go down with the thing it monitors).
- Phase 5/6 framed as on-call paging, not classroom exercises.

## New Chat Starter (copy-paste)
```
I'm continuing my DigiStack Telecom Enterprise WebSphere admin practice project.
I've uploaded my Master Reference, Progress Log, and current Phase file.
Fresher, no coding/DB background — teach concept before build, always.
Focus: 70% WebSphere administration, 30% application development.
Deliver one sprint at a time, wait for my go-ahead before advancing.
Pick up exactly where the Progress Log says I left off:
[paste "Current Position" section from Progress Log]
```
End-of-session message: `"Update my Progress Log with what we covered today and what's next."`

## Open / Deferred Items
- Pacing/cycle rhythm — not yet decided
- Exact PostgreSQL 16 point release — deferred to Phase 1 V4
- ~20 catalog-only app names — deferred to before Phase 5
