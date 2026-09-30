# Phase 5B — LDAP Realm (Enterprise Extension)

## Goal
Replace JDBCRealm with LDAPRealm. Simulate enterprise bank authentication
where users are managed in Active Directory / OpenLDAP — not the app DB.

## Real Bank Context
In real banks (HDFC, ICICI, SBI IT):
- Branch staff login → authenticated against Active Directory
- Customer login → authenticated against Core Banking DB
- This phase covers the STAFF/ADMIN side (Active Directory pattern)

## Tomcat Skill
- #5 JNDIRealm (LDAP)
- #5 Role mapping from LDAP groups → Tomcat roles

## What You Will Set Up
```
Admin Portal :8081
│
▼
Tomcat JNDIRealm
│
▼
OpenLDAP (running locally, simulates Active Directory)
│
├── ou=users
│ ├── uid=branch.manager (role: BRANCH_MANAGER)
│ └── uid=teller.one (role: TELLER)
└── ou=groups
├── cn=BRANCH_MANAGERS
└── cn=TELLERS
```

## Step 1 — Install OpenLDAP on RHEL 8
```bash
# Install
sudo dnf install -y openldap openldap-servers openldap-clients

# Start
sudo systemctl enable --now slapd

# Verify
ldapsearch -x -H ldap://localhost -b "" -s base
```

## Step 2 — Base LDAP Structure (digistack.ldif)
```ldif
# Base
dn: dc=digistack,dc=local
objectClass: top
objectClass: dcObject
objectClass: organization
o: DigiStack Bank
dc: digistack

# Users OU
dn: ou=users,dc=digistack,dc=local
objectClass: organizationalUnit
ou: users

# Groups OU
dn: ou=groups,dc=digistack,dc=local
objectClass: organizationalUnit
ou: groups

# Branch Manager User
dn: uid=branch.manager,ou=users,dc=digistack,dc=local
objectClass: inetOrgPerson
uid: branch.manager
cn: Branch Manager
sn: Manager
mail: bm@digistack.in
userPassword: manager123

# Teller User
dn: uid=teller.one,ou=users,dc=digistack,dc=local
objectClass: inetOrgPerson
uid: teller.one
cn: Teller One
sn: One
mail: teller@digistack.in
userPassword: teller123

# Group: BRANCH_MANAGERS
dn: cn=BRANCH_MANAGERS,ou=groups,dc=digistack,dc=local
objectClass: groupOfNames
cn: BRANCH_MANAGERS
member: uid=branch.manager,ou=users,dc=digistack,dc=local

# Group: TELLERS
dn: cn=TELLERS,ou=groups,dc=digistack,dc=local
objectClass: groupOfNames
cn: TELLERS
member: uid=teller.one,ou=users,dc=digistack,dc=local
```

```bash
# Load into LDAP
ldapadd -x -H ldap://localhost \
  -D "cn=admin,dc=digistack,dc=local" \
  -w adminpassword \
  -f digistack.ldif
```

## Step 3 — Verify LDAP Users
```bash
# Search all users
ldapsearch -x -H ldap://localhost \
  -b "ou=users,dc=digistack,dc=local" \
  "(objectClass=inetOrgPerson)" uid cn mail

# Test bind (login test)
ldapsearch -x -H ldap://localhost \
  -D "uid=branch.manager,ou=users,dc=digistack,dc=local" \
  -w manager123 \
  -b "dc=digistack,dc=local" "(uid=branch.manager)"

# Expected: returns the user entry — bind successful
```

## Step 4 — JNDIRealm in server.xml (Admin Portal Tomcat)
File: /opt/tomcat-admin/conf/server.xml

```xml
<Realm className="org.apache.catalina.realm.JNDIRealm"
       connectionURL="ldap://localhost:389"
       userPattern="uid={0},ou=users,dc=digistack,dc=local"
       roleBase="ou=groups,dc=digistack,dc=local"
       roleName="cn"
       roleSearch="(member={0})"
       roleSubtree="false"/>
```

## Step 5 — Security Constraint (web.xml — Admin Portal)
```xml
<!-- BRANCH_MANAGER: full access -->
<security-constraint>
  <web-resource-collection>
    <web-resource-name>Admin Full</web-resource-name>
    <url-pattern>/admin/*</url-pattern>
  </web-resource-collection>
  <auth-constraint>
    <role-name>BRANCH_MANAGERS</role-name>
  </auth-constraint>
</security-constraint>

<!-- TELLER: read-only view -->
<security-constraint>
  <web-resource-collection>
    <web-resource-name>Teller View</web-resource-name>
    <url-pattern>/admin/view/*</url-pattern>
  </web-resource-collection>
  <auth-constraint>
    <role-name>TELLERS</role-name>
  </auth-constraint>
</security-constraint>

<login-config>
  <auth-method>FORM</auth-method>
  <form-login-config>
    <form-login-page>/login.jsp</form-login-page>
    <form-error-page>/login-error.jsp</form-error-page>
  </form-login-config>
</login-config>

<security-role>
  <role-name>BRANCH_MANAGERS</role-name>
</security-role>
<security-role>
  <role-name>TELLERS</role-name>
</security-role>
```

## Step 6 — Test Role-Based Access
```bash
# Login as branch.manager → can freeze accounts
curl -c bm.txt -b bm.txt \
  -d "username=branch.manager&password=manager123" \
  http://localhost:8081/admin/j_security_check

curl -b bm.txt \
  -X POST http://localhost:8081/admin/accounts/DSB-SAV-00001/freeze
# Expected: 200 OK

# Login as teller.one → cannot freeze (403)
curl -c t.txt -b t.txt \
  -d "username=teller.one&password=teller123" \
  http://localhost:8081/admin/j_security_check

curl -b t.txt \
  -X POST http://localhost:8081/admin/accounts/DSB-SAV-00001/freeze
# Expected: 403 Forbidden
```

## Realm Progression Summary
```
Phase 5: MemoryRealm (tomcat-users.xml)
↓
JDBCRealm (CUSTOMERS table)
↓
Phase 5B: JNDIRealm (OpenLDAP / Active Directory)
```


## Phase-End Interview Questions
1. What is `JNDIRealm` and when would you use it over `JDBCRealm` — which does DigiStack Admin Portal use for staff, and which for customers?
2. What is the difference between `userPattern` and `userSearch` in JNDIRealm — which does DigiStack Phase 5B use and why?
3. In DigiStack LDAP, `branch.manager` is a member of `cn=BRANCH_MANAGERS` — exactly how does Tomcat map this `cn` to the `BRANCH_MANAGERS` role?
4. The OpenLDAP server goes down during business hours — can `branch.manager` still login to the Admin Portal? Why or why not?
5. In DigiStack, which `conf/server.xml` file gets the `JNDIRealm` config — `tomcat-admin` or `tomcat-customer1`?

## Status
- [ ] Not started
- [ ] In progress
- [ ] Completed

## Notes
(paste your LDAP errors or ldapsearch output here)