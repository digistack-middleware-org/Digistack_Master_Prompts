ID: ARCHVIZ01
Version: 0.1 (scaffold)
Status: Not Populated

Title: Network Diagram

Imports:
STD (§Ports, §IP/VLAN Addressing)
SOE01 (§8 Firewall)

Populate At: P01 v1 (basic subnet/IP scheme), extended at P01 v8 (IHS
web-tier), P01 v11/v12 (SSL/mTLS hops)

---

## What this will show
Subnet/VLAN layout (DMZ/App/Data zones per STD), IP addressing per host,
firewall allow-rules per SOE01 §8, as a Mermaid or ASCII network diagram.

## Diagram (as of P01 v1)

Domain: digistack.cloud | Subnet: 192.168.10.0/24 | Default-deny, explicit-allow.

```
                         Internet / Browser
                                 │
                          (not yet fronted by IHS — v8)
                                 │
        ┌────────────────────────────────────────────────┐
        │  DMZ  (VLAN10)                                  │
        │   dsb-ihs      .10.20   1vCPU/1GB   [NOT BUILT] │
        └────────────────────────────────────────────────┘
                                 │  80/443 (planned, v8+)
        ┌────────────────────────────────────────────────┐
        │  App  (VLAN20)                                  │
        │   dsb-dmgr (+Node1)  .10.10  2vCPU/3GB  [NOT BUILT — RESET 2026-08-25]  │
        │   dsb-node02         .10.11  2vCPU/2GB  [NOT BUILT — v5] │
        │   dsb-tomcat         .10.12  1vCPU/1GB  [NOT BUILT — P03 v26/v27]       │
        │              Hosts: Mobile Banking (mobile.digistack.cloud:8080)         │
        │                     ATM Simulator (atm.digistack.cloud:8080)            │
        └────────────────────────────────────────────────┘
                                 │  9080/9443 (direct to AppServer, no plugin yet)
        ┌────────────────────────────────────────────────┐
        │  Data  (VLAN30)                                 │
        │   dsb-db     .10.30  2vCPU/2GB  [NOT BUILT — RESET 2026-08-25]          │
        │              PostgreSQL:5432 (P01 v1–v22 only)                           │
        │              Decommissioned (VM deleted) at P03 v23 Sprint 4             │
        │   dsb-oracle .10.32  2vCPU/4GB  [NOT BUILT — P02 v22.5]                │
        │              Oracle 21c XE:1521 / DIGISTACK_CBS PDB (v22.5 onward)      │
        │              NEVER shares host with dsb-db                               │
        │   dsb-mq     .10.31  1vCPU/1.5GB [NOT BUILT — P02 v19]                 │
        └────────────────────────────────────────────────┘
                                 │
        ┌────────────────────────────────────────────────┐ │
        │  Mgmt  (separate)                                │
        │   dsb-monitor .10.40  [NOT BUILT — P04 v31]      │
        │   dsb-elk     .10.41  [NOT BUILT — P04 v32]      │
        │   dsb-tracing .10.42  [NOT BUILT — P04 v33,      │
        │    co-locate on dsb-monitor if RAM permits]      │
        └────────────────────────────────────────────────┘ │
```

Live firewall-relevant ports at v1 (SOE01 §8, default-deny elsewhere):
- 22 SSH — inbound, admin subnet only
- 9060/9043 WAS Admin Console — inbound, admin subnet only
- 9080/9443 WAS App traffic — inbound (direct browser→AppServer today; IHS-only restriction takes effect at v8)
- 5432 PostgreSQL — inbound, app-tier subnet only (P01 v1 through
  v22; closed at P03 v23 Sprint 4)
- 1521 Oracle 21c XE — inbound, app-tier subnet only (v22.5 onward)

Note: browser hits dsb-dmgr's AppServer port directly right now (9080)
since IHS/reverse-proxy doesn't exist until v8 — the "from LB/IHS tier
only" restriction on 9080/9443 is a target-state rule, not yet enforced.

Ports added at later versions (per STD port matrix — add to this diagram
at the version that builds them):
- 80/443   IHS HTTP/HTTPS — v4.5 (standalone IHS era), extended v8 (cluster era)
- 8080     Tomcat HTTP — P03 v26/v27 (Mobile/ATM on dsb-tomcat)
- 1521     Oracle 21c XE — P02 v22.5 (dsb-oracle)
- 1414     IBM MQ Listener — P02 v19 (dsb-mq)
- 9090     Prometheus — P04 v31 (dsb-monitor)
- 3000     Grafana — P04 v31 (dsb-monitor)
- 9200     OpenSearch — P04 v32 (dsb-elk)
- 5601     OpenSearch Dashboards — P04 v32 (dsb-elk)

## Status
**Reset — 2026-08-25 (full project reset #2).** Previously marked
Populated as of P01 v1 (same date, earlier revision) with dsb-dmgr and
dsb-db shown live. The physical lab VM was lost the same day this
diagram was drawn — nothing is actually built. All hosts below now
correctly read [NOT BUILT], including dsb-dmgr/dsb-db, until the
physical rebuild reaches P01 v1 again. Re-populate once SetupDoc-v1.md
exists for real.