# 🏦 DigiStack WAS LAB — Master Implementation Plan
### WebSphere ND 9.0.5.28 | 27-Week Solo-Job-Ready Program
> **Purpose of this file:** Paste this into your new Claude Pro account as the FIRST message.  
> It is your lab mentor's complete briefing — context, role, infrastructure, rules, and phase-by-phase plan.

---

## ⚡ HOW TO USE THIS DOCUMENT (Read First)

1. **Paste this entire file** as your first message in the new Claude Pro account.
2. Tell Claude: *"This is my DigiStack WAS LAB project. You are my senior WAS admin mentor. Read everything and confirm you understand the full stack before we begin."*
3. Each session, start with: *"I am working on Lab X.X — [topic]. My assumption is [state it]. Let's go."*
4. Claude will enforce lab rules, remind you of snapshots, and challenge you with "prove it" questions.

---

## 🎯 PROJECT GOAL

Crack a **10-year WAS Admin interview** and perform **solo job tasks** from Day 1 on a production banking middleware stack.

This is NOT a theory course. Every topic = hands-on on real VMs. The `digistack-bank-v1.ear` application is your vehicle. WebSphere is the product you are mastering.

**Target role:** WAS ND Senior Admin in a banking environment (IBM WebSphere ND 9.0.5.28, IBM MQ, PostgreSQL/Oracle XA, IHS, LDAP, Prometheus/Grafana, OpenSearch).

---

## 🖥️ LAB INFRASTRUCTURE — 7 VMs

| VM | Role | Key Services | Used In |
|---|---|---|---|
| `dsb-dmgr` | Deployment Manager + Node1 + App | WAS DMGR 9043/9443, AppServer 1 | Phases 2–7, 10–11 |
| `dsb-node02` | 2nd cluster member | AppServer 2, NodeAgent | Clustering, HA, XA recovery |
| `dsb-db` | PostgreSQL 16 | Port 5432, schemas: `debit`, `credit`, `digistack_bank` | DataSources, XA, tranlog, sessions |
| `dsb-ihs` | IHS 9.0.5.28 | Ports 80/443, plugin-cfg.xml | Web tier, plugin, SSL |
| `dsb-mq` | IBM MQ 9.3/9.4 Dev | Port 1414, QMGR `DIGIQM1` + `DIGIQM2` | Phase 8 |
| `dsb-monitor` | Prometheus + Grafana | Ports 9090/3000 | Phase 4 (PMI→Prometheus) |
| `dsb-elk` | OpenSearch + Filebeat | Ports 9200/5601 | Phase 4 (central logs) |

**Install paths (non-negotiable — enforce these):**
```
WAS:      /apps/IBM/WebSphere/AppServer
Logs:     /applogs
Data:     /appdata
Profiles: /apps/IBM/WebSphere/AppServer/profiles/
```

**Cluster name:** `DigiStackCluster`  
**Cell name:** `DigiStackCell`  
**Node names:** `DigiStackNode01` (dmgr), `DigiStackNode02` (node02)

---

## 🏗️ THE APPLICATION — `digistack-bank-v1.ear`

**Architecture:** `Servlet → EJB (CMT) → two XA DataSources → PostgreSQL 16`

| Component | Detail |
|---|---|
| `FundsTransferServlet` | Entry point, HTTP POST `/funds/transfer` |
| `FundsTransferEJB` | CMT (Container-Managed Transaction), calls both DSes |
| `DEBIT_DS` | JNDI: `jdbc/DEBIT_DS` → PostgreSQL schema `debit` |
| `CREDIT_DS` | JNDI: `jdbc/CREDIT_DS` → PostgreSQL schema `credit` |
| XA class | `org.postgresql.xa.PGXADataSource` (pgjdbc) |
| Recovery proof | `SELECT * FROM pg_prepared_xacts;` on `dsb-db` |

**Why this app matters for interviews:**  
It exercises the single most differentiating WAS skill — XA 2-phase commit, in-doubt transaction recovery, and proof of zero money lost. No other middleware demo does this cleanly.

---

## 🧑‍🏫 MENTOR ROLE — ALWAYS ENFORCE THESE RULES

> Claude: You are my senior WAS admin colleague and lab mentor. You trained for 10 years in production banking middleware. You are slightly skeptical — you challenge me. Follow ALL rules below on every response.

### The 10 Rules (enforce without exception):

1. **Hands-on first.** Every answer includes actual commands, Jython, SQL, or console steps I can run. No theory without a lab action.
2. **Ask or state assumption.** If I don't say which lab I'm on, ask "Which phase are you working on?" OR state your assumption explicitly before answering.
3. **Production incident framing.** Every procedure: `symptom → cause → diagnosis command → fix → verify → prevent`.
4. **Bank-grade rigor.** Always name the money/transaction implication: rollback, in-doubt XA, heuristic outcome, zero lost transfers.
5. **Copy-paste blocks.** Fenced code blocks, correct language tag (`bash`, `jython`, `sql`, `yaml`). Full command paths (e.g., `/apps/IBM/WebSphere/AppServer/bin/wsadmin.sh -lang jython`).
6. **Teach verification.** Every procedure ends with "how to prove it worked" (`psql SELECT`, `SystemOut grep`, `ss -tlnp`, `amqsbcg`, `CURDEPTH`).
7. **Failure drills.** When I ask "what happens if...", give exact steps to break it, exact error codes (`WTRN0062E`, `WTRN0107W`, `WSVR0605W`, `AMQ2035`, `AMQ2538`, `SRVE0199E`, `DSRA` codes), and fix.
8. **STAR interview mode.** Interview questions answered in STAR format grounded in MY labs (T.5, E.3, MQ.2, etc.) with exact proof I can reference.
9. **Troubleshooting order:** `logs (SystemOut/FFDC/trace) → javacore/thread dumps → config (server.xml/resources.xml) → network/ports → DB side (pg views) → MQ side (runmqsc)`.
10. **PostgreSQL XA specifics.** Always reference `pg_prepared_xacts`, `pgjdbc XADataSource class`, and `psql` verification where XA is involved.

### Lab Safety Rules (enforce before destructive labs):
- ⚠️ **Before T.5, E.3, Lab 5.2:** *"Have you snapshotted all 7 VMs? Don't proceed without it."*
- ⚠️ **Daily:** *"Run `df -h` — thin 40G disks fill fast. Clean FFDC/javacores/heapdumps after analysis."*
- ⚠️ **Performance tuning:** One lever at a time. Measure before and after each change. Never touch two knobs simultaneously.

---

## 📁 ARTIFACTS I AM BUILDING (help me create/improve on request)

| Artifact | Purpose | Primary Lab |
|---|---|---|
| `wasOps.py` | wsadmin Jython toolkit: start/stop, status, deploy, pool changes, tran timeouts, properties-driven | Lab 3.1 |
| `wtrn-error-cheatsheet.md` | WTRN/WSVR error patterns with causes and fixes | Lab T.7 |
| `health-check-all.sh` | All-7-VM health: servers, ports, heap, disk, cert expiry, `pg_prepared_xacts`, MQ CURDEPTH; cron-ready | Lab 6.3 |
| `mq-health.sh` | QMGR status, channel status, CURDEPTH, DLQ depth | Lab MQ.4 |
| `capacity-report.md` | Baseline vs tuned (JMeter results, Grafana evidence, sizing justification) | Lab PC.3 |
| `~/lab-journal/YYYY-MM-DD.md` | Daily: commands run, errors hit, fixes applied | Daily |

---

## 📌 PHASE 1 — FOUNDATION (Weeks 1–2)

**Goal:** All 7 VMs up, networked, secured. Linux baseline for WAS.

### Lab 0.1 — VM Build & Networking
**Deliverable:** All 7 VMs reachable by hostname from every other VM.

```bash
# /etc/hosts entry block (put this on ALL 7 VMs)
192.168.x.x   dsb-dmgr
192.168.x.x   dsb-node02
192.168.x.x   dsb-db
192.168.x.x   dsb-ihs
192.168.x.x   dsb-mq
192.168.x.x   dsb-monitor
192.168.x.x   dsb-elk

# Verify from each VM:
for h in dsb-dmgr dsb-node02 dsb-db dsb-ihs dsb-mq dsb-monitor dsb-elk; do ping -c1 $h; done

# NFS share: dsb-dmgr exports /appdata → dsb-node02
# firewalld: open 9043, 9443, 9080, 9060, 2809, 7276, 7286, 9352, 9633 on WAS VMs
# SELinux: permissive for lab (enforcing = Phase 5 hardening)
```

**Verify:** `ping dsb-dmgr` from `dsb-elk` responds. `/etc/hosts` identical on all 7.

### Lab 0.2 — Linux Baseline for WAS
**Deliverable:** `wasadmin` user, ulimits, port awareness.

```bash
# wasadmin user
useradd -m -s /bin/bash wasadmin
echo "wasadmin ALL=(ALL) NOPASSWD: /apps/IBM/WebSphere/AppServer/bin/*" >> /etc/sudoers

# /etc/security/limits.conf on all JVM VMs (dsb-dmgr, dsb-node02)
wasadmin soft nofile 65536
wasadmin hard nofile 65536
wasadmin soft nproc  16384
wasadmin hard nproc  16384

# Verify ulimits AFTER login as wasadmin:
su - wasadmin -c "ulimit -a"

# Port sanity check (before WAS install — should all be free):
ss -tlnp | grep -E "9043|9443|9080|1414|5432"

# Process check template (use daily):
ps -ef | grep java | grep -v grep

# Kill -3 (thread dump) syntax — memorize this:
kill -3 $(ps -ef | grep AppServer | grep -v grep | awk '{print $2}')
# Result: javacore.YYYYMMDD.HHMMSS.PID.txt in /applogs or profile dir
```

**Verify:** `ulimit -n` returns 65536 as `wasadmin`. `df -h` on all 3 WAS VMs — confirm >30G free.

### Lab 0.3 — Directory Layout & Prereqs
**Deliverable:** Standard directory tree. PostgreSQL and MQ prereqs installed.

```bash
# On dsb-dmgr and dsb-node02:
mkdir -p /apps/IBM/WebSphere/AppServer
mkdir -p /applogs
mkdir -p /appdata
chown -R wasadmin:wasadmin /apps /applogs /appdata

# RHEL 8 packages for WAS:
dnf install -y compat-libstdc++-33 libXtst gtk2 libXft motif ksh

# PostgreSQL prereqs on dsb-db:
dnf install -y postgresql-server postgresql-contrib
postgresql-setup --initdb
systemctl enable --now postgresql

# Create XA schemas:
sudo -u postgres psql <<EOF
CREATE DATABASE digistack_bank;
CREATE SCHEMA debit;
CREATE SCHEMA credit;
CREATE USER wasapp WITH PASSWORD 'wasapp123';
GRANT ALL ON SCHEMA debit, credit TO wasapp;
EOF
```

**Verify:** `psql -U wasapp -d digistack_bank -c "SELECT current_schema();"` returns successfully.

---

## 📌 PHASE 2 — INSTALLATION & PROFILES (Weeks 3–4)

**Goal:** WAS ND 9.0.5.28 installed. DMGR + federated node. First deployment.

### Lab 1.1 — IIM Silent Install
**Deliverable:** WAS ND 9.0.5.28 on both `dsb-dmgr` and `dsb-node02`.

```bash
# IIM silent install (run as wasadmin):
/path/to/IIM/installc \
  --launcher.ini /path/to/IIM/silent-install.ini \
  -input /path/to/response_file.xml \
  -log /applogs/iim_install.log \
  -acceptLicense

# Verify:
/apps/IBM/WebSphere/AppServer/bin/versionInfo.sh | grep -E "WebSphere|Version"
# Expected: WebSphere Application Server ND 9.0.5.28
```

**Interview anchor:** "How do you maintain version consistency across nodes?" → IIM + response files ensure identical binary on both nodes.

### Lab 1.2 — Profiles
**Deliverable:** DMGR profile + Custom node, federated.

```bash
# DMGR profile on dsb-dmgr:
/apps/IBM/WebSphere/AppServer/bin/manageprofiles.sh \
  -create -profileType Dmgr \
  -profileName Dmgr01 \
  -profilePath /apps/IBM/WebSphere/AppServer/profiles/Dmgr01 \
  -cellName DigiStackCell \
  -nodeName DigiStackDmgrNode \
  -hostName dsb-dmgr

# Custom node on dsb-node02:
/apps/IBM/WebSphere/AppServer/bin/manageprofiles.sh \
  -create -profileType managed \
  -profileName Custom02 \
  -profilePath /apps/IBM/WebSphere/AppServer/profiles/Custom02 \
  -cellName DigiStackCell \
  -nodeName DigiStackNode02 \
  -hostName dsb-node02

# Federate node02 to dmgr:
/apps/IBM/WebSphere/AppServer/bin/addNode.sh \
  dsb-dmgr 8879 -profileName Custom02 \
  -username admin -password adminpass

# Start DMGR:
/apps/IBM/WebSphere/AppServer/profiles/Dmgr01/bin/startManager.sh
```

**Verify:** Console at `https://dsb-dmgr:9043/ibm/console` → System Administration → Nodes → both nodes SYNCHRONIZED.

### Lab 1.3 — First Deployment
**Deliverable:** HelloWorld.war deployed. wsadmin first run.

```bash
# Start node agent on node02:
/apps/IBM/WebSphere/AppServer/profiles/Custom02/bin/startNode.sh

# wsadmin first touch:
/apps/IBM/WebSphere/AppServer/bin/wsadmin.sh \
  -lang jython \
  -host dsb-dmgr \
  -port 8879 \
  -username admin \
  -password adminpass \
  -c "print AdminControl.queryNames('type=Server,*')"
```

---

## 📌 PHASE 3 — CORE ADMINISTRATION (Weeks 5–7)

**Goal:** Cluster built. `digistack-bank-v1.ear` deployed. PostgreSQL XA DataSources. SIB JMS.

### Lab 2.1 — Cluster Build

```jython
# wsadmin Jython — create cluster DigiStackCluster
cluster = AdminConfig.create('ServerCluster', AdminConfig.getid('/Cell:DigiStackCell/'), '[[name DigiStackCluster]]')

# Add member on Node1 (dsb-dmgr):
AdminConfig.create('ClusterMember', cluster,
  [['memberName','DigiStackMember1'],['nodeName','DigiStackNode01']])

# Add member on Node2 (dsb-node02):
AdminConfig.create('ClusterMember', cluster,
  [['memberName','DigiStackMember2'],['nodeName','DigiStackNode02']])

AdminConfig.save()
```

**Verify:** `AdminControl.queryNames('type=Cluster,*')` returns `DigiStackCluster`.

### Lab 2.2 — Deploy `digistack-bank-v1.ear`

```jython
# Deploy via wsadmin (properties-driven — this is the interview-grade method):
AdminApp.install('/appdata/digistack-bank-v1.ear',
  '[-appname DigiStackBank '
  '-cell DigiStackCell '
  '-cluster DigiStackCluster '
  '-contextroot /bank '
  '-MapModulesToServers [[".*",".*","WebSphere:cluster=DigiStackCluster"]] '
  ']')
AdminConfig.save()
AdminApp.startApplication('DigiStackBank')
```

**Verify:** `curl -k https://dsb-ihs/bank/` returns HTTP 200. Check `SystemOut.log` on both cluster members for `WSVR0001I: Server ... open for e-business`.

### Lab 2.3 — PostgreSQL XA DataSources ⭐

```jython
# CRITICAL: Use XA class for 2PC. Non-XA = only 1PC. Banks need XA.
# pgjdbc XADataSource class: org.postgresql.xa.PGXADataSource

# Step 1: Create JDBC Provider (XA)
cell = AdminConfig.getid('/Cell:DigiStackCell/')
jdbcProv = AdminConfig.create('JDBCProvider', cell, [
  ['name',        'PostgreSQL XA Provider'],
  ['implementationClassName', 'org.postgresql.xa.PGXADataSource'],
  ['xa',          'true'],
  ['classpath',   '/appdata/jdbc/postgresql-42.x.x.jar']
])

# Step 2: Create J2C Auth Alias
secConfig = AdminConfig.getid('/Cell:DigiStackCell/Security:/')
alias = AdminConfig.create('JAASAuthData', secConfig, [
  ['alias',    'DigiStackAlias'],
  ['userId',   'wasapp'],
  ['password', 'wasapp123']
])

# Step 3: Create DEBIT_DS (XA)
debitDS = AdminConfig.create('DataSource', jdbcProv, [
  ['name',           'DEBIT_DS'],
  ['jndiName',       'jdbc/DEBIT_DS'],
  ['authDataAlias',  'DigiStackAlias'],
  ['xaRecoveryAuthAlias', 'DigiStackAlias']
])
# Custom props for pgjdbc XADataSource:
AdminConfig.create('J2EEResourceProperty',
  AdminConfig.create('J2EEResourcePropertySet', debitDS, []),
  [['name','serverName'],['value','dsb-db']])
AdminConfig.create('J2EEResourceProperty',
  AdminConfig.create('J2EEResourcePropertySet', debitDS, []),
  [['name','databaseName'],['value','digistack_bank']])
AdminConfig.create('J2EEResourceProperty',
  AdminConfig.create('J2EEResourcePropertySet', debitDS, []),
  [['name','currentSchema'],['value','debit']])

# Repeat for CREDIT_DS with currentSchema=credit
# ...

AdminConfig.save()
```

**Verify:** Console → Resources → JDBC → Data Sources → `DEBIT_DS` → Test Connection.  
Also: `psql -U wasapp -d digistack_bank -c "SELECT current_schema();"` from `dsb-db`.

**Interview:** "XA vs non-XA DataSource — when does it matter?" → Non-XA = 1PC only, not safe for funds transfer. XA = 2PC, coordinator can recover mid-crash.

### Lab 2.4 — SIB Bus & JMS

**Goal:** SIB bus on cluster. Test queue. MDB. Kill member mid-send → prove rollback.

```bash
# Console path: Service Integration → Buses → New bus → Add cluster member
# Bus name: DigiStackBus
# Queue: BANK.FUNDTRANSFER.Q

# After setup, verify queue depth from wsadmin:
mbean = AdminControl.queryNames('type=SIBQueuePoint,name=BANK.FUNDTRANSFER.Q,*')
print AdminControl.getAttribute(mbean, 'depth')
```

**Kill drill:** Start a JMeter send loop → `kill -9` DigiStackMember1 → messages must NOT be lost → restart member → verify queue depth recovered and MDB processed all messages.

---

## 📌 PHASE 3B — TRANSACTIONS & XA (Weeks 7–8)

> ⭐ **This is your interview differentiator.** No other candidate walks into a WAS interview and says "I have lab proof of XA 2PC recovery with a mid-crash kill and zero money lost." Build this section carefully.

**Setup first:**
```sql
-- On dsb-db, create schemas and tables:
CREATE SCHEMA debit;
CREATE SCHEMA credit;

CREATE TABLE debit.accounts (
  id BIGSERIAL PRIMARY KEY,
  account_id VARCHAR(20) NOT NULL,
  balance NUMERIC(18,2) NOT NULL
);

CREATE TABLE credit.accounts (
  id BIGSERIAL PRIMARY KEY,
  account_id VARCHAR(20) NOT NULL,
  balance NUMERIC(18,2) NOT NULL
);

INSERT INTO debit.accounts VALUES (1, 'DEBIT001', 10000.00);
INSERT INTO debit.accounts VALUES (2, 'CREDIT001', 0.00);
```

### Lab T.1 — ACID Proof

```bash
# Trigger FundsTransfer via curl:
curl -X POST https://dsb-ihs/bank/funds/transfer \
  -d "from=DEBIT001&to=CREDIT001&amount=500"

# Inject failure: make credit EJB throw RuntimeException mid-transaction
# Expected: DEBIT not reduced (full rollback)

# Verify via psql:
psql -U wasapp -d digistack_bank -c \
  "SELECT account_id, balance FROM debit.accounts WHERE account_id='DEBIT001';"
# Must still show 10000.00
```

### Lab T.2 — Local vs Global XA / 1PC vs 2PC

**In trace (`Transaction=all`):**
- 1PC marker: `ResourceImpl.commit` — no PREPARE phase
- 2PC markers: `XAResource.prepare` → `XAResource.commit` (or `rollback`)
- Look for `WTRN0062I: The transaction coordinator has begun a global transaction`

```bash
# Enable XA trace on AppServer (console or wsadmin):
# Diagnostic Trace → Transaction=all:com.ibm.ws.Transaction.*=all
# Then trigger transfer → inspect /applogs/<server>/trace.log
grep -E "WTRN|XAResource|prepare|commit" /applogs/DigiStackMember1/trace.log | head -50
```

### Lab T.3 — Tranlogs & pg_prepared_xacts

```bash
# Locate tranlog:
ls -la /apps/IBM/WebSphere/AppServer/profiles/Dmgr01/tranlog/

# The tranlog is WAS's crash recovery record.
# In-doubt XA branches (after mid-crash) appear in PostgreSQL:
psql -U wasapp -d digistack_bank -c "SELECT * FROM pg_prepared_xacts;"
# Format: transaction | gid | prepared | owner | database
# gid = "WAS_XID_..." — WAS's XA transaction ID
```

**Interview:** "Why does tranlog size matter?" → If full, WAS cannot log new XA branches → new transactions fail with `WTRN0107W`. In banking = payment outage.

### Lab T.5 — ⭐ XA Recovery Money Lab

> **SNAPSHOT ALL VMs BEFORE THIS LAB.** Non-negotiable.

```bash
# The Kill Drill:
# 1. Start JMeter sending Fund Transfers continuously
# 2. Watch pg_prepared_xacts (refresh every 2s):
watch -n 2 "psql -U wasapp -d digistack_bank -c 'SELECT * FROM pg_prepared_xacts;'"

# 3. MID-TRANSFER, kill DigiStackMember1 hard:
kill -9 $(ps -ef | grep DigiStackMember1 | grep -v grep | awk '{print $2}')

# 4. Observe in-doubt transaction in pg_prepared_xacts:
# Should see 1 row with gid starting "WAS_..." — this is money in limbo

# 5. Restart the server:
/apps/IBM/WebSphere/AppServer/profiles/Custom01/bin/startServer.sh DigiStackMember1

# 6. Watch SystemOut.log for WTRN recovery replay:
tail -f /applogs/DigiStackMember1/SystemOut.log | grep -E "WTRN|recovery|commit|rollback"
# Look for: WTRN0107W (in-doubt), then WTRN0062I (recovered)

# 7. PROVE ZERO MONEY LOST:
psql -U wasapp -d digistack_bank -c \
  "SELECT * FROM pg_prepared_xacts;"
# Must be EMPTY — no in-doubt transactions

psql -U wasapp -d digistack_bank -c \
  "SELECT account_id, balance FROM debit.accounts;
   SELECT account_id, balance FROM credit.accounts;"
# Each transfer either fully committed (both sides) or fully rolled back (both sides)
# NEVER: debit reduced but credit not credited — that's money disappearing
```

**Interview STAR answer for T.5:**
- **S:** Mid-payment server crash during fund transfer in 2-node cluster
- **T:** Prove zero money lost and recover without manual intervention
- **A:** Used `kill -9` on active cluster member mid-XA, observed `pg_prepared_xacts` showing in-doubt branch with WAS XID, restarted server, watched WTRN recovery log entries, verified `pg_prepared_xacts` empty after recovery
- **R:** Transfer either fully committed or fully rolled back — zero money lost, proven via psql balance check

### Lab T.7 — WTRN Cheatsheet (Build in `wtrn-error-cheatsheet.md`)

| Code | Meaning | Cause | Action |
|---|---|---|---|
| `WTRN0062E` | Transaction timed out | Long-running TX, slow DB, deadlock | Check DB query times, increase timeout or fix query |
| `WTRN0107W` | In-doubt transaction exists | Server crash mid-2PC | Restart server → WTRN recovery replay |
| `WSVR0605W` | Server stopped with active transactions | Graceful stop during active TX | Wait for TX completion before stop, or investigate via trace |
| `DSRA0302E` | XAException during XA operation | DB-level XA error | Check `pg_prepared_xacts`, DB logs, pgjdbc version |

```bash
# Enable full XA trace:
/apps/IBM/WebSphere/AppServer/bin/wsadmin.sh -lang jython \
  -c "AdminControl.setAttribute(AdminControl.queryNames('type=RasLoggingService,*'), \
      'traceSpecification', 'Transaction=all:com.ibm.ws.Transaction.*=all')"
```

---

## 📌 PHASE 4 — OPS & TROUBLESHOOTING (Weeks 9–14)

**Goal:** Jython toolkit. Heap/thread dumps. HPEL. Log shipping to OpenSearch. JVM tuning. Session HA. Prometheus.

### Lab 3.1 — Build `wasOps.py`

```python
# wasOps.py skeleton — fill as you go through labs
# Usage: wsadmin.sh -lang jython -f wasOps.py -profileName Dmgr01 -- status
import sys

props_file = '/appdata/wasOps.properties'  # externalise everything

def get_server_status(server_name):
    """Check server status — returns STARTED/STOPPED"""
    try:
        state = AdminControl.getAttribute(
            AdminControl.queryNames('type=Server,name=%s,*' % server_name),
            'state')
        return state
    except:
        return 'NOT_RUNNING'

def set_ds_pool_size(ds_jndi, max_connections):
    """Change DS pool max connections — takes effect immediately"""
    ds = AdminConfig.getid('/DataSource:%s/' % ds_jndi)
    pool = AdminConfig.showAtt(ds, 'connectionPool')
    AdminConfig.modify(pool[0][1], [['maxConnections', max_connections]])
    AdminConfig.save()
    print 'Pool for %s set to %s' % (ds_jndi, max_connections)

def set_tran_timeout(server_name, timeout_secs):
    """Set total lifetime timeout on a server's TX service"""
    txService = AdminConfig.getid(
        '/Server:%s/TransactionService:/' % server_name)
    AdminConfig.modify(txService, [['totalTranLifetimeTimeout', timeout_secs]])
    AdminConfig.save()
    print 'TX timeout for %s set to %ss' % (server_name, timeout_secs)

# Add: deploy, undeploy, node sync, thread dump trigger...
```

### Lab 3.2 — Javacores, Heap Dumps, MAT

```bash
# Thread dump (javacore):
kill -3 $(ps -ef | grep DigiStackMember1 | grep -v grep | awk '{print $2}')
# Output: /apps/IBM/WebSphere/AppServer/profiles/Custom01/javacore.*.txt

# Heap dump (OOM simulation — reduce Xmx first):
# JVM args: -Xmx256m -verbose:gc -Xgcpolicy:gencon
# Then hit a heavy endpoint → OOM → heapdump.*.phd

# Analyze with MAT (Eclipse Memory Analyzer):
# Open heapdump → Leak Suspects → identify largest retained set
# Interview: "What class is leaking?" → Name the retained set, the class, the GC root

# Verbose GC parse:
grep -E "GC|allocation|freed" /applogs/DigiStackMember1/verbosegc.log | tail -50
```

### Lab 3.3 — HPEL + OpenSearch Log Pipeline

```bash
# Enable HPEL on AppServer (console or wsadmin):
# Server → Logging and Tracing → IBM logging facility = High Performance Extensible Logging

# Query HPEL logs (60-min window):
/apps/IBM/WebSphere/AppServer/bin/logViewer.sh \
  -repositoryDir /apps/IBM/WebSphere/AppServer/profiles/Custom01/logs/hpelRepository \
  -minLevel WARNING \
  -startDate 2025-01-01T00:00:00 \
  -stopDate 2025-01-01T01:00:00 \
  -outLog /applogs/hpel_query.log

# Filebeat config on dsb-dmgr (ship to dsb-elk):
# /etc/filebeat/filebeat.yml:
filebeat.inputs:
- type: log
  paths:
    - /applogs/*/SystemOut.log
    - /applogs/*/SystemErr.log
  fields:
    service: was
    environment: lab

output.logstash:
  hosts: ["dsb-elk:5044"]
```

### Lab 3.6 — Prometheus / Grafana (PMI via JMX Exporter)

```yaml
# /appdata/jmx_exporter/was-config.yaml:
startDelaySeconds: 0
hostPort: localhost:1099
username: admin
password: adminpass
rules:
  - pattern: "WebSphere<name=JVM,(.*)><>HeapSize"
    name: was_jvm_heap_size_bytes
  - pattern: "WebSphere<name=ThreadPool,(.*)><>ActiveThreads"
    name: was_thread_pool_active_threads
  - pattern: "WebSphere<name=ConnectionPool,(.*)><>PoolSize"
    name: was_ds_pool_size
```

**Grafana dashboards to build:**
- JVM Heap (used vs max, over time)
- WebContainer thread pool (active / total)
- DS pool (DEBIT_DS and CREDIT_DS — pool size, wait time)
- SIB queue depth (BANK.FUNDTRANSFER.Q)

---

## 📌 PHASE 5 — SECURITY (Weeks 15–17)

**Goal:** LDAP, SSL end-to-end, LTPA/SSO.

### Lab 4.1 — Global Security & LDAP

```bash
# OpenLDAP install on dsb-dmgr (or dedicated LDAP VM):
dnf install -y openldap-servers openldap-clients

# WAS console path:
# Security → Global Security → Available realm definitions → Federated repositories
# → Add repository → LDAP type: Custom → Host: dsb-dmgr → Port: 389

# Verify LDAP auth:
/apps/IBM/WebSphere/AppServer/bin/wsadmin.sh \
  -lang jython \
  -user ldap_wasadmin \
  -password ldap_password
```

### Lab 4.2 — SSL Full Stack

```bash
# Generate self-signed cert on DMGR:
/apps/IBM/WebSphere/AppServer/bin/iKeyman.sh
# Or via console: Security → SSL certificate and key management → Key stores and certs

# CSR + sign flow:
keytool -genkeypair -alias wasalias -keyalg RSA -keysize 2048 \
  -keystore /appdata/ssl/was-keystore.jks -storepass changeit

keytool -certreq -alias wasalias \
  -keystore /appdata/ssl/was-keystore.jks \
  -file /appdata/ssl/was.csr

# Break SSL deliberately and fix (interview-grade):
# Change truststore to exclude the cert → HTTPS connection fails with PKIX error
# Fix: re-import cert into truststore
# Verify: curl -v --cacert /appdata/ssl/ca.crt https://dsb-dmgr:9443/ibm/console

# Cert expiry check script (seed for health-check-all.sh):
keytool -list -v -keystore /appdata/ssl/was-keystore.jks -storepass changeit \
  | grep -E "Alias|Valid from|until"
```

---

## 📌 PHASE 6 — WEB TIER & HA (Weeks 18–19)

**Goal:** IHS routing to both cluster members. HA kill drills.

### Lab 5.1 — IHS + Plugin

```bash
# On dsb-ihs: install IHS 9.0.5.28 (same IIM method as WAS)

# Generate plugin-cfg.xml from DMGR:
/apps/IBM/WebSphere/AppServer/bin/GenPluginCfg.sh \
  -profileName Dmgr01 \
  -cell DigiStackCell \
  -webserver WebServer1

# Propagate to dsb-ihs:
/apps/IBM/WebSphere/AppServer/bin/wsadmin.sh -lang jython \
  -c "AdminControl.invoke(AdminControl.queryNames('type=WebServer,name=WebServer1,*'), 'propagatePlugin', '', '')"

# IHS httpd.conf:
LoadModule was_ap22_module /apps/IBM/HTTPServer/Plugins/bin/64bits/mod_was_ap22_http.so
WebSpherePluginConfig /apps/IBM/HTTPServer/Plugins/config/webserver1/plugin-cfg.xml

# Read plugin-cfg.xml line by line — know these elements:
# <ServerCluster> → your cluster
# <Server> → each cluster member with host/port
# <VirtualHostGroup> → virtual hosts
# <UriGroup> → which URIs get routed to WAS
# <Route> → ties them together

# Break routing 3 ways and fix:
# 1. Wrong plugin path → 503 "No backend server"
# 2. AppServer stopped → 503 with specific backend error
# 3. Wrong virtual host → 404
```

### Lab 5.2 — HA Kill Drills

```bash
# ⚠️ SNAPSHOT ALL VMs BEFORE THIS LAB

# Drill 1: Kill cluster member under JMeter load
kill -9 $(ps -ef | grep DigiStackMember1 | grep -v grep | awk '{print $2}')
# Expected: IHS reroutes traffic to Member2 within 1-2 retry cycles
# Observe in IHS access_log: all requests now going to Member2

# Drill 2: Kill node agent on dsb-node02
kill -9 $(ps -ef | grep nodeagent | grep -v grep | awk '{print $2}')
# Expected: DMGR loses sync with node02, but apps on node02 KEEP RUNNING
# Console shows node02 UNSYNCHRONIZED — not dead, just out of contact

# Drill 3: Kill DMGR
kill -9 $(ps -ef | grep dmgr | grep -v grep | awk '{print $2}')
# Expected: APPS KEEP RUNNING. IHS keeps routing. No end-user impact.
# What BREAKS: console access, wsadmin, config changes, new deployments
# Interview: "Killing Dmgr in prod — what breaks?" → Answer from this drill

# Drill 4: Kill member mid-XA → same as Lab T.5
```

---

## 📌 PHASE 7 — AUTOMATION, PATCHING & DR (Weeks 19–20)

**Goal:** Properties-driven cell rebuild. Health check script. Backup/DR.

### Lab 6.2 — Properties-Driven Cell Rebuild

```bash
# Extract current cell config as properties:
/apps/IBM/WebSphere/AppServer/bin/extractConfigProperties.sh \
  -profileName Dmgr01 \
  -properties /appdata/cell-config.props

# Apply on a fresh cell:
/apps/IBM/WebSphere/AppServer/bin/applyConfigProperties.sh \
  -profileName Dmgr01 \
  -properties /appdata/cell-config.props \
  -reporter /applogs/apply-config-report.txt
```

### Lab 6.3 — `health-check-all.sh` (Build This)

```bash
#!/bin/bash
# health-check-all.sh — DigiStack Bank | All 7 VMs
set -euo pipefail

DMGR_HOST="dsb-dmgr"
NODE2_HOST="dsb-node02"
DB_HOST="dsb-db"
IHS_HOST="dsb-ihs"
MQ_HOST="dsb-mq"
ALERT_EMAIL="wasadmin@digistack.internal"
LOG="/applogs/health-check-$(date +%Y%m%d).log"

log() { echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOG"; }
alert() { log "ALERT: $1"; echo "$1" | mail -s "DigiStack ALERT" "$ALERT_EMAIL"; }

# 1. WAS server status
for server in DigiStackMember1 DigiStackMember2; do
  status=$(/apps/IBM/WebSphere/AppServer/bin/wsadmin.sh -lang jython \
    -c "print AdminControl.queryNames('type=Server,name=${server},state=STARTED,*')" 2>/dev/null)
  [[ -z "$status" ]] && alert "Server $server is NOT started"
done

# 2. Port checks
for check in "$DMGR_HOST:9043" "$DMGR_HOST:9443" "$IHS_HOST:80" "$IHS_HOST:443" "$MQ_HOST:1414" "$DB_HOST:5432"; do
  host="${check%:*}"; port="${check#*:}"
  nc -z -w 3 "$host" "$port" || alert "Port $port on $host is UNREACHABLE"
done

# 3. Disk check (alert if >80% on any WAS VM)
for vm in $DMGR_HOST $NODE2_HOST; do
  usage=$(ssh wasadmin@$vm "df -h /apps | tail -1 | awk '{print \$5}'" | tr -d '%')
  [[ $usage -gt 80 ]] && alert "Disk on $vm is ${usage}% full — clean javacores/heapdumps/FFDC"
done

# 4. In-doubt XA check (money risk!)
indoubt=$(psql -h $DB_HOST -U wasapp -d digistack_bank -t \
  -c "SELECT COUNT(*) FROM pg_prepared_xacts;")
[[ $indoubt -gt 0 ]] && alert "CRITICAL: $indoubt in-doubt XA transaction(s) in pg_prepared_xacts — coordinate with DBA"

# 5. SSL cert expiry (alert if <30 days)
CERT_DAYS=$(keytool -list -v \
  -keystore /appdata/ssl/was-keystore.jks -storepass changeit 2>/dev/null \
  | grep "until:" | head -1 | awk '{print $NF}')
# ... parse and compare to today + 30d ...

# 6. MQ CURDEPTH check
. /opt/mqm/bin/setmqenv -m DIGIQM1 -n
depth=$(echo "DISPLAY QLOCAL(BANK.FUNDTRANSFER.Q) CURDEPTH" | runmqsc DIGIQM1 \
  | grep CURDEPTH | awk -F'[()]' '{print $2}')
[[ $depth -gt 1000 ]] && alert "MQ BANK.FUNDTRANSFER.Q depth is $depth — possible MDB consumer stopped"

log "Health check complete."
```

---

## 📌 PHASE 8 — IBM MQ (Weeks 20–22)

**Goal:** MQ on `dsb-mq`. Two QMGRs. WAS↔MQ integration. AMQ error codes.

### Lab MQ.1 — Queue Manager Basics

```bash
# On dsb-mq (as mqm user):
. /opt/mqm/bin/setmqenv -s

# Create QMGRs:
crtmqm DIGIQM1
crtmqm DIGIQM2  # "mainframe" simulator
strmqm DIGIQM1
strmqm DIGIQM2

# Verify:
dspmq
# QMNAME(DIGIQM1) STATUS(Running)
# QMNAME(DIGIQM2) STATUS(Running)

# runmqsc: 20 commands to know cold:
runmqsc DIGIQM1 <<EOF
DEFINE QLOCAL(BANK.PAYMENT.REQUEST.Q) MAXDEPTH(5000) DEFSOPT(EXCL)
DEFINE QLOCAL(BANK.PAYMENT.RESPONSE.Q) MAXDEPTH(5000)
DEFINE QLOCAL(BANK.DLQ) USAGE(NORMAL)
ALTER QMGR DEADQ(BANK.DLQ)
DEFINE LISTENER(LISTENER.1414) TRPTYPE(TCP) PORT(1414) CONTROL(QMGR)
START LISTENER(LISTENER.1414)
DEFINE CHANNEL(DIGISVRCONN) CHLTYPE(SVRCONN)
SET CHLAUTH(DIGISVRCONN) TYPE(BLOCKUSER) USERLIST('nobody')
END
EOF

# Test put/get:
echo "TEST FUNDS TRANSFER" | amqsput BANK.PAYMENT.REQUEST.Q DIGIQM1
amqsget BANK.PAYMENT.REQUEST.Q DIGIQM1
```

### Lab MQ.2 — SDR/RCVR Channel Between QMGRs

```bash
# DIGIQM1 → DIGIQM2 (bank → "mainframe"):
runmqsc DIGIQM1 <<EOF
DEFINE CHANNEL(DIGIQM1.TO.DIGIQM2) CHLTYPE(SDR) CONNAME('dsb-mq(1415)') XMITQ(DIGIQM2.XMIT)
DEFINE QLOCAL(DIGIQM2.XMIT) USAGE(XMITQ)
START CHANNEL(DIGIQM1.TO.DIGIQM2)
EOF

# Break channel 3 ways (build troubleshooting muscle):
# 1. Wrong port → AMQ9213 (connection refused)
# 2. CHLAUTH blocking → AMQ2035 (not authorized)
# 3. XMITQ not defined → AMQ2085 (unknown object)
```

### Lab MQ.4 — AMQ Error Code Cheatsheet

| AMQ Code | Meaning | Fix |
|---|---|---|
| `AMQ9999` | Channel program ended abnormally | Check AMQERR01.LOG, fix CHLAUTH or SSL |
| `AMQ9208` | Error on receive from host | Network issue, channel flapping — check `netstat -an` port 1414 |
| `AMQ2035` | Not authorized | CHLAUTH rule blocking — `DISPLAY CHLAUTH(*)` check |
| `AMQ2059` | Queue manager unavailable | QMGR not started — `strmqm DIGIQM1` |
| `AMQ2085` | Unknown object | Queue or object doesn't exist — `DISPLAY QLOCAL(*)` |
| `AMQ2538` | Host not available | DNS/network — ping `dsb-mq` from WAS side |

```bash
# DLQ inspection (amqsbcg = browse, not destructive):
amqsbcg BANK.DLQ DIGIQM1
# Shows: dead-letter header (reason code, original queue, put date)

# mq-health.sh seed:
runmqsc DIGIQM1 <<EOF
DISPLAY QMGR STATUS
DISPLAY CHSTATUS(*)
DISPLAY QLOCAL(*) CURDEPTH
EOF
```

---

## 📌 PHASE 9 — ADJACENT MIDDLEWARE (Weeks 23–24)

**Goal:** Know WHY banks pay for WAS. Understand IHS alternatives. Liberty basics.

### Lab AM.3 — WAS vs Tomcat Comparison (Interview Gold)

```bash
# Deploy digistack-bank-v1.ear on Tomcat — observe what FAILS:
# 1. EJB (CMT) → Tomcat has no EJB container → UnsupportedOperationException
# 2. XA DataSource → Tomcat DBCP2 does NOT support XA → single-resource only
# 3. SIBus JMS → no SIB in Tomcat → JNDI lookup fails
# 4. LTPA SSO → no LTPA token support
# 5. wsadmin → doesn't exist

# Interview answer: "WAS vs Tomcat — why do banks pay for WAS?"
# → EJB CMT + JTA (true XA 2PC) → money cannot be lost
# → Proven in Lab T.5 — Tomcat cannot do this
# → Add: IBM support contract, PMR, security certifications
```

### Lab AM.4 — Liberty Profile

```bash
# Create Liberty server:
/apps/IBM/WebSphere/AppServer/bin/server create DigiStackLiberty

# server.xml minimum for a notification module:
cat > /apps/IBM/WebSphere/AppServer/usr/servers/DigiStackLiberty/server.xml <<EOF
<server description="DigiStack Liberty">
  <featureManager>
    <feature>servlet-4.0</feature>
    <feature>jndi-1.0</feature>
    <feature>jdbc-4.2</feature>
    <feature>mail-2.0</feature>
  </featureManager>
  <httpEndpoint id="defaultHttpEndpoint" httpPort="9081" httpsPort="9444"/>
  <dataSource jndiName="jdbc/NotifyDS">
    <jdbcDriver libraryRef="PGLib"/>
    <properties.postgresql serverName="dsb-db" databaseName="digistack_bank"
                           user="wasapp" password="wasapp123"/>
  </dataSource>
  <library id="PGLib">
    <fileset dir="/appdata/jdbc" includes="postgresql-*.jar"/>
  </library>
</server>
EOF

/apps/IBM/WebSphere/AppServer/bin/server start DigiStackLiberty
# Logs: /apps/IBM/WebSphere/AppServer/usr/servers/DigiStackLiberty/logs/messages.log
```

---

## 📌 PHASE 10 — PERFORMANCE & CAPACITY (Weeks 25–26)

**Goal:** Prove JVM sizing decisions with data. Capacity report with Grafana evidence.

### Lab PC.1 — JMeter Load Profile

```
Thread groups:
- Ramp 1: 50 users, 60s ramp, 10 min sustain
- Ramp 2: 200 users, 120s ramp, 10 min sustain
- Ramp 3: 500 users, 180s ramp, 10 min sustain

Target endpoints:
- /bank/funds/transfer (XA path — the money path)
- /bank/balance (read-only)

Capture at each ramp: throughput (req/s), p95 latency, error rate
Simultaneously watch: nmon + Grafana heap/thread/pool dashboards + GC log
```

### Lab PC.2 — One Lever at a Time (Never Touch Two)

| Lever | Before | After | Measurement |
|---|---|---|---|
| Heap (`-Xms/-Xmx`) | 512m/1g | 1g/2g | GC frequency, OOM rate |
| GC Policy | `gencon` | `optthruput` | GC pause time, throughput |
| WebContainer Threads | 50 | 100 | Thread wait time, throughput |
| DS Pool (each DS) | 10 | 25 | DS wait time, timeout rate |
| Session mgmt | Memory-only | Memory-to-memory replication | Failover time |

```bash
# JVM args (console → AppServer → Java and Process Management → JVM):
-Xms1024m -Xmx2048m -Xgcpolicy:gencon
-verbose:gc -Xverbosegclog:/applogs/DigiStackMember1/verbosegc.log,20,10000
```

---

## 📌 PHASE 11 — EXPERT & FINAL EXAM (Week 27)

**Goal:** Fix 11 things under time pressure. Full cell rebuild from bare VMs. Interview-ready.

### Lab E.3 — Outage War-Game (11 Failures)

```bash
# ⚠️ SNAPSHOT ALL VMs FIRST

# Break list (induce one at a time, time your fix):
# 1.  Expired SSL cert           → HTTPS fails, PKIX error in browser
# 2.  Port conflict on 9043      → DMGR won't start
# 3.  DataSource (DEBIT_DS) down → DSRA0302E in SystemOut
# 4.  Corrupted server.xml       → AppServer fails to start (XML parse error)
# 5.  Dead node agent            → node02 UNSYNCHRONIZED in console
# 6.  Full /applogs disk         → WAS stops writing logs, potential hang
# 7.  Dead MQ channel            → AMQ9999 in AMQERR01.LOG
# 8.  MQ queue full              → AMQ2051 (put inhibited) or CURDEPTH = MAXDEPTH
# 9.  Hung thread                → WSVR0605W, javacore shows stuck thread
# 10. Wrong virtual host         → 404 on /bank/ URL
# 11. In-doubt XA transaction    → pg_prepared_xacts not empty after restart

# Fix sequence for each:
# logs → config → network/ports → DB side → MQ side
# Document root cause, fix, prevention in lab-journal
```

### Final Exam — From Bare VMs (Fully Scripted, One Day)

```bash
# Script sequence (build this across all phases):
# 1.  IIM install WAS ND 9.0.5.28 on dsb-dmgr + dsb-node02
# 2.  Create DMGR profile, custom node, federate
# 3.  Build DigiStackCluster (2 members)
# 4.  Create JDBC Provider (XA), J2C alias, DEBIT_DS + CREDIT_DS
# 5.  Deploy digistack-bank-v1.ear
# 6.  Configure IHS + plugin-cfg.xml + propagate
# 7.  Enable SSL (IHS ↔ plugin ↔ AppServer)
# 8.  Configure OpenLDAP + federated repo in WAS global security
# 9.  Configure IBM MQ (DIGIQM1 + DIGIQM2, channels, WAS JMS provider)
# 10. XA Kill Proof: kill member mid-transfer → restart → pg_prepared_xacts empty
# 11. End-to-end: Browser → dsb-ihs → WAS → JMS → SDR → RCVR → reply
# 12. JMeter 200-user run → capture Grafana → one-page capacity report

# Time target: < 8 hours total
```

---

## 🎤 MASTER INTERVIEW QUESTION BANK

> In interviews: cite your lab. "I proved this in my 7-VM homelab using kill -9 mid-XA."

| # | Question | Your Lab Proof |
|---|---|---|
| 1 | "Server died mid-payment — what happens to the money?" | T.5: `pg_prepared_xacts` shows in-doubt, restart triggers WTRN recovery, psql shows zero money lost |
| 2 | "Explain 2PC and the coordinator's role" | T.2: traced `XAResource.prepare → commit` in `Transaction=all` trace |
| 3 | "What's a heuristic hazard and what do you do?" | T.6: forced resolution, golden rule = coordinate with DBA |
| 4 | "Why does tranlog size matter?" | T.3: `WTRN0107W` = tranlog full = payment outage |
| 5 | "Walk me through your worst production outage" | E.3 + T.8 war-game post-mortem — 11 failures, timed fixes |
| 6 | "WAS vs Tomcat — why do banks pay for WAS?" | AM.3: FundsTransfer EJB+XA fails on Tomcat — no EJB container, no JTA XA |
| 7 | "How do you size a JVM? Prove it" | PC.1–PC.3: JMeter data + Grafana GC/heap/thread dashboards |
| 8 | "MQ channel is down — walk me through triage" | MQ.2/MQ.4: AMQERR01.LOG → `DISPLAY CHSTATUS(*)` → AMQ code → fix |
| 9 | "Kill the Dmgr in production — what breaks?" | Lab 5.2: apps keep running, IHS keeps routing; console/wsadmin dead |
| 10 | "Automation: rebuild your whole cell from zero" | Lab 6.2 + Final Exam: fully scripted, timed under 8 hours |
| 11 | "How do you monitor WAS centrally?" | Lab 3.3 (OpenSearch dashboard) + Lab 3.6 (Prometheus/Grafana) |
| 12 | "Oracle vs PostgreSQL for XA — differences?" | T.3–T.5: `pg_prepared_xacts` vs Oracle's `v$transaction`; `pgjdbc XADataSource` class specifics |

---

## ✅ SOLO-JOB READINESS CHECKLIST

You can hold the pager alone when every box is YES:

- [ ] Rebuild entire cell from scripts in <8 hours (Final Exam)
- [ ] Explain what still works when Dmgr dies — from drill proof (Lab 5.2)
- [ ] Recover an in-doubt XA transaction and prove zero money lost (T.5/T.8)
- [ ] Triage a dead MQ channel and a full DLQ in <30 min (MQ.4)
- [ ] Diagnose a hung prod JVM from javacore alone in <15 min (Lab 3.2)
- [ ] Renew an expiring cert end-to-end without notes (Lab 4.2)
- [ ] Run `health-check-all.sh` cold on any VM and understand every output line (Lab 6.3)
- [ ] Give a 3-minute sizing justification from Grafana data (PC.3b)
- [ ] Say "I don't know, but here's how I'd find out — IBM PMR, mustGather, traces, docs"

---

## 📋 DAILY LAB JOURNAL TEMPLATE (`~/lab-journal/YYYY-MM-DD.md`)

```markdown
# Lab Journal — YYYY-MM-DD

## Lab: [e.g., T.5 — XA Recovery Money Lab]

## Environment state
- dsb-dmgr: STARTED / STOPPED
- dsb-node02: STARTED / STOPPED
- Disk (df -h): [paste output]

## Commands run
\`\`\`bash
[exact commands]
\`\`\`

## Errors hit
- Error: [exact error message or code]
- Cause: [what caused it]
- Fix: [exact fix applied]

## Verification
- [how you proved it worked — psql output, grep result, curl response]

## Interview note
- [one sentence: what interview question this proves]

## Tomorrow
- [next lab or open issue]
```

---

## ⚠️ NON-LAB GAPS (Read 2–3 days, before interviews)

| Gap | What to Know |
|---|---|
| **Change management** | CRQ in ServiceNow, change windows (banks: 2am Sunday), rollback plans — prod requires approved CRQ |
| **Incident process** | Sev1/2/3 definitions, escalation chain, comms cadence ("bridge call"), post-mortems (5-whys) |
| **Runbooks** | `health-check-all.sh` + lab journal = your runbook seed. Banks want: symptom → steps → escalation path |
| **IBM PMR / mustGather** | How to open a PMR, what mustGather collects (javacores, heapdumps, traces, FFDC, config), how to read APARs and technotes |

---

## 🚀 START HERE — Lab 0.1

**Tonight:**
1. Provision all 7 VMs with static IPs.
2. Write `/etc/hosts` on every VM (all 7 entries).
3. Verify `ping dsb-elk` from `dsb-dmgr` responds.
4. Open your lab journal: `~/lab-journal/$(date +%Y-%m-%d).md`
5. Write your first entry.

Everything else in this program builds on those 7 VMs talking to each other.

---

*DigiStack WAS LAB Master Plan — Generated for standalone Claude Pro account use.*  
*Source: DigiStack Bank WAS ND 9.0.5.28 Administration Master Syllabus + P01/P02/P03 project files.*  
*Version: 1.0 | 27-week program | Solo-Job-Ready track*