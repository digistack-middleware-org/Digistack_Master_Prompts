# WEBSPHERE 222-DAY PROGRESS LOG (v11 — Rebuilt from WAS-Program-v10-Full-222-Days.md)

Tracking unit = **Day + Sprint** (each Day has 30 fixed Sprints; Lab Day and
Incident Day use their own 30-sprint sub-templates). Mark a Sprint checkbox as
you complete it; a Day is only fully done when all 30 Sprints are checked.

> **v11 note:** This replaces `Progress-Log-v7-Reordered.md` as the Day-Tag
> source of truth. v7's authority table was built for the pre-v10 folder
> ordering (JDBC at Days 25–35, etc.). v10 restructured content again on top
> of that (new Architecture Deep Dive, JNDI, and Enterprise Administration
> chapters; 8 generic filler Lab Days removed; incident/closeout placement
> shifted). The table below is pulled directly from v10's own bolded
> **LAB DAY** / **INCIDENT DAY** / **CLOSEOUT** tags — nothing carried over
> from v7's numbers.

---

## ⚠️ Unresolved conflict inside v10 itself — needs your decision

`WAS-Program-v10-Full-222-Days.md`'s own "Balance Check" table (at the bottom
of that file) claims:
- **17** Closeout Days
- **~19** Lab Days ("named + generic")

But a literal count of every bolded tag actually written in that file's
day-by-day list gives:
- **18** Closeout Days (see list below)
- **11** true "LAB DAY"-template days (full cumulative/all-practical template)
- Plus roughly **60+** additional single-topic days labeled "– Lab:" (e.g.
  "Day 12 – **Lab:** Install IIM 1.9.x") — these read as **Standard Days**
  whose one topic is a hands-on task (Sprint 7 covers it), not full
  alternate-template Lab Days.

The "~19" in v10's summary likely means it's counting some of those "– Lab:"
single-topic days as equivalent to a Lab Day, but the file never says which
ones, and doing so would blow past 19 by a lot if applied consistently. I
have **not** guessed at a fix. Until you tell me how to treat the "– Lab:"
single-topic days, this log tags them as **Standard Days** (topic = a lab
task), and only the 11 bolded **LAB DAY** entries get the full alternate
template. Similarly, I could not find which Closeout v10 intends to drop to
get from 18 down to 17 — all 18 are tagged in the text, so all 18 are kept
below until you say otherwise.

**Options, if you want to resolve this:**
1. Keep it as-is (18 Closeouts, 11 full Lab Days, ~60 Standard Days with a
   lab-flavored topic) — what this log currently reflects.
2. Tell me which Closeout Day v10 meant to demote (e.g. maybe Day 27 or Day
   51, since the note about "Custom Profile and DMGR no longer get standalone
   closeouts" already accounts for 2 removed vs. v7 — but that arithmetic was
   already applied and still lands on 18, not 17).
3. Promote some subset of the "– Lab:" single-topic days to full Lab Day
   template status to reach "~19" — you'd need to specify which ones.

---

## Lab Environment — Locked Software Versions (unchanged from v7/v10)

| Component | Version |
|---|---|
| WebSphere Application Server ND | 9.0.5.28 (v10 target; earlier docs said 9.0.5.21 — v10's Day 4/Day 13/Day 15 all specify **9.0.5.28**, treated as current target version) |
| Java | IBM Java 8 (SDK 8.0) |
| IBM HTTP Server (IHS) | 9.0.5.28 |
| Web Server Plug-ins | 9.0.5.28 |
| IBM Installation Manager | 1.9.x |
| IBM MQ | 9.3.x or 9.4.x |
| OS | RHEL 8.x / Rocky Linux 8.x |
| Database | PostgreSQL 13+ (lab) / Oracle 19c (production-representative) |

> **Note:** v7 and Session-Rules-v1 both listed WAS/IHS/Plug-ins as 9.0.5.21.
> v10 explicitly upgrades the target to **9.0.5.28** in three separate places
> (Day 4, Day 13, Day 15). Flagging this version bump in case it wasn't
> intentional — if you actually want to stay on 9.0.5.21, say so and I'll
> revert v10's version references.

---

## Current Progress

**Last Completed:** Nothing yet — restarted from scratch.

**Next:** Day 1, Sprint 1 / 24

**Topic:** Orientation — Day 1: Enterprise IT overview, banking IT org
structure, ITIL basics, ServiceNow/Jira/Confluence

**Day 1 template type:** Standard Day

**Resume Prompt:** > Continue from **Day 1, Sprint 1**.

> Program reset at user request. Prior progress (previously tracked as far
> as Day 19, Sprint 1 under v7/v10 numbering) is retained only in the
> History table below for reference and does not carry forward as
> completed work.

---

## Day-Tag Authority Table (v11 — extracted directly from WAS-Program-v10-Full-222-Days.md)

- **LAB DAYS — full alternate template (11 total, bolded "LAB DAY" in v10):**
  8, 33, 40, 53, 109, 128, 135, 142, 155, 204, 218
- **INCIDENT DAYS (11 total):**
  44, 59, 95, 103, 124, 139, 153, 166, 176, 205, 220
- **CLOSEOUT DAYS (18 total, per literal tag count — see unresolved conflict above):**
  9, 17, 27, 38, 51, 60, 75, 81, 89, 96, 104, 114, 122, 148, 162, 170, 181, 191
- **All other Days (182 total)** = Standard 30-Sprint template. This includes
  every single-topic day v10 labels "– Lab:" (e.g. 12, 13, 21, 22, 23, 24, 26,
  35, 41, 48, 49, 50, 54, 57, 62, 64, 66, 68, 72, 73, 77, 79, 85, 91, 92, 94,
  98, 100, 108, 111, 112, 117, 118, 127, 130, 137, 138, 151, 157, 159, 161,
  165, 173, 174, 175, 178, 179, 180, 183, 184, 185, 186, 193, 196, 199, 201,
  206, 208, 210, 219, and Day 19 itself) — these run the normal Standard
  30-sprint template, with Sprint 7 (Hands-on Lab) and Sprint 8 (Admin
  Console + wsadmin) carrying that day's specific lab task.

**Total scope confirmed:** 222 Days × 30 Sprints = **6,660 total sprints**.

---

## Sprint Checklist for Current Day (reset this block every new Day)

### If today is a STANDARD Day:

- [x] Sprint 1 – Technical Concept
- [ ] Sprint 2 – Why It Matters
- [ ] Sprint 3 – Enterprise Architecture
- [ ] Sprint 4 – Real Banking Example
- [ ] Sprint 5 – Real Banking Production Scenario
- [ ] Sprint 6 – Daily Production Activity
- [ ] Sprint 7 – Hands-on Lab
- [ ] Sprint 8 – Admin Console + wsadmin (Jython) Steps
- [ ] Sprint 9 – Production Incident 1
- [ ] Sprint 10 – Investigation (Incident 1)
- [ ] Sprint 11 – Troubleshooting (Incident 1)
- [ ] Sprint 12 – Root Cause Analysis (Incident 1)
- [ ] Sprint 13 – Resolution (Incident 1)
- [ ] Sprint 14 – Prevention (Incident 1)
- [ ] Sprint 15 – Production Incident 2
- [ ] Sprint 16 – Investigation (Incident 2)
- [ ] Sprint 17 – Troubleshooting (Incident 2)
- [ ] Sprint 18 – Root Cause Analysis (Incident 2)
- [ ] Sprint 19 – Resolution (Incident 2)
- [ ] Sprint 20 – Prevention (Incident 2)
- [ ] Sprint 21 – Enterprise Best Practices
- [ ] Sprint 22 – Change Management + Monitoring
- [ ] Sprint 23 – Mock Production Ticket
- [ ] Sprint 24 – Interview Preparation


### If today is an inserted LAB DAY (cumulative — one of: 8, 33, 40, 53, 109, 128, 135, 142, 155, 204, 218):

- [ ] Sprint 1 – Recap – topics covered since last Lab Day
- [ ] Sprint 2 – Linux commands drill *(basic OS-level verification only — df/ps/grep/tail for WAS log & process checks)*
- [ ] Sprint 3 – Admin Console and wsadmin/Jython walkthrough drill
- [ ] Sprint 5 – Configuration file review/edit drill
- [ ] Sprint 6 – Verification checks
- [ ] Sprint 7 – Functional testing pass
- [ ] Sprint 8 – Log checks (SystemOut/SystemErr/native)
- [ ] Sprint 9 – Troubleshooting drill 1
- [ ] Sprint 10 – Troubleshooting drill 2
- [ ] Sprint 11 – Troubleshooting drill 3
- [ ] Sprint 12 – Rollback drill
- [ ] Sprint 13 – Documentation pass 1
- [ ] Sprint 14 – Documentation pass 2
- [ ] Sprint 15 – Cumulative lab exercise 1
- [ ] Sprint 16 – Cumulative lab exercise 2
- [ ] Sprint 17 – Cumulative lab exercise 3
- [ ] Sprint 18 – Cumulative lab exercise 4
- [ ] Sprint 19 – Cross-topic integration test
- [ ] Sprint 20 – Peer/interview-style walkthrough
- [ ] Sprint 21 – Enterprise best-practices review


### If today is an inserted INCIDENT DAY (one of: 44, 59, 95, 103, 124, 139, 153, 166, 176, 205, 220):

- [ ] Sprint 1 – Investigation (Incident 1)
- [ ] Sprint 2 – Troubleshooting & RCA (Incident 1)
- [ ] Sprint 3 – Resolution, Prevention & Lessons Learned (Incident 1)
- [ ] Sprint 4 – Investigation (Incident 2)
- [ ] Sprint 5 – Troubleshooting & RCA (Incident 2)
- [ ] Sprint 6 – Resolution, Prevention & Lessons Learned (Incident 2)
- [ ] Sprint 7 – Investigation (Incident 3)
- [ ] Sprint 8 – Troubleshooting & RCA (Incident 3)
- [ ] Sprint 9 – Resolution, Prevention & Lessons Learned (Incident 3)
- [ ] Sprint 10 – Investigation (Incident 4)
- [ ] Sprint 11 – Troubleshooting & RCA (Incident 4)
- [ ] Sprint 12 – Resolution, Prevention & Lessons Learned (Incident 4)
- [ ] Sprint 13 – Cross-incident pattern review
- [ ] Sprint 14 – Documentation pass — all 4 tickets
- [ ] Sprint 15 – Change Management tie-in
- [ ] Sprint 16 – Monitoring/alerting gap review
- [ ] Sprint 17 – Mock Production Ticket (bonus 7th, timed)
- [ ] Sprint 18 – Interview Prep — incident Q&A
- [ ] Sprint 19 – Commands/Logs/Config quick reference
- [ ] Sprint 20 – Enterprise Best Practices — incident response
- [ ] Sprint 21 – On-Call/Escalation Process Review


---

## History (log every completed Sprint or Day)

| Date | Day | Sprint | Topic / Notes |
| ---- | --: | -----: | -------------- |
| 2024-03-19 (narrative date used in-session) | 1 | 1–10 | Enterprise IT overview / ITIL / ServiceNow — Sprints 1–10 complete, including full INC0067341 (Credit Card Processing Engine OOM) narrative through Investigation |
| (session date) | 19 | 1 | Profile Concepts: Standalone vs. DMGR vs. Custom — Sprint 1 complete. Jump from Day 1 (logged) to Day 19 was intentional. |

> Progress Log is read-only mounted in this Project; the History table above
> and the "Current Progress" block must be manually updated after each
> session — there is no auto-sync. Re-upload this file (or a
> further-updated version) to replace the v7 copy in Project knowledge.

---

## Day Status — All 222 Days (grouped by v10 folder)

### Phase A — Foundations (1–17)
- **Orientation (1–3):** Standard, Standard, Standard
- **WAS Intro (4–9):** 4 Std · 5 Std · 6 Std · 7 Std · **8 LAB DAY** · **9 CLOSEOUT**
- **Install (10–17):** 10 Std · 11 Std · 12 Std(lab: install IIM) · 13 Std(lab: install ND) · 14 Std · 15 Std · 16 Std · **17 CLOSEOUT**

### Phase B — Build (18–96)
- **Standalone Profile (18–19):** 18 Std · 19 Std(lab) *(current position)*
- **Basic Deployment (20–22):** 20 Std · 21 Std(lab) · 22 Std(lab)
- **Custom Profile (23):** 23 Std(lab)
- **DMGR (24–25):** 24 Std(lab) · 25 Std
- **Federation (26–27):** 26 Std(lab) · **27 CLOSEOUT**
- **Synchronization (28):** 28 Std
- **Architecture Deep Dive (29–31):** 29 Std · 30 Std · 31 Std
- **Deployment to Federated Servers (32–38):** 32 Std · **33 LAB DAY** · 34 Std · 35 Std(lab) · 36 Std · 37 Std · **38 CLOSEOUT**
- **Clustering (39–48):** 39 Std · **40 LAB DAY** · 41 Std(lab) · 42 Std · 43 Std · **44 INCIDENT DAY** · 45 Std · 46 Std · 47 Std · 48 Std(lab)
- **Cluster Failover (49):** 49 Std(lab)
- **Deployment to Cluster (50–51):** 50 Std(lab) · **51 CLOSEOUT**
- **JDBC (52–60):** 52 Std · **53 LAB DAY** · 54 Std(lab) · 55 Std · 56 Std · 57 Std(lab) · 58 Std · **59 INCIDENT DAY** · **60 CLOSEOUT**
- **JNDI (61–62):** 61 Std · 62 Std(lab)
- **SIBus (63–64):** 63 Std · 64 Std(lab + mini-closeout content)
- **IHS (65–75):** 65 Std · 66 Std(lab) · 67 Std · 68 Std(lab) · 69 Std · 70 Std · 71 Std · 72 Std(lab) · 73 Std(lab) · 74 Std · **75 CLOSEOUT**
- **Session Management (76–81):** 76 Std · 77 Std(lab) · 78 Std · 79 Std(lab) · 80 Std · **81 CLOSEOUT**
- **SSL Fundamentals & Config (82–94):** 82 Std · 83 Std · 84 Std · 85 Std(lab) · 86 Std · 87 Std · 88 Std · **89 CLOSEOUT** · 90 Std · 91 Std(lab) · 92 Std(lab) · 93 Std · 94 Std(lab)
- **SSL End-to-End (95–96):** **95 INCIDENT DAY** · **96 CLOSEOUT**

### Phase C — Operations (97–191)
- **Security (97–108):** 97 Std · 98 Std(lab) · 99 Std · 100 Std(lab) · 101 Std · 102 Std · **103 INCIDENT DAY** · **104 CLOSEOUT** · 105 Std · 106 Std · 107 Std · 108 Std(lab)
- **Maintenance (109–114):** **109 LAB DAY** · 110 Std · 111 Std(lab) · 112 Std(lab) · 113 Std · **114 CLOSEOUT**
- **Enterprise Administration (115–122):** 115 Std · 116 Std · 117 Std(lab) · 118 Std(lab) · 119 Std · 120 Std · 121 Std · **122 CLOSEOUT**
- **Troubleshooting (123–148):** 123 Std · **124 INCIDENT DAY** · 125 Std · 126 Std · 127 Std(lab) · **128 LAB DAY** · 129 Std · 130 Std(lab) · 131 Std · 132 Std · 133 Std · 134 Std · **135 LAB DAY** · 136 Std · 137 Std(lab) · 138 Std(lab) · **139 INCIDENT DAY** · 140 Std · 141 Std · **142 LAB DAY** · 143 Std · 144 Std · 145 Std · 146 Std · 147 Std · **148 CLOSEOUT**
- **Performance Tuning (149–162):** 149 Std · 150 Std · 151 Std(lab) · 152 Std · **153 INCIDENT DAY** · 154 Std · **155 LAB DAY** · 156 Std · 157 Std(lab) · 158 Std · 159 Std(lab) · 160 Std · 161 Std(lab) · **162 CLOSEOUT**
- **Migration (163–170):** 163 Std · 164 Std · 165 Std(lab) · **166 INCIDENT DAY** · 167 Std · 168 Std · 169 Std · **170 CLOSEOUT**
- **wsadmin Automation (171–191):** 171 Std · 172 Std · 173 Std(lab) · 174 Std(lab) · 175 Std(lab) · **176 INCIDENT DAY** · 177 Std · 178 Std(lab) · 179 Std(lab) · 180 Std(lab) · **181 CLOSEOUT** · 182 Std · 183 Std(lab) · 184 Std(lab) · 185 Std(lab) · 186 Std(lab) · 187 Std · 188 Std · 189 Std · 190 Std · **191 CLOSEOUT**

### Phase D — Enterprise & Advanced Layer (192–222)
- **IBM MQ + Messaging (192–199):** 192 Std · 193 Std(lab) · 194 Std · 195 Std · 196 Std(lab) · 197 Std · 198 Std · 199 Std(lab)
- **WebSphere Liberty (200–202):** 200 Std · 201 Std(lab) · 202 Std
- **Load Balancers (203–206):** 203 Std · **204 LAB DAY** · **205 INCIDENT DAY** · 206 Std(lab)
- **Monitoring & Observability (207–208):** 207 Std · 208 Std(lab)
- **Automation & CI/CD (209–210):** 209 Std · 210 Std(lab)
- **DR, Backup & Compliance (211–212):** 211 Std · 212 Std
- **Enterprise Documentation Practice (213–214):** 213 Std · 214 Std
- **Banking Production Simulation Marathon (215–217):** 215 Std · 216 Std · 217 Std
- **Inserted Lab Day (218):** **218 LAB DAY**
- **Capstone + Interview Readiness (219–222):** 219 Std(lab: capstone) · **220 INCIDENT DAY** · 221 Std · 222 Std

---

### Notes

- Mark a Sprint box only after that sprint's full long-form response has been
  delivered and reviewed. Mark a Day box only once all 30 Sprints are checked.
- Reset the "Sprint Checklist for Current Day" block each time you move to a
  new Day — pick the STANDARD, LAB DAY, or INCIDENT DAY template per the
  table above.
- This log supersedes `Progress-Log-v11.md` entirely for Day-Tag
  purposes. Keep v7 only as a historical record of the pre-v10 ordering.
- **Open item:** see the "Unresolved conflict inside v10 itself" section at
  the top — v10's own summary math (17 Closeouts / ~19 Lab Days) doesn't match
  its literal tags (18 Closeouts / 11 full Lab Days). This log uses the
  literal tags until you say otherwise.
- **Open item:** v10 bumped the target WAS/IHS/Plug-ins version from 9.0.5.21
  (v7/Session-Rules) to 9.0.5.28. Flagged above — confirm if intentional.
