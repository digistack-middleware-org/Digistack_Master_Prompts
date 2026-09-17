# 📅 WebSphere Session Management — MODULE-WISE 56-DAY PLAN (GitHub Markdown)

> **Environment:** BankCell01 → DMGR → Node01/Node02 → PaymentCluster (PaymentJVM01–04) → IHS01/IHS02 → PaymentApp.ear
>
> **Method per topic:** 80% Theory | 15% Real Banking Scenario | 5% Interview
> Every topic includes: Admin Console steps + wsadmin/Jython steps
>
> **Rule:** One topic at a time. No skipping. Move module by module.

---

## 🟦 MODULE 1 — Session Basics (Days 1–4) | Topics T1–T9

| Day | Topics |
|-----|--------|
| 1 | T1: HTTP session (railway ticket) • T2: Why web needs sessions (stateless • T3: 🏦 Net-banking login MUST have session |
| 2 | T4: JSESSIONID (name tag) • T5: How session is born (Set-Cookie) • T6: Sessions live in JVM memory |
| 3 | T7: Session timeout (30 min default) • T8: Login vs logout (create vs invalidate) |
| 4 | T9: 🔥 JVM restart = sessions LOST + Console/wsadmin demo |

✅ **Exit:** You can explain JSESSIONID, timeout, and session lifecycle.

---

## 🟦 MODULE 2 — Clustering & Affinity (Days 5–8) | Topics T10–T15

| Day | Topics |
|-----|--------|
| 5 | T10: Single server vs cluster • T11: Session affinity (teller who knows you) |
| 6 | T12: plugin-cfg.xml & clone IDs • T13: Sticky sessions decoded |
| 7 | T14: 🏦 "Your" JVM dies mid-transfer • T15: ⚠️ Affinity vs Replication vs Persistence |
| 8 | 🔄 Module 2 recap + Console/wsadmin practice |

✅ **Exit:** You can explain affinity + clone IDs + plugin routing.

---

## 🟦 MODULE 3 — Memory-to-Memory (M-to-M) Replication ⭐ (Days 9–15) | Topics T16–T25

| Day | Topics |
|-----|--------|
| 9 | T16: What is M-to-M replication (photocopy teller) • T17: Single replica vs multi-row topology |
 10 | T18: Replication domain • T19: Replica number / backup count |
| 11 | T20: Replication modesboth/server/client) • T21: Timeout, frequency, disk offload |
| 12 | T22: Admin Console config step-by-step (cluster → replica domain) |
| 13 | T23: wsadmin/Jython — DRSSettings via AdminConfig |
| 14 | T24: Inside DRS — Data Replication Service internals |
| 15 | T25: 🏦 PaymentCluster 4-JVM backup map + Module 3 recap |

✅ **Exit:** Configure M-to-M in Console + wsadmin from memory.

---

## 🟦 MODULE 4 — DB Session (Days 16–20) | Topics T26–T33

| Day | Topics |
|-----|--------|
| 16 | T26: DB session persistence (the vault) • T27: 🏦 When banks choose DB over M-to-M |
| 17 | T28: Prerequisites (schema, DataSource, JNDI) • T29: Persistence schedule (end-of-service/time/manual) |
| 18 | T30: Performance reality (DB overhead) • T31: Admin Console config step-by-step |
| 19 | T32: wsadmin/Jython config steps • T33: 🏦 M-to-M vs DB comparison + decision guide |
| 20 | 🔄 Module 4 recap + practice |

✅ **Exit:** Choose M-to-M vs DB for any bank app — and justify it.

---

## 🟦 MODULE 5 — Tuning & Sizing (Days 21–24) | Topics T34–T40

| Day | Topics |
|-----|--------|
| 21 | T34: Fat sessions kill clusters • T35: Serialization cost |
| 22 | T36 Invalidation timeout, max in-memory, overflow • T37: Tuning replica count, frequency, trigger |
| 23 | T38: Session cache vs store • T39: Console tuning steps |
| 24 | T40: 🏦 Capacity math — 50,000 users + Module 5 recap |

✅ **Exit:** Size PaymentCluster like a PROD admin.

---

## 🟦 MODULE 6 — Security & Sessions (Days 25–27) | Topics T41–T45

| Day | Topics |
|-----|--------|
| 25 | T41: Security-integrated sessions • T42: Cookie settings (Secure, HttpOnly, domain, path) |
| 26 | T43: SSL-only cookies 🏦 (RBI/regulator) • T44: Session fixation protection |
| 27 | T45: What survives failover vs forces re-login + Module 6 recap |

✅ **Exit:** Quote cookie security + fixation protection in an interview.

---

## 🟦 MODULE 7 — Plugin / IHS Integration (Days 28–30) | Topics T46–T50

| Day | Topics |
|-----|--------|
| 28 | T46: Clone ID & ":" separator decoded • T47: Plugin routing logic |
| 29 | T48: Affinity after plugin regeneration • T49: Broken affinity — symptoms & causes |
| 30 | T50: Cookie path, URI group, virtual host + Module 7 recap |

✅ **Exit:** Explain IHS → Plugin → Cluster routing end-to-end.

---

## 🟦 MODULE 8 — Failover Deep-Dive ⭐ (Days 31–34) | Topics T51–T55

| Day | Topics |
|-----|--------|
| 31 | T51: 🏦 Fullover walk-through: JVM1 dies mid-transfer → user lands on JVM2 |
| 32 | T52: M-to-M failover timeline (millisecond reconstruction) • T53: DB persistence failover timeline |
| 33 | T54: What is STILL lost even with replication • T55: Anti-patterns that break failover |
| 34 | 🔄 Module 8 recap + whiteboard practice |

✅ **Exit:** Narrate a full failover story — the #1 10-yr interview question.

---

## 🟦 MODULE 9 — Troubleshooting: 10 Scenarios ⭐⭐ (Days 35–40) | Topics T56–T65

| Day | Topics |
|-----|--------|
| 35 | S1 (T56): Random logouts at peak load • S2 (T57): Rolling restart gone wrong |
| 36 | S3 (T58): Re-login loop on JVM2 (clone ID) • S4 (T59): UAT works, PROD fails (config drift) |
| 37 | S5 (T60): Logout every 30 min despite activity • S6 (T61): OutOfMemoryError from sessions |
| 38 | S7 (T62): DRS errors — not replicating • S8 (T63): Session collision after VM clone |
| 39 | S9 (T64): DataSource down → all logins fail • S10 (T65): 🏦 Payment lost mid-transfer on failover |
| 40 | 🔄 Module 9 recap — all 10 scenarios: Symptoms → Logs → Console → wsadmin → Fix |

✅ **Exit:** Troubleshoot any session issue alone — without Google. **This is your goal.**

---

## 🟦 MODULE 10 — Banking PROD Procedures (Days 41–44) | Topics T66–T71

| Day | Topics |
|-----|--------|
| 41 | T66: 🏦 Rolling restart zero session loss (drain) • T67: Pre-restart session impact checklist |
| 42 | T68: Zero-session-loss deployment • T69: Session monitoring (PMI, TPV) |
| 43 | T70: UAT vs PROD config comparison • T71: 🏦 Audit compliance (timeout policy) |
| 44 | 🔄 Module 10 recap + monitoring practice |

✅ **Exit:** Run PaymentCluster PROD procedures independently.

---

## 🟦 MODULE 11 — Expert Design Thinking (Days 45–46) | Topics T72–T75

| Day | Topics |
|-----|--------|
| 45 | T72: Design session strategy for a 3-tier bank app • T73: 🏦 DR across sites — what's realistic |
| 46 | T74: Session sizing math (users × size × replica) • T75: 🎤 Interview answer template "Design session for PaymentCluster" |

✅ **Exit:** Design + defend a session architecture at Lead level.

---

## 🟦 MODULE 12 — Interview Marathon (Days 47–51) | Topics T76–T79

| Day | Topics |
|-----|--------|
| 47 | T76: Top 50 Q&As — Part 1 (Q1–Q25) |
| 48 | T76: Top 50 Q&As — Part 2 (Q26–Q50) |
| 49 | T77: STAR-format answers ("session issue you fixed") |
| 50 | T78: Whiteboard: User → IHS → Plugin → Cluster with M-to-M |
| 51 | T79: Trick questions (affinity ≠ replication; restart ≠ failover) |

✅ **Exit:** Interview polish complete.

---

## 🟩 MOCKS + RECAPS + FINAL (Days 52–56)

| Day | Activity |
|-----|----------|
| 52 | 🎤 MOCK INTERVIEW 1 — Levels 1–2 (basics, affinity, replication) |
| 53 | 🎤 MOCK INTERVIEW 2 — Levels 3–5 (failover, troubleshooting, design) |
| 54 | 🔄 Full course recap — Level 1 & 2 fast revision |
| 55 | 🔄 Full course recap — Level 3, 4 & 5 fast revision |
| 56 | 🏁 Final review + weak-topic patch-up + COURSE COMPLETE 🎓 |

---

## 📊 Module → Days Quick Map

| Module | Days | Topics | Count |
|--------|------|--------|-------|
| M1 — Session Basics | 1–4 | T1–T9 | 9 |
| M2 — Clustering & Affinity | 5–8 | T10–T15 | 6 |
| M3 — M-to-M ⭐ | 9–15 | T16–T25 | 10 |
| M4 — Persistence | 16–20 | T26–T33 | 8 |
| M5 — Tuning & Sizing | 21–24 | T34–T40 | 7 |
| M6 — Security | 25–27 | T41–T45 | 5 |
| M7 — Plugin/IHS | 28–30 | T46–T50 | 5 |
| M8 — Failover ⭐ | 31–34 | T51–T55 | 5 |
| M9 — Troubleshooting ⭐⭐ | 35–40 | T56–T65 | 10 |
| M10 — PROD Procedures | 41–44 | T66–T71 | 6 |
| M11 — Expert Design | 45–46 | T72–T75 | 4 |
| M12 — Interview Marathon | 47–51 | T76–T79 | 4 |
| Mocks + Recaps + Final | 52–56 | — | — |

---

## ⏱️ How Each 1-Hour Day Looks

| Minutes | Activity |
|---------|----------|
| 0–40 | Theory 80% + Banking Scenario 15% + Interview Hook 5% |
| 40–55 | Console steps + wsadmin/Jython practice |
| 55–60 | Notes + say the interview answer out loud |

---

## 📅 PHASE 2 — After the Course (Separate Phase)

| # | Deliverable | Count |
|---|-------------|-------|
| 1 | 10-yr level Interview Q&A on this topic | 20 |
| 2 | "Recent Issues You Faced" questions (10-yr level) | 5 |
| 3 | Real Banking Scenario-based questions (-yr level) | 20 |
| 4 | Real Banking Troubleshooting questions (10-yr level) | 20 |
| 5 | Real outage scenarios / war stories on this topic | 5 |
| 6 | Behavioral/Leadership questions (10-yr level) | 5 |
| 7 | Architecture/Design questions (Lead level) | 5 |
| | **Total Phase 2** | **80** |

---

## ✅ COURSE SUMMARY

| Item | Value |
|------|-------|
| Duration | 56 days @ 1 hr/day (8 weeks) |
| Total topics | 79 + recap days + 2 mock interviews |
| Total levels | 5 |
| Total modules | 12 — ALL included |
| Phase 2 (separate) | 80 interview/scenario/war-story items |
| Method | 80% Theory / 15% Banking Scenario / 5% Interview — every topic, Console + wsadmin |
| Goal | Crack 10-yr WAS interview + run PROD alone without help |

---

>🏁 **Day 56:** Interview-ready at 10-yr level + able to run PaymentCluster PROD alone.
