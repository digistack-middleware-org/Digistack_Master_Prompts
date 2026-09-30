# DigiStack Bank — Progress Tracker

## Overall Progress

| Phase | Name | Status | Started | Completed |
|-------|------|--------|---------|-----------|
| 0 | Environment Setup | ✅ Done | - | - |
| 1 | Database Schema | 🔄 In Progress | - | - |
| 2 | CBS Service | ⏳ Pending | - | - |
| 3 | Nginx Reverse Proxy | ⏳ Pending | - | - |
| 4 | Payment Engine + Kafka | ⏳ Pending | - | - |
| 5 | Customer & Admin Portals | ⏳ Pending | - | - |
| 5B | LDAP Realm (Enterprise) | ⏳ Pending | - | - |
| 6 | Performance Tuning | ⏳ Pending | - | - |
| 7 | Clustering & HA | ⏳ Pending | - | - |
| 8 | SSL/TLS & Hardening | ⏳ Pending | - | - |
| 9 | Monitoring & JNDI DataSource | ⏳ Pending | - | - |
| 10 | Docker / Kubernetes / CI-CD | ⏳ Pending | - | - |
| 11 | Tomcat 10 Migration | ⏳ Pending | - | - |
| 12 | Spring Boot Embedded Tomcat | ⏳ Pending | - | - |

---

## Tomcat Instance Registry

| Instance | Port | Shutdown Port | Service | Install Path |
|----------|------|---------------|---------|-------------|
| tomcat-admin | 8081 | 8101 | Admin Portal | /opt/tomcat-admin/ |
| tomcat-customer1 | 8082 | 8102 | Customer Portal Node 1 | /opt/tomcat-customer1/ |
| tomcat-customer2 | 8083 | 8103 | Customer Portal Node 2 | /opt/tomcat-customer2/ |
| tomcat-cbs | 8090 | 8104 | CBS Service | /opt/tomcat-cbs/ |
| tomcat-payment | 8091 | 8105 | Payment Engine | /opt/tomcat-payment/ |
| tomcat-notification | 8092 | 8106 | Notification Service | /opt/tomcat-notification/ |
| tomcat10-cbs | 8094 | 8107 | CBS (Tomcat 10 migration) | /opt/tomcat10-cbs/ |
| springboot-cbs | 8095 | N/A | CBS (Spring Boot embedded) | java -jar |

---

## WAR Registry

| WAR File | Deployed To | Context Path | Phase |
|----------|-------------|--------------|-------|
| cbs.war | tomcat-cbs :8090 | /cbs | Phase 2 |
| payment.war | tomcat-payment :8091 | /payment | Phase 4 |
| notification.war | tomcat-notification :8092 | /notification | Phase 4 |
| customer-portal.war | tomcat-customer1 :8082 | /customer | Phase 5 |
| customer-portal.war | tomcat-customer2 :8083 | /customer | Phase 5 |
| admin-portal.war | tomcat-admin :8081 | /admin | Phase 5 |

---

## Database Registry

| Table | Purpose | Phase Introduced |
|-------|---------|-----------------|
| CUSTOMERS | Customer master (CIF) | Phase 1 |
| ACCOUNTS | Bank accounts | Phase 1 |
| BENEFICIARIES | Transfer beneficiaries | Phase 1 |
| TRANSACTIONS | All transactions (IMPS/NEFT/RTGS) | Phase 1 |
| AUDIT_LOG | Every admin action logged | Phase 1 |
| NOTIFICATIONS | Kafka consumer output | Phase 1 |

---

## Kafka Registry

| Topic | Producer | Consumer | Phase |
|-------|----------|----------|-------|
| payments | Payment Engine :8091 | Notification Service :8092 | Phase 4 |
| notifications | Notification Service :8092 | DB / Log write | Phase 4 |

---

## Phase-wise Interview Questions Bank

### Phase 0 — Environment & Fundamentals
1. What is the difference between CATALINA_HOME and CATALINA_BASE?
2. Why should Tomcat never run as root user?
3. What happens if two Tomcat instances share the same shutdown port?
4. What are the 5 main directories in a Tomcat installation and what does each do?
5. What is the difference between bin/startup.sh and systemd service?

### Phase 1 — Database
1. Why use GENERATED ALWAYS AS IDENTITY instead of a sequence in Oracle?
2. What is a CIF number in real banking?
3. Why is AUDIT_LOG a separate table and not just application logging?

### Phase 2 — CBS Deployment
1. What is the difference between auto-deploy and hot-deploy in Tomcat?
2. Where do you look first when a WAR fails to deploy?
3. What does reloadable="true" do and why avoid it in production?
4. What is the difference between catalina.out and localhost.YYYY-MM-DD.log?
5. What files does Tomcat create in the work/ directory?

### Phase 3 — Nginx & Connectors
1. What is the difference between HTTP proxy_pass and AJP connector?
2. Why was AJP disabled by default in Tomcat 9.0.31+?
3. What is the risk of enabling gzip compression at both Nginx and Tomcat?

### Phase 4 — Kafka & Lifecycle
1. Why use ServletContextListener to start a Kafka consumer?
2. What happens to a Kafka consumer if contextDestroyed() is not implemented?
3. What is the difference between connector threads and application threads?

### Phase 5 — Security Realms
1. What is the difference between MemoryRealm, JDBCRealm, and JNDIRealm?
2. How does FORM authentication work in Tomcat?
3. What is j_security_check and who handles it?
4. Where do you configure session timeout — web.xml or context.xml?

### Phase 5B — LDAP Realm
1. What is JNDIRealm and when would you use it over JDBCRealm?
2. What is the difference between userPattern and userSearch in JNDIRealm?
3. How do you map LDAP groups to Tomcat roles?
4. What happens if the LDAP server is down — can users still login?

### Phase 6 — Performance Tuning (Most Interview-Heavy)
1. What is the difference between maxThreads, maxConnections, and acceptCount?
2. What happens when acceptCount is full — what does the client see?
3. How do you find a stuck thread in Tomcat production?
4. How do you analyze a heap dump from an OutOfMemoryError?
5. What is the difference between BIO and NIO connectors?
6. What does %D mean in the Tomcat access log pattern?
7. What is the difference between -Xms and -Xmx?

### Phase 7 — Clustering & HA
1. What is the difference between DeltaManager and BackupManager?
2. How does sticky session work and when does it fail?
3. What happens to a user session when a node dies without replication?
4. What is multicast and why is it sometimes blocked in cloud environments?
5. What is the jvmRoute attribute used for in clustering?

### Phase 8 — SSL & Hardening
1. What is the Tomcat shutdown port and how do you disable it?
2. What is RemoteAddrValve and where is it configured?
3. What is the difference between SSL termination at Nginx vs at Tomcat?
4. Why should docs, examples, and manager apps be removed in production?
5. What does keytool -genkeypair create?

### Phase 9 — Monitoring & JNDI
1. What JMX MBeans does Tomcat expose?
2. What is the difference between JNDI DataSource and raw JDBC in a servlet?
3. How does HikariCP connection pool leak detection work?
4. What Prometheus metrics matter most for Tomcat health?
5. How do you check active DB connections via JMX?

### Phase 10 — Docker & CI/CD
1. What is the base image for a Tomcat 9 container?
2. How do you pass JAVA_OPTS to a Dockerized Tomcat?
3. What is blue-green deployment and how does it work with Tomcat?
4. How does the Tomcat Manager text API work for automated deployments?

### Phase 11 — Tomcat 10 Migration
1. What is the single biggest breaking change between Tomcat 9 and Tomcat 10?
2. Can a Tomcat 9 WAR run on Tomcat 10 without recompiling?
3. What changed in web.xml between Servlet 4.0 and Servlet 6.0?
4. What is the Tomcat Migration Tool?
5. Why did javax.* change to jakarta.*?

### Phase 12 — Spring Boot Embedded Tomcat
1. How does Spring Boot embed Tomcat — what happens at startup?
2. What is the equivalent of server.xml Executor in Spring Boot?
3. Can a Spring Boot JAR be deployed to standalone Tomcat? How?
4. What is Spring Boot Actuator and how does it replace Tomcat Manager?
5. What version of Tomcat does Spring Boot 3.x embed?

---

## Errors & Blockers Log

| Date | Phase | Error | Resolution |
|------|-------|-------|------------|
| - | - | - | - |

---

## Key Commands Quick Reference

```bash
# Start / Stop instances
systemctl start tomcat-cbs
systemctl stop tomcat-cbs
systemctl status tomcat-cbs

# Watch logs live
tail -f /opt/tomcat-cbs/logs/catalina.out
tail -f /opt/tomcat-cbs/logs/localhost.$(date +%Y-%m-%d).log

# Check ports
ss -tlnp | grep -E '8080|8081|8082|8083|8090|8091|8092'

# Thread dump
jstack $(pgrep -f tomcat-cbs) > /tmp/threaddump.txt

# Heap dump
jmap -dump:format=b,file=/tmp/heap.hprof $(pgrep -f tomcat-cbs)

# Kafka
kafka-topics.sh --list --bootstrap-server localhost:9092
kafka-console-consumer.sh --topic payments --bootstrap-server localhost:9092

# Oracle
sqlplus digistack/digistack123@localhost:1521/XE
```

---

## Notes
(general project notes here)