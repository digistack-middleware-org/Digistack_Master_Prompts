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
        │   dsb-tomcat         .10.12  1vCPU/1GB  [NOT BUILT — P03] │
        └────────────────────────────────────────────────┘
                                 │  9080/9443 (direct to AppServer, no plugin yet)
        ┌────────────────────────────────────────────────┐
        │  Data  (VLAN30)                                 │
        │   dsb-db     .10.30  2vCPU/2GB  [NOT BUILT — RESET 2026-08-25]  :5432   │
        │   dsb-mq     .10.31  1vCPU/1.5GB [NOT BUILT — P02 v19] │
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
- 5432 PostgreSQL — inbound, app-tier subnet only (dsb-dmgr → dsb-db)

Note: browser hits dsb-dmgr's AppServer port directly right now (9080)
since IHS/reverse-proxy doesn't exist until v8 — the "from LB/IHS tier
only" restriction on 9080/9443 is a target-state rule, not yet enforced.

## Status
**Reset — 2026-08-25 (full project reset #2).** Previously marked
Populated as of P01 v1 (same date, earlier revision) with dsb-dmgr and
dsb-db shown live. The physical lab VM was lost the same day this
diagram was drawn — nothing is actually built. All hosts below now
correctly read [NOT BUILT], including dsb-dmgr/dsb-db, until the
physical rebuild reaches P01 v1 again. Re-populate once SetupDoc-v1.md
exists for real.