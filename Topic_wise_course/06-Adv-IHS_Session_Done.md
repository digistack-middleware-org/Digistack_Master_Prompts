# 📘 MASTER COURSE: IHS + WebSphere Session Management (Merged)

> **Duration:** ~78 Days @ 1 hr/day (~11 weeks)
> **Persona:** 25 yrs IBM WebSphere/IHS Admin @ Citibank-style MNC Bank

---

## 🧭 Merge Logic

Follows the natural request flow:
**Web foundations → IHS → Plugin → Sessions → Replication → Failover → Troubleshooting → Interview**

- No topic is learned twice — the ~15% overlap is **merged, not repeated**
- **Deduplication savings:** 40 + 56 = 96 → **78 days (18 days saved)**

---

## 🟢 PHASE 1: WEB & ARCHITECTURE FOUNDATIONS — Days 1–
*(IHS M0 — no overlap, kept as-is)*

| Day | Content |
|-----|---------|
| 1 | Web server vs App server vs DB server • Where IHS sits in NetBanking architecture |
| 2 | HTTP/HTTPS flow, ports, DNS • Trace: Browser → Firewall → IHS → Plugin → WAS → DB |
| 3 | Status codes 200/301/302/403/404/500/502/503 • `curl -v`, F12 |

---

## 🟢 PHASE 2: IHS BASICS — Days 4–7
*(IHS M1 + M2)*

| Day | Content |
|-----|---------|
| 4 | What is IHS, why in front of WAS, Plugin as bridge |
| 5 | DMZ architecture, httpd.conf, processes/threads |
| 6 | Installation Manager + silent install; managed vs unmanaged node |
| 7 | Directory structure, httpd.conf/error_log/access_log, binaries, env vars |

---

## 🔒 PHASE 3: httpd.conf DEEP DIVE — Days 8–13
*(IHS M3 — full depth)*

| Day | Content |
|-----|---------|
| 8 | ServerRoot, Listen, ServerName, LoadModule |
| 9 | Line-by-line httpd.conf walkthrough (80% of daily job!) |
| 10 | VirtualHosts theory • 🏦 retail + corporate on one IHS |
| 11 | VirtualHosts hands-on: edit → configtest → restart → test |
| 12 | DocumentRoot, Alias, Redirect, Include, modular config |
| 13 | Timeout, KeepAlive, MaxClients for bank peak load |

---

## 🟢 PHASE 4: ADMINISTRATION — Day 14

| Day | Content |
|-----|---------|
| 14 | apachectl start/stop/restart/graceful/configtest • ✅ Week 2 quiz |

---

## 🔒 PHASE 5: WEBSPHERE PLUGIN + AFFINITY ❤️ — Days 15–20
*(IHS M5 **MERGED** with Session M2 topics T10–T15 — one integrated view)*

| Day | Content |
|-----|---------|
| 15 | plugin-cfg.xml Part 1: `<Config>`, `<Log>`, `<VirtualHostGroup>`, `<ServerCluster>` |
| 16 | plugin-cfg.xml Part 2: `<Server>`, `<Transport>`, `<UriGroup>`, `<Route>` |
| 17 | Load balancing (Round Robin, weighted) **+ Session affinity theory (teller analogy)** |
| 18 | **Clone IDs decoded 🔥** — JSESSIONID `:` separator, sticky sessions end-to-end |
| 19 | Generate & propagate plugin (Console + manual) • hand-editing plugin safely |
| 20 | 🏦 **4 JVMs, 50K NetBanking users** — "your" JVM dies mid-transfer • Primary/Backup, RetryInterval |

> ✅ This is the heart of the merge — you learn plugin routing AND affinity as ONE instead of twice.

---

## 🟢 PHASE 6: SESSION FUNDAMENTALS + INTEGRATION — Days 21–24
*(IHS M6 **MERGED** with Session M1 T1–T9)*

| Day | Content |
|-----|---------|
| 21 | HTTP session basics (railway ticket), statelessness, why NetBanking needs sessions |
| 22 | JSESSIONID birth (Set-Cookie), sessions in JVM memory, login vs logout, timeout |
| 23 | Full WAS–IHS integration: web server definition → plugin → deploy test app → verify via 80/443 |
| 24 | 🔥 JVM restart = sessions LOST demo • http_plugin.log reading • 503 diagnosis • ✅ recap |

---

## 🔒 PHASE 7: SSL/HTTPS FULL DEPTH 🔐 — Days 25–28
*(IHS M7 — full depth; Session cookie security merged into Day 28)*

| Day | Content |
|-----|---------|
| 25 | Handshake, cert chain, CA, CSR, one-way vs two-way SSL |
| 26 | iKeyman/gskcmd: CMS kdb, `-keydb -create`, `-certreq`, `-cert -receive` |
| 27 | Full SSL setup: SSLEnable, KeyFile, SSLCipherSpec • 🏦 CA cert flow for www.citibank.co.in |
| 28 | Cert renewal + `openssl s_client` • **+ Session cookie security: Secure, HttpOnly, SSL-only cookies 🏦 (RBI), fixation protection** |

---

## 🟢 PHASE 8: LOGGING — Days 29–30

| Day | Content |
|-----|---------|
| 29 | access_log, error_log, CustomLog, LogFormat |
| 30 | Plugin log levels, rotation • 🏦 X-Forwarded-For (RBI) • `tail -f`, `grep`, `awk` |

---

## 🔒 PHASE 9: M-TO-M REPLICATION ⭐ — Days 31–37
*(Session M3 — zero overlap, full depth)*

| Day | Content |
|-----|---------|
| 31 | What is M-to-M (photocopy teller) • single vs multi-row topology |
| 32 | Replication domain • replica number/backup count |
| 33 | Modes (both/server/client) • timeout, frequency, disk offload |
| 34 | Console config step-by-step (cluster → replica domain) |
| 35 | wsadmin/Jython — DRSSettings via AdminConfig |
| 36 | Inside DRS internals |
| 37 | 🏦 PaymentCluster 4-JVM backup map + recap • ⚠️ Affinity ≠ Replication ≠ Persistence (trick Q decoded) |

---

## 🔒 PHASE 10: DB SESSION PERSISTENCE — Days 38–41
*(Session M4)*

| Day | Content |
|-----|---------|
| 38 | DB persistence (the vault) • 🏦 when banks choose DB over M-to-M |
| 39 | Prerequisites (schema, DataSource, JNDI) • persistence schedule |
| 40 | Performance reality • Console + wsadmin config |
| 41 | M-to-M vs DB decision guide + recap |

---

## 🟢 PHASE 11: TUNING, SIZING & SESSION SECURITY — Days 42–44
*(Session M5 + M6 remnants)*

| Day | Content |
|-----|---------|
| 42 | Fat sessions kill clusters • serialization cost • invalidation timeout, overflow |
| 43 | Tuning replica count/frequency/trigger • session cache vs store • 🏦 capacity math: 50K users |
| 44 | Security-integrated sessions • what survives failover vs forces re-login |

---

## 🟠 PHASE 12: IHS SECURITY, REWRITE, HA & PERFORMANCE — Days 45–52
*(IHS M9–M12)*

| Day | Content |
|-----|---------|
| 45 | ✅ Week 6 recap |
| 46 | Hardening 1: ServerTokens Prod, disable TRACE, restrict methods |
| 47 | Hardening 2: htpasswd, IP control • 🏦 PCI-DSS — kill TLS 1.0 on 40 servers • HTTP→HTTPS redirect |
| 48 | mod_rewrite: RewriteRule/RewriteCond, flags • Force HTTPS + old→new app redirect |
| 49 | 🏦 Maintenance page: 2 AM EOD batch rewrite except internal IPs |
| 50 | HA: IHS behind F5, plugin backups • 🏦 DC+DR 11 AM peak failure |
| 51 | Performance: MPM, ThreadsPerChild, KeepAlive, mod_deflate • 🏦 10x festival spike |
| 52 | ✅ Revision: hardening checklist + rewrite practice |

---

## 🔴 PHASE 13: FAILOVER DEEP-DIVE ⭐ — Days 53–55
*(Session M8)*

| Day | Content |
|-----|---------|
| 53 | 🏦 Full failover: JVM1 dies mid-transfer → user lands on JVM2 (whiteboard walk-through) |
| 54 | M-to-M failover timeline (ms reconstruction) vs DB persistence timeline • what's STILL lost |
| 55 | Anti-patterns that break failover • recap — **the #1 10-yr question** |

---

## 🔴 PHASE 14: TROUBLESHOOTING (IHS + SESSIONS MERGED) — Days 56–62
*(IHS M13 **MERGED** with Session M9 — unified RCA library)*

| Day | Content |
|-----|---------|
| 56 | 503 & 404 flowcharts: stale plugin-cfg.xml, app down • port checks (9043/9443/9080) |
| 57 | SSL handshake failures, cert expiry • IHS won't start: port/permission/syntax |
| 58 | Session scenarios S1–S2: random logouts at peak • rolling restart gone wrong |
| 59 | S3–S4: re-login loop on JVM2 (clone ID!) • UAT works PROD fails (config drift) |
| 60 | S5–S6: logout every 30 min • OutOfMemoryError from sessions |
| 61 | S7–S8: DRS not replicating • session collision after VM clone |
| 62 | S9–S10: 🏦 DataSource down → logins fail • 🏦 payment lost mid-transfer on failover |

---

## 🟢 PHASE 15: PROD OPERATIONS — Days 63–66
*(IHS M14 **MERGED** with Session M10)*

| Day | Content |
|-----|---------|
| 63 | 🏦 Rolling restart with zero session loss (drain) • pre-restart session checklist |
| 64 | 🏦 Zero-session-loss deployment • IHS config backup, cert expiry tracking, silent installs |
| 65 | Session monitoring (PMI, TPV) • UAT vs PROD comparison • 🏦 audit/timeout compliance |
| 66 | ✅ Recap: daily admin checklist — can you run PROD alone? |

---

## 🟦 PHASE 16: EXPERT DESIGN — Days 67–68
*(Session M11)*

| Day | Content |
|-----|---------|
| 67 | Design session strategy for 3-tier bank app • 🏦 DR across sites — what's realistic |
| 68 | Session sizing math (users × size × replicas) • 🎤 "Design for PaymentCluster" answer template |

---

## 🏁 PHASE 17: INTERVIEW MARATHON + MOCKS — Days 69–78

| Day | Content |
|-----|---------|
| 69 | Top 25 Q&A — IHS side (httpd.conf, VirtualHosts, SSL) |
| 70 | Top 25 Q&A — Session side (affinity, replication, failover) |
| 71 | Trick questions: affinity ≠ replication, restart ≠ failover, graceful ≠ normal |
| 72 | Whiteboard: User → IHS → Plugin → cloneID → Cluster with M-to-M (FULL stack) |
| 73 | STAR war stories: "production outage you handled" (IHS + session versions) |
| 74 | 🎤 MOCK 1 — Levels 1–2 (basics, affinity, replication) |
| 75 | 🎤 MOCK 2 — Levels 3–5 (failover, troubleshooting, design) |
| 76 | 🔄 Fast revision — IHS track |
| 77 | 🔄 Fast revision — Session track |
| 78 | 🏁 Final review + weak-topic patch-up + **COURSE COMPLETE 🎓** |

---

## 📊 What Was Merged / Deduplicated

| Original Overlap | Resolution |
|------------------|------------|
| IHS Day 17 (sticky sessions) ↔ Session T10–T13 | → Merged into Days 17–20 (one plugin + affinity story) |
| IHS Days 15–19 ↔ Session T46–T50 (plugin routing) | → Merged into Phase 5 |
| IHS Days 20–21 ↔ Session M1 (integration/503) | → Merged into Days 21–24 |
| IHS Days 22–25 ↔ Session T42–T44 (SSL cookies) | → Cookie security folded into Day 28 |
| IHS Day 36 ↔ Session S-scenarios | → Unified troubleshooting library (Days 56–62) |
| IHS Day 39 ↔ Session T66–T71 (ops) | → Merged Days 63–66 |
| Session M12 + IHS Day 40 interview prep | → Combined 10-day interview phase |
| Duplicate recap days (4 + 5 separate ones) | → Consolidated |

**Saved: ~18 days** ⏱️

---

## ✅ Master Course Summary

| Item | Value |
|------|-------|
| **Duration** | 78 days @ 1 hr/day (~11 weeks) |
| **Covers** | IHS complete + WebSphere Session Management complete |
| **Method** | 80% Theory / 15% 🏦 Banking / 5% 🎤 Interview — every topic with Console + wsadmin |
| **Lab rule** | Mandatory lab; Console + Jython practice on every topic |
| **Goal** | Run IHS + WAS PaymentCluster PROD **alone** + crack a 10-yr experience interview |
| **vs Two courses** | 96 days → **78 days** (18 days saved, zero content lost) |

---

## 🔑 Why This Merge Order Works (Interview Logic)

> You learn the **exact path a request travels**, in order:
>
> **Browser → IHS (Days 1–14) → Plugin + cloneID (15–20) → Session born in JVM (21–24) → SSL secure cookie (25–28) → Replication keeps it safe (31–37) → DB fallback (38–41) → Tuning for scale (42–44) → Hardening the web tier (45–52) → Failover story (53–55) → Break-fix everything (56–62) → Run PROD (63–66) → Design it (67–68) → Win the interview (69–78)**

By the time you reach failover (Day 53), you already know httpd.conf, plugin-cfg.xml,
clone IDs, and replication — so the full whiteboard story flows naturally.
**No course does this; most people learn these as disconnected islands.**

---

## ✅ Rules of the Master Course

1. **Miss a day** → don't skip, shift everything by 1
2. **Revision days (14, 24, 28, 37, 41, 45, 52, 55, 66)** are sacred
3. **Lab mandatory** — doing = 90% retention vs reading = 40%
4. Every config change: **`configtest` first → graceful restart**
5. Every session config: **Console + wsadmin both** — never one only
6. Days 15–20 (Plugin + Affinity) and 53–55 (Failover) are your **money days** — revise them twice

---

## 📊 Phase → Days Quick Map

| Phase | Days | Focus |
|-------|------|-------|
| 1️⃣ Web Foundations | 1–3 | HTTP clarity |
| 2️⃣ IHS Basics | 4–7 | Install + files |
| 3️⃣ 🔒 httpd.conf | 8–13 | Config mastery |
| 4️⃣ Administration | 14 | Manage IHS |
| 5️⃣ 🔒 Plugin + Affinity ❤️ | 15–20 | MERGED heart |
| 6️⃣ Sessions + Integration | 21–24 | MERGED |
| 7️⃣ 🔒 SSL + Cookie Security | 25–28 | MERGED |
| 8️⃣ Logging | 29–30 | Log analysis |
| 9️⃣ 🔒 M-to-M ⭐ | 31–37 | Replication |
| 🔟 DB Persistence | 38–41 | The vault |
| 1️⃣1️⃣ Tuning + Session Security | 42–44 | Sizing |
| 1️⃣2️⃣ IHS Sec / HA / Perf | 45–52 | Production-ready |
| 1️⃣3️⃣ Failover ⭐ | 53–55 | #1 interview Q |
| 1️⃣4️⃣ Troubleshooting | 56–62 | MERGED RCA |
| 1️⃣5️⃣ PROD Ops | 63–66 | MERGED |
| 1️⃣6️⃣ Expert Design | 67–68 | Lead level |
| 1️⃣7️⃣ Interview + Mocks 🏁 | 69–78 | Job-ready |

---

## ⚠️ PHASE 2 — After Day 78 (Capstone)

- 🎓 **Capstone project:** 2× IHS behind F5 + WAS ND PaymentCluster (4 JVMs) + M-to-M replication + SSL end-to-end + maintenance page rewrite + full production runbook
- **80-item bonus pack:** 20 interview Q&A • 20 banking scenarios • 20 troubleshooting cases • 5 STAR war stories • 5 leadership questions • 5 architecture designs • 5 "recent production issues"

---