# 🏦 Bank Simulation Application on JBoss/WildFly — Phase-Wise Practical Plan

> A hands-on project to rebuild 10 years of JBoss/WildFly administration experience by building and operating a full multi-node banking platform.

---

## 📐 Architecture

```
Browser
  │
Nginx (Load Balancer)
  │
JBOSS Web Tier (Customer Portal + Admin Portal) ── cluster of 2 nodes
  │
JBOSS CBS Service (Core Banking: accounts, balance, beneficiary)
  │
JBOSS Payment Engine (transfers, validation)
  │                         │
  ▼                         ▼
Kafka (payment-events)   Oracle DB (accounts, txn ledger)
  │
Notification Service (email/log consumer)
```

**Platform:** WildFly 26 (latest) or EAP 7.4 (if you want the "bank runs EAP" story). Use WildFly for practice — EAP equivalents noted where relevant.

---

## 📚 Topic Coverage Map

| #   | Topic Area                                                              |
| --- | ----------------------------------------------------------------------- |
| 1️⃣  | Fundamentals & Architecture (JBoss vs EAP, Modules, MSC, Standalone)    |
| 2️⃣  | Installation & Server Management                                        |
| 3️⃣  | Configuration (CLI, Console & XML)                                      |
| 4️⃣  | Deployment Management                                                   |
| 5️⃣  | Datasources & Transactions                                              |
| 6️⃣  | Clustering, HA & Load Balancing                                         |
| 7️⃣  | Security (Elytron, RBAC, SSL/TLS)                                       |
| 8️⃣  | Performance & Tuning                                                    |
| 9️⃣  | Logging & Monitoring                                                    |
| 🔟  | Messaging (JMS /                                               |
| 1️⃣1️⃣ | Domain Mode Deep Dive                                                 |
| 1️⃣2️⃣ | Migration, Upgrades & Automation                                      |
|⃣3️⃣ | Troubleshooting & Production Operations                               |

> ✅ Every phase below maps directly to these topics — **practical only, no theory-only**.

---

## 🚀 Phase 0 — Environment Setup

### 🎯 Topics: #2 Installation & Server Management

### Build
- [ ] Install WildFly via **zip** into `/opt/wildfly`
- [ ] Run **2 standalone instances** on one host using **port offsets** (100, 200)
- [ ] Configure `standalone.conf`: heap sizes, GC (G1GC), `JAVA_OPTS`
- [ ] Create **systemd service** running as **non-root** user `wildfly`
- [ ] Run `add-user.sh` → management user (ManagementRealm)
- [ ] Practice graceful shutdown:

```bash
jboss-cli.sh --connect ":shutdown(timeout=30)"
```

### Practice Exercises
- [ ] Break boot (corrupt XML) → read `server.log` boot errors → fix
- [ ] Take a config snapshot:

```bash
jboss-cli.sh --connect ":take-snapshot()"
```

> 💡 Snapshots become your **rollback habit** for the whole project.

### ✅ Deliverable
> 2 running WildFly instances as systemd services, snapshots taken, add-user done.

---

## 🏦 Phase 1 — Core Banking (CBS) Service

### 🎯 Topics: #4 Deployment, #5 Datasource & Transactions

### Build
- [ ] Simple WAR with JPA/Hibernate entities:
  - **Customer** (customerId, name)
  - **Account** (accountId, customerId, balance, status: `ACTIVE` / `FROZEN`)
  - **Transaction** (txnId, fromAcct, toAcct, amount, timestamp)
- [ ] Oracle XE (or PostgreSQL if pain — but keep Oracle for realism)
- [ ] REST endpoints: freeze/unfreeze, get balance, list transactions

### JBoss Practice
- [ ] **Oracle JDBC driver as a MODULE**:

```
modules/com/oracle/ojdbc/main/
├── module.xml
└── ojdbc11.jar
```

- [ ] Configure **non-XA datasource**: pool min/max, validation (`valid-connection-checker`, `background-validation`)
- [ ] CLI practice:

```=datasources/data-source=CBS_DS:add(...)
/subsystem=datasources/data-source=CBS_DS:write-attribute(name=max-pool-size, value=50)
```

- [ ] **Deploy all 4 ways**:
  1. CLI `deploy`
  2. Web Console
  3. Filesystem scanner (`deployments/` + marker files `.dodeploy`)
  4. Maven (`wildfly-maven-plugin`)
- [ ] Exploded vs WAR deployment
- [ ] **Deployment overlay** for hot-fix files

### ✅ Deliverable
> CBS WAR deployed, datasource tuned, deploy/undeploy/rollback mastered.

---

## 💸 Phase 2 — Payment Engine + Kafka + Notification

### 🎯 Topics: #10 Messaging, #5 XA Transactions

### Build
- [ ] Payment Engine WAR: validates transfer → publishes event to Kafka
- [ ] Kafka topics: `payment-events`, `notification-events`
- [ ] Notification Service consuming `notification-events` (Spring Boot fat-jar or another WildFly app)

### JBoss Practice
- [ ] Artemis/JMS subsystem:
  - **pooled-connection-factory**
  - Queues, DLQ + redelivery (`max-delivery-attempts`, `dead [ ] Journal storage config — **file vs JDBC journal** (try JDBC with Oracle!)
- [ ] CLI: create queue, change DLQ settings, restart, verify
- [ ] **XA datasource** on Oracle + **XA recovery manager**:
  - Check `transactions` subsystem (Narayana)
  - Tune `default-timeout`, orphan detection
- [ ] Break & fix: force a rollback mid-transfer → watch recovery log

### ✅ Deliverable
> Payment Engine publishes to Kafka, Notification consumes, DLQ demonstrated with a poison message.

---

## 🔐 Phase 3 — Web Tier: Customer & Admin Portals

### 🎯 Topics: #7 Security

### Build
- [ ] **Admin Portal WAR:**
  - Open account (creates Customer-1, Customer-2)
  - Single customer → multiple accounts
  - Freeze / unfreeze account
- [ ] **Customer Portal WAR:**
  - Login → Dashboard (balance + last 10 transactions)
  - Add beneficiary (other customer)
  - **Internal transfer** (own account → own account)
  - **External transfer** (Customer-1 → Customer-2)
- [ ] Portals call CBS / Payment Engine REST APIs

### JBoss Practice
- [ ] **Legacy security domain** (Database login module) first
- [ ] Then **Elytron** (great interview material!)
- [ ] Users/roles in DB tables: `ROLE_ADMIN`, `ROLE_CUSTOMER`
- [ ] **RBAC on management interface**: users as Monitor / Operator / Administrator — prove access differences in console
- [ ] **SSL/TLS**: keystore → Elytron `key-store` → `ssl-context` → HTTPS listener → mutual TLS
- [ ] **Credential store** (`elytron-tool.sh`) for DB passwords instead of plaintext in `standalone.xml`

### ✅ Deliverable
> Portals role-secured, HTTPS enabled, passwords vaulted.

---

## 🔄 Phase 4 — Clustering & HA with Nginx

### 🎯 Topics: #6 Clustering, HA & Load Balancing

### Build
- [ ] 2 web-tier nodes in a cluster (`<distributable/>` in `web.xml`)
- [ ] Nginx load balancing (`proxy_pass`) — compare with **mod_cluster** (JBoss-native; install if you want the full story)
- [ ] Login → kill node 1 → verify session failover

### JBoss Practice
- [ ] JGroups: **TCP discovery** (UDP multicast often fails on laptops/cloud)
- [ ] HA profile in `standalone-ha.xml`
- [ ] Infinispan web session cache: **distributed vs replicated** — observe state transfer in logs
- [ ] **HA Singleton**: scheduled job ("EOD interest calc") that runs on only one node (MSC `SingletonService`)
- [ ] **Zero-downtime**: rolling restart node 1 → node 2 while JMeter load runs

### ✅ Deliverable
> Kill a node mid-session — user doesn't even notice. 🥇 That demo is gold.

---

## 🏗️ Phase 5 — Domain Mode

### 🎯 Topics: #1, #11 Domain Mode Deep Dive

Convert the whole bank to domain mode:

- [ ] Domain Controller + Host Controller (same machine first, then second VM/container)
- [ ] `domain.xml`: profiles per tier (`web-profile`, `cbs-profile`)
- [ ] Server groups: `web-servers`, `cbs-servers`
- [ ] `host.xml` / `host-slave.xml` with static discovery of domain controller
- [ ] Deploy EARs to server groups via console — **one deployment, all nodes**
- [ ] Per-server-group config: port offsets, JVM settings in `host.xml`
- [ ] Rolling restarts across server groups:

```bash
/server-group=web-servers:restart-servers(blocking=true)
```

### ✅ Deliverable
> Entire bank running in domain mode; **one CLI command deploys to all nodes**.

---

## 📊 Phase 6 — Performance, Monitoring & Load Test

### 🎯 Topics: #8 Performance & Tuning, #9 Logging & Monitoring

### Build
- [ ] JMeter test plan (100+ users doing transfers)
- [ ] Prometheus (`microprofile-metrics` / `/metrics` endpoint) + Grafana dashboards
- [ ] ELK (or at least syslog logging)

### Practice
- [ ] Undertow tuning: worker threads, buffer pools, HTTP/2
- [ ] EJB pool sizing, connection pool flush + statistics:

```bash
/subsystem=datasources/data-source=CBS_DS:read-resource(include-runtime=true,metrics=true)
```

- [ ] GC tuning: G1GC heap sizing → compare **before/after**
- [ ] Slow request analysis: access log with `%T` timing
- [ ] **Break things on purpose:**
  - Connection leak → leak detection + `jstack`/`jmap` → heap dump → MAT

### ✅ Deliverable
> Grafana dashboard + a written "before/after tuning" report — **perfect interview artifact**.

---

## ⚙️ Phase 7 — Automation, CI/CD & Containers

### 🎯 Topics: #12 Migration, Upgrades & Automation

- [ ] CLI batch scripts (`provision.cli` + `jboss-cli.sh --file=provision.cli`) for full config provisioning
- [ ] **Ansible playbook**: install WildFly, configure datasource, deploy app
- [ ] **Docker**: WildFly s2i image or plain Dockerfile, one container per role
- [ ] **Jenkins pipeline**: build → deploy to "staging" server group
- [ ] **Upgrade drill**: WildFly 24 → 26 side-by-side (blue-green), verify config migration
- [ ] `javax` → `jakarta` application migration practice

### ✅ Deliverable
> One-command environment provisioning + CI/CD pipeline.

---

## 🚨 Phase 8 — Troubleshooting Drills (Continuous)

### 🎯 Topics: #13 Troubleshooting & Production Operations

Weekly "chaos drills" — break, then fix:

- [ ] Delete a module → `ClassNotFoundException` → triage via module dependencies
- [ ] Port conflict between instances → diagnose
- [ ] Force OOM → heap dump → MAT analysis
- [ ] Simulate stuck thread (`Thread.sleep` in EJB) → `jstack` → find it
- [ ] High CPU → `top` + `jstack` correlation
- [ ] Corrupt `standalone.xml` → restore from snapshot
- [ ] Write a **runbook** for each incident — this IS the postmortem skill

### ✅ Deliverable
> An `incident-runbooks/` folder in your repo with real postmortems.

---

## 🧰 Tech Stack Summary

| Layer          | Technology                                          |
| -------------- | --------------------------------------------------- |
| Web            | JSP/Servlet or Thymeleaf (keep simple)              |
| Services       | REST (JAX-RS), EJB for business logic (EJB pools!)  |
| DB             | Oracle XE, JPA/Hibernate                            |
| Messaging      | Kafka + Artemis (inside WildFly)                    |
| Auth           | Elytron + DB roles                                  |
| Load Balancer  | Nginx → mod_cluster                                 |
| CI/CD          | Maven, Jenkins, Ansible, Docker                     |
| Monitoring     | Prometheus + Grafana, ELK                           |
| Load Testing   | JMeter                                              |

---

## 🗓️ Suggested Timeline

| Phases     | Duration   |
| ---------- | ---------- |
| Phase 0–1  | 2 weeks    |
| Phase 2–3  | 3 weeks    |
| Phase 4–5  | 3 weeks    |
| Phase 6–7  | 2–3 weeks  |
| Phase 8    | Continuous |

---
