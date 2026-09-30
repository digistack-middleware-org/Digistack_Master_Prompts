# DigiStack Bank — Tomcat Admin Project Overview

## Bank Name: DigiStack Bank (DSB)

## What This Simulates
| System | Real Bank Equivalent | Tomcat Port |
|--------|---------------------|-------------|
| CBS Service | Finacle / BaNCS Core Banking | :8090 |
| Payment Engine | Payment Switch / IMPS Engine | :8091 |
| Notification Service | Alert & SMS Gateway | :8092 |
| Customer Portal | Internet Banking (NetBanking) | :8082 / :8083 |
| Admin Portal | Branch Back-Office Tool | :8081 |

## Stack
- OS: RHEL 8
- Java: 17
- Tomcat: 9 (tarball, multiple instances, NOT embedded)
- Database: Oracle 21c XE + ojdbc8.jar
- Message Broker: Kafka (single broker)
- Reverse Proxy: Nginx
- Load Testing: JMeter
- Monitoring: Prometheus + Grafana
- CI/CD: Jenkins
- Containers: Docker

## Architecture
```
Browser
│
Nginx (reverse proxy, SSL termination, load balancer)
│
┌──────────────────┬─────────────────────────┐
│ Admin Portal │ Customer Portal │
│ Tomcat :8081 │ Tomcat :8082 / :8083 │
└────────┬─────────┴──────────┬──────────────┘
│ REST (HTTP) │
▼ ▼
┌─────────────────┐ ┌──────────────────────┐
│ CBS Service │ │ Payment Engine │
│ Tomcat :8090 │ │ Tomcat :8091 │
│ accounts, │ │ validation, ledger │
│ customers, │ └──────────┬────────────┘
│ freeze ops │ │
└────────┬────────┘ │ Kafka: payments / notifications
└──────────┬────────────┘
▼ ▼
Oracle DB (XE) Notification Service
Tomcat :8092
```

## Priority
Tomcat Administration skills — bank is the context, not the goal.