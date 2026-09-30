# Phase 9 — Monitoring & JNDI DataSource

## Goal
JMX + Prometheus + Grafana monitoring. Replace raw JDBC with HikariCP JNDI DataSource.

## Tomcat Instance (CBS — primary monitoring target)
Port: 8090 | Instance: /opt/tomcat-cbs/ | Shutdown Port: 8104

## Notification Service — Tomcat Instance (introduced Phase 4, configured here)
Port: 8092 | Instance: /opt/tomcat-notification/ | Shutdown Port: 8106
```xml
<!-- /opt/tomcat-notification/conf/server.xml -->
<Server port="8106" shutdown="SHUTDOWN">
  <Connector port="8092"
             protocol="HTTP/1.1"
             connectionTimeout="20000"
             redirectPort="8443"/>
```

## Tomcat Skills
- #6 JMX Remote
- #6 Prometheus + Grafana dashboards
- #7 JNDI DataSource (HikariCP)

## Step 1 — Enable JMX (setenv.sh)
```bash
export JAVA_OPTS="$JAVA_OPTS \
  -Dcom.sun.management.jmxremote \
  -Dcom.sun.management.jmxremote.port=9010 \
  -Dcom.sun.management.jmxremote.ssl=false \
  -Dcom.sun.management.jmxremote.authenticate=false"
```

## Step 2 — Prometheus JMX Exporter
```yaml
# jmx_exporter_config.yml
rules:
  - pattern: "Catalina<type=ThreadPool, name=\"(.*)\"><>(.*)"
    name: tomcat_threadpool_$2
  - pattern: "Catalina<type=GlobalRequestProcessor, name=\"(.*)\"><>(.*)"
    name: tomcat_requests_$2
  - pattern: "java.lang<type=Memory><HeapMemoryUsage>(.*)"
    name: jvm_heap_$1
```

## Step 3 — JNDI DataSource (context.xml)
```xml
<!-- /opt/tomcat-cbs/conf/context.xml -->
<Context>
  <Resource name="jdbc/DigiStackDB"
            auth="Container"
            type="javax.sql.DataSource"
            factory="com.zaxxer.hikari.HikariJNDIFactory"
            driverClassName="oracle.jdbc.OracleDriver"
            jdbcUrl="jdbc:oracle:thin:@localhost:1521:XE"
            username="digistack"
            password="digistack123"
            maximumPoolSize="20"
            minimumIdle="5"
            connectionTimeout="30000"
            idleTimeout="600000"
            maxLifetime="1800000"
            connectionTestQuery="SELECT 1 FROM DUAL"
            leakDetectionThreshold="60000"/>
</Context>
```

## Step 2B — Copy Required JARs to Tomcat lib
```bash
# HikariCP JAR — download or use Maven
# https://repo1.maven.org/maven2/com/zaxxer/HikariCP/5.1.0/HikariCP-5.1.0.jar
cp HikariCP-5.1.0.jar /opt/tomcat-cbs/lib/
cp ojdbc8.jar /opt/tomcat-cbs/lib/

# Verify
ls /opt/tomcat-cbs/lib/ | grep -E "hikari|ojdbc"
# HikariCP-5.1.0.jar  ojdbc8.jar

# Restart to pick up new JARs
systemctl restart tomcat-cbs
```

## Grafana Dashboard Panels
- Active Threads vs Max Threads
- Heap Used vs Heap Max
- Request rate (req/sec)
- Average response time
- DB pool active connections
- DB pool wait time

## Verification
```bash
# JMX via jconsole
jconsole localhost:9010

# Prometheus metrics endpoint
curl http://localhost:9404/metrics | grep tomcat

# JNDI lookup in servlet
Context ctx = new InitialContext();
DataSource ds = (DataSource) ctx.lookup("java:comp/env/jdbc/DigiStackDB");
Connection conn = ds.getConnection();
```

## Phase-End Interview Questions
1. What JMX MBeans does Tomcat expose for the CBS thread pool — what is the MBean type name?
2. What is the difference between a JNDI `DataSource` and raw `DriverManager.getConnection()` in a CBS servlet?
3. In DigiStack `context.xml`, `leakDetectionThreshold=60000` — what does this detect and where is the warning logged?
4. What Prometheus metric tells you CBS is running out of threads — what is the threshold to alert on?
5. In `jconsole localhost:9010`, which MBean shows current active DB connections from HikariCP?

## Status
- [ ] Not started
- [ ] In progress
- [ ] Completed

## Notes