# 📅 SSL Course — Day-Wise Plan
## WebSphere SSL: Browser → WAS → LDAP → MQ → DB | Zero to Expert | 1 hr/day | ~43 days ≈ 9 weeks
**WAS 8.5.5 / 9.0 ND | BankCell01 (DMGR, Node01/Node02, PaymentCluster, CustPortalCluster)**

> **Goal:** Crack a 10-yr level interview AND run SSL alone in production without help.
> **Per-topic mix:** Theory 80% → Real Banking Scenario 15% → Interview 5% (Basic→Expert)
> **Every topic:** Admin Console steps + wsadmin steps

---

## 🗓️ WEEK 1 — SSL Foundations I (Days 1–5)

| Day | Topic | Coverage |
|---|---|---|
| **1** | 1–2 | What is SSL/TLS (vault handshake analogy); symmetric vs asymmetric encryption |
| **2** | 3–4 | What's inside a certificate (CN, SAN, issuer, validity); public/private key pairs |
| **3** | 5–6 | CA vs self-signed (bank rules); what a truststore really means |
| **4** | 7–8 | Handshake step-by-step; TLS versions & cipher suites (why banks ban TLSv1.0) |
| **5** | 9–10 | One-way vs mutual SSL; **LAB: create & inspect cert with iKeyman + keytool** |

---

## 🗓️ WEEK 2 — Keystores & Cert Management (Days 6–10)

| Day | Topic | Coverage |
|---|---|---|
| **6** | 11–12 | Keystore vs Truststore (the #1 confusion); PKCS12/JKS/CMS-KDB types |
| **7** | 13–14 | iKeyman vs keytool vs openssl — when to use which; WAS default certs (DefaultPersonal/DefaultRoot) |
| **8** | 15–16 | Chained certs (root→intermediate→server); importing signer certs (console + wsadmin) |
| **9** | 17–18 | CSR generation in WAS + bank CA signing; cert expiry monitoring script |
| **10** | 19–20 | SAN/wildcard certs for PaymentCluster; **LAB: full CSR→sign→replace default cert on DMGR** |

---

## 🗓️ WEEKS 3–4 — WebSphere SSL Internals (Days 11–16)

| Day | Topic | Coverage |
|---|---|---|
| **11** | 21 | SSL config objects in security.xml — SSLConfig, key sets, the object model |
| **12** | 22 | SSL config groups & scopes: cell→node→server inheritance (**the scope trap**) |
| **13** | 23–24 | Outbound vs inbound SSL — how WAS picks a config; admin console SSL (9043) |
| **14** | 25–26 | Node agent ↔ DMGR SSL (sync/file transfer failures); CSIv2/SAS — SSL for EJB/IIOP |
| **15** | 27–28 | Safe default-cert replacement procedure; dynamic outbound endpoint (`ssl.Default`) |
| **16** | 29–30 | Cert expiry = classic outage; **console + wsadmin: getSSLConfig, createSSLConfig, change key/trust** |

---

## 🗓️ WEEKS 5–6 — Full Chain: Browser → IHS → Plugin → WAS (Days 17–22)

| Day | Topic | Coverage |
|---|---|---|
| **17** | 31 | Browser → IHS: enabling HTTPS 443, virtual host mapping |
| **18** | 32–33 | IHS keystore (kdb/GSKit) — server cert + chain; plugin-cfg.xml routing basics |
| **19** | 34 | **Plugin → WAS SSL**: plugin keyring, truststore, Secure transport in plugin-cfg |
| **20** | 35 | End-to-end HTTPS trace: browser dev tools + plugin log + SystemOut |
| **21** | 36–37 | Virtual hosts & host aliases (9080/9443); common breaks: plugin cert mismatch, SSLHandshakeException in plugin log |
| **22** | 38 | **LAB: full HTTPS IHS → PaymentCluster with valid chain** |

---

## 🗓️ WEEKS 7–8 — Backend SSL: WAS → LDAP, MQ, DB (Days 23–28)

| Day | Topic | Coverage |
|---|---|---|
| **23** | 39 | WAS → LDAP over SSL (ldaps:636): signer import, test, why login dies on expiry |
| **24** | 40 | WAS → MQ SSL: **cipher suite name mapping WAS ↔ MQ (the notorious mismatch)** |
| **25** | 41–42 | WAS → DB2/Oracle over SSL: JDBC URL props, per-DataSource truststore; J2C alias + SSL |
| **26** | 43–44 | Certificate-based DB auth (no passwords); scoped outbound SSL configs per backend |
| **27** | 45 | **LAB: enable ldaps for registry + SSL DataSource for PaymentDB** |
| **28** | — | 🧪 Backend SSL assessment: quiz + fix a broken ldaps config |

---

## 🗓️ WEEKS 9–11 — ⭐ SSL TROUBLESHOOTING MASTERY (Days 29–36, full pace)

*Each: Symptom → Causes → Investigation → Logs (SSLTrace, SystemOut codes) → Fix → Validation*

| Day | Scenario | Theme |
|---|---|---|
| **29** | 46 | `SSLHandshakeException: certificate_unknown` — trust missing (the daily classic) |
| **30** | 47 | **Certificate expired** — console + app + backend all down at once (war story) |
| **31** | 48 | `unable to find valid certification path` — incomplete chain (intermediate missing) |
| **32** | 49 | Hostname verification failure — CN/SAN mismatch (CertPathChecker) |
| **33** | 50 | `no cipher suites in common` — TLS version/cipher mismatch diagnosis |
| **34** | 51–52 | Node agent won't sync after cert change; plugin log handshake failure → 503 on HTTPS only |
| **35** | 53–54 | LDAP SSL broke → nobody can login (signer renewal under pressure); MQ channel cipher mismatch walkthrough |
| **36** | 55–56 | Enabling SSL trace (`-Djavax.net.debug`) + reading handshake dumps; 🧪 **Gauntlet: 5 mock SSL outages from real logs** |

---

## 🗓️ WEEK 12 — Production Procedures & Hardening (Days 37–40)

| Day | Topic | Coverage |
|---|---|---|
| **37** | 57 | Zero-downtime cert renewal — full cutover plan (backup→import→swap→validate) |
| **38** | 58 | Disable TLSv1.0/1.1 & weak ciphers — audit compliance (console + ssl.xml + JVM props) |
| **39** | 59–60 | Cert expiry monitoring automation across all nodes; DR keystore/cert parity (split-brain SSL) |
| **40** | 61 | Bank ITIL change flow: ticket → approval → backup → change → validate → close |

---

## 🗓️ WEEK 13 — Expert / Lead Capstone (Days 41–43)

| Day | Topic | Coverage |
|---|---|---|
| **41** | 62 | Multi-cell SSL architecture: DMGR chains, plugin trust, PROD/UAT/DR design |
| **42** | 63 | End-to-end encryption strategy: browser→IHS→WAS→LDAP→MQ→DB — **draw & defend it (Lead interview)** |
| **43** | 64 | **FINAL PROJECT: secure BankCell01 fully** — replace all default certs, TLSv1.2 only, ldaps + SSL DB, expiry monitoring, DR key parity |

---

## 📊 Summary

| Block | Days | Pace |
|---|---|---|
| SSL Foundations | 1–5 | 🐢 Full |
| Keystores & Certs | 6–10 | 🐢 Full |
| WAS SSL Internals | 11–16 | 🐢 Full |
| Browser→IHS→Plugin→WAS | 17–22 | 🐢 Full |
| Backends (LDAP/MQ/DB) | 23–28 | 🐢 Full |
| ⭐ Troubleshooting | 29–36 | 🐢 Full — untouched |
| Procedures/Hardening | 37–40 | ⚡ Compressed |
| Capstone | 41–43 | ⚡ Compressed |
| **TOTAL** | **43 days ≈ 9 weeks** | |

---

## 🎯 PHASE 9 — INTERVIEW MASTERY PACK (after Day 43)

| # | Pack | Count |
|---|---|---|
| 1 | Real interview Q&A (10-yr level) | 20 |
| 2 | "Recent SSL issues you faced" answers | 5 |
| 3 | Banking scenario-based questions | 20 |
| 4 | Troubleshooting-based questions | 20 |
| 5 | Outage war stories (STAR format) | 5 |
| 6 | Behavioral/Leadership questions | 5 |
| 7 | Architecture/Design (Lead level) | 5 |

---

## ✅ Completion Rule Per Topic

- [ ] Theory understood
- [ ] Console step done
- [ ] wsadmin step done
- [ ] Banking scenario discussed
- [ ] Interview Qs answered
- [ ] → Say `NEXT`

## 🔖 Commands

| Command | Action |
|---|---|
| `START` / `NEXT` | Move to next topic |
| `QUIZ` | Get assessed on current topic |
| `REVIEW <topic#>` | Revisit a topic |
| `EXERCISE` | Extra hands-on practice |

---
