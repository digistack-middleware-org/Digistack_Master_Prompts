# 📅 WebSphere Session Management — FULL 56-DAY PLAN (Day Wise)
## All 12 Modules Included | 79 Topics + Recaps + 2 Mock Interviews | 1 hr/day

> **Course by:** Ox Alpha (25 Years IBM WAS Admin/Architect — Large MNC Bank)
> **Environment:** BankCell01 → DMGR → Node01/Node02 → PaymentCluster (PaymentJVM01–04) → IHS01/IHS02 → PaymentApp.ear
> **Method per topic:** 80% Theory | 15% Real Banking Scenario | 5% Interview
> **Every topic includes:** Admin Console steps + wsadmin/Jython steps
> **Rule:** One topic at a time. No skipping. Move topic by topic.

---

## 🗓️ WEEK 1 — Session Basics + Affinity (Days 1–7)

| Day | Module | Topics | Count |
|-----|--------|--------|-------|
| **1** | M1 | T1: HTTP session (railway ticket) • T2: Why web needs sessions (stateless) • T3: 🏦 Net-banking login MUST have session | 3 |
| **2** | M1 | T4: JSESSIONID (name tag) • T5: How session is born (Set-Cookie) • T6: Sessions live in JVM memory | 3 |
| **3** | M1 | T7: Session timeout (30 min default) • T8: Login vs logout (create vs invalidate) | 2 |
| **4** | M1 | T9: 🔥 JVM restart = sessions LOST + **Console/wsadmin demo** | 1 |
| **5** | M2 | T10: Single server vs cluster • T11: Session affinity (teller who knows you) | 2 |
| **6** | M2 | T12: plugin-cfg.xml & clone IDs • T13: Sticky sessions decoded | 2 |
| **7** | M2 | T14: 🏦 "Your" JVM dies mid-transfer • T15: ⚠️ Affinity vs Replication vs Persistence | 2 |

**🎯 Milestone Day 7:** You can explain JSESSIONID, timeout, and affinity in an interview.

---

## 🗓️ WEEK 2 — Affinity Recap + M-to-M Replication (Days 8–14)

| Day | Module | Topics | Count |
|-----|--------|--------|-------|
| **8** | M2 | 🔄 **Module 2 recap** + Console/wsadmin practice | — |
| **9** | M3 ⭐ | T16: What is M-to-M replication (photocopy teller) • T17: Single replica vs multi-row topology | 2 |
| **10** | M3 | T18: Replication domain • T19: Replica number / backup count | 2 |
| **11** | M3 | T20: Replication modes (both/server/client) • T21: Timeout, frequency, disk offload | 2 |
| **12** | M3 | T22: **Admin Console config step-by-step** (cluster → replica domain) | 1 |
| **13** | M3 | T23: **wsadmin/Jython — DRSSettings via AdminConfig** | 1 |
| **14** | M3 | T24: Inside DRS — Data Replication Service internals | 1 |

**🎯 Milestone Day 14:** You can configure M-to-M in Console + wsadmin from memory.

---

## 🗓️ WEEK 3 — M-to-M Finish + DB Persistence (Days 15–21)

| Day | Module | Topics | Count |
|-----|--------|--------|-------|
| **15** | M3 | T25: 🏦 PaymentCluster 4-JVM backup map + **Module 3 recap** | 1 |
| **16** | M4 | T26: DB session persistence (the vault) • T27: 🏦 When banks choose DB over M-to-M | 2 |
| **17** | M4 | T28: Prerequisites (schema, DataSource, JNDI) • T29: Persistence schedule (end-of-service/time/manual) | 2 |
| **18** | M4 | T30: Performance reality (DB overhead) • T31: **Admin Console config step-by-step** | 2 |
| **19** | M4 | T32: **wsadmin/Jython config steps** • T33: 🏦 M-to-M vs DB comparison + decision guide | 2 |
| **20** | M4 | 🔄 **Module 4 recap** + practice | — |
| **21** | M5 | T34: Fat sessions kill clusters • T35: Serialization cost | 2 |

**🎯 Milestone Day 21:** You can choose M-to-M vs DB for any bank app — and justify it.

---

## 🗓️ WEEK 4 — Tuning + Security (Days 22–28)

| Day | Module | Topics | Count |
|-----|--------|--------|-------|
| **22** | M5 | T36: Invalidation timeout, max in-memory, overflow • T37: Tuning replica count, frequency, trigger | 2 |
| **23** | M5 | T38: Session cache vs store • T39: **Console tuning steps** | 2 |
| **24** | M5 | T40: 🏦 Capacity math 50,000 users + **Module 5 recap** | 1 |
| **25** | M6 | T41: Security-integrated sessions • T42: Cookie settings (Secure, HttpOnly, domain, path) | 2 |
| **26** | M6 | T43: SSL-only cookies 🏦 (RBI/regulator) • T44: Session fixation protection | 2 |
| **27** | M6 | T45: What survives failover vs forces re-login + **Module 6 recap** | 1 |
| **28** | M7 | T46: Clone ID & ":" separator decoded • T47: Plugin routing logic | 2 |

**🎯 Milestone Day 28:** You can size PaymentCluster and quote cookie security like a PROD admin.

---

## 🗓️ WEEK 5 — Plugin + Failover Deep-Dive (Days 29–35)

| Day | Module | Topics | Count |
|-----|--------|--------|-------|
| **29** | M7 | T48: Affinity after plugin regeneration • T49: Broken affinity — symptoms & causes | 2 |
| **30** | M7 | T50: Cookie path, URI group, virtual host + **Module 7 recap** | 1 |
| **31** | M8 ⭐ | T51: 🏦 Full failover walk-through: JVM1 dies mid-transfer → user lands on JVM2 | 1 |
| **32** | M8 | T52: M-to-M failover timeline (millisecond reconstruction) • T53: DB persistence failover timeline | 2 |
| **33** | M8 | T54: What is STILL lost even with replication • T55: Anti-patterns that break failover | 2 |
| **34** | M8 | 🔄 **Module 8 recap** + whiteboard practice | — |
| **35** | M9 ⭐⭐ | S1 (T56): Random logouts peak load • S2 (T57): Rolling restart gone wrong | 2 |

**🎯 Milestone Day 35:** You can narrate a full failover story — the #1 10-yr interview question.

---

## 🗓️ WEEK 6 — Troubleshooting (Days 36–42)

| Day | Module | Topics | Count |
|-----|--------|--------|-------|
| **36** | M9 | S3 (T58): Re-login loop JVM2 (clone ID) • S4 (T59): UAT works, PROD fails (config drift) | 2 |
| **37** | M9 | S5 (T60): Logout every 30 min despite activity • S6 (T61): OutOfMemoryError from sessions | 2 |
| **38** | M9 | S7 (T62): DRS errors — not replicating • S8 (T63): Session collision after VM clone | 2 |
| **39** | M9 | S9 (T64): DataSource down → all logins fail • S10 (T65): 🏦 Payment lost mid-transfer on failover | 2 |
| **40** | M9 | 🔄 **Module 9 recap** — all 10 scenarios: Symptoms → Logs → Console → wsadmin → Fix | — |
| **41** | M10 | T66: 🏦 Rolling restart zero session loss (drain) • T67: Pre-restart session impact checklist | 2 |
| **42** | M10 | T68: Zero-session-loss deployment • T69: Session monitoring (PMI, TPV) | 2 |

**🎯 Milestone Day 42:** You can troubleshoot any session issue alone — without Google. **This is your goal.**

---

## 🗓️ WEEK 7 — PROD Procedures + Expert Design (Days 43–49)

| Day | Module | Topics | Count |
|-----|--------|--------|-------|
| **43** | M10 | T70: UAT vs PROD config comparison • T71: 🏦 Audit compliance (timeout policy) | 2 |
| **44** | M10 | 🔄 **Module 10 recap** + monitoring practice | — |
| **45** | M11 | T72: Design session strategy 3-tier bank app • T73: 🏦 DR across sites — what's realistic | 2 |
| **46** | M11 | T74: Session sizing math (users × size × replica) • T75: 🎤 Interview answer template "Design session for PaymentCluster" | 2 |
| **47** | M12 | T76: **Top 50 Q&As — Part 1** (Q1–Q25) | 1 |
| **48** | M12 | T76: **Top 50 Q&As — Part 2** (Q26–Q50) | 1 |
| **49** | M12 | T77: STAR-format answers ("session issue you fixed") | 1 |

**🎯 Milestone Day 49:** You can design + defend a session architecture at Lead level.

---

## 🗓️ WEEK 8 — Interview Marathon + Mocks (Days 50–56)

| Day | Module | Topics | Count |
|-----|--------|--------|-------|
| **50** | M12 | T78: Whiteboard: User → IHS → Plugin → Cluster with M-to-M | 1 |
| **51** | M12 | T79: Trick questions (affinity ≠ replication; restart ≠ failover) | 1 |
| **52** | 🎤 | **MOCK INTERVIEW 1** — Levels 1–2 (basics, affinity, replication) | — |
| **53** | 🎤 | **MOCK INTERVIEW 2** — Levels 3–5 (failover, troubleshooting, design) | — |
| **54** | 🔄 | Full course recap — Level 1 & 2 fast revision | — |
| **55** | 🔄 | Full course recap — Level 3, 4 & 5 fast revision | — |
| **56** | 🏁 | Final review + weak-topic patch-up + **COURSE COMPLETE** 🎓 | — |

**🏁 Day 56:** Interview-ready at 10-yr level + able to run PaymentCluster PROD alone.

---

## 📊 Weekly Summary

| Week | Days | Modules | Focus |
|------|------|---------|-------|
| 1 | 1–7 | M1, M2 | Basics + Affinity |
| 2 | 8–14 | M2 recap, M3 ⭐ | M-to-M Replication |
| 3 | 15–21 | M3 recap, M4 | DB Persistence |
| 4 | 22–28 | M5, M6, M7 start | Tuning + Security |
| 5 | 29–35 | M7, M8 ⭐, M9 start | Plugin + Failover |
| 6 | 36–42 | M9 ⭐⭐, M10 | Troubleshooting heart |
| 7 | 43–49 | M10, M11, M12 | PROD + Design + Top 50 |
| 8 | 50–56 | M12 + Mocks 🎤 | Interview polish |
| **Total** | **56 days** | **All 12** | ✅ |

---

## 📚 Module → Topics → Days Reference (Complete Map)

| Module | Topics | Days | Status |
|--------|--------|------|--------|
| M1 — Session Basics | T1–T9 | 1–4 | ✅ Included |
| M2 — Clustering & Affinity | T10–T15 | 5–8 | ✅ Included |
| M3 — Memory-to-Memory (M-to-M) ⭐ | T16–T25 | 9–15 | ✅ Included |
| M4 — DB Session Persistence | T26–T33 | 16–20 | ✅ Included |
| M5 — Tuning & Sizing | T34–T40 | 21–24 | ✅ Included |
| M6 — Security & Sessions | T41–T45 | 25–27 | ✅ Included |
| M7 — Plugin / IHS Integration | T46–T50 | 28–30 | ✅ Included |
| M8 — Failover Deep-Dive ⭐ | T51–T55 | 31–34 | ✅ Included |
| M9 — Troubleshooting (10 Scenarios) ⭐⭐ | T56–T65 | 35–40 | ✅ Included |
| M10 — Banking PROD Procedures | T66–T71 | 41–44 | ✅ Included |
| M11 — Expert Design Thinking | T72–T75 | 45–46 | ✅ Included |
| M12 — Interview Marathon | T76–T79 | 47–51 | ✅ Included |
| Mocks + Recaps + Final 🎤 | — | 52–56 | ✅ Included |

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
|---|------------|-------|
| 1 | 10-yr level Interview Q&A on this topic | 20 |
| 2 | "Recent Issues You Faced" questions (10-yr level) | 5 |
| 3 | Real Banking Scenario-based questions (10-yr level) | 20 |
| 4 | Real Banking Troubleshooting questions (10-yr level) | 20 |
| 5 | Real outage scenarios / war stories on this topic | 5 |
| 6 | Behavioral/Leadership questions (10-yr level) | 5 |
| 7 | Architecture/Design questions (Lead level) | 5 |
| | **Total Phase 2** | **80 items** |

---

## ✅ COURSE SUMMARY

| Item | Value |
|------|-------|
| **Duration** | 56 days @ 1 hr/day (8 weeks) |
| **Total topics** | 79 + recap days + 2 mock interviews |
| **Total levels** | 5 |
| **Total modules** | 12 — ALL included |
| **Phase 2 (separate)** | 80 interview/scenario/war-story items |
| **Method** | 80% Theory / 15% Banking Scenario / 5% Interview — every topic, Console + wsadmin |
| **Goal** | Crack 10-yr WAS interview + run PROD alone without help |

---