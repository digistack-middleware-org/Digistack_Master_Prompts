ID: CAP01
Version: 1.1
Status: Active

Title: Capacity & Sizing Standard

Imports:
IDX
STD
SOE01

Exports:
JVM heap sizing formula
Thread pool sizing guidance
JDBC pool sizing (cross-referenced from P01 v7)
MQ sizing
CPU/memory/storage sizing baseline
Log retention standard
Cluster sizing / scale-out triggers
Performance thresholds
Lab Hardware Sizing (this environment)

Used By:
P01
P04
P05
P06
P09
P09.1
P10
P10.1

Next:
(none — reference document, cited by version, not sequenced)

---

Purpose
-------
Defines the numeric sizing baseline every DigiStack Bank component is
built against — the answer to "how big" wherever SOE01 answers "what" and
"how configured." Every heap value, pool size, and scale-out trigger
referenced elsewhere in this roadmap (P01 v7, P01 v14, P04 v33, P09 v61)
points back to this document as the single source of truth, rather than
each version inventing its own number in isolation.

Lab-Scale Disclaimer (standing, applies to every section below)
------------------------------------------------------------------------
Consistent with ARCH01's Risks/Accepted Tradeoffs and P04 v33's Load-Test
Scale Disclaimer: every figure in §1-§8 below is sized for a generic
"enterprise lab" VM baseline and validates the *sizing methodology*, not
a production capacity claim. §1a below replaces §1's generic figures with
numbers that actually fit this project's real build environment (single
VMware Workstation host, AMD Ryzen 5 7530U, 16 GB RAM / 14.8 GB usable,
~234 GB free storage) — use §1a for anything actually provisioned on this
machine; use §1's generic figures only for narrative "how would a real
bank size this" discussion. SetupDoc-v<N>.md must record actual achieved
figures honestly against §1a, never implying they represent real
production scale.

Version 1.1 change note: Added §1a, Lab Hardware Sizing, and adjusted
§2.1's heap formula with lab-specific values. No other section changed.

---

1. CPU / Memory / Storage Baseline (per Node Role) — Generic Enterprise Reference
----------------------------------------------------------------------------------------
| Node Role | vCPU | RAM | Storage (see SOE01 §3 for layout) | Notes |
|---|---|---|---|---|
| WAS Cluster Member (App/Portal) | 2 | 4 GB | 20 GB + logs | Baseline per doc 01 |
| WAS Cluster Member (CBS) | 4 | 8 GB | 20 GB + logs | Heavier — hosts all business services (ARCH02 §2) |
| DMgr | 2 | 4 GB | 20 GB | No app traffic — admin only |
| IHS | 2 | 2 GB | 10 GB + logs | Lightweight — reverse proxy only |
| PostgreSQL (Primary) | 4 | 8 GB | 50 GB+, per §6 growth projection | Scales with P09 v64's RDS sizing when cloud phase reached |
| IBM MQ Queue Manager | 2 | 4 GB | 20 GB | Per §4 queue depth sizing |
| Monitoring stack (Prometheus/Grafana/Alertmanager) | 2 | 4 GB | 30 GB (30-day Prometheus retention, per P04 v31) | Single VM, per P04 v31's VM Setup Note |
| ELK stack | 2 | 4 GB | 50 GB (90-day OpenSearch retention, per P04 v31) | Single VM, per P04 v32's VM Setup Note |
| Jaeger | 1 | 2 GB | 10 GB (7-day retention, per P04 v31) | May co-locate with monitoring VM |
| Tomcat (Mobile/ATM host) | 1 | 1 GB | 20 GB | Mobile/ATM channel simulators only (P03 v26/v27) |

This table is what a real bank's capacity planning would look like at
small-production scale — kept as the reference point for interview
discussion (P03.1 Interview-2/Interview-5) even though this project's
actual host cannot run it as-is.

1a. Lab Hardware Sizing (This Environment — Actual Values Used)
------------------------------------------------------------------------
Host: VMware Workstation, AMD Ryzen 5 7530U (6c/12t, 2.0 GHz), 16 GB RAM
(14.8 GB usable), ~234 GB free storage. See SOE01 §1a for the full
per-role VM table (vCPU/RAM/disk/power-on schedule) — reproduced in
summary here for sizing cross-reference:

| VM Role | vCPU | RAM |
|---|---|---|
| DMgr + Node1 (combined) | 2 | 3 GB |
| Node2 (2nd cluster member) | 2 | 2 GB |
| IHS | 1 | 1 GB |
| PostgreSQL | 2 | 2 GB |
| IBM MQ | 1 | 1.5 GB |
| Monitoring (Prometheus/Grafana) | 1 | 1.5 GB |
| ELK stack | 1 | 1.5 GB |
| Tomcat (Mobile/ATM host) | 1 | 1 GB |

Total if every VM were powered on simultaneously: 11 vCPU / ~13.5 GB RAM —
exceeds this host's realistic comfortable ceiling. Per SOE01 §1a's
concurrent-VM budget, no more than 4-5 VMs run at once; the numbers above
are per-VM sizing, not a claim that all seven run together.

2. JVM Heap Sizing
------------------------
### 2.1 Formula

**Generic enterprise reference:**

Xms = Xmx (fixed heap — avoids resize pauses under load)
Xmx (App/Portal member) = 1.5 GB, within a 4 GB RAM node
Xmx (CBS member) = 3 GB, within an 8 GB RAM node
Xmx (DMgr) = 1 GB, within a 4 GB RAM node


**Lab-adjusted (this environment, per §1a's RAM budget):**

Xms = Xmx (fixed heap — same rationale, still avoids resize pauses)
Xmx (Node1/App member, on the combined DMgr+Node1 VM) = 1 GB, within a 3 GB VM
Xmx (Node2/CBS-equivalent member) = 1.25 GB, within a 2 GB VM
Xmx (DMgr, sharing the combined VM with Node1) = 0.75 GB, within the same 3 GB VM


Rule of thumb applied (both tables): heap ≤ ~40-50% of node/VM RAM,
leaving headroom for OS, native memory, and other WAS overhead (thread
stacks, JIT, GC metadata) — never size heap to consume the whole VM. On
the lab-adjusted table this margin is tighter than the enterprise
reference, so GC behavior should be watched more closely during P01 v14's
tuning exercise — a heap that's fine at enterprise scale can genuinely
thrash on a 2-3 GB VM if left at enterprise-style ratios.

### 2.2 Tuning Reference
Actual GC-log-driven tuning (before/after comparison, GC pause analysis)
is exercised at P01 v14 and revisited at P04 v33's performance baseline —
this section fixes the *starting point*, those versions prove and refine
it with real data, against whichever table (generic or lab-adjusted) the
node in question actually uses. If P01 v14's actual tuning result differs
from either baseline, this document is updated to match what was proven,
not silently left stale (same correction discipline STD's Dependency
Matrix already follows).

3. Thread Pool Sizing
--------------------------
| Thread Pool | Baseline Size (generic) | Lab-Adjusted Size | Notes |
|---|---|---|---|
| Web Container | 50 | 20 | Handles incoming HTTP/HTTPS (P04 v33's named pool) — reduced to match this host's realistic concurrent-request ceiling |
| ORB/EJB | 30 | 15 | Remote EJB + internal WAS calls |
| Default | 20 | 10 | Fallback pool |
| MDB Listener Port | 10 per queue | 5 per queue | Sized separately — MDB listener starvation is a distinct failure mode from Web Container exhaustion (per P04 v33) |

Scale-out trigger: sustained thread pool utilization >80% for 5+ minutes
(per P04 v34's alert-engineering threshold design) is the signal to
either add a cluster member (§7) or investigate a blocking call (P03.1
Interview-4 Scenario 5), not to blindly raise the pool size first — this
holds at both sizing scales.

4. JDBC Pool Sizing (Cross-Reference)
--------------------------------------------
Owned by P01 v7's Connection Pool Sizing worked example — not restated
here. Formula recap: `(cluster members × max pool size per member) +
admin/replication headroom ≤ PostgreSQL max_connections`. On this host,
run P01 v7's worked example against the lab-adjusted 2-member topology
(§1a) rather than the enterprise 3-member example originally illustrated
— e.g., 2 members × 20-connection pool each = 40 connections required at
peak, comfortably under a modestly-configured `max_connections=100` on
the 2 GB PostgreSQL VM. Any change to cluster member count (§7 below)
must be re-validated against this formula before being applied.

5. MQ Sizing
------------------
| Item | Baseline (generic) | Lab-Adjusted | Notes |
|---|---|---|---|
| Max queue depth (BANK.REQUEST.Q, BANK.RESPONSE.Q) | 5,000 messages | 1,000 messages | Alert at 70% (P04 v34); reduced ceiling matches the 1.5 GB MQ VM in §1a |
| Max message size | 4 MB | 4 MB | Payment message payloads (P03 v25) stay well under this regardless of scale |
| DLQ threshold | Backout count = 3 | Backout count = 3 | Unchanged — this is a logic setting, not a resource one |
| Channel count (per Queue Manager) | 10 baseline | 5 baseline | Scales with cross-region channels added at P06 v41 (revisit if that Part is ever run on constrained hardware) |

6. Storage / Log Retention
--------------------------------
| Data | Retention (generic) | Lab-Adjusted | Notes |
|---|---|---|---|
| Prometheus metrics | 30 days | 14 days | Per P04 v31; halved to fit §1a's smaller monitoring VM disk |
| OpenSearch logs | 90 days | 30 days | Per P04 v31; reduced for the same reason |
| Jaeger traces | 7 days | 7 days | Already short — unchanged |
| SystemOut/SystemErr (local, pre-shipping) | 7 days local rotation, then shipped/archived | 3 days local rotation | Rotation only — never manual deletion (STD Golden Rule) |
| pg_dump backups | Last 4 dumps retained | Last 2 dumps retained | Matches §1a's smaller /backup mount |
| Restore-test results | Last 2 retained | Last 2 retained | Unchanged |
| S3-archived logs/reports (P09 v65 onward) | Lifecycle-tiered: 90 days hot → Glacier after 1 year | Unchanged | Cloud-phase retention isn't constrained by this host once P09 is reached (AWS-hosted, not local VMware) |

Database growth projection (feeds P04 v35's Capacity Management and P09
v64's RDS sizing): baseline projection of +5% data volume per
version-cycle at lab scale — unchanged from the generic figure, since
this is a rate assumption, not a resource ceiling; replaced with real
trend data once P04 v31-v34 metrics exist.

7. Cluster Sizing / Scale-Out Triggers
--------------------------------------------
| Trigger | Threshold | Action |
|---|---|---|
| CPU utilization | Sustained >75% for 10+ min across cluster | Add cluster member — **on this host, "adding a member" means powering on Node2 rather than provisioning new hardware; a genuine 3rd/4th member exceeds available RAM per §1a and should be treated as a documented, deliberate exception, not a default** |
| Thread pool utilization | Sustained >80% for 5+ min (lab-adjusted pool sizes, §3) | Investigate first (§3); scale out if genuinely load-driven |
| JDBC pool wait time | >100ms average sustained | Re-check §4's lab-adjusted math before resizing pool or scaling cluster |
| MQ queue depth | Sustained >70% of lab-adjusted max depth (§5) | Check consumer health (MDB/listener) before assuming scale-out is needed |
| Session count | Approaching per-node session capacity (memory-bound, per §1a's per-VM RAM) | Add cluster member (Node2), within the constraint above |

Baseline cluster size: 2 members (per P01 v5's initial clustering
exercise) — this also happens to be this host's practical ceiling per
§1a, so the enterprise "start small, scale when needed" principle and
this host's hardware ceiling point the same direction without conflict.

8. Performance Thresholds (SLO Baseline, Cross-Reference)
------------------------------------------------------------------
Formal SLO/SLI/Error Budget definition is owned by P04 v33 — this section
provides the sizing-side inputs those SLOs are measured against. Targets
below are unchanged regardless of lab hardware, since they describe
acceptable customer-facing latency, not infrastructure capacity — if
lab-adjusted sizing (§1a-§7) can't hit these under load testing, that gap
itself becomes a legitimate, documented finding in P04 v33's SetupDoc
rather than a reason to quietly loosen the target:

| Flow | Target Latency (p99) | Ties to |
|---|---|---|
| Fund Transfer | < 2s end-to-end | P04 v33's SLO example |
| Balance Inquiry | < 500ms | REST endpoint, P02 v16 |
| Login | < 1s | Includes MFA/OTP round-trip, P02 v17 |

---

Relationship to Other Standards
------------------------------------
- **SOE01** owns the *configuration flags* (JVM args, sysctl) and the
  per-VM lab hardware table (§1a there mirrors §1a here); this document
  owns the *numbers* those flags are set to, at both enterprise and
  lab-adjusted scale.
- **P01 v7** remains the sole owner of the JDBC pool sizing formula
  itself — this document cross-references it (§4) and supplies the
  lab-adjusted worked example.
- **P04 v31/v33/v35** are where these baselines are proven, tuned, and
  replaced with real measured data over time — this document is the
  starting hypothesis, not a number frozen forever. Any version that
  produces real data superseding a baseline here (generic or
  lab-adjusted) should trigger an update to this file, documented the
  same way STD's Dependency Matrix correction was (2026-07-22 audit
  precedent).

---

*This is CAP01. Companion standards: SOE01 (configuration baseline this
document's numbers plug into, including the matching §1a lab hardware
table), STDGAP01 (TCS01's Priority model — Critical paths in §8 are
exactly what TCS01's Critical-priority test cases validate against).*