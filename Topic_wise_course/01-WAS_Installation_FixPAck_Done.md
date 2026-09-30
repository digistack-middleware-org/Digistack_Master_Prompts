# 📅 Day-Wise Study Plan: WebSphere Installation & Fix Pack (Zero → 10-Yr Expert)

**Duration:** 30 Days | **Daily Time:** 3–4 hrs (2 hrs theory + 1–2 hrs hands-on/lab)

---

## 🗓️ WEEK 1: Foundations (Days 1–7)

| Day | Topic | Hands-On Task | Interview Focus |
|-----|-------|--------------|-----------------|
| **1** | What is WAS? Cell → Node → Dmgr topology (Mod 1.1) | Draw WAS topology diagram on paper for a bank with 5 branches | "Explain WAS architecture in simple terms" |
| **2** | WAS Base vs ND vs Liberty; Profiles explained (Mod 1.2) | Compare feature list, note which banks use which | "Why ND for banking apps?" |
| **3** | OS prep: RHEL/AIX sizing, filesystems, ulimits, kernel params (Mod 1.3) | Setup a VirtualBox/VM with RHEL; create wasadmin user, set ulimits | "What pre-checks before install?" |
| **4** | Port planning & firewall basics (Mod 1.3) | Make a port matrix sheet: 9060, 9043, 8879, 9443 for 10 nodes | "Explain key WAS ports" |
| **5** | Installation Manager theory + silent install (Mod 2.1) | Download IM, install silently with response file | "Why silent installs in banks?" |
| **6** | IM commands & local repositories (Mod 2.2) | Create local repo from downloaded ZIPs; run imcl listInstalledPackages | "How do you patch DMZ servers with no internet?" |
| **7** | **VISION + Self-Test Week 1** | Write your own notes from memory; quiz yourself on all commands | — |

---

## 🗓️ WEEK 2: Installation End-to-End (Days 8–14)

| Day | Topic | Hands-On Task | Interview Focus |
|-----|-------|--------------|-----------------|
| **8** | Silent WAS ND install (Mod 3.1) | Install WAS ND v9 via response file; run versionInfo.sh | "Walk me through a fresh WAS install" |
| **9** | Dmgr profile creation (Mod 3.2) | Create Dmgr profile via manageprofiles.sh command line | "Dmgr vs custom profile" |
| **10** | Custom node + federation (Mod 3.2) | Create custom node, federate to Dmgr; verify in console | "What happens during federation?" |
| **11** | Cluster & AppSrv creation (Mod 3.2) | Build 2-node cluster (like a Payments/NEFT app cluster) | "Cluster creation steps" |
| **12** | Start/stop scripts + log analysis (Mod 3.3) | Start everything, read SystemOut.log, startServer.log fully | "App not starting — where do you look?" |
| **13** | Admin console security + LDAP + wsadmin basics (Mod 3.4) | Enable admin security, write 3 basic Jython commands | "How did you secure admin console in your bank?" |
| **14** | **MINI PROJECT:** Build a complete 2-node bank topology from scratch (blank VM → working cluster) in under 3 hours. Document it as a "Build Book" | — | — |

---

## 🗓️ WEEK 3: Fix Packs — The Core (Days 15–21)

| Day | Topic | Hands-On Task | Interview Focus |
|-----|-------|--------------|-----------------|
| **15** | Fix Pack vs iFix vs IFIX vs Refresh Pack; version matrix (Mod 4.1) | Download a real WAS 9.0.5.x fix pack; read IBM fix list page | "Explain fix pack types" |
| **16** | Pre-patch: backups, baseline, CR/change management (Mod 4.2) | Run profileBackup + backupConfig; document baseline with versionInfo.sh | "What do you do BEFORE patching?" |
| **17** | Applying fix packs — silent method, correct ORDER: Dmgr → NodeAgent → Servers (Mod 4.3) | Patch your lab Dmgr then nodes via imcl; verify with historyInfo.sh | "Why patch Dmgr first?" |
| **18** | Rolling patch for zero downtime (Mod 4.3) | Simulate rolling patch: patch node1 JVMs, smoke test, then node2 | "How do you patch internet banking with zero downtime?" |
| **19** | Java SDK patching (Mod 4.4) | Update IBM JDK 8 in lab; verify java -version levels match | "WAS patch vs Java patch — same or different?" |
| **20** | Post-patch verification + rollback (Mod 4.5, 4.6) | Full smoke test checklist; then deliberately ROLLBACK the fix pack and restore | "Tell me your rollback plan" |
| **21** | **REVISION + Self-Test Week 3** | Write a complete patch runbook + rollback runbook from memory (this is gold for interviews!) | — |

---

## 🗓️ WEEK 4: Enterprise Practice + Troubleshooting (Days 22–27)

| Day | Topic | Hands-On Task | Interview Focus |
|-----|-------|--------------|-----------------|
| **22** | Golden images, response file standardization (Mod 5.1) | Create ONE master response file template; reuse it | "How do you standardize 60 servers?" |
| **23** | Multi-env strategy: DEV→UAT→PROD, PROD/DR parity (Mod 5.2) | Write a real patch promotion plan with soak period | "Why did DR fail after PROD patch?" type Qs |
| **24** | Automation: shell script wrapping imcl for bulk patching (Mod 5.3) | Write a script that patches multiple nodes & collects versionInfo output | "How do you patch 200 JVMs in a weekend?" |
| **25** | Install/patch failures troubleshooting (Mod 6.1, 6.2) | Break your lab on purpose: full /tmp, partial patch, stop NodeAgent mid-patch — then FIX each | "Tell me about a failed patch you handled" |
| **26** | Version drift detection + audit reporting (Mod 6.3) | Write drift-detection script across nodes; generate a compliance-style report | "How do you prove patch compliance to auditors?" |
| **27** | Security & CVE patching, PCI-DSS 90-day rule, hardening (Mod 7) | Read one real IBM Security Bulletin; write a patch justification for management | "How do you handle an emergency CVE patch?" |

---

## 🗓️ FINAL DAYS: Expert Consolidation (Days 28–30)

| Day | Task |
|-----|------|
| **28** | Module 8: Upgrade vs Fix Pack, EOL planning, capacity/sizing, build documentation & governance. Write a mock "WAS 8.5 EOL migration roadmap" one-pager |
| **29** | **GRAND MOCK PRACTICE:** Full drill — blank VM → install → profile → cluster → patch → rollback → verify, timed, solo, no notes |
| **30** | **INTERVIEW DRY RUN:** Answer all interview questions from Weeks 1–4 out loud. Record yourself. Refine your "war stories" (patch outage, drift issue, emergency CVE patch) |

---

## ⏰ Daily Routine Template

1. **30 min** — Revise previous day's notes
2. **90 min** — Today's theory (Theory 80%)
3. **60 min** — Hands-on lab task
4. **30 min** — Practice 3 interview Q&A out loud + write 1 banking scenario note

---

## 📌 Rules for Success

- ✅ Never skip the lab — banks want hands-on, not bookish admins
- ✅ After each day, write **1 banking scenario** in your own words (your interview arsenal)
- ✅ Maintain 3 documents from Day 1: **Build Book, Patch Runbook, Rollback Runbook** — these make you sound like a true 10-yr admin
- ✅ If a day is missed, don't skip — extend the plan; consistency > speed

---

## 📌 Next Phases After Course Completion

1. 20 Real Interview Q&A (10-yr level)
2. 5 "Recent Issues You Faced" Q&A
3. 20 Real Banking Scenario Questions
. 20 Real Banking Troubleshooting Questions
5. 5 Real Outage War Stories
6. 5 Behavioral/Leadership Questions7. 5 Architecture/Design Questions (Lead level)
