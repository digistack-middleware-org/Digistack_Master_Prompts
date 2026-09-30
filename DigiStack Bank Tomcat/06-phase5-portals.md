# Phase 5 — Customer & Admin Portals

## Goal
Build both portals. Master Tomcat security realms, auth, session management.

## Tomcat Skills
- #5 Realms (MemoryRealm → JDBCRealm)
- #5 Security constraints (web.xml)
- #5 Session management
- #5 Security headers (Valve/Filter)

## Tomcat Instances
| Portal | Port | Shutdown Port | Instance |
|--------|------|---------------|----------|
| Admin Portal | :8081 | 8101 | /opt/tomcat-admin/ |
| Customer Portal | :8082 | 8102 | /opt/tomcat-customer1/ |
| Customer Portal (HA) | :8083 | 8103 | /opt/tomcat-customer2/ |

### server.xml changes per instance
```bash
# Admin Portal — /opt/tomcat-admin/conf/server.xml
# <Server port="8101" ...>  <Connector port="8081" ...>

# Customer Portal 1 — /opt/tomcat-customer1/conf/server.xml
# <Server port="8102" ...>  <Connector port="8082" ...>

# Customer Portal 2 — /opt/tomcat-customer2/conf/server.xml
# <Server port="8103" ...>  <Connector port="8083" ...>
```

## Admin Portal Features (Internal Bank Tool)
- Login (role: BRANCH_MANAGER / TELLER)
- Open new account
- Freeze / Unfreeze account (with reason code)
- View customer profile
- Every action → AUDIT_LOG

## Customer Portal Features (Internet Banking)
- Login + OTP simulation
- Dashboard: balance + last 10 transactions
- Fund transfer (IMPS / NEFT / RTGS)
- Add beneficiary (24hr cooling period)
- View statement

## Realm Progression

### Step 1 — MemoryRealm (tomcat-users.xml)
File: /opt/tomcat-admin/conf/tomcat-users.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<tomcat-users>
  <role rolename="BRANCH_MANAGER"/>
  <role rolename="TELLER"/>
  <user username="admin" password="admin123" roles="BRANCH_MANAGER"/>
  <user username="teller1" password="teller123" roles="TELLER"/>
</tomcat-users>
```

Add to `/opt/tomcat-admin/conf/server.xml` inside `<Engine>`:
```xml
<Realm className="org.apache.catalina.realm.UserDatabaseRealm"
       resourceName="UserDatabase"/>
```

### Step 2 — JDBCRealm (read from CUSTOMERS table)
File: /opt/tomcat-customer1/conf/server.xml — inside `<Engine>`:
```xml
<Realm className="org.apache.catalina.realm.JDBCRealm"
       driverName="oracle.jdbc.OracleDriver"
       connectionURL="jdbc:oracle:thin:@localhost:1521:XE"
       connectionName="digistack"
       connectionPassword="digistack123"
       userTable="CUSTOMERS"
       userNameCol="LOGIN"
       userCredCol="PASSWORD_HASH"
       userRoleTable="CUSTOMERS"
       roleNameCol="'CUSTOMER'"/>
```

**Note:** ojdbc8.jar must be in `/opt/tomcat-customer1/lib/`


## Security Constraint (web.xml)
```xml
<security-constraint>
  <web-resource-collection>
    <web-resource-name>Admin Area</web-resource-name>
    <url-pattern>/admin/*</url-pattern>
  </web-resource-collection>
  <auth-constraint>
    <role-name>BRANCH_MANAGER</role-name>
  </auth-constraint>
</security-constraint>

<login-config>
  <auth-method>FORM</auth-method>
  <form-login-config>
    <form-login-page>/login.jsp</form-login-page>
    <form-error-page>/login-error.jsp</form-error-page>
  </form-login-config>
</login-config>
```

## Session Config (web.xml)
```xml
<session-config>
  <session-timeout>15</session-timeout>
</session-config>
```

## Verification
```bash
# Try accessing admin without login
curl -I http://localhost:8081/admin/dashboard
# Expect: 302 redirect to login page

# After login
curl -c cookies.txt -b cookies.txt \
  -d "username=admin&password=admin123" \
  http://localhost:8081/admin/j_security_check
```

## Phase-End Interview Questions
1. What is the difference between MemoryRealm, JDBCRealm, and JNDIRealm — when do you use each?
2. How does FORM authentication work in Tomcat — what is j_security_check and who handles it?
3. In DigiStack Admin Portal, a TELLER tries to access /admin/dashboard (BRANCH_MANAGER only) — what HTTP status does Tomcat return?
4. Where do you configure session timeout in DigiStack — web.xml or context.xml — and what is the difference?
5. After a user logs in to the Customer Portal, which Tomcat log file records the authenticated username?

## Status
- [ ] Not started
- [ ] In progress
- [ ] Completed

## Notes