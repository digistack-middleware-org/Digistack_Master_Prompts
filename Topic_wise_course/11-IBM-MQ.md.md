# 📅 IBM MQ for Senior WebSphere Admin — 30-Day Plan

> **Role:** WebSphere Admin who manages MQ as part of the WAS ecosystem (10-yr level)
> **Daily commitment:** 2–3 hours (1.5 hr theory + 1 hr hands-on)
> **Setup:** Linux VM / Docker with IBM MQ Developer Edition (free) + WebSphere Liberty (free)
> **Daily structure:** Theory → Banking Scenario → Hands-on Lab → 🎤 Interview Point

---

## 🗓️ WEEK 1: MQ FUNDAMENTALS

### Day 1 – Environment Setup + What is MQ
- **Theory:** Why MQ exists (guaranteed delivery, async, decoupling); MQ vs REST vs Kafka (bank context)
- **Lab:** Install MQ Developer edition in Docker/Linux VM; verify with `dspmqver`
- **Bank lens:** NEFT payment flow — payment server down 1 hour, money never lost
- 🎤 *Why does a bank use MQ instead of REST?*

### Day 2 – Core MQ Objects
- **Theory:** Queue Manager, Local Queue, Remote Queue, XMITQ, DLQ, Alias Queue, Listener, Channel
- **Lab:** `crtmqm QM1`, `strmqm`, create QL.TEST, QL.DEAD, run listener
- **Bank lens:** Salary file queue, payment queue, DLQ — map them on paper
- 🎤 *Explain Queue Manager in simple words*

### Day 3 – Messages & Message Properties
- **Theory:** Persistent vs non-persistent, MsgId, CorrelId, Expiry, Priority, Backout count (concept only)
- **Lab:** `amqsput` / `amqsget` — first messages; browse with `amqsbcg`
- **Bank lens:** Fund transfer = persistent; stock price feed = non-persistent
- 🎤 *When would you allow non-persistent messages in a bank?*

### Day 4 – Bindings vs Client Mode ⭐ CRITICAL
- **Theory:** Bindings mode (same box, shared memory) vs Client mode (network, SVRCONN)
- **Lab:** Connect a test client in client mode to your QMGR
- **Bank lens:** WAS+QMGR same server = bindings; WAS cluster → central MQ servers = client mode
- 🎤 *Bindings vs client mode — #1 WAS admin MQ question*

### Day 5 – Daily Commands Every WAS Admin Uses
- **Theory:** `strmqm`, `endmqm` (-w, -i, -p), `dspmq`; DISPLAY QLOCAL, QMSTATUS, CHSTATUS; `dmpmqcfg` backup
- **Lab:** Create 5 queues, check depths, stop/start QMGR all 3 ways, backup config
- **Bank lens:** App says "MQ down" — your 5-minute proof checklist
- 🎤 *How do you check queue depth and QMGR status?*

### Day 6 – Revision + Mini Test
- Rewrite all commands from memory; explain QMGR/queue/channel/listener aloud
- **Lab:** Kill QMGR → restart → verify reconnect, without notes
- ✅ **Gate:** QMGR + queues + test messages in 30 min flat?

---

## 🗓️ WEEK 2: WAS ↔ MQ INTEGRATION (CORE STRENGTH)

### Day 7 – JMS Resources in WAS Part 1
- **Theory:** JMS Provider, Queue Connection Factory (QCF), Queue destinations, JNDI lookup flow
- **Lab:** Install WebSphere Liberty/trial → create JMS resources pointing to Day-1 QMGR
- **Bank lens:** Internet banking app's QCF → payment queue
- 🎤 *Walk me through configuring MQ in WAS from scratch*

### Day 8 – JMS Resources in WAS Part 2
- **Theory:** Activation Spec vs Listener Port (legacy); connection/session pool (maxConnections, agedTimeout, reapTime)
- **Lab:** Configure activation spec; deploy tiny test app sending messages
- **Bank lens:** Pool exhaustion on salary day — maxConnections < app threads = customer timeouts
- 🎤 *Listener port vs activation spec?*

### Day 9 – CCDT & Connection Details
- **Theory:** Client Channel Definition Table; QCF fields (host/port/channel/QMGR) vs CCDT URL
- **Lab:** Connect WAS to QMGR via explicit properties, then via CCDT file
- **Bank lens:** Banks use CCDT so channel changes don't need WAS config changes
- 🎤 *What is CCDT and why do banks use it?*

### Day 10 – Troubleshooting WAS–MQ Errors Part 1
- **Theory:** 2059 (QMGR unavailable), 2538 (host not available), 2009 (connection broken); reading SystemOut.log + AMQERR01.log together
- **Lab:** Break it on purpose — stop listener (2538), stop QMGR (2059) — read, fix
- **Bank lens:** The 3-way blame game (WAS vs MQ vs Network) — logs end the argument
- 🎤 *App can't connect to MQ — walk me through your troubleshooting*

### Day 11 – Troubleshooting Part 2: Security Errors
- **Theory:** JMSWMQ2013, 2035 (not authorized), MQCSP auth, CHLAUTH (BLOCKUSER, address blocks)
- **Lab:** Enable CONNAUTH, block a user, see failure, fix credentials in QCF
- **Bank lens:** MQ hardening Sunday → 50 apps fail Monday with 2013 — happens in EVERY bank
- 🎤 *What is MQCSP? Why did apps break after MQ 8 upgrade?*

### Day 12 – SSL/TLS Between WAS and MQ
- **Theory:** WAS truststore/keystore vs MQ key repository; CipherSuite vs CipherSpec mismatch
- **Lab:** Self-signed certs → TLS on SVRCONN channel → connect WAS securely
- **Bank lens:** Cert expiry outage — AMQ9637, cipher mismatch after patching
- 🎤 *How do you configure SSL between WAS and MQ?*

### Day 13 – Version Compatibility (RA / Client Jars)
- **Theory:** WAS MQ Resource Adapter versions; MQ client jar compatibility matrix; updating RA in WAS
- **Lab:** Document your WAS version → supported MQ client level (make a cheat sheet)
- **Bank lens:** WAS patching weekend → apps fail → RA version mismatch — 2 AM fix story
- 🎤 *WAS patched, now MQ connections fail — what do you check first?*

### Day 14 – Revision + Break-Fix Marathon
- Break setup 5 ways: dead QMGR, wrong password, expired cert, stopped channel, pool exhaustion
- Fix all 5 without notes; time yourself
- ✅ **Gate:** End-to-end WAS→MQ config + 5 failure modes fixed?

---

## 🗓️ WEEK 3: MQ OPERATIONS YOU OWN

### Day 15 – Transactions & Message Reliability
- **Theory:** Syncpoint, JTA, XA/two-phase (conceptual); poison messages — BOTHRESH, BOQNAME
- **Lab:** Send a "bad" message → watch backout loop → set backout queue → verify
- **Bank lens:** Loan app redelivery storm → WAS CPU 90% → backout config saves the day
- 🎤 *What is a poison message and how do you handle it?*

### Day 16 – Channel Operations
- **Theory:** SVRCONN channels; states RUNNING/RETRYING/STOPPED; START/STOP/RESET; RESOLVE CHL; HBINT, MAXINST
- **Lab:** Stop/start channels, force RETRYING state, recover it
- **Bank lens:** Nightly 2 AM channel RETRYING — firewall idle timeout — HBINT fix CR
- 🎤 *Channel in RETRYING state — what do you do?*

### Day 17 – DLQ Handling (Hands-on Heavy Day)
- **Theory:** DLQ message structure, dead letter header, reason codes (2085, 2053, 2035); `runmqdlq` rules table
- **Lab:** Force 3 messages into DLQ, identify reasons, replay one safely
- **Bank lens:** 500 salary messages in DLQ — reason 2085 — app typo'd queue name — you replay = hero
- 🎤 *Walk me through a real DLQ incident*

### Day 18 – Monitoring & Health Checks
- **Theory:** Queue depth alerts, Q_DEPTH_HIGH events, channel status; WAS PMI ↔ MQ correlation
- **Lab:** Write a health-check script (qdepth + channel + listener) — keep it forever
- **Bank lens:** 80% depth alert before payments back up — auto-ticket to ServiceNow
- 🎤 *How do you proactively monitor MQ from the WAS side?*

### Day 19 – HA/DR from WAS Admin Seat
- **Theory:** Multi-Instance QMGR concept; WAS-side reconnect (clientReconnectTimeout, stale connections)
- **Lab:** Document/simulate QMGR restart → verify WAS recovers connections
- **Bank lens:** DR drill — QMGR failover 2 min, WAS held dead connections 10 min — QCF fix = best story
- 🎤 *QMGR fails over — what happens on the WAS side?*

### Day 20 – Trace & Evidence Gathering
- **Theory:** `strmqtrc` / `endmqtrc`, `runmqras`; proving client-side vs queue-side with evidence
- **Lab:** Trace a failing connection, read trace highlights
- **Bank lens:** Ending WAS-vs-MQ blame game with trace proof
- 🎤 *How do you collect MQ traces when IBM asks for evidence?*

### Day 21 – Revision + Full Ops Drill
- Simulate full morning routine: QMGRs, channels, depths, DLQ, WAS logs — daily runbook
- ✅ **Gate:** DLQ + channel + security scenarios without notes?

---

## 🗓️ WEEK 4: SENIOR-LEVEL JUDGMENT

### Day 22 – RCA Writing
- **Theory:** RCA structure: timeline → root cause → impact → fix → preventive actions
- **Lab:** Pick your best lab failure → write a full professional RCA
- **Bank lens:** "Payments slow at month-end" — pool undersized — your name on the RCA
- 🎤 *Tell me about an RCA you wrote*

### Day 23 – Change Management & Bank Discipline
- **Theory:** CR process, CAB, freeze windows (salary days, month/quarter-end); pre-change checklist (dmpmqcfg, WAS backup, rollback plan)
- **Bank lens:** Why nothing changes in a bank between 25th–5th
- 🎤 *Describe your change process for a WAS-MQ change*

### Day 24 – Coordination & Ownership
- **Theory:** Working with MQ/app/network teams without blame; recurring P3 → permanent fix; WAS↔MQ connection matrix
- **Lab:** Build the connection matrix for your lab — real artifact
- 🎤 *How do you handle it when MQ team says it's a WAS issue?*

### Day 25 – Upgrade Awareness
- **Theory:** MQ 8 → 9 upgrade: WAS app impact, RA compatibility, phased approach; your validation checklist + rollback
- **Bank lens:** 300 QMGRs upgraded in waves over 6 months — WAS validation each wave
- 🎤 *MQ team is upgrading — what do YOU do as WAS admin?*

### Day 26 – War Story Building
- Convert 5 best lab incidents into STAR format (Situation, Task, Action, Result)
- Stories: 2013 flood, cert expiry, pool exhaustion, DR reconnect, DLQ salary replay
- Practice each under 2 minutes — these = "recent issues you faced" answers

### Day 27 – Capstone Lab Day
- Full rebuild, timed: QMGR → queues → TLS → WAS JMS config → test message → break → fix
- ✅ Target: under 2 hours total — this lab IS your interview confidence

### Day 28 – Mock Interview Round 1
- Answer 20 interview questions aloud; record yourself; note weak areas
- Redo weak topics same day

### Day 29 – Scenario + Troubleshooting Drill
- Practice scenario + troubleshooting question sets aloud
- For each: answer → bank scenario → prevention

### Day 30 – Final Polish
- Behavioral (5) + design (5) question sets
- Review: RCA, connection matrix, runbook, war stories, command cheat sheet
- ✅ **You're interview-ready**

---

## 📌 Rules for Success

1. **Never skip labs** — reading without doing = you sound like 2 years, not 10
2. **Keep 4 artifacts:** RCA sample, connection matrix, health-check script, 5 war stories
3. **Every day ends:** 🎤 interview point spoken aloud once
4. **Missed a day?** Shift the plan — consistency beats speed
5. After Day 30 → ask for **Phase 5 Q&A sets** (20 interview Q&A, 20 scenarios, 20 troubleshooting, 5 war stories, 5 behavioral, 5 design, 5 "recent issues")
