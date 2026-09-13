# PHASE 2: CORE ADMIN ESSENTIALS (Days 11–22)

## Goal
Own datasources, transactions, security, JVM, patching — the daily-driver skills.

## Days
D11: DataSources ⭐⭐ — connection-url, driver module, min/max-pool, validation
     (valid-connection-checker, background-validation), JNDI, XA vs non-XA;
     golden settings: min10/max50, bg-validation=true
D12: Transactions (JTA/Arjuna) ⭐ — TM, XA, tx logs in standalone-data, orphan
     detection, timeouts, recovery listener; NEVER delete tx logs
D13: Logging ⭐ — handlers (periodic/size-rotating), categories, levels,
     patterns; bank standard: daily rotate, 14 days, 100MB cap
D14: Users & Realms — add-user.sh, ManagementRealm/ApplicationRealm, RBAC
     roles (Administrator/Deployer/Maintainer/SuperUser); no shared admins
D15: RBAC & Audit ⭐ — include/exclude mapping, scoped roles, sensitivity
     constraints (datasource = high sensitivity), audit-log → SIEM
D16: HTTPS/SSL/Elytron ⭐ — keystore/truststore, server-ssl-context, TLS
     versions, cert renewal automation across estate
D17: Undertow & AJP — listeners, virtual hosts, AJP for mod_cluster,
     max-connections tuning for payday load
D18: EJB & JVM Config — EJB pools/timeouts, -Xms/-Xmx, G1GC, metaspace cap,
     JVM element vs JAVA_OPTS; heap sizing math (heap < VM RAM!)
D19: Patching ⭐ — patch apply/history/rollback, CPs, minor upgrades; UAT →
     Prod one-node-at-a-time with drain
D20: CLI Scripting — batch mode, .cli scripts, if/try, shell combos; nightly
     pool-check script across 40 servers
D21: Domain Deep Dive ⭐ — DC vs HC, host.xml, groups↔profiles, rolling
     restarts; DC as SPOF and host-controller recovery
D22: ✅ Revision — capstone: full payment-app standalone config from memory
     (datasource + logging + HTTPS + JVM)

## Banking Scenario Seeds
- Monday-crash pool leak fixed by idle-connection removal + validation
- Half-done transfer: XA rollback, Arjuna logs = auditor proof money can't vanish
- DEBUG "temporarily" in prod filled /opt in one hour
- Shared "jbossadmin" = couldn't prove who changed datasource — audit nightmare
- Deployer role blocked from touching prod pools at 2 AM (sensitivity constraint)
- Missed cert expiry → 2,000 failed logins in 20 min → cron-based monitoring
- 8GB heap on 8GB VM → OS starved, OOM killer ate JVM
- CVE forced 40-server patch in 48h — automation beat 7-day SLA, CISO commendation
- 12-node domain: one CLI command rolling-restarted payment-group, zero downtime

## Interview Seeds
- Design a prod datasource for payments; pool exhaustion root causes
- XA transactions; in-flight transactions during crash?
- Logging policy for 10-instance cluster
- Management access design for 20-person team
- Stop juniors changing prod datasources?
- HTTPS on EAP 7 with Elytron; cert expiry across estate
- Request flow browser→JBoss; why AJP?
- Where to set heap; how to size; -Xms=-Xmx why?
- EAP CP rollout on 10-node prod cluster
- Same change on 40 servers — safely and provably?
- DC dies — what happens? Domain HA design

## Phase-Done Checklist
(a) Compact revision notes  (b) 7 deliverables (20 interview Q&A,
5 recent issues, 20 scenario Qs, 20 troubleshooting Qs, 5 war stories,
5 behavioral, 5 architecture)
