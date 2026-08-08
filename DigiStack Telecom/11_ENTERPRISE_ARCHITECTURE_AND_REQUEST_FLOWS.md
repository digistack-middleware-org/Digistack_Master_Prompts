# 11 — Enterprise Architecture & Request Flows
## DigiStack Telecom Enterprise — Primary Reference

This is your go-to reference during WebSphere administration and troubleshooting work (Phase 3, 5, 6). Keep it open alongside whatever sprint you're on.

---

## 1. VM Topology (5 VMs)

| VM | Role | vCPU | RAM | Disk |
|---|---|---|---|---|
| VM1 | DMGR + Node1 (combined) | 2 | 3.5 GB | 40 GB |
| VM2 | Node2 | 2 | 3 GB | 40 GB |
| VM3 | IBM HTTP Server (IHS) | 1 | 0.75 GB | 20 GB |
| VM4 | PostgreSQL 16.x | 1 | 1.5 GB | 30 GB |
| VM5 | IBM MQ | 1 | 1.25 GB | 20 GB |

**Known SPOF:** VM1 carries the DMGR *plus* one member from every cluster. Losing VM1 = losing the DMGR + one member of all 3 clusters simultaneously. This is deliberately not fixed until the Phase 6 DR/SPOF-audit sprint — it's realistic and worth diagnosing, not an oversight.

---

## 2. Cluster Shape (3 clusters, each spans both nodes)

```
                    ┌─────────────── Cell: DigistackCell01 ─────────────┐
                    │                                                    │
   VM1: Node1 ──────┼── Customer Cluster member ── Business Cluster member ── Integration Cluster member
                    │                                                    │
   VM2: Node2 ──────┼── Customer Cluster member ── Business Cluster member ── Integration Cluster member
                    │                                                    │
                    └────────────────────────────────────────────────────┘
```

| Cluster | Apps |
|---|---|
| **Customer Cluster** | Authentication Service, SelfCare Portal, CRM, SIM Service |
| **Business Cluster** | Order Service, Provisioning Service, Billing Service, Payment Service |
| **Integration Cluster** | Notification Service, CDR Service, Admin Portal |

---

## 3. Application Landscape (11 apps)

| App | Cluster | Primary WAS Admin Focus | DB Schema |
|---|---|---|---|
| Authentication Service | Customer | LTPA/SSO, JAAS | `auth` |
| SelfCare Portal | Customer | Session mgmt, IHS/plugin routing | `selfcare` |
| CRM | Customer | Security roles, JDBC | `crm` |
| SIM Service | Customer | JDBC transactions, connection pool | `sim` |
| Order Service | Business | JMS producer → `ORDER.QUEUE` | `ordermgmt` |
| Provisioning Service | Business | MDB, consumes `ORDER.QUEUE` → produces `BILLING.QUEUE` | `provisioning` |
| Billing Service | Business | XA/2PC, consumes `BILLING.QUEUE` → produces `PAYMENT.QUEUE` | `billing` |
| Payment Service | Business | XA across DB+MQ, consumes `PAYMENT.QUEUE` → produces `NOTIFY.QUEUE` | `payment` |
| Notification Service | Integration | Fan-out async, consumes `NOTIFY.QUEUE` | `notification` |
| CDR Service | Integration | Batch/high-volume MDB, `CDR.QUEUE`, DLQ | `cdr` |
| Admin Portal | Integration | PMI, health checks | `adminconsole` |

---

## 4. Request Flows

### 4.1 Customer Login
```
Browser → IHS (VM3) → plugin-cfg.xml routes to → Customer Cluster
        → Authentication Service (LTPA token issued, SSO established)
        → SelfCare Portal (session created, JSESSIONID assigned)
```

### 4.2 SIM Activation (standalone flow)
```
SelfCare Portal → SIM Service
                 → JDBC transaction: UPDATE sim SET status='ACTIVE'
                 → INSERT transaction record
                 → (optional) publish event → downstream consumers
```

### 4.3 New Connection — Full End-to-End Flow (the main teaching flow)
```
1. Customer Login
   Browser → IHS → Customer Cluster → Authentication Service (SSO)

2. Request New SIM
   SelfCare Portal → Order Service

3. Order Service
   Creates order record → publishes to ORDER.QUEUE (JMS producer)

4. IBM MQ: ORDER.QUEUE
   ↓
5. Provisioning Service (MDB, consumes ORDER.QUEUE)
   Activates network-side service → publishes to BILLING.QUEUE

6. IBM MQ: BILLING.QUEUE
   ↓
7. Billing Service (MDB, consumes BILLING.QUEUE)
   Generates invoice (XA transaction, 2PC) → publishes to PAYMENT.QUEUE

8. IBM MQ: PAYMENT.QUEUE
   ↓
9. Payment Service (MDB, consumes PAYMENT.QUEUE)
   Processes payment (XA across DB+MQ) → publishes to NOTIFY.QUEUE

10. IBM MQ: NOTIFY.QUEUE
    ↓
11. Notification Service (MDB, consumes NOTIFY.QUEUE)
    Sends simulated SMS/email: "SIM Activated"
```

### 4.4 CDR Mediation (independent, always-on, not part of the order flow)
```
CDR.QUEUE → CDR Service (high-volume MDB) → rating logic → cdr schema
          → Dead Letter Queue (DLQ) if a message repeatedly fails processing
```

---

## 5. MQ Queue Chain (reference)

```
ORDER.QUEUE → BILLING.QUEUE → PAYMENT.QUEUE → NOTIFY.QUEUE
                                                    (independent) CDR.QUEUE
```
Each queue has its own connection factory + activation spec. A shared Dead Letter Queue pattern applies to all of them.

---

## 6. Deployment / Traffic Path

```
Customer Browser
      │
      ▼
IBM HTTP Server (VM3)  ── serves static content, SSL termination
      │
      ▼  (plugin-cfg.xml routes based on context-root)
WebSphere Cluster (VM1 Node1 + VM2 Node2)
      │
      ├──► PostgreSQL (VM4) — JDBC, per-app schema, connection pooled
      └──► IBM MQ (VM5) — JMS, per-queue connection factory
```

---

## 7. How to use this doc during troubleshooting
When a Phase 5 (Production Support) incident scenario shows up, start here:
1. Which app is affected? → check Section 3 for its cluster and DB schema
2. Which cluster/VM is involved? → check Section 1/2 for topology and the known SPOF
3. Is it mid-flow? → check Section 4 to see what should have happened next in the chain
4. Is a queue involved? → check Section 5 for the expected producer/consumer pairing

This doc doesn't change as you build — update it only if the architecture itself changes (new ADR).
