# 01_Architecture

**Status:** ⏳ Scaffolded — not yet populated
**Type:** Cross-cutting reference folder (like 00_Core) — no Part number, not phase-sequenced.

---

## Purpose

Diagram-based companion to ARCH01 (Enterprise Architecture) and ARCH02
(Solution Architecture) in `00_Core/`. Those two files remain the
**text/prose source of truth** — nothing here overrides them. This
folder exists so you have visual artifacts ready for interview questions
("draw me the cluster," "show me the request flow") without re-deriving
them from prose on the spot.

**Enterprise Architecture itself is intentionally NOT duplicated here** —
see ARCH01 directly for that.

## Fill-In Discipline

Every file below is a scaffold only: a title, a short "what this diagram
will show," and a status line. Per this project's SDD01 ("write it as
you go, not after") and ARCH01's Guiding Filter, a diagram is only
populated **after** the Part/Version that actually builds the thing it
depicts — never drawn ahead of the build as a guess. Each file's own
"Populate At" field tells you exactly which Version triggers it.

## Diagram Index

| File | Depicts | Populate At |
|---|---|---|
| `01_Network_Diagram.md` | Subnet/VLAN layout, IP scheme, firewall zones | P01 v1 (basic), extended at P01 v8 (IHS), P01 v11/v12 (SSL) |
| `02_VM_Layout.md` | Which VMs exist, roles, power state | P01 v1 (dsb-dmgr), extended every version that powers on a new VM |
| `03_Request_Flows.md` | End-to-end request path (browser → IHS → plugin → AppServer → DB) | P01 v1 (basic path), extended at v7 (JNDI), v8 (IHS), v12 (mTLS) |
| `04_Cluster_Architecture.md` | DMgr/Node/Cluster/Member topology | P01 v5 (cluster stood up), deepened at v6 |
| `05_MQ_Architecture.md` | Queue Manager, channels, queues, DLQ | P02 v19 |
| `06_Database_ER_Diagram.md` | Table relationships (ER diagram) | Starts at P01 v1 (`app_config`), grows every version that adds a table (v2 `users`, v3 `accounts`, v6 `is_frozen`, ...) |
| `07_Security_Architecture.md` | Roles, groups, SSL/mTLS hops, LDAP/LTPA | P01 v10 (roles), v11/v12 (SSL/mTLS) |
| `08_Deployment_Architecture.md` | EAR/WAR layout, deployment targets | P01 v1 (first EAR), extended each version that changes packaging |
| `09_DR_Architecture.md` | DR site pairing, RPO/RTO, failover paths | P05 (HA/DR phase) |

## Update Rule

When a diagram file is populated, update this table's status implicitly
by leaving the "Populate At" column as history — add a **Populated:
Yes (as of vN, date)** line inside that specific file, same pattern STD
uses for its Version Pins ("target" → "CONFIRMED").

---

*This is the 01_Architecture index. Companion standards: ARCH01/ARCH02
(00_Core) — text source of truth this folder visualizes.*