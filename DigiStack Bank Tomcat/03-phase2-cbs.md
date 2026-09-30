# Phase 2 — CBS Service (Core Banking)

## Goal
Build CBS as a plain servlet WAR. Practice WAR deployment, logs, hot-deploy.

## Tomcat Skills
- #2 Deployment (manual WAR, auto-deploy, reloadable)
- #6 Log analysis (catalina.out, localhost logs)

## Tomcat Instance
Port: 8090
Instance: /opt/tomcat-cbs/
Shutdown Port: 8104

### server.xml — HTTP Connector for CBS
File: /opt/tomcat-cbs/conf/server.xml
```xml
<!-- Change shutdown port -->
<Server port="8104" shutdown="SHUTDOWN">

<!-- HTTP Connector -->
<Connector port="8090"
           protocol="HTTP/1.1"
           connectionTimeout="20000"
           redirectPort="8443"/>
```

## REST Endpoints
| Method | Endpoint | Description | Role |
|--------|----------|-------------|------|
| POST | /cbs/customers | Create customer | ADMIN |
| POST | /cbs/accounts | Open account | ADMIN |
| POST | /cbs/accounts/{acct}/freeze | Freeze account | ADMIN |
| POST | /cbs/accounts/{acct}/unfreeze | Unfreeze account | ADMIN |
| GET | /cbs/accounts/{acct} | Get balance | AUTH |
| GET | /cbs/accounts/{acct}/transactions | Mini statement | AUTH |
| POST | /cbs/beneficiaries | Add beneficiary | AUTH |
| POST | /cbs/transfer/internal | Own account transfer | AUTH |
| POST | /cbs/transfer/external | To another customer | AUTH |

## API Response Format (Real Bank Style)
```json
{
  "status": "SUCCESS",
  "code": "CBS-200",
  "message": "Transfer completed",
  "data": { }
}
```

## Error Codes
| Code | Meaning |
|------|---------|
| CBS-4001 | Insufficient balance |
| CBS-4002 | Account is frozen |
| CBS-4003 | Beneficiary not registered |
| CBS-4004 | Daily transfer limit exceeded |
| CBS-4005 | Invalid account number |
| CBS-5001 | Internal server error |

## Maven Build (pom.xml)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.digistack</groupId>
  <artifactId>cbs</artifactId>
  <version>1.0</version>
  <packaging>war</packaging>

  <dependencies>
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>4.0.1</version>
      <scope>provided</scope>
    </dependency>
    <!-- ojdbc8.jar — copy manually to WEB-INF/lib or Tomcat lib -->
  </dependencies>

  <build>
    <finalName>cbs</finalName>
  </build>
</project>
```

```bash
# Build WAR
mvn clean package
# Output: target/cbs.war

# Deploy
cp target/cbs.war /opt/tomcat-cbs/webapps/

# Watch deploy
tail -f /opt/tomcat-cbs/logs/catalina.out
```

## WAR Structure
```
cbs.war
├── WEB-INF/
│ ├── web.xml
│ └── classes/
│ └── com/digistack/cbs/
│ ├── CustomerServlet.java
│ ├── AccountServlet.java
│ └── TransferServlet.java
└── index.jsp
```

## web.xml (WEB-INF/web.xml)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
         http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

  <servlet>
    <servlet-name>CustomerServlet</servlet-name>
    <servlet-class>com.digistack.cbs.CustomerServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>CustomerServlet</servlet-name>
    <url-pattern>/customers/*</url-pattern>
  </servlet-mapping>

  <servlet>
    <servlet-name>AccountServlet</servlet-name>
    <servlet-class>com.digistack.cbs.AccountServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>AccountServlet</servlet-name>
    <url-pattern>/accounts/*</url-pattern>
  </servlet-mapping>

  <servlet>
    <servlet-name>TransferServlet</servlet-name>
    <servlet-class>com.digistack.cbs.TransferServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>TransferServlet</servlet-name>
    <url-pattern>/transfer/*</url-pattern>
  </servlet-mapping>

</web-app>
```

## Tomcat Deployment Exercises
1. Manual deploy: cp cbs.war /opt/tomcat-cbs/webapps/
2. Watch: tail -f /opt/tomcat-cbs/logs/catalina.out
3. Auto-deploy: copy WAR while Tomcat running
4. Break it: corrupt web.xml → read localhost.YYYY-MM-DD.log
5. reloadable="true" vs "false" in context.xml

## Verification
```bash
# Health check
curl http://localhost:8090/cbs/accounts/DSB-SAV-00001

# Expected
{"status":"SUCCESS","code":"CBS-200","data":{"acct_no":"DSB-SAV-00001","balance":50000.00}}
```

## Phase-End Interview Questions
1. What is the difference between auto-deploy and hot-deploy in Tomcat?
2. Where do you look first when a WAR fails to deploy — `catalina.out` or `localhost.log`?
3. What does `reloadable="true"` do in context.xml and why is it dangerous in production?
4. What files does Tomcat create in the `work/` directory when CBS WAR deploys?
5. After copying `cbs.war` to webapps/, what log line in `catalina.out` confirms successful deployment?

## Status
- [ ] Not started
- [ ] In progress
- [ ] Completed

## Notes
(paste your deployment errors or curl output here)