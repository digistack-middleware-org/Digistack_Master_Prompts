# PHASE 4: PERFORMANCE & TROUBLESHOOTING (Days 35–46) ⭐ THE HEART
# This phase decides whether you pass 10-yr interviews.

## Goal
Diagnose ANY JBoss problem from evidence — never restart-and-pray.

## Days
D35: Methodology ⭐ — symptom → logs → JVM state → resources → network → DB;
     6-layer elimination; evidence BEFORE restart
D36: Thread Dumps ⭐⭐ — jstack, kill -3, BLOCKED/WAITING states, deadlocks,
     diff two dumps 30s apart; 198/200 threads BLOCKED on one synchronized block
D37: Heap Dumps ⭐ — jmap, -XX:+HeapDumpOnOutOfMemoryError, Eclipse MAT,
     dominator tree, leak suspects; OOM types (heap/metaspace/native);
     Thursday-11PM OOM = unbounded FX-rate cache
D38: GC Analysis ⭐ — -Xlog:gc*, young/old gen, pause times, G1 tuning;
     1.2s full GCs every 90s = p99 spikes; fix dropped p99 60%
D39: High CPU ⭐ — top -H → nid (0x hex) → thread dump mapping; sample twice;
     app CPU vs GC CPU; 200MB JSON serialization culprit
D40: Pool & JDBC ⭐⭐ — InUseCount via CLI, blocking-timeout-millis, leak
     detection, statement cache; ranked causes: leaks > small pool > slow DB
D41: Arjuna Failures ⭐ — reading Arjuna logs, heuristics (mixed/committed/
     aborted), orphaned tx, XAER_RMERR, recovery manager replay
D42: Deployment & Classloading Failures — missing modules, duplicate JARs,
     NoSuchMethodError (two library versions), resolution order
D43: Log Analysis at Scale ⭐ — grep/awk patterns, cross-instance timestamp
     correlation, Splunk/ELK shipping; 9:02–9:07 failures → 9:00 deploy on 3 nodes
D44: Boot Failures ⭐ — boot sequence, port in use, bad XML, datasource
     waiting on DB (deploy hang!); systemd DB-before-JBoss dependency
D45: Monitoring ⭐ — server state, sessions, pool usage, queue depth, GC,
     threads; JMX + CLI sweeps + Grafana dashboards with thresholds
D46: ✅ Revision — capstone: "payment app down Monday 9 AM" full resolution
     from memory

## Banking Scenario Seeds
- Juniors wanted instant restart (loses evidence); thread dump → DB pool wait
  → slow query; 20-min fix vs hourly recurrence
- Two dumps 30s apart, identical blocking = proof, not guess
- Thursday OOMs: MAT dominator tree → 2GB FX cache → eviction via JMX → gone
- 100% CPU node: hot thread = app serializing entire customer history
- "Connection not available, timed out after 30000ms" — most common prod issue
- Heuristics mixed: debit committed, credit rolled back; Arjuna replay
  completed it; walked auditor through logs proving no money lost
- Release night NoSuchMethodError: WAR jar vs module jar — resolution order
  ended a 3-hour debate in 10 minutes
- Post-reboot 20-min boots: datasource waiting for late-starting Oracle;
  systemd dependency = zero false 6 AM pages
- 80% pool alert fired 2 weeks before exhaustion — Monday story never repeated

## Interview Seeds
- "JBoss is slow" — no other info. Full diagnostic flow
- Analyze a hung instance with thread dumps
- OOMs every Thursday — find the leak
- Periodic latency spikes — GC's role
- 100% CPU → culprit thread, step by step
- JCA timeout at peak — rank causes + response
- Heuristic transaction explained; can money be lost? Recovery?
- NoSuchMethodError after deploy — cause + fix approach
- Errors across 10 nodes same minute — correlate to root cause
- 20-min boots after DB maintenance — what/fix
- The 10 metrics you monitor on a payment cluster and why

## Phase-Done Checklist
(a) Compact revision notes  (b) 7 deliverables (20 interview Q&A,
5 recent issues, 20 scenario Qs, 20 troubleshooting Qs, 5 war stories,
5 behavioral, 5 architecture)
