# 📘 IHS (IBM HTTP Server) — Course in Correct Learning Order
**40 Days @ 1 hr/day | Designed by: Ox Alpha**
**Persona: 25 yrs IBM WebSphere/IHS Admin @ Citibank-style MNC Bank**

> 🔒 **Protected Full-Depth Modules:** Module 3 (Days 8–13), Module 5 (Days 15–19),
> Module 7 (Days 22–25) = 15 days = 37% of the plan
>
> **Why this order works:** You can't understand the Plugin (Day 15) without httpd.conf (Day 8);
> you can't do SSL (Day 22) without VirtualHosts (Day 10); troubleshooting (Day 36) only makes
> sense after Integration + SSL + Logging.

---

## 🟢 PHASE 1: WEB & ARCHITECTURE FOUNDATIONS — Module 0 (Days 1–3)

### Day 1 — Server Types
- Web server vs Application server vs Database server
- 💼 Banking: Where IHS sits in Citibank NetBanking architecture

### Day 2 — HTTP/HTTPS Request Flow
- Request/response flow, ports, IP, DNS, hostname
- Trace: Browser → Firewall → IHS → Plugin → WAS → DB

### Day 3 — HTTP Status Codes
- 200, 301, 302, 403, 404, 500, 502, 503 (with real IHS examples)
- Practice: `curl -v`, browser F12 trace

---

## 🟢 PHASE 2: IHS BASICS — Modules 1+2 (Days 4–7)

### Day 4 — IHS Introduction
- What is IHS (Apache-based); why IHS in front of WAS
- WebSphere Plugin as the bridge between web & app tier

### Day 5 — 3-Tier DMZ Architecture
- Banking regulatory rule: web tier in DMZ, app tier in trusted zone
- IHS architecture: httpd.conf, child processes, worker threads

### Day 6 — IHS Installation
- Installation Manager + silent install (response file)
- Managed vs unmanaged node concept

### Day 7 — Directory Structure & Core Files
- Key files: httpd.conf, error_log, access_log, pid
- bin/, conf/, logs/; binaries: apachectl, httpd, htpasswd
- Env vars: IHS_HOME, WAS_PLUGIN_HOME

---

## 🔒 PHASE 3: httpd.conf DEEP DIVE — Module 3 (Days 8–13)

### Day 8 — Global Directives
- ServerRoot, Listen, ServerName, User/Group
- LoadModule — how modules load

### Day 9 — Read httpd.conf Line by Line
- vi walkthrough of full file (80% of your daily job!)

### Day 10 — VirtualHosts — Theory
- Name-based vs IP-based
- 💼 Banking: retail.citibank.com + corporate.citibank.com on one IHS

### Day 11 — VirtualHosts — Hands-On
- Edit httpd.conf, add VirtualHost, configtest, restart, test

### Day 12 — DocumentRoot, Alias, Redirect, Include
- Directory blocks; modular httpd.conf design

### Day 13 — Thread Sizing Directives
- Timeout, KeepAlive, MaxClients/MaxConnections for bank peak load

---

## 🟢 PHASE 4: ADMINISTRATION — Module 4 (Day 14)

### Day 14 — IHS Administration + Revision
- `apachectl start | stop | restart | graceful | configtest`
- Graceful vs normal restart; IHS Admin Server; ps/netstat checks
- ✅ Week 2 recap quiz

---

## 🔒 PHASE 5: WEBSPHERE PLUGIN (THE HEART ❤️) — Module 5 (Days 15–19)

### Day 15 — plugin-cfg.xml — Part 1
- `<Config>`, `<Log>`, `<VirtualHostGroup>`, `<ServerCluster>`

### Day 16 — plugin-cfg.xml — Part 2
- `<Server>`, `<Transport>`, `<UriGroup>`, `<Route>` — every tag explained

### Day 17 — Load Balancing & Session Affinity
- Round Robin, weighted, sticky sessions / JSESSIONID
- RetryInterval, Primary vs Backup servers
- 💼 Banking: 4 app servers, 50K NetBanking users

### Day 18 — Generate & Propagate Plugin
- WAS Console: Servers → Web Servers → Generate Plug-in
- Manual copy vs automatic propagation

### Day 19 — Manual Edit of plugin-cfg.xml
- 10-yr level skill: hand-editing plugin safely
- What triggers regeneration (deploy/restart)

---

## 🟢 PHASE 6: END-TO-END INTEGRATION — Module 6 (Days 20–21)

### Day 20 — Full WAS–IHS Integration
- Install WAS → IHS → web server definition → generate/propagate plugin
- Deploy test app → access via IHS port (80/443)

### Day 21 — Verify Integration + Revision
- Read http_plugin.log; ripplestart web server; 503 diagnosis
- ✅ Week 3 recap

---

## 🔒 PHASE 7: SSL/HTTPS (FULL DEPTH 🔐) — Module 7 (Days 22–25)

### Day 22 — SSL/TLS Fundamentals
- Handshake, certificate chain, CA, self-signed, CSR
- One-way vs two-way SSL (bank-to-bank integrations — interview note)

### Day 23 — iKeyman / gskcmd Utilities
- CMS kdb, JKS/PKCS12 concepts
- `gskcmd -keydb -create`, `-certreq -create`, `-cert -receive`

### Day 24 — Full SSL Setup on IHS
- SSLEnable, KeyFile, SSLCipherSpec in httpd.conf
- 💼 Real CA cert flow for www.citibank.co.in (DigiCert/Symantec)

### Day 25 — Certificate Renewal & Verification
- Expiring certs = #1 cause of bank outages!
- `openssl s_client -connect host:443`
- Admin Console SSL equivalent

---

## 🟢 PHASE 8: LOGGING — Module 8 (Days 26–27)

### Day 26 — Logging — Part 1
- access_log, error_log, CustomLog, LogFormat

### Day 27 — Logging — Part 2
- Plugin log levels (Error/Warn/Trace); rotating logs
- 💼 RBI compliance — X-Forwarded-For full client IP logging
- `tail -f`, `grep`, `awk` basics

---

## 🟠 PHASE 9: SECURITY, REWRITE, HA & PERFORMANCE — Modules 9–12 (Days 28–35)

### Day 28 — ✅ Week 4 Revision
- Cert lifecycle recap + log analysis quiz

### Day 29 — Security Hardening — Part 1
- ServerTokens Prod, ServerSignature Off, disable TRACE
- Restrict HTTP methods

### Day 30 — Security Hardening — Part 2
- htpasswd Basic Auth (protect /admin), IP-based access control
- 💼 PCI-DSS — disable TLS 1.0/weak ciphers on 40 servers
- HTTP → HTTPS redirect

### Day 31 — mod_rewrite — Basics
- RewriteRule, RewriteCond, flags (R, L, P, PT)
- Rule 1: Force HTTPS | Rule 2: Old app → New app redirect

### Day 32 — Maintenance Page Use Case
- 💼 Banking: 2 AM EOD batch — rewrite all NetBanking to maintenance
  page except internal IPs

### Day 33 — High Availability
- Multiple IHS behind F5, stateless IHS, plugin backup servers
- 💼 IHS failure at 11 AM peak — no outage, DC+DR design

### Day 34 — Performance Tuning
- MPM: ThreadsPerChild, ServerLimit, MaxRequestWorkers
- KeepAlive for plugin traffic, mod_deflate, static caching
- 💼 10x festival spike — 20K concurrent connections

### Day 35 — ✅ Week 5 Revision
- Hardening checklist + rewrite rule practice

---

## 🔴 PHASE 10: TROUBLESHOOTING & OPERATIONS — Modules 13+14 (Days 36–39)

### Day 36 — Troubleshooting — 503 & 404
- Root cause flowcharts: stale plugin-cfg.xml, app server down
- IHS→Plugin→WAS port checks (9043, 9443, 9080, 9444)

### Day 37 — Troubleshooting — SSL & Startup Failures
- SSL handshake failures, cert expiry diagnosis
- IHS won't start: port conflict, permission, syntax error

### Day 38 — Troubleshooting — Performance Issues
- CPU/memory spike on IHS, core dump basics
- Log triage: error_log + http_plugin.log together

### Day 39 — Operations & Automation
- Daily/weekly admin checklist (config backup, cert expiry tracking)
- Silent installs, scripted plugin propagation, iFix/PMR with IBM

---

## 🏁 PHASE 11: INTERVIEW PREP — Day 40

### Day 40 — 🎓 Interview Prep
- Top 10 interview Q&A (10-yr level)
- War story: "Tell me about a production outage you handled"

---

## 📊 Order Summary Table

| Phase | Days | Module(s) | Key Skill |
|-------|------|-----------|-----------|
| 1️⃣ Web Foundations | 1–3 | M0 | HTTP clarity |
| 2️⃣ IHS Basics | 4–7 | M1, M2 | Install + files |
| 3️⃣ 🔒 httpd.conf Deep Dive | 8–13 | M3 | Read/edit config confidently |
| 4️⃣ Administration | 14 | M4 | Manage IHS |
| 5️⃣ 🔒 Plugin (Heart ❤️) | 15–19 | M5 | Routing + load balancing |
| 6️⃣ Integration | 20–21 | M6 | WAS↔IHS end-to-end |
| 7️⃣ 🔒 SSL/HTTPS | 22–25 | M7 | Cert lifecycle |
| 8️⃣ Logging | 26–27 | M8 | Log analysis |
| 9️⃣ Security/HA/Tuning | 28–35 | M9–M12 | Production-ready configs |
| 🔟 Troubleshooting/Ops | 36–39 | M13, M14 | RCA skills |
| 1️⃣1️⃣ Interview | 40 | 🎓 | Job-ready |

---

## ⚠️ Phase 2 — Recommended After Day 40
- 🎓 Capstone Project: 2 IHS + WAS ND clusters + SSL + HA + runbook
- 20 Interview Q&A | 20 Troubleshooting Qs | 20 Banking Scenarios
- 5 War Stories | 5 Leadership Qs | 5 Architecture Qs

---

## ✅ Rules of the Course
1. Miss a day → don't skip content, shift everything by 1
2. Revision days (14, 21, 28, 35) are sacred
3. Lab environment mandatory — doing = 90% retention vs reading = 40%
4. Every config change: `configtest` first, then graceful restart

**▶ Say "Start Day 1" to begin — Lesson 1: Web Basics — Server Types! 🚀**
