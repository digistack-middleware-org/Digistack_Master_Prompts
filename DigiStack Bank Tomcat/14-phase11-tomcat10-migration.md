# Phase 11 — Tomcat 10 Migration (Jakarta EE)

## Goal
Migrate DigiStack Bank from Tomcat 9 (javax.*) to Tomcat 10 (jakarta.*).
This is the single biggest breaking change in modern Java EE history.

## Real World Context
Many enterprises are migrating from:
- Tomcat 9 → Tomcat 10
- Java EE 8 → Jakarta EE 9/10
- javax.* → jakarta.* namespace

Knowing this migration = big advantage in interviews.

## The Core Breaking Change
```
Tomcat 9 → uses javax.servlet.*
Tomcat 10 → uses jakarta.servlet.*
```
Every import in your servlet code must change.

## Step 1 — Install Tomcat 10 Alongside Tomcat 9
```bash
# Download Tomcat 10
cd /opt
wget https://downloads.apache.org/tomcat/tomcat-10/v10.1.x/bin/apache-tomcat-10.1.x.tar.gz
tar -xzf apache-tomcat-10.1.x.tar.gz
mv apache-tomcat-10.1.x tomcat10-cbs

# Keep Tomcat 9 running — migrate one service at a time
# Tomcat 9 CBS  → :8090  (old)
# Tomcat 10 CBS → :8094  (new — migration target)
chown -R tomcat:tomcat /opt/tomcat10-cbs
```

## Step 2 — Code Changes (Every Servlet)

### Before (Tomcat 9 — javax)
```java
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import javax.naming.InitialContext;
import javax.sql.DataSource;
```

### After (Tomcat 10 — jakarta)
```java
import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import jakarta.servlet.http.HttpSession;
import jakarta.naming.InitialContext;
import jakarta.sql.DataSource;
```

### Simple migration command (Linux)
```bash
# Find all Java files and replace javax → jakarta
find ./src -name "*.java" | xargs sed -i \
  's/import javax\.servlet/import jakarta.servlet/g'

find ./src -name "*.java" | xargs sed -i \
  's/import javax\.naming/import jakarta.naming/g'

find ./src -name "*.java" | xargs sed -i \
  's/import javax\.sql/import jakarta.sql/g'

# Verify changes
grep -r "import javax" ./src
# Should return nothing if migration complete
```

## Step 3 — pom.xml Dependency Change
```xml
<!-- Tomcat 9 / Java EE 8 — REMOVE this -->
<dependency>
  <groupId>javax.servlet</groupId>
  <artifactId>javax.servlet-api</artifactId>
  <version>4.0.1</version>
  <scope>provided</scope>
</dependency>

<!-- Tomcat 10 / Jakarta EE 9 — ADD this -->
<dependency>
  <groupId>jakarta.servlet</groupId>
  <artifactId>jakarta.servlet-api</artifactId>
  <version>6.0.0</version>
  <scope>provided</scope>
</dependency>
```

## Step 4 — web.xml Namespace Change
```xml
<!-- Tomcat 9 web.xml header -->
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
         http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

<!-- Tomcat 10 web.xml header -->
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee
         https://jakarta.ee/xml/ns/jakartaee/web-app_6_0.xsd"
         version="6.0">
```

## Step 5 — Migration Verification
```bash
# Build new WAR
mvn clean package

# Deploy to Tomcat 10
cp target/cbs.war /opt/tomcat10-cbs/webapps/

# Watch logs
tail -f /opt/tomcat10-cbs/logs/catalina.out

# Test endpoint
curl http://localhost:8094/cbs/accounts/DSB-SAV-00001
# Must return same response as Tomcat 9
```

## Step 6 — Side-by-Side Comparison
```bash
# Run both simultaneously
curl http://localhost:8090/cbs/accounts/DSB-SAV-00001  # Tomcat 9
curl http://localhost:8094/cbs/accounts/DSB-SAV-00001  # Tomcat 10

# Both must return identical response
# Then switch Nginx upstream to Tomcat 10
```

## Tomcat 9 vs Tomcat 10 Quick Reference
| Item | Tomcat 9 | Tomcat 10 |
|------|----------|-----------|
| Servlet API | javax.servlet | jakarta.servlet |
| Servlet Spec | 4.0 | 6.0 |
| Java EE | Java EE 8 | Jakarta EE 10 |
| Min Java | Java 8 | Java 11 |
| web.xml ns | xmlns.jcp.org | jakarta.ee |

## Tomcat Migration Tool (Official Apache Tool)
```
# Download from Apache
wget https://downloads.apache.org/tomcat/migration-tool/v1.0.x/migration-1.0.x-bin.tar.gz
tar -xzf migration-1.0.x-bin.tar.gz
cd migration-1.0.x

# Migrate your existing Tomcat 9 WAR automatically (no source code needed)
./migrate.sh --input /opt/tomcat-cbs/webapps/cbs.war \
             --output /tmp/cbs-migrated.war

# What it does: rewrites javax.* → jakarta.* in compiled bytecode
# and updates web.xml xmlns — works on the WAR directly

# Deploy migrated WAR to Tomcat 10
cp /tmp/cbs-migrated.war /opt/tomcat10-cbs/webapps/cbs.war
tail -f /opt/tomcat10-cbs/logs/catalina.out
```
## Common Migration Errors
```
Error 1 — forgot to change import

ClassNotFoundException: javax.servlet.HttpServlet
Fix: Change import to jakarta.servlet.HttpServlet

Error 2 — old WAR deployed to Tomcat 10

java.lang.NoClassDefFoundError: javax/servlet/Servlet
Fix: Rebuild WAR with jakarta dependencies

Error 3 — web.xml namespace mismatch

org.xml.sax.SAXParseException: cvc-elt.1
Fix: Update web.xml xmlns to jakarta.ee
```

## Phase-End Interview Questions
1. What is the single biggest breaking change between Tomcat 9 and Tomcat 10 — show the import diff for DigiStack's `AccountServlet.java`?
2. Why did `javax.*` change to `jakarta.*` — what legal event caused this namespace change?
3. You deploy the original `cbs.war` (built for Tomcat 9) to `/opt/tomcat10-cbs/webapps/` — what exact error appears in `catalina.out`?
4. What does the Tomcat Migration Tool do that `sed -i 's/javax/jakarta/g'` cannot?
5. In DigiStack Phase 11, both Tomcat 9 (`:8090`) and Tomcat 10 (`:8094`) run simultaneously — how do you switch Nginx from old to new with zero downtime?

## Status
- [ ] Not started
- [ ] In progress
- [ ] Completed

## Notes
(paste migration errors here)