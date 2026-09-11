# 🔌 Course 7 (Revised) — WebSphere Resources: JMS, MQ, J2C — Zero to Expert
## ~26 Days @ 1 hr/day | Ox Alpha | WAS 8.5.5/9.0 ND | BankCell01

> **Note:** JDBC compressed to a 2-day bridge (separate dedicated course exists) — just enough to understand MQ/J2C wiring.
> **Format:** Theory 80% → Banking Scenario 15% → Interview 5% | **Console + wsadmin every topic**
> **Lab:** PaymentCluster (payments via MQ + PaymentDB), CustPortalCluster

---

## 📗 PHASE 1 — RESOURCE FOUNDATIONS (Days 1–3)

| Day | Topic |
|---|---|
| **1** | What is a "resource" in WAS? JNDI tree explained — the bank's internal phone directory |
| **2** | Resource providers vs instances; scope: cell/node/server (**the scope trap**) |
| **3** | WebSphere Variables — `${PAYMENT_DB_HOST}` — why hardcoding is a firing offense (console + wsadmin) |

---

## 📗 PHASE 2 — JDBC BRIDGE (⚡ Compressed — Days 4–5)

| Day | Topic |
|---|---|
| **4** | Quick recap: JDBC provider + DataSource + connection pool — concepts & JNDI lookup only (detail → separate course) |
| **5** | XA vs non-XA in 30 minutes — because MQ/J2C topics reference it constantly (payment+ledger example) |

---

## 📗 PHASE 3 — J2C AUTH & CREDENTIALS (Days 6–8)

| Day | Topic |
|---|---|
| **6** | J2C authentication aliases — DB & MQ service accounts (console + wsadmin) |
| **7** | Mapping alias to resources; container-managed auth; rotation with 2-alias trick |
| **8** | **LAB: Swap DB service account with zero downtime** |

---

## 📗 PHASE 4 — JMS IN WEBSPHERE (Days 9–13)

| Day | Topic |
|---|---|
| **9** | Messaging concepts: queues, topics, producers/consumers — bank branch letterbox analogy |
| **10** | WAS default messaging vs WebSphere MQ as JMS provider — when banks use which |
| **11** | SIB (Service Integration Bus): bus, bus members, destinations — full setup |
| **12** | JMS objects: QCF/TCF, queues/topics, activation specs vs listener ports (legacy!) |
| **13** | **LAB: Build a bus + queue + activation spec; send a test payment message** |

---

## 📗 PHASE 5 — WEBSPHERE MQ INTEGRATION (⭐ Bank Gold — Days 14–21, full pace)

| Day | Topic |
|---|---|
| **14** | MQ architecture: queue managers, channels (SVRCONN), listeners — mapped to WAS |
| **15** | Client mode vs bindings mode — performance vs flexibility trade-off |
| **16** | Creating MQ QCF + queue in WAS pointing to PaymentQM (console walkthrough) |
| **17** | Same via wsadmin (`AdminTask.createWMQConnectionFactory` etc.) — scripted |
| **18** | CCDT files & multi-QM failover (client channel definition table) |
| **19** | MQ SSL from WAS (cipher mapping WAS ↔ MQ names) |
| **20** | MQ user auth, CHLAEN rules, MCAUSER — the connection-refused trilogy |
| **21** | **LAB: End-to-end — PaymentApp puts a message → MQ → back-end reads it** |

---

## 📗 PHASE 6 — J2C RESOURCE ADAPTERS & BACKENDS (Days 22–23)

| Day | Topic |
|---|---|
| **22** | JCA resource adapters: CICS/IMS adapters (mainframe banking!), RAR deployment, connection factories |
| **23** | Mail/scheduler resources + URL providers (quick coverage) |

---

## 📗 PHASE 7 — ⭐ TROUBLESHOOTING (Days 24–26)

| Day | Scenario |
|---|---|
| **24** | MQ errors decoded: `2035` (auth), `2059` (QM unavailable), `2538` (listener), `2009` (connection lost) — full investigation flow |
| **25** | J2C/JMS issues: `J2CA0021E` connection leak, activation spec not consuming, `NameNotFoundException`, message backlog triage |
| **26** | 🧪 **Gauntlet:** 5 mock incidents from real logs (MQ channel down, alias wrong password, queue full `MQRC 2053`, JNDI lookup fails, messages stuck in flight) |

---

## 📊 Summary

| Phase | Days | Pace |
|---|---|---|
| 1. Foundations | 1–3 | ⚡ |
| 2. JDBC Bridge | 4–5 | ⚡ Compressed (own course has detail) |
| 3. J2C Auth | 6–8 | ⚡ |
| 4. JMS/SIB | 9–13 | 🐢 Full |
| 5. ⭐ MQ Integration | 14–21 | 🐢 Full — untouched |
| 6. J2C Adapters | 22–23 | ⚡ |
| 7. ⭐ Troubleshooting | 24–26 | 🐢 Full |
| **TOTAL** | **26 days** | |

---

## 🎯 After Course: Interview Mastery Pack (same structure as other courses)

| # | Pack | Count |
|---|---|---|
| 1 | Real interview Q&A (10-yr level) | 20 |
| 2 | "Recent issues you faced" answers | 5 |
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
| `START-C7` / `NEXT` | Move to next topic |
| `QUIZ` | Get assessed on current topic |
| `REVIEW <day#>` | Revisit a day |
| `EXERCISE` | Extra hands-on practice |

---
