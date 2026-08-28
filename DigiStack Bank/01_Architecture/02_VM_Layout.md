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
| dsb-db | PostgreSQL 16 (P01–v22 only) | 2 | 4 GB | 40 GB (thin) | **Off** — lost in 2026-08-25 reset #2, not yet rebuilt. Powers on at P01 v1; decommissioned (final pg_dump, snapshot, VM deleted) at P03 v23 Sprint 4. Oracle 21c XE is NEVER installed here — Oracle runs on dsb-oracle only. |
| dsb-node02 | 2nd cluster member | 2 | 2 GB | 40 GB | Off — powers on at P01 v5 |
| dsb-ihs | IBM HTTP Server | 1 | 1 GB | 20 GB | Off — powers on at P01 v8 |
| dsb-mq | IBM MQ | 1 | 1.5 GB | 20 GB | Off — powers on at P02 v19 |
| dsb-monitor | Prometheus/Grafana | 1 | 1.5 GB | 30 GB | Off — powers on at P04 v31 |
| dsb-elk | OpenSearch stack | 1 | 1.5 GB | 40 GB | Off — powers on at P04 v32 |
| dsb-tomcat  | Mobile/ATM host (mobile.digistack.cloud + atm.digistack.cloud) | 1 | 1 GB   | 20 GB | Off — powers on at P03 v26 (Mobile Banking) / v27 (ATM Simulator) |
| dsb-oracle  | Oracle 21c XE (DIGISTACK_CBS PDB) | 2 | 4 GB | 60 GB | Off — powers on at P02 v22.5 (dedicated Oracle VM, never shared with dsb-db) |
| dsb-tracing | Jaeger tracing backend  | 1 | 1 GB   | 10 GB | Off — powers on at P04 v33; co-locate on dsb-monitor if RAM permits per SOE01 §1a |

Currently running: 0 VMs. When rebuilt: 2 VMs, 4 vCPU / 5 GB RAM
(dsb-dmgr 3 GB + dsb-db 2 GB) — within the 4-5 concurrent-VM
budget (SOE01 §1a). Note: Oracle 21c XE is NOT installed on dsb-db.
Oracle runs on its own dedicated VM dsb-oracle (4 GB RAM / 2 vCPU /
60 GB disk, Oracle Linux 8), provisioned at P02 v22.5. dsb-db retains
its original 2 GB sizing throughout its life and is permanently
decommissioned at P03 v23 Sprint 4. No cluster exists yet (that's a
DMgr+federated-node construct, v5), so dsb-dmgr today is a standalone
AppServer profile, not yet a cell member.

## Status
**Reset — 2026-08-25 (full project reset #2).** Both VMs were briefly
shown live earlier the same day this diagram was drawn, then lost when
the physical lab was reset. Table corrected to Off/not-rebuilt for
both. Currently running: 0 VMs. Re-populate as VMs are actually
recreated, starting with dsb-dmgr at P01 v1 Sprint 1.