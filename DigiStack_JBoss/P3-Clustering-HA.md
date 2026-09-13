# PHASE 3: CLUSTERING & HIGH AVAILABILITY (Days 23–34) ⭐ BANK HEART

## Goal
Design and operate clustered, zero-downtime JBoss for critical banking apps.

## Days
D23: Load Balancing Concepts — horizontal vs vertical, sticky vs non-sticky
     sessions, failover; nothing critical unclustered in a bank
D24: mod_cluster + Apache ⭐⭐ — dynamic registration, back-channel, httpd
     config, context filtering; auto-scale = just start the server
D25: Session Replication ⭐ — <distributable/>, Infinispan, replication vs
     distribution vs invalidation; serialization gotchas kill failover
D26: Infinispan & Caching — replicated vs distributed, SSO cache across apps,
     cache modes; when caching hurts
D27: Failover Testing Discipline ⭐ — kill node under load, session continuity,
     recovery timing; quarterly drills on payments cluster
D28: LB Patterns — Apache/mod_cluster vs F5 vs OpenShift router; TLS
     termination point; layered health checks (test the REAL path incl. DB)
D29: Zero-Downtime Ops ⭐ — disable → drain → stop → patch → start → enable;
     graceful shutdown/suspension; the most-asked interview procedure
D30: ActiveMQ Artemis ⭐ — messaging-activemq subsystem, queues/topics,
     connection factories, DLQ, paging, queue-depth alerts
D31: Frontend & SSO Integration — vhost mapping, SPNEGO/SAML via Elytron,
     header forwarding, keep-alive; clock-skew bounced every login story
D32: DB HA from JBoss Side ⭐ — RAC/PG-replication/AlwaysOn connection-urls,
     fast connection failover, validation+retry; DB HA ≠ app HA without this
D33: Baselines & Sizing — response time, TPS, GC pause, pool utilization;
     three-way match: threads vs connections vs pool; 500 TPS sizing sheet
D34: ✅ Revision — capstone: full HA design Apache→mod_cluster→10 nodes→RAC
     with failover test plan

## Banking Scenario Seeds
- Multicast misconfig: 6 nodes up, Apache balancing to only 2 → half users errored
- Failover worked BUT logged users out — unserializable session (HashMap+socket)
- SSO cache across 4 apps; cache hit-ratio stats proved ROI
- Drill found mod_cluster dead-node detection at 3 min → tuned to 20s
- F5 health check hit cached homepage while app was dead (DB down) — lesson
- Zero-downtime CVE patch: 10 nodes, users never saw a blip
- Queue depth 50,000: stuck consumer, alerts fired before payments delayed
- RAC failover: configured app re-routed in 30s; unconfigured app hung 8 min

## Interview Seeds
- Clustering beyond load sharing; sticky vs non-sticky?
- mod_cluster vs mod_jk/mod_proxy; node not receiving traffic — diagnose
- Replication on but users lose state on failover — why?
- Infinispan cache modes; when does caching hurt in a bank?
- Failover test plan + metrics for payments cluster
- Where should TLS terminate? Layered health checks design
- Patch 10-node prod cluster with zero user impact — exact steps
- JMS queue backing up — full troubleshooting flow
- SSO flow from portal to JBoss app
- DB failover: why do some apps recover instantly, others hang?
- Size a cluster for 500 TPS

## Phase-Done Checklist
(a) Compact revision notes  (b) 7 deliverables (20 interview Q&A,
5 recent issues, 20 scenario Qs, 20 troubleshooting Qs, 5 war stories,
5 behavioral, 5 architecture)
