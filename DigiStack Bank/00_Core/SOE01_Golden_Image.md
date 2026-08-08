ID: SOE01
Version: 1.7
Status: Active

Title: Standard Operating Environment — Golden Image Specification

Imports:
IDX
STD
CAP01

Exports:
Golden Image baseline (OS, packages, filesystem, kernel, WAS/IHS/MQ/PostgreSQL versions)
Security hardening baseline
Standard JVM arguments
Log location standard
Backup/monitoring agent baseline
Lab Hardware Baseline (this environment)

Used By:
P01
P04 
P08 
P09
P10
P10.1

Next:
(none — reference document, cited by version, not sequenced)

---

Purpose
-------
Defines the exact, versioned baseline every DigiStack Bank VM/node is
built from — the "golden image" a fresh server is validated against
before it's considered fit to join a cell. P08 v51's Ansible playbooks
encode this spec; they do not define it independently. If a playbook's
default ever differs from this document, the playbook is wrong — fix the
playbook, not this spec (same rule DBS01 already applies to migration
scripts).

Any drift between a running node and this spec is a defect, tracked the
same way P06 v43's configuration-drift detection and P09 v71's
configuration-sprawl audit already track drift elsewhere in this project
— this document is the "golden template" those two mechanisms check
against at the OS/middleware layer.

Version 1.4 change note (2026-07-28): Reverted a premature WebSphere
ND version-pin update in §9 (was showing 9.0.5.28, cited P01 v1's
SetupDoc-v1.md as source — but P01 v1 has not been built; Progress_Log.md
and 01_Application_Development's README both confirm 0/14 versions
complete). Placeholder 9.0.3 restored, matching STD's reverted pin.
Also added port 8080 (Tomcat HTTP) to §8's firewall table — STD already
defines this port but it was missing here, and every VM/port sizing
table elsewhere in this document already accounts for the Tomcat role.

Version 1.5 change note (2026-07-28, same day, later edit): Project
owner explicitly requested 9.0.5.28 be adopted as the *target* package
standard ahead of Sprint 1 (mirrored in STD v1.6, same day) — a
forward-looking planning decision, not a claim that P01 v1 has been
built (it hasn't). §9's table below is updated with concrete target
values for WebSphere ND, IHS, Web Server Plug-ins, and IBM Installation
Manager, replacing vaguer "latest supported at install time" language
where the owner has now specified a concrete number. All entries remain
explicitly marked placeholder/target, unconfirmed until validated
against an actual SetupDoc-v1.md §4.1 record.

Version 1.1 change note: Added §1a, Lab Hardware Baseline — this
project's actual build environment (single VMware Workstation host,
AMD Ryzen 5 7530U, 16 GB RAM, ~234 GB free storage) constrains how many
VMs from §1's generic role table can run concurrently. §1a is the
authoritative sizing for this specific environment; §1's enterprise
figures remain the reference for "how a real bank would size this,"
per this project's standing WebSphere-Practice-First Test (ARCH01).

Version 1.6 change note (2026-07-29): Added CAP01 to this document's own
Imports list. §11's JVM args section explicitly derives its -Xms/-Xmx
values from CAP01 §2.1's lab-adjusted heap table, and this document's
own closing line already names CAP01 a "Companion standard" — the
Imports field had never been updated to reflect that real dependency.
Content unchanged; metadata-only correction, same category as the
2026-07-28 audit's STD/SOE01 port-table fix.

Version 1.7 change note (2026-07-30): WebSphere ND pin promoted from
target/placeholder to CONFIRMED, mirroring STD v1.8's same-day change —
P01 v1 signed off, SetupDoc-v1.md §4.1/§8 is the source record. IHS/Web
Server Plug-ins/IBM Installation Manager pins remain target/unconfirmed
until P01 v8 is reached.
---

1. Supported Operating System
----------------------------------
| Item | Value |
|---|---|
| OS | Rocky Linux 8.x (RHEL 8.x compatible) — per STD |
| Kernel | Distribution-default, patched to latest minor per Patch Manager cadence (P09 v62) |
| Architecture | x86_64 |
| SELinux | Enforcing (targeted policy) — exceptions documented per-node in SetupDoc-v<N>.md, never silently disabled |
| Minimal install | Yes — "Minimal Install" base, no GUI, no unnecessary package groups |

1a. Lab Hardware Baseline (This Environment)
--------------------------------------------------
Host: VMware Workstation on AMD Ryzen 5 7530U (6c/12t, 2.0 GHz base),
16 GB RAM (14.8 GB usable), ~234 GB free storage, integrated GPU
(irrelevant for this workload).

This is the real constraint every VM sizing decision in this project must
respect. The generic enterprise sizing in §1 (used for all "how would a
real bank size this" narrative) is not what actually runs concurrently on
this host — the table below is.

| VM Role | vCPU | RAM | Disk (thin-provisioned) | Powered On When |
|---|---|---|---|---|
| DMgr + Node1 (combined) | 2 | 3 GB | 40 GB | Almost always — base of every exercise from P01 v5 onward |
| Node2 (2nd cluster member) | 2 | 2 GB | 40 GB | Only for clustering/failover/HA topics (P01 v5, P05 v36, P07 rolling upgrade) |
| IHS | 1 | 1 GB | 20 GB | Whenever web-tier routing is being exercised (P01 v8 onward) |
| PostgreSQL | 2 | 2 GB | 40 GB | Almost always — most versions need a live DB |
| IBM MQ | 1 | 1.5 GB | 20 GB | From P02 v19 onward, only when MQ topics are active |
| Monitoring (Prometheus/Grafana/Alertmanager) | 1 | 1.5 GB | 30 GB | Only from P04 v31 onward, and only during observability-focused sessions |
| ELK stack | 1 | 1.5 GB | 40 GB | Only from P04 v32 onward, only during logging-focused sessions |
| Tomcat (Mobile/ATM host) | 1 | 1 GB | 20 GB | Only when Mobile/ATM topics are active (P03 v26/v27 onward) |

Concurrent-VM budget: **do not run more than 4-5 of the above
simultaneously.** A typical P01/P02-era session (DMgr+Node1, Node2, IHS,
PostgreSQL) totals ~7-8 GB RAM and ~4 vCPU allocated — leaves headroom for
the Windows host and VMware Workstation overhead on a 16 GB machine. Once
P04's monitoring/ELK VMs exist, they are powered on only for the session
that needs them, then shut down again — never left running alongside
every other VM at once.

Rule: this is not a shortcut around real WAS admin practice — it mirrors
how a real admin manages a personal/sandbox environment with finite
capacity, and reinforces the discipline of knowing exactly which
component a given topic actually requires (ARCH01's Guiding Filter,
applied at the hardware level).

Storage discipline (specific to this host):
- Use thin-provisioned VMDKs, never thick — the 234 GB free budget cannot
  absorb thick-provisioned disks across 6+ VMs.
- Consolidate/delete VMware snapshots immediately after each version's
  sign-off (per STDGAP01/TCS01 §2.7's sign-off rubric) — do not keep
  long snapshot chains; they are the single fastest way to exhaust local
  storage on this host.
- Budget ~40 GB per VM disk (not the enterprise-scale 50 GB+ implied for
  DB nodes in §1/CAP01) — sufficient to prove the mechanism without
  needing production-scale headroom.

2. Linux Packages (Baseline)
--------------------------------
| Category | Packages |
|---|---|
| Core utilities | tar, unzip, wget, curl, net-tools, bind-utils, lsof, sysstat, tcpdump |
| Build/runtime | glibc, libstdc++, compat-libstdc++ (as required by WAS ND) |
| Monitoring agents | node_exporter (P04 v31), filebeat (P04 v32) |
| Backup agent | pg_dump client tools (DB nodes only) / AWS Backup agent (P09 v58 onward, cloud-hosted nodes) |
| Time sync | chrony |
| Editors/diagnostics | vim, less, strace |

Rule: no package is installed "because it might be useful." Every package
above ties to a specific topic already in this roadmap (monitoring →
P04 v31/v32, backup → P05 v38/P09 v58). A new package requirement
discovered mid-build is added here first, then to the Ansible playbook
(P08 v51) — never the other way around.

3. Filesystem Layout & Mount Points
----------------------------------------
| Mount Point | Purpose | Suggested Minimum Size (lab scale, per §1a) |
|---|---|---|
| / | OS root | 10 GB |
| /apps/IBM | WebSphere ND / IHS / IM install root | 15 GB |
| /var/log | System + application logs | 5 GB (separate from root — prevents log growth from filling OS) |
| /data/db | PostgreSQL data directory (DB nodes only) | 20 GB, revisit if P09 v64's growth projection demands more |
| /data/mq | IBM MQ Queue Manager data (MQ nodes only) | 5 GB |
| /backup | Local staging before pg_dump/backupConfig ships off-box | 10 GB |

Rule: WAS profiles, logs, and application data are never colocated on the
OS root partition — a full log volume must never be able to crash the OS
itself. This is the filesystem-level enforcement of STD's "no manual log
deletion — rotation only" rule.

4. Kernel Parameters (sysctl)
----------------------------------
| Parameter | Value | Rationale |
|---|---|---|
| net.core.somaxconn | 1024 | WAS/IHS connection backlog under cluster load |
| net.ipv4.tcp_tw_reuse | 1 | High connection churn (ATM/Mobile simulators, P03 v26/v27) |
| vm.swappiness | 10 | Avoid JVM heap swapping under memory pressure — especially important on this host given the tight RAM budget in §1a |
| fs.file-max | 500000 | High concurrent connection ceiling for WAS + IHS + MQ combined |
| kernel.shmmax / kernel.shmall | Sized to largest configured PostgreSQL shared_buffers (kept modest per CAP01's reduced DB sizing) | Required for PostgreSQL on DB nodes |

5. limits.conf
------------------
| User | Item | Soft | Hard |
|---|---|---|---|
| wasadmin | nofile | 8192 | 65536 |
| wasadmin | nproc | 4096 | 16384 |
| postgres | nofile | 8192 | 65536 |
| mqm | nofile | 8192 | 65536 |

Rationale: WAS ND, PostgreSQL, and IBM MQ each independently recommend
raised file-descriptor limits under load; the connection-pool exhaustion
scenario in P05 v38 / P03.1 Interview-4 Scenario 4 assumes these limits
are already correctly set — a limits.conf misconfiguration is itself a
plausible root cause worth keeping in the troubleshooting scenario bank.

6. Time Synchronization
---------------------------
- chrony, pointed at the Global Shared Services NTP source (per ARCH01's
  Global Shared Services layer / P06's standing layer).
- Drift tolerance: alerts if offset exceeds 500ms (ties into P04 v34's
  alert-engineering discipline once monitoring exists).
- Rationale: clock drift across cluster members breaks LTPA token
  validity windows (P02 v17) and produces misleading distributed traces
  (P04 v33) — a frequently underrated real-world WAS issue.

7. DNS
----------
- Internal DNS resolves all `digistack-<role>-<region>-01` hostnames (per
  STD's naming convention) and all subdomains (`mobile.`, `atm.`, `card.`
  — per P03's Channel Simulator Standard).
- Search domain: `digistack.cloud` (internal).
- No node relies on `/etc/hosts` static entries in Prod — DEV-only
  exception must be documented in that node's SetupDoc (in practice, on
  this single-host lab, `/etc/hosts`-style resolution across VMs is an
  acceptable documented exception throughout — call it out explicitly in
  every SetupDoc rather than silently assuming a full internal DNS server
  is standing up on this hardware).

8. Firewall (firewalld / iptables baseline)
------------------------------------------------
| Port | Service | Direction |
|---|---|---|
| 22 | SSH (from bastion/jump host only, per P09 v56 once cloud phase begins; on-prem equivalent = a designated admin subnet) | Inbound, restricted source |
| 9060/9043 | WAS Admin Console | Inbound, admin subnet only |
| 9080/9443 | WAS App traffic | Inbound, from IHS/LB tier only |
| 80/443 | IHS | Inbound, from LB tier only |
| 1414 | MQ Listener | Inbound, from app-tier subnet only |
| 5432 | PostgreSQL | Inbound, from app-tier/DB-replica subnet only |
| 8080 | Tomcat HTTP (Mobile/ATM channel host) | Inbound, from LB tier only |
| 9090/3000/9200/5601 | Prometheus/Grafana/OpenSearch/Dashboards | Inbound, monitoring subnet only |

Rule: default-deny, explicit-allow. Every port opened here must already
exist in STD's §Ports table — this document doesn't invent new ports, it
enforces which source subnets may reach the ones STD already defines.

9. Middleware Versions (Golden Baseline)
---------------------------------------------
| Component | Version | Notes |
|---|---|---|
| IBM Installation Manager | 1.9.x (target, placeholder — response-file driven, P07 v44 pattern) | Version pinned per SetupDoc, re-validated at each P07 migration |
| WebSphere ND | 9.0.5.28 (target — not yet installed; P01 v1 reset to not-started 2026-08-04) | Upgraded only via P07's Migration Strategy — never ad hoc on a single node |
| Java SDK | IBM Java 8 (SDK 8.0), bundled/certified with WAS ND 9.0.5.28 target | Coexistence handled per P07 v44's managesdk discipline |
| IBM HTTP Server | 9.0.5.28 (target — version-matched to WAS ND pin above, per IBM's Support Matrix, P07 v44's planning gate) | Mismatched IHS/WAS versions is a named migration failure mode — never install IHS independently of this check |
| Web Server Plug-ins | 9.0.5.28 (target — matches WAS ND/IHS pin above) | Generated/propagated per P01 v8's plugin-cfg.xml workflow |
| IBM MQ | IBM MQ Advanced for Developers, 9.3.x/9.4.x (free tier, per STD's Licensing Reality Check) | Version pinned per SetupDoc-v19.md |
| PostgreSQL | 16 (target, adopted 2026-08-04 per STD v1.9/v1.10). Not yet installed anywhere — dsb-db has not been provisioned; P01 v1 reset to not-started | Project-wide standard — see MASTER INDEX Open Decisions |

10. Security Hardening Baseline
------------------------------------
- SSH: key-based auth only, root login disabled, password auth disabled.
- OS accounts: `wasadmin`, `postgres`, `mqm` — no shared/generic service
  accounts; least-privilege sudo rules per role.
- SELinux: enforcing, with WAS/IHS/MQ-specific policy modules applied per
  IBM's documented SELinux guidance (not blanket `setenforce 0`).
- Auditd: enabled, logging privileged command execution.
- OS-level patching: per P09 v62's Patch Manager cadence (cloud) / P05
  v38's OS Patching process item (on-prem) — this document doesn't
  define the cadence, it defines the baseline the cadence patches
  against.

11. Standard JVM Arguments
-------------------------------
Baseline generic args applied to every WAS JVM before any
version-specific tuning (P01 v14, P04 v33) is layered on top:

-Xms<per CAP01 §2.1 / §1a lab-adjusted value>
-Xmx<per CAP01 §2.1 / §1a lab-adjusted value>
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/var/log/was/heapdumps/
-Xverbosegclog:/var/log/was/gc/gc_%Y%m%d.log
-Dcom.ibm.websphere.security.util.authCacheTimeout=600


Rule: exact `-Xms`/`-Xmx` values are not hardcoded here — they're derived
per-node from CAP01 §2.1's lab-adjusted heap table, since heap sizing
depends on cluster member count, role, and — on this host — the tight
overall RAM budget in §1a. This document fixes the *flags*; CAP01 fixes
the *numbers*.

12. Log Locations (Standard Paths)
---------------------------------------
| Log | Path |
|---|---|
| SystemOut/SystemErr | `/apps/IBM/WebSphere/AppServer/profiles/<profile>/logs/<server>/` |
| HPEL logs | `/apps/IBM/WebSphere/AppServer/profiles/<profile>/logs/<server>/hpel/` |
| IHS access/error logs | `/var/log/ihs/` |
| MQ error logs | `/var/mqm/qmgrs/<QM>/errors/` |
| PostgreSQL logs | `/var/log/postgresql/` |
| Ansible/wsadmin execution logs (P08 v51/v52) | `/var/log/digistack-automation/` |

Rule: every path above is what Filebeat (P04 v32) is configured to ship
from — this document is the authoritative source-of-truth path list;
P04 v32's Filebeat config references these paths, it doesn't redefine
them independently.

13. Monitoring & Backup Agent Baseline
-------------------------------------------
| Agent | Purpose | Introduced |
|---|---|---|
| node_exporter | Linux-level metrics → Prometheus | P04 v31 |
| filebeat | Log shipping → Logstash/OpenSearch | P04 v32 |
| pg_dump (scripted, cron-driven) | Weekly DB backup per STD §Backup | P01 v1 onward |
| AWS Backup agent | Off-site/cloud backup | P09 v58 onward, cloud-hosted nodes only |

Golden Image Validation Checklist (applied to every new node before it
joins a cell)
------------------------------------------------------------------------------
□ OS matches §1, minimal install confirmed
□ VM sized per §1a's lab hardware baseline, not §1's generic enterprise
  figures — confirm this node's role and RAM allocation match §1a's table
□ All §2 packages present, no extras beyond this list without a
  documented exception
□ Filesystem layout matches §3 — no WAS/log/DB data on OS root
□ sysctl values match §4, limits.conf matches §5
□ chrony synced within tolerance (§6), DNS resolves per §7 (or the
  documented single-host exception)
□ Firewall rules match §8 exactly — no ad hoc opened ports
□ Middleware versions match §9's pinned baseline
□ Security hardening (§10) confirmed — no disabled SELinux, no root SSH
□ Standard JVM args (§11) applied, heap values pulled from CAP01's
  lab-adjusted table
□ Log paths match §12 — Filebeat confirmed shipping from all of them
  (when the monitoring VM is powered on)
□ Monitoring + backup agents (§13) installed and reporting
□ VM disk is thin-provisioned; no long-lived snapshot chain left over
  from a prior version's sign-off

A node that fails any checklist item is not "mostly done" — it does not
join the cluster until every item passes, consistent with this project's
Golden Rule: "Always validate deployments."

---

Relationship to Other Standards
------------------------------------
- **STD** owns naming/ports/environments; this document enforces which
  subnets may reach STD's ports and pins the exact versions STD names
  generically.
- **CAP01** (Capacity & Sizing Standard) owns the numeric values (heap
  size, pool size) this document's §11 JVM args reference, including the
  lab-adjusted values matching §1a's hardware budget.
- **P08 v51** encodes this entire document as Ansible playbooks —
  idempotency proof in P08 v51 is, in effect, proof that a playbook run
  reproduces this golden image exactly, every time.
- **P06 v43** / **P09 v71** perform drift detection against this document
  as the template.

---

*This is SOE01. Companion standards: STD (naming/ports baseline), CAP01
(sizing values referenced in §11, including the lab-adjusted table
matching this host's constraints), STDGAP01 (SDD01 SetupDoc template —
every SetupDoc-v<N>.md's §2 VM Setup section should cite this document
rather than restate it).*