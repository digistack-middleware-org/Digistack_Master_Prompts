ID: ARCHVIZ02
Version: 0.1 (scaffold)
Status: Not Populated

Title: VM Layout

Imports:
SOE01 (§1a Lab Hardware Baseline)
CAP01 (§1a Lab Hardware Sizing)

Populate At: P01 v1 (dsb-dmgr), extended every version that powers on a
new VM (P01 v5 Node2, P01 v8 IHS, P02 v19 MQ, ...)

---

## What this will show
Every VM (dsb-dmgr, dsb-node02, dsb-ihs, dsb-db, dsb-mq, dsb-monitor,
dsb-elk, dsb-tomcat), its role, vCPU/RAM, and power-on schedule — visual
version of SOE01 §1a / CAP01 §1a's tables.

## VM Layout (as of P01 v1)

| VM | Role | vCPU | RAM | Disk | Power State (actual, per SOE01 §1a) |
|---|---|---|---|---|---|
| dsb-dmgr (+Node1) | Standalone AppServer, hosts digistack-bank-v1.ear | 2 | 3 GB | 40 GB (thin) | **Off** — lost in 2026-08-25 reset #2, not yet rebuilt |
| dsb-db | PostgreSQL 16 | 2 | 2 GB | 40 GB (thin) | **Off** — lost in 2026-08-25 reset #2, not yet rebuilt |
| dsb-node02 | 2nd cluster member | 2 | 2 GB | 40 GB | Off — powers on at P01 v5 |
| dsb-ihs | IBM HTTP Server | 1 | 1 GB | 20 GB | Off — powers on at P01 v8 |
| dsb-mq | IBM MQ | 1 | 1.5 GB | 20 GB | Off — powers on at P02 v19 |
| dsb-monitor | Prometheus/Grafana | 1 | 1.5 GB | 30 GB | Off — powers on at P04 v31 |
| dsb-elk | OpenSearch stack | 1 | 1.5 GB | 40 GB | Off — powers on at P04 v32 |
| dsb-tomcat  | Mobile/ATM host         | 1 | 1 GB   | 20 GB | Off — powers on at P03 v26/v27 |
| dsb-tracing | Jaeger tracing backend  | 1 | 1 GB   | 10 GB | Off — powers on at P04 v33; co-locate on dsb-monitor if RAM permits per SOE01 §1a |

Currently running: 2 VMs, 4 vCPU / 5 GB RAM — well under the 4-5
concurrent-VM budget (SOE01 §1a). No cluster exists yet (that's a
DMgr+federated-node construct, v5), so dsb-dmgr today is a standalone
AppServer profile, not yet a cell member.

## Status
**Reset — 2026-08-25 (full project reset #2).** Both VMs were briefly
shown live earlier the same day this diagram was drawn, then lost when
the physical lab was reset. Table corrected to Off/not-rebuilt for
both. Currently running: 0 VMs. Re-populate as VMs are actually
recreated, starting with dsb-dmgr at P01 v1 Sprint 1.