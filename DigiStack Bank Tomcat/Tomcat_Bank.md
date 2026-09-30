# 🏦 Bank Simulation Project — Tomcat Admin Hands Level)

A phase-wise practice project simulating a real bank, designed to master **Apache Tomcat administration** end-to-end — from fundamentals to clustering, tuning, security, monitoring, Docker, and CI/CD.

---

## 📐 Target Architecture (Final State)

```text
Browser
   │
Nginx (reverse proxy, load balancer, SSL termination)
   │
┌───────────────┬───────────────────┐
│ Admin Portal  │ Customer Portal   │  (2 Tomcat instances, load balanced)
│ Tomcat :8081  │ Tomcat :8082/8083 │
└───────┬───────┴─────────┬─────────┘
        │  REST (HTTP)    │
        ▼                 ▼
┌──────────────┐   ┌──────────────────┐
│ CBS Service  │   │ Payment Engine   │
│ Tomcat :8090 │   │ Tomcat :8091     │
│ accounts,    │   │ validation,      │
│ customers,   │   │ ledger           │
│ freeze ops   │   └──────┬───────────┘
└──────┬───────┘          │
       │                  │  Kafka topics: payments, notifications
       └────────┬─────────┘
                ▼              ▼
         Oracle DB (XE)   Notification Service (Tomcat :8092)
                          (Kafka consumer)
```

> **4–5 Tomcat instances** = perfect playground for clustering, deployment, and tuning practice.

---

## ✅ Topic Coverage Map

| # | Tomcat Topic | Practiced in Phase |
|---|--------------|--------------------|
| 1 | Fundamentals & Architecture | Phase 0, 2 |
| 2 | Deployment 2, 3, 10 |
| 3 | Performance Tuning | Phase 4, 6 |
| 4 | Clustering, HA & Load Balancing | Phase 7 |
| 5 | Security | Phase 0, 5, 8 |
| 6 | Monitoring & Troubleshooting | Phase 6, 9 |
| 7 | Database Connectivity | Phase 1, 9 |
| 8 Phase 2, 4, 5 |
| 9 | Production Operations | Phase 0, 6, 10 |
| 10 | Integration & Ecosystem | Phase 3, 10 |

---

## 🛠 Phase 0 — Environment Setup (Week 1)

**Goal:** Raw infrastructure before any code.

- [ ] Install Java 17, Oracle XE (or PostgreSQL — config identical), Kafka (single broker), Nginx
- [ ] Install **2 separate Tomcat 9 installations** from tarball (NOT embedded — practice.xml manually)
- [ ] Explore directory structure: `bin `logs`, `webapps`, `temp`, `work`
- [ ] Instance 2: change shutdown port `8005 → 8105`, HTTP port `8080 → 8081`
- [ ] Create non-root `tomcat` user; run Tomcat as that user
- [ ] Write **systemd units** for both instances

**Topics hit:** #1 Fundamentals, #5 Security basics, #9 systemd

---

## 🗄 Phase 1 — Database Schema (Week 1–2)

```sql
CREATE TABLE CUSTOMERS (
  cust_id      NUMBER PRIMARY KEY,
  name         VARCHAR2(100),
  email        VARCHAR2(100),
  login        VARCHAR2(50) UNIQUE,
  password_hash VARCHAR2(200),
  status       VARCHAR2(20) DEFAULT 'ACTIVE'
);

CREATE TABLE ACCOUNTS (   VARCHAR2(20) PRIMARY KEY,
  cust_id   NUMBER REFERENCES CUSTOMERS(cust_id),
  type      VARCHAR2(10),          -- SAVINGS / CURRENT
  balance   NUMBER(15,2),
  status    VARCHAR2(20) DEFAULT 'ACTIVE'   -- ACTIVE / FROZEN
);

CREATE TABLE BENEFICIARIES (
  id                 NUMBER PRIMARY KEY,
  owner_cust_id      NUMBER REFERENCES CUSTOMERS(cust_id), VARCHAR2(20),
  nickname           VARCHAR2(50)
);

CREATE TABLE TRANSACTIONS (
  txn_id          VARCHAR2(30) PRIMARY KEY,
  from_acct       VARCHAR2(20),
  to_acct         VARCHAR2(20),
  amount2),
  type            VARCHAR2(20),    -- INTERNAL / EXTERNAL
  status          VARCHAR2(20),
  kafka_event_id  VARCHAR2(64),
  created_at      TIMESTAMP DEFAULT SYSTIMESTAMP
);
```

-1 gets 2 accounts (savings + current) → proves "one customer, multiple accounts"
- [ ] Pre-seed Customer-1 and Customer-2 with balances

**Topics hit:** #7 foundation — later migrated to **JNDI DataSource** (key exercise)

---

## ⚙️ Phase 2 — CBS Service (Week 2–3)

Core Banking Service — plain servlets/JSP + JSON (plain WAR on Tomcat teaches more than Spring Boot).

### REST Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/cbs/customers` | Open customer |
| POST | `/cbs/accounts` | Open account (Admin only) |
| POST | `/cbs/accounts/{acct}/freeze` | Freeze account (Admin only) |
| POST | `/cbs/accounts/{acct}/unfreeze` | Unfreeze account (Admin only) |
| GET | `/cbs/accounts/{acct}` | Balance |
| GET | `/cbs/accounts/{acct}/transactions?limit=10` | Recent transactions |
| POST | `/cbs/beneficiaries` | Add beneficiary |
| POST | `/cbs/transfer/internal` | Own-account transfer |
| POST | `/cbs/transfer/external` | Customer-1 → Customer-2 |

### Tomcat Skills in This Phase
- [ ] Deploy WAR manually to `webapps/`, watch `catalina.out`
- [ ] Try auto-deploy: copy WAR while Tomcat running, watch lifecycle logs
- [ ] Break a WAR on purpose (bad `web.xml`) → read `localhost.YYYY-MM-DD.log`
- [ ] Set `reloadable="true"` then `false` — observe behavior

**Topics hit:** #2 Deployment, #6 Log analysis

---

## 🌐 Phase 3 — Nginx Reverse Proxy (Week 3)

- [ ] `proxy_pass http://localhost:8090` for CBS
- [ ] Switch to **AJP connector** vs HTTP `proxy_pass` — compare behavior
- [ ] Configure `proxy_send_timeout`, upstream keepalive
- [ ] Serve static assets from Nginx, dynamic from Tomcat
- [ ] Enable gzip compression at Nginx AND at Tomcat connector — compare

**Topics hit:** #2 Reverse proxy, #3 Compression

---

## 💳 Phase 4 — Payment Engine + Kafka (Week 4–5)

- [ ] Payment Engine Tomcat app (`:8091`): CBS calls it via REST
- [ ] Validates: balance check, frozen-status check
- [ ] Writes transaction, publishes to Kafka topic `payments`
- [ ] **Notification Service** (Tomcat `:8092`) consumes `payments` topic → writes notification to table/log

### Tomcat Skills
- [ ] Kafka consumer inside servlet app via `ServletContextListener` (app lifecycle)
- [ ] Practice thread pool sizing — consumer threads vs connector threads

**Topics hit:** #8 Lifecycle, #3 Thread pools

---

## 👤 Phase 5 — Customer & Admin Portals (Week 5–6)

- [ ] Customer portal WAR: login, dashboard (balance + last 10 txns), transfer forms, beneficiary management
- [ ] Admin portal WAR: open accounts, freeze/unfreeze

### Tomcat Skills
- [ ] Form authentication: start with **MemoryRealm** (`tomcat-users migrate to **JDBCRealm**
- [ ] `<security-constraint>` in `web.xml`: `/admin/*` restricted to role `ADMIN`
- [ ] Session timeout config: `web.xml` vs `context.xml`
- [ ] Security headers (clickjacking protection) via custom **Valve/Filter**

**Topics hit:** #5 Realms & constraints management

---

## 🚀 Phase 6 — Performance Tuning (Week 6–7)

Load test with JMeter: 200 concurrent transfers.

- [ ] Switch connector **B** — measure difference
- [ ] Configure shared **Executor** thread pool
- [ ] Tune: `maxThreads`, `acceptCount`, `maxConnections`, `connectionTimeout`
- [ ] JVM: deliberately set small heap `-Xms512m -Xmx512m` → trigger `OutOfMemoryError` → heap dump with `jmap` → analyze in **MAT**
- [ ] Add `Thread.sleep(60000)` endpoint → catch stuck thread with **jstack**
- [ ] Enable Tomcat access log with `%D` (request time) to find slow requests

**Topics hit:** #3 Tuning (full), #6 Thread/heap dumps — *most interview-heavy phase*

---

## 🔗 Phase 7 — Clustering & HA (Week 7–8)

Run **2 instances of Customer Portal** behind Nginx.

- [ ] **Sticky sessions** first (Nginx: `hash $cookie_JSESSIONID`)
- [ ] **DeltaManager clustering** (`<Cluster>` in `server.xml`, multicast)
- [ ] Test failover: kill one node mid-session → user survives on the other
- [ ] Try unicast (StaticMembership) if multicast is blocked

**Topics hit:** #4 — completely hands-on

---

## 8 — SSL/TLS & Hardening (Week 8)

- [ ] Self-signed cert via `keytool` → HTTPS on Nginx (SSL termination) AND directly on Tomcat (practice both)
- [ ] Restrict TLS 1.2/1.3, disable weak ciphers
- [ ] Remove `docs`, `examples`, `manager` from production instances
- [ ] Protect Manager app: distinct credentials + IP-restricting `RemoteAddrValve`
- [ ] Demo the shutdown port: `telnet localhost 8005` → `SHUTDOWN` → then disable it

**Topics hit:** #5 — fully

---

## 📊 Phase 9 — Monitoring & JNDI DataSource (Week 9)

- [ ] Enable **JMX Remote** on all Tomcats
- [ ] Prometheus JMX exporter + Grafana dashboards: threads, heap, request time, pool stats
- [ ] Replace Phase 2 raw JDBC with **HikariCP JNDI DataSource** in `context.xml`
- [ ] Practice pool sizing, `validationQuery`, leak detection

**Topics hit:** #6 JMX/Prometheus, #7 JNDI + HikariCP

---

## 🐳 Phase 10 — Docker / Kubernetes / CI-CD (Week 10+)

- [ ] Dockerize each Tomcat app (`Dockerfile` based on `tomcat:9-jdk17`)
- [ ] Docker Compose: nginx + 4 tomcats + kafka + oracle
- [ ] Jenkins pipeline: build WAR Manager **text API** (`/manager/text/deploy`)
- [ ] Rolling deploy script: deploy to node B → verify → switch Nginx upstream (mini **blue-green**)
- [ ] Optional: Kubernetes Deployment with session affinity

**Topics hit:** #10 Ecosystem, #2 Deployment strategies, #9 Capacity

---

## 🗓 Timeline Summary

| Phase | Weeks | Focus |
|-------|-------| 0 | 1 | Environment, systemd, non-root |
| 1 | 1–2 | Oracle schema |
| 2 | 2–3 | CBS service + WAR deployment |
| 3 | 3 | Nginx reverse proxy (HTTP vs AJP) |
| 4 | 4–5 | Payment Engine + Kafka |
| 5 | 5–6 | Portals + Realms + security constraints |
| 6 | 6–7 | Tuning, JVM, jstack, jmap, OOM |
| 7 | 7–8 | Clustering, sticky sessions, replication |
| 8 | 8 | SSL/TLS + hardening |
| 9 | 9 | JMX, Prometheus, Grafana, HikariCP |
| 10 | 10+ | Docker, K8s, Jenkins CI/CD |

---

## 🏁 Outcome

After completing all phases you will have **hands-on, demonstrable experience** in:

- Tomcat architecture (Catalina, Coyote, Jasper) & server.xml mastery
- WAR deployment, hot deploy, blue-green & rolling strategies
- Connector & JVM tuning with real load-test evidence
- Session replication & failover (DeltaManager + sticky sessions)
- SSL/TLS, realms, security constraints, hardening
- Thread/heap dump analysis, OOM troubleshooting
- JNDI DataSources with HikariCP pool monitoring
- JMX + Prometheus + Grafana monitoring
- Docker, Kubernetes, Jenkins CI/CD to Tomcat
- Nginx integration (HTTP proxy_pass vs AJP)
```
