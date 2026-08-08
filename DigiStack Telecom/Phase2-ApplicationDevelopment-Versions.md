# Phase 2 — Application Development: Versions & Sprints

Goal: Build and deploy all 11 core apps of DigiStack Telecom Enterprise.

**Design philosophy:** thin, realistic business logic (each app: 3–5 screens, simple flows) + real enterprise middleware plumbing underneath. Front end: JSP + Servlet + CSS/Bootstrap — classic WAS-deployed web apps, styled to look modern.

**App landscape (11 apps):** Authentication Service, SelfCare Portal, CRM, SIM Service, Order Service, Provisioning Service, Billing Service, Payment Service, Notification Service, CDR Service, Admin Portal.

Every version follows the same 6-sprint structure:
1. Learning Session (Concept)
2. Application Development
3. WebSphere Administration (Console + wsadmin)
4. Production Incident Simulation
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-1: Authentication Service
1. Concept — LTPA, JAAS, SSO, why an auth service exists
2. Build the Authentication Service app (login, token issuance)
3. WAS Admin — deploy app + configure LTPA/JAAS
4. Incident Simulation — SSO failure troubleshooting
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-2: SelfCare Portal
1. Concept — session management (JSESSIONID), IHS/plugin routing, SSL
2. Build SelfCare Portal app (consumes SSO)
3. WAS Admin — deploy + configure session replication
4. Incident Simulation — session loss / sticky session issue
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-3: CRM
1. Concept — security roles, role-based authorization
2. Build CRM app
3. WAS Admin — configure security roles + JDBC binding
4. Incident Simulation — authorization failure troubleshooting
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-4: SIM Service
1. Concept — JDBC transactions, connection pooling; SIM activation flow (enter SIM number → update status to ACTIVE → log transaction)
2. Build SIM Service app (3-5 screens: SIM lookup, activation form, status view)
3. WAS Admin — configure JDBC transaction settings, connection pool sizing
4. Incident Simulation — transaction left in-flight / connection pool exhaustion during activation
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-5: Order Service
1. Concept — MQ foundations, JMS producer
2. Build Order Service app (creates order → ORDER.QUEUE)
3. WAS Admin — configure JMS connection factory + queue destination
4. Incident Simulation — message not reaching queue
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-6: Provisioning Service
1. Concept — MDB & activation specification
2. Build Provisioning Service (MDB: consumes ORDER.QUEUE, produces BILLING.QUEUE)
3. WAS Admin — configure activation spec + MDB deployment
4. Incident Simulation — MDB not picking up messages
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-7: Billing Service
1. Concept — transactions: XA, two-phase commit
2. Build Billing Service (consumes BILLING.QUEUE, produces PAYMENT.QUEUE)
3. WAS Admin — configure XA datasource + transaction settings
4. Incident Simulation — XA rollback / heuristic exception
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-8: Payment Service
1. Concept — XA coordination across DB + MQ
2. Build Payment Service (consumes PAYMENT.QUEUE, produces NOTIFY.QUEUE)
3. WAS Admin — configure dual XA resources
4. Incident Simulation — distributed transaction failure
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-9: Notification Service
1. Concept — fan-out async messaging
2. Build Notification Service (consumes NOTIFY.QUEUE)
3. WAS Admin — configure MDB + connection pool tuning
4. Incident Simulation — notification backlog / slow consumer
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-10: CDR Service
1. Concept — high-volume MDB tuning, Dead Letter Queue
2. Build CDR Service (CDR.QUEUE consumer)
3. WAS Admin — tune MDB max sessions/threads + configure DLQ
4. Incident Simulation — DLQ buildup / poison message
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-11: Admin Portal + Health Checks
1. Concept — PMI basics; health endpoints, dependency checks (DB, MQ), cluster member health, readiness vs. liveness, graceful shutdown
2. Build Admin Portal app with health/readiness/liveness endpoints + DB/MQ dependency checks
3. WAS Admin — enable PMI, wire health checks into cluster monitoring
4. Incident Simulation — Admin Portal fails during an Integration Cluster incident; false-positive health check scenario
5. Documentation + Interview Questions
6. Hands-on Challenge
