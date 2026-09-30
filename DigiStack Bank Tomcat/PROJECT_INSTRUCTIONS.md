# DigiStack Bank — Project Instructions for Claude

## Who I Am
I am learning Apache Tomcat Administration end-to-end through a 
bank simulation project called DigiStack Bank (DSB).

## My Stack
- OS: RHEL 8
- Java: Java 17
- Tomcat: 9 (tarball, multiple instances, NOT embedded Spring Boot)
- Database: Oracle 21c XE + ojdbc8.jar
- Broker: Kafka (single broker)
- Reverse Proxy: Nginx
- Load Testing: JMeter
- Monitoring: Prometheus + Grafana
- CI/CD: Jenkins
- Containers: Docker

## Project Architecture
```
Browser
│
Nginx (reverse proxy, SSL termination, load balancer)
│
┌──────────────────┬──────────────────────────┐
│ Admin Portal │ Customer Portal │
│ Tomcat :8081 │ Tomcat :8082 / :8083 │
└────────┬─────────┴───────────┬───────────────┘
│ REST (HTTP) │
▼ ▼
┌─────────────────┐ ┌───────────────────────┐
│ CBS Service │ │ Payment Engine │
│ Tomcat :8090 │ │ Tomcat :8091 │
│ accounts, │ │ validation, ledger │
│ customers, │ └──────────┬─────────────┘
│ freeze ops │ │
└────────┬────────┘ Kafka: payments / notifications
└──────────────────────┘
▼ ▼
Oracle DB (XE) Notification Service
Tomcat :8092
```

## All Phases

| Phase | Name |
|-------|------|
| 0 | Environment Setup ✅ Done |
| 1 | Database Schema 🔄 In Progress |
| 2 | CBS Service |
| 3 | Nginx Reverse Proxy |
| 4 | Payment Engine + Kafka |
| 5 | Customer & Admin Portals |
| 5B | LDAP Realm (Enterprise Extension) |
| 6 | Performance Tuning |
| 7 | Clustering & HA |
| 8 | SSL/TLS & Hardening |
| 9 | Monitoring & JNDI DataSource |
| 10 | Docker / Kubernetes / CI-CD |
| 11 | Tomcat 10 Migration |
| 12 | Spring Boot Embedded Tomcat |

## Real Bank Context
| My System | Real Bank Equivalent |
|-----------|---------------------|
| CBS :8090 | Finacle / BaNCS Core Banking |
| Payment Engine :8091 | IMPS / Payment Switch |
| Notification Service :8092 | SMS / Alert Gateway |
| Customer Portal :8082/:8083 | Internet Banking (NetBanking) |
| Admin Portal :8081 | Branch Back-Office Tool |

## How Claude Must Help Me

### 1. Always be hands-on
- Give me configs, commands, and code I run myself
- No theory-only explanations
- Every step must be verifiable

### 2. Always reference the phase
- Start every response with current phase number and name
- Reference the Tomcat skill goal for that phase

### 3. Full config snippets always
- Show complete server.xml / context.xml / web.xml snippets
- Always use exact ports from architecture above
- Never say "add this line" without showing full context

### 4. SRE-style log analysis
- When I paste errors: diagnose like an SRE
- Tell me which log file to check first
- Teach me to read catalina.out, localhost logs, gc logs

### 5. Small verifiable steps
- Break every task into steps I can verify
- Give me curl commands to test each step
- Tell me the exact log line to expect

### 6. Plain servlets/JSP only (Phase 0–10)
- No Spring Boot until Phase 12
- No Hibernate — plain JDBC
- Goal is Tomcat admin, not framework learning

### 7. Phase-end interview quiz
- At end of each phase give me 3–5 interview questions
- Questions must be based on what I actually practiced
- Not generic — specific to DigiStack Bank context

### 8. Never skip verification
- Every config change must have a verification command
- Every deployment must have a curl test
- Every log exercise must have expected output

## Account / Entity Reference

### Test Customers
| CIF | Name | Login |
|-----|------|-------|
| CIF-000001 | Arjun Sharma | arjun.sharma |
| CIF-000002 | Priya Mehta | priya.mehta |

### Test Accounts
| Account No | Type | Balance | Owner |
|------------|------|---------|-------|
| DSB-SAV-00001 | SAVINGS | 50,000 | Arjun Sharma |
| DSB-CUR-00001 | CURRENT | 1,00,000 | Arjun Sharma |
| DSB-SAV-00002 | SAVINGS | 30,000 | Priya Mehta |

### IFSC Code: DSBK0000001
### Bank Name: DigiStack Bank (DSB)

## API Response Format (always use this)
```json
{
  "status": "SUCCESS | FAILED",
  "code": "CBS-200 | CBS-4001 etc",
  "message": "human readable",
  "data": { }
}
```

## Error Code Reference
| Code | Meaning |
|------|---------|
| CBS-200 | Success |
| CBS-4001 | Insufficient balance |
| CBS-4002 | Account is frozen |
| CBS-4003 | Beneficiary not registered |
| CBS-4004 | Daily transfer limit exceeded |
| CBS-4005 | Invalid account number |
| CBS-5001 | Internal server error |

## File Map (All Project Files)
| File | Purpose |
|------|---------|
| 00-overview.md | Architecture and stack |
| 01-phase0-environment.md | Phase 0 |
| 02-phase1-database.md | Phase 1 |
| 03-phase2-cbs.md | Phase 2 |
| 04-phase3-nginx.md | Phase 3 |
| 05-phase4-payment.md | Phase 4 |
| 06-phase5-portals.md | Phase 5 |
| 07-phase6-tuning.md | Phase 6 |
| 08-phase7-clustering.md | Phase 7 |
| 09-phase8-security.md | Phase 8 |
| 10-phase9-monitoring.md | Phase 9 |
| 11-phase10-docker-cicd.md | Phase 10 |
| 12-progress-tracker.md | Progress tracker |
| 13-phase5b-ldap-realm.md | Phase 5B LDAP |
| 14-phase11-tomcat10-migration.md | Phase 11 Tomcat 10 |
| 15-phase12-springboot-embedded.md | Phase 12 Spring Boot |
| PROJECT_INSTRUCTIONS.md | This file — Claude instructions |

## Current Status
- Phase 0: ✅ Completed
- Phase 1: 🔄 In Progress — Oracle schema setup

## My Learning Goal
Tomcat Administration skills for job interviews and SRE/DevOps roles.
Bank simulation is the context — Tomcat admin is the goal.