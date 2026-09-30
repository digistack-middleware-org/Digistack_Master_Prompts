# WebSphere Migration — 40-Day Fast-Track Plan
> **Module-wise |10-Yr WAS Admin | Migration Topics Only | Banking Focused**

**Why 40 days works:** You already know WAS deeply — you only need migration-specific skill.
Overlapping topics are merged; revision is built into each module's last day.

**Rule per day:** Theory → Banking Scenario → Write 5 interview answers in your own words.

---

## 📘 Module 1: Migration Basics, Types & Planning — (6 Days)

| Day | Topic |
|-----|-------|
| 1 | Migration vs upgrade vs coexistence; tWAS→tWAS (8.5.5→9.0.5); Base vs ND differences |
| 2 | tWAS→Liberty; JDK migration (Java 6→8, IBM JDK→Semeru); EOS dates & compliance drivers |
| 3 | Platform migration: AIX/Solaris→RHEL; VM→Cloud (AWS/Azure/OpenShift) — overview |
| 4 | App portfolio inventory + Transformation Advisor (Simple/Moderate/Complex) + binary scanner reports |
| 5 | Java EE spec changes, deprecated APIs; feasibility study, risk assessment, wave planning & estimation |
| 6 | Rollback strategy design; **Bank Scenario:** 120 apps → 8 waves in 18 months — write YOUR project story |

---

## 📗 Module 2: Migration Tools — (4 Days)

| Day | Topic |
|-----|-------|
| 7 | WebSphere Migration Tool (WMT) full workflow; `manageprofiles augment` vs `migrate` (hands-on) |
| 8 | wsadmin export/import (`extract`, `import`, configArchive); `backupConfig`/`restoreConfig`; profile registry backup |
| 9 | IBM Installation Manager silent installs, response files; config diff tools old vs new cell |
| 10 | Jython automation for migration; **Bank Scenario:** Trade Finance cell via WMT — UAT dry run + change doc |

---

## 📙 Module 3: Profile & Cell Migration (Execution) — (7 Days)

| Day | Topic |
|-----|-------|
| 11 | Pre-migration checklist (build your personal template) + migration order: Dmgr → nodes → servers |
| 12 | Custom profile migration + Node Agent re-registration + addNode |
| 13 | Cluster recreation (H+V); static vs dynamic cluster migration |
| 14 | Porting: virtual hosts, JDBC providers, datasources, J2C aliases, mail sessions |
| 15 | Porting: shared libs, JVM properties, custom services, PMI |
| 16 | Post-migration verification: `versionInfo.sh`, smoke tests, health URLs — write smoke script |
| 17 | **Bank Scenario:** NEFT payment cell weekend migration — full runbook with rollback; write it + revise module |

---

## 📕 Module 4: Application Migration & Compatibility — (5 Days)

| Day | Topic |
|-----|-------|
| 18 | EAR/WAR redeploy vs rebuild; classloader PARENT_FIRST/PARENT_LAST |
| 19 | JAR conflicts (log4j, Spring); third-party certification per WAS version |
| 20 | Descriptor changes: web.xml / ejb-jar.xml / ibm-*-bnd.xmi→.xml; servlet/EJB spec changes |
| 21 | JNDI/binding changes; session serialization; ClassNotFoundException RCA |
| 22 | **Bank Scenario:** Internet banking breaks post-migration — wrong library path; write full RCA + timed mocks |

---

## 📒 Module 5: Web Tier, Security & Messaging Migration — (7 Days)

| Day | Topic |
|-----|-------|
| 23 | IHS alignment; plugin-cfg.xml regeneration/propagation; cloneIDs |
| 24 | Session affinity validation; SSL IHS↔Plugin↔WAS re-pointing; **Scenario:** 503 after cut-over RCA |
| 25 | LTPA key export/import; keystore/truststore migration (key.p12, trust.p12); cert expiry handling |
| 26 | LDAP repointing, federated repositories, J2C alias recreation; hardening (CIS, PCI-DSS) |
| 27 | **Bank Scenario:** SSO breaks post-migration — LTPA keys missing; RCA + prevention |
| 28 | SIBus migration; MQ link/JMS repointing; MQ client alignment; ESB/DataPower endpoints |
| 29 | **Bank Scenario:** Payments stuck — SIBus engine down on new cell; RCA + revise module |

---

## 📔 Module 6: Cut-over Strategies & Platform Migration — (6 Days)

| Day | Topic |
|-----|-------|
| 30 | Coexistence rules; plugin pointing to both cells; canary traffic shifting (10%→50%→100%) |
| 31 | Blue-Green & DNS/LB cut-over; weekend freeze pattern; parallel run + rollback triggers; **Scenario:** 2-week canary on card app |
| 32 | AIX→RHEL specifics: paths, permissions, non-root installs, endianness |
| 33 | Lift-and-shift vs rebuild-the-cell — write decision matrix |
| 34 | WAS on AWS/Azure; containerized tWAS (Hybrid Edition); Liberty on OpenShift roadmap |
| 35 | **Bank Scenario:** 80 JVMs AIX→RHEL→AWS (phased) — write as YOUR project + revise module |

---

## 📕 Module 7: Post-Migration + Troubleshooting (Interview Heavy) — (5 Days)

| Day | Topic |
|-----|-------|
| 36 | Performance baseline old vs new; GC/heap re-tuning after JDK change; pool revalidation; monitoring/log4j re-integration; DR replication; PVU/ILMT |
| 37 | Troubleshooting: Dmgr profile fails; federation failures (SoapConnectorTimeout, port conflicts); app startup failures (classloader/JNDI/datasource) |
| 38 | Troubleshooting: SSL/LTPA failures; session loss/affinity after cut-over; plugin 404/503; perf drop after JDK change; rollback execution |
| 39 | **Drill day:** 12 random failure scenarios, 5-min RCA each, aloud |

---

## 🎯 Final Interview Prep — (1 Day)

| Day | Topic |
|-----|-------|
| 40 | Full mock interview (90 min): all modules + your 5 war stories + consistent environment numbers + grand revision |

---

## 📊 Summary

| Module | Topic | Days |
|--------|-------|------|
| 1 | Basics, Types & Planning | 1–6 |
| 2 | Migration Tools | 7–10 |
| 3 | Profile & Cell Migration | 11–17 |
| 4 | Application Migration | 18–22 |
| 5 | Web Tier, Security & Messaging | 23–29 |
| 6 | Cut-over & Platform Migration | 30–35 |
| 7 | Post-Migration + Troubleshooting | 36–39 |
| Final | Interview Prep | 40 |

**Total: 40 Days ✅**

---

# ✅ Phase 2: Interview Mastery Pack — WebSphere Migration
> **Starts AFTER the 40-Day Course | All 7 Sections | 10-Yr / Lead Level | Banking Focused**

## 📦 What You'll Get

### 1️⃣ 20 Real Interview Q&A — 10-Yr Level
- Direct questions interviewers actually ask migration candidates
- Model answers in first-person ("I migrated...", "In my environment...")
- Follow-up traps the interviewer may throw + how to counter them

### 2️⃣ 5 "Recent Issues You Faced" Questions
--format answers: Situation → Action → Result → Lesson
- With realistic environment numbers (JVM counts, cell names, timelines) so you can adapt them as YOUR OWN

### 3️⃣ 20 Real Banking Scenario Questions
- Payment apps, internet banking, card systems, Trade Finance
- e.g., *"Your bank must migrate 80 JVMs off EOS WAS 8.5.5 on AIX in 12 months — walk me through your plan"*
- Full model answers with wave plans, cut-over strategy, rollback gates

### 4️⃣ 20 Real Banking Troubleshooting Questions
- Post-migration failures: apps down, sessions lost, SSO broken, payments stuck, plugin 503s, federation timeouts
- RCA format answers: Symptom → Diagnosis → Root Cause → Fix → Prevention

### 5️⃣ 5 Real Outage Scenarios / War Stories
- Detailed 3 AM migration outage stories (what failed, war-room, recovery time, post-mortem)
- Told the way you'd tell them in an interview — believable, specific, with lessons learned

### 6️⃣ 5 Behavioral / Leadership Questions
- e.g., *"Tell me about a migration that went wrong and how you handled it"*
- STAR-format answers for lead-level interviews

### 7️⃣ 5 Architecture / Design Questions — Lead Level
- Design a migration architecture for a 200-JVM banking estate
- Blue-Green vs Canary for a core banking cell — defend your choice
- With diagrams (text-described), trade-offs, and risk tables

---

## 📋 Recommended Order for Phase 2 (after Day 40)

| Order | Section | Suggested Days |
|-------|---------|----------------|
| 1 | 20 Interview Q&A | 2 days |
| 2 | 20 Troubleshooting Questions | 2 days |
| 3 | 20 Banking Scenario Questions | 2 days |
| 4 | 5 Recent Issues | 1 day |
| 5 | 5 War Stories | 1 day |
| 6 | 5 Behavioral/Leadership | 1 day |
| 7 | 5 Architecture/Design | 2 days |
| — | Final polish: own your stories aloud | 1 day |

**Phase 2 Total: ~12 Days** → **Grand total: 40 + 12 = 52 Days to fully interview-ready Lead level** 🚀

---

## ⚠️ One Important Rule

The "Recent Issues" and "War Stories" must become **YOUR** stories — rewrite each one with your real environment details (cell names, JVM counts, app names) so nothing sounds memorized in the interview.

---

## 📌 Progress Tracker

- [ ] Module 1 (Days 1–6)
- [ ] Module 2 (Days 7–10)
- [ ] Module 3 (Days 11–17)
- [ ] Module 4 (Days 18–22)
- [ ] Module 5 (Days 23–29)
- [ ] Module 6 (Days 30–35)
- [ ] Module 7 (Days 36–39)
- [ ] Final Interview Prep (Day 40)
- [ ] Phase 2 — Section 1: 20 Interview Q&A
- [ ] Phase 2 — Section 2: 5 Recent Issues
- [ ] Phase 2 — Section 3: 20 Banking Scenarios
- [ ] Phase 2 — Section 4: 20 Troubleshooting Questions
- [ ] Phase 2 — Section 5: 5 War Stories
- [ ] Phase 2 — Section 6: 5 Behavioral/Leadership
- [ ] Phase 2 — Section 7: 5 Architecture/Design
