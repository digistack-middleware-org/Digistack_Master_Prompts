# Phase 12 — Spring Boot Embedded Tomcat

## Goal
Understand how Spring Boot uses Tomcat internally.
Compare embedded vs standalone. Know how to tune embedded Tomcat.

## Real World Context
Most modern Java applications use Spring Boot with embedded Tomcat.
Interviews often ask:
- "How is embedded Tomcat different from standalone?"
- "How do you tune thread pool in Spring Boot?"
- "How do you replace embedded Tomcat with Jetty?"

## Embedded vs Standalone — Key Differences
| Feature | Standalone Tomcat | Embedded (Spring Boot) |
|---------|------------------|----------------------|
| Startup | startup.sh | java -jar app.jar |
| WAR deploy | copy to webapps/ | packaged inside JAR |
| server.xml | manual edit | application.properties |
| Multiple apps | yes (multiple WARs) | one app per JVM |
| Hot deploy | yes | DevTools only |
| Admin console | Manager app | Actuator endpoints |
| Port change | server.xml | server.port=8090 |
| Thread pool | Executor in server.xml | server.tomcat.threads.max |

## Step 1 — Create Spring Boot CBS (Embedded Tomcat)

### pom.xml
```xml
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>3.2.0</version>
</parent>

<dependencies>
  <!-- Embeds Tomcat 10 automatically -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>

  <!-- Actuator — replaces Tomcat Manager -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
  </dependency>

  <!-- Oracle JDBC -->
  <dependency>
    <groupId>com.oracle.database.jdbc</groupId>
    <artifactId>ojdbc8</artifactId>
    <version>21.9.0.0</version>
  </dependency>

  <!-- HikariCP (default in Spring Boot) -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jdbc</artifactId>
  </dependency>
</dependencies>
```

## Step 2 — application.properties (replaces server.xml)
```properties
# Port (replaces HTTP Connector port in server.xml)
server.port=8095

# Tomcat thread pool (replaces Executor in server.xml)
server.tomcat.threads.max=200
server.tomcat.threads.min-spare=25
server.tomcat.accept-count=100
server.tomcat.max-connections=1000
server.tomcat.connection-timeout=20000

# Access log (replaces AccessLogValve)
server.tomcat.accesslog.enabled=true
server.tomcat.accesslog.pattern=%h %l %u %t "%r" %s %b %D
server.tomcat.accesslog.directory=logs

# HikariCP DataSource (replaces context.xml JNDI)
spring.datasource.url=jdbc:oracle:thin:@localhost:1521:XE
spring.datasource.username=digistack
spring.datasource.password=digistack123
spring.datasource.driver-class-name=oracle.jdbc.OracleDriver
spring.datasource.hikari.maximum-pool-size=20
spring.datasource.hikari.minimum-idle=5
spring.datasource.hikari.connection-timeout=30000
spring.datasource.hikari.leak-detection-threshold=60000

# Actuator (replaces Tomcat Manager app)
management.endpoints.web.exposure.include=health,metrics,threaddump,heapdump
management.endpoint.health.show-details=always
```

## Step 3 — CBS REST Controller (Spring Boot style)
```java
// Same logic as your servlet — different framework
@RestController
@RequestMapping("/cbs")
public class AccountController {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    @GetMapping("/accounts/{acctNo}")
    public ResponseEntity<Map<String, Object>> getBalance(
            @PathVariable String acctNo) {

        String sql = "SELECT acct_no, balance, status FROM accounts WHERE acct_no = ?";

        try {
            Map<String, Object> account = jdbcTemplate.queryForMap(sql, acctNo);
            return ResponseEntity.ok(Map.of(
                "status", "SUCCESS",
                "code", "CBS-200",
                "data", account
            ));
        } catch (EmptyResultDataAccessException e) {
            return ResponseEntity.status(404).body(Map.of(
                "status", "FAILED",
                "code", "CBS-4005",
                "message", "Invalid account number"
            ));
        }
    }
}
```

## Step 4 — Run and Verify
```bash
# Build
mvn clean package

# Run (embedded Tomcat starts automatically)
java -jar target/cbs-springboot.jar

# Check startup log — note embedded Tomcat
# Expected:
# Tomcat started on port(s): 8095 (http) with context path ''

# Test
curl http://localhost:8095/cbs/accounts/DSB-SAV-00001

# Actuator — like Tomcat Manager but REST-based
curl http://localhost:8095/actuator/health
curl http://localhost:8095/actuator/metrics/tomcat.threads.busy
curl http://localhost:8095/actuator/threaddump
```

## Step 5 — Deploy as WAR to Standalone Tomcat
Spring Boot can also produce a WAR (best of both worlds):

```xml
<!-- pom.xml — change packaging -->
<packaging>war</packaging>

<!-- Mark embedded Tomcat as provided -->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-tomcat</artifactId>
  <scope>provided</scope>
</dependency>
```

```java
// Main class must extend SpringBootServletInitializer
@SpringBootApplication
public class CbsApplication extends SpringBootServletInitializer {
    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder app) {
        return app.sources(CbsApplication.class);
    }
    public static void main(String[] args) {
        SpringApplication.run(CbsApplication.class, args);
    }
}
```

```bash
# Build WAR
mvn clean package
# Produces: target/cbs.war

# Deploy to standalone Tomcat 10
cp target/cbs.war /opt/tomcat10-cbs/webapps/
curl http://localhost:8094/cbs/accounts/DSB-SAV-00001
```

## Side-by-Side: Same Feature, Different Config
| Feature | Standalone Tomcat | Spring Boot Embedded |
|---------|------------------|---------------------|
| Max threads | server.xml Executor maxThreads | server.tomcat.threads.max |
| Access log | AccessLogValve in server.xml | server.tomcat.accesslog.enabled |
| DataSource | context.xml Resource | spring.datasource.hikari.* |
| Health check | Manager app /manager/status | /actuator/health |
| Thread dump | jstack PID | /actuator/threaddump |
| Heap dump | jmap -dump | /actuator/heapdump |

## Phase-End Interview Questions
1. When `java -jar cbs-springboot.jar` runs, what log line confirms embedded Tomcat started on `:8095`?
2. In DigiStack `application.properties`, `server.tomcat.threads.max=200` — what is the exact equivalent XML in standalone Tomcat's `server.xml`?
3. To deploy `cbs-springboot.war` to `/opt/tomcat10-cbs/webapps/`, what two changes are needed in `pom.xml` and what must the main class extend?
4. In standalone DigiStack, you check thread health via `jstack` — what is the exact Actuator endpoint equivalent in Spring Boot CBS?
5. Spring Boot 3.x embeds Tomcat 10 (jakarta.*) — if you also run standalone `tomcat-cbs` at `:8090` (Tomcat 9, javax.*), can they coexist on the same RHEL 8 server? What determines which Java version each uses?

## Status
- [ ] Not started
- [ ] In progress
- [ ] Completed

## Notes
(paste your Spring Boot errors or output here)