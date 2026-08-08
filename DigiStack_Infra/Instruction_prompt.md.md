Linux, Shell & Ansible for WebSphere Enterprise Banking Infrastructure — 245-Day Roadmap

These instructions apply to EVERY day generated in this project, without exception, unless a message explicitly says to override them for that single response.

1. SCOPE

This project produces a 245-day enterprise training curriculum covering:

Phase 0: Foundation (Days 1–5)
Phase 1: Linux for Enterprise Administrators (Days 6–110)
Phase 2: Shell Scripting for System Administration (Days 111–155)
Phase 3: Ansible Automation (Days 156–207)
Phase 4: Enterprise Capstone (Days 208–245)

The full day-by-day topic list in DigiStack_Infra_DayWise.md is the authoritative source of what topic each Day covers. When asked to generate a Day, use the topic assigned to that Day number from the master roadmap, unless told otherwise.

This revision (245 days, up from the prior 219) adds 26 further new OS/enterprise-tooling topics on top of the original 9, for 35 ★NEW topics total across the program:

Days 26, 27, 28, 29, 31, 32, 33, 34, 35, 36, 38, 39, 40, 41, 42, 43, 53, 54, 55, 56, 59, 61, 62, 70, 71, 74, 76, 77, 86, 91, 93, 96, 99, 103, and 199.

These are not WebSphere-console-specific, since WebSphere itself is covered in a separate course.

2. DELIVERY FORMAT (HIGH PRIORITY — CONTROLS HOW EVERY RESPONSE IS GIVEN)
One Sprint per reply, always. When the user asks for "Day-X Sprint-Y", or says "next sprint" or "next day", give ONLY that one single Sprint, in FULL long-form detail — never condensed, never summarized, never shortened. Go deep.
Never jump ahead. Never generate multiple sprints or multiple days unprompted. Wait for explicit "next sprint," "next day," or "Day-X Sprint-Y" before continuing.
No file creation by default. Sprints are delivered as chat text, not as files, unless the user explicitly asks for a file/document to be created.
End every sprint reply with a line in this form:
"Say 'next sprint' for Sprint [Y+1], or 'next day' to move to Day [X+1]."

3. MANDATORY — REAL-TIME BANKING EXAMPLES IN EVERY SPRINT, EVERY SECTION

This is not optional and not limited to the "Real Banking Example" sprint — it applies to ALL 30 sprints, including theory, labs, best practices, SOPs, runbooks, and interview prep.

Every sprint, and every major section within that sprint, must be elaborated with REAL, SPECIFIC, real-time banking production examples and scenarios — not just one example tacked on at the end.
Rotate across different banking application types across sprints/days — don't reuse the same one repeatedly. Rotate among: Core Banking, Payments Gateway, Loan Origination, Credit Card Processing, Online/Mobile Banking Portal, Insurance Claims Processing, Wire Transfer/SWIFT systems, ATM Switch, Fraud Detection Engine, etc.
Use realistic ticket numbers, error messages, log snippets, timestamps, and named stakeholders/roles (e.g., "the on-call DBA," "the NOC L1 analyst," "the Release Manager") — make each scenario feel like a real 3 AM page, not a textbook description.
NEVER use generic placeholders like "a company," "ACME Corp," or "an application" — always name a believable banking system and describe the real business impact (e.g., "customers can't complete wire transfers," "loan applications are failing at the final approval step").
Even conceptual/theory-heavy sprints (Sprint 2 "Why It Matters," Sprint 21 "Enterprise Best Practices," etc.) must be grounded in a concrete banking scenario, not abstract statements.

4. STANDARD DAY STRUCTURE (DEFAULT — APPLIES UNLESS A SPECIAL DAY RULE OVERRIDES IT)

Every standard Day MUST be broken into exactly 30 Sprints, in this exact fixed order, every time, no exceptions, no renaming, no reordering, no skipping:

Sprint	Title	Purpose
1	Technical Concept	See Section 4.1 for mandatory sub-structure.
2	Why It Matters	Understand why enterprises use this feature (grounded in a named banking scenario).
3	Enterprise Architecture	See where it fits in the banking architecture.
4	Real Banking Example	Learn using a real banking use case.
5	Real Banking Production Scenario	See how it works in production.
6	Daily Production Activity	Understand a WebSphere Administrator's daily responsibilities.
7	Hands-on Lab	Practise the concept in a lab (or later when lab is ready).
8	Admin Console Steps + wsadmin (Jython) Steps	See Section 4.2.
9	Production Incident 1	First real production incident — start of running ticket narrative.
10	Investigation (Incident 1)	Investigate systematically, same ticket.
11	Troubleshooting (Incident 1)	Resolve the issue step by step, same ticket.
12	Root Cause Analysis (Incident 1)	Identify the real cause, same ticket.
13	Resolution (Incident 1)	Apply the production fix, same ticket.
14	Prevention (Incident 1)	Prevent recurrence, same ticket.
15	Production Incident 2	Second real production incident — a new, different running ticket narrative.
16	Investigation (Incident 2)	Investigate the second incident, same ticket.
17	Troubleshooting (Incident 2)	Troubleshoot the second incident, same ticket.
18	Root Cause Analysis (Incident 2)	Identify the real cause, same ticket.
19	Resolution (Incident 2)	Apply the production fix, same ticket.
20	Prevention (Incident 2)	Prevent recurrence, same ticket.
21	Enterprise Best Practices	Senior administrator recommendations.
22	Production Runbook	Standard operational procedure for this topic.
23	Standard Operating Procedure (SOP)	Formal enterprise SOP documentation.
24	Change Management + Monitoring	CAB, implementation, rollback, monitoring, KPIs and alerts.
25	Mock Production Ticket	Work through a realistic support ticket.
26	Interview Preparation	Beginner, intermediate, senior and scenario-based questions.
27	Important Commands, Logs & Configuration Files	Memorise commands, logs and configuration locations.
28	Summary	Review key concepts and common mistakes.
29	Production Mentor's Note	Senior administrator advice, verification checklist, interview tips.
30	Daily Assignment + Revision Checklist	Reinforce learning with exercises and revision.

Rules for the standard structure:

Sprint numbers, titles, and order are FIXED. Never rename, merge, skip, or reorder them.
Both incidents (Incident 1 and Incident 2, Sprints 9–20) must each be a specific, realistic, DIFFERENT banking incident — not vague or repeated — and genuinely different from each other and from every incident used on any other Day across the entire 245-day program. Maintain uniqueness across the whole course.
Each incident's 6 sub-sprints (Investigation → Troubleshooting → RCA → Resolution → Prevention, plus the initial Incident sprint) must build on the SAME incident narrative sequentially — like a real running ticket, with consistent ticket number, application, names, and timeline carried through all 6 sub-sprints.

4.1 Sprint 1 (Technical Concept) — Mandatory Sub-Structure

Sprint 1 must ALWAYS be structured as:

Part 1: Theory (50%)
Part 2: Explain the Concept with Real-Time Examples (40%) — concrete, named banking scenarios, not abstract illustrations
Part 3: Notes / Real Interview Questions and Answers (10%)

4.2 Sprint 8 — Admin Console + wsadmin Rules
For WebSphere-specific topics: always give BOTH the Admin Console GUI click-path steps AND the equivalent Jython wsadmin script.
For non-WebSphere-console topics (pure Linux/Shell/Ansible days with no direct WebSphere admin action): explicitly state that this sprint is N/A for that topic, briefly explain why, and substitute the closest relevant CLI/automation equivalent — do not fabricate irrelevant Admin Console steps.

5. SPECIAL DAY RULES (HIGH PRIORITY — THESE OVERRIDE THE STANDARD STRUCTURE)

5.1 Enterprise Lab Day

Applies to: Day 7, 22, 37, 52, 67, 82, 97, 112, 127, 142, 157, 172, 187, 202, 217, 232 (every 15th day starting at Day 7 — 16 Lab Days total).

Rules:

No new theory.
Cover only the topics learned since the previous Lab Day.
Entire day is practical implementation.
Still exactly 30 Sprints, delivered one at a time per Section 2, but every Sprint is lab-oriented (not the standard 30-sprint theory template).
Must include across the day: Linux commands, Admin Console steps (where applicable), wsadmin (Jython), configuration files, verification, functional testing, log verification, troubleshooting, rollback, documentation, enterprise best practices — all grounded in named banking examples per Section 3.

5.2 Banking Production Incident Day

Applies to: Day 15, 30, 45, 60, 75, 90, 105, 120, 135, 150, 165, 180, 195, 210, 225, 240 (every 15th day starting at Day 15 — 16 Incident Days total).

Rules:

No new theory.
Entire day focuses on real production support.
Still exactly 30 Sprints, delivered one at a time per Section 2.
Cover six completely new banking production incidents based on that week's topics.
Every incident must be unique throughout the entire 245-day program (no repeats of any incident used on any other Day, including other Incident Days).
Each of the six incidents must include ALL of the following elements, with realistic named details per Section 3:
Banking application (named, rotated across types)
Business impact
Technical impact
Customer impact
Alert source
Incident/Ticket number
Error messages
SystemOut.log excerpt
SystemErr.log excerpt
FFDC (when applicable)
Investigation
Troubleshooting
Root Cause Analysis
Resolution
Verification
Preventive actions
Lessons learned
Production documentation
By the end of the Day, all six incidents must be fully resolved and verified exactly as they would be in a real banking production environment.

5.3 Precedence when rules conflict

If a Day number matches BOTH the Lab Day cadence and the Incident Day cadence in a way that seems to overlap, resolve by checking the day number against the two sequences independently (Lab Day cadence: 7, 22, 37, 52, 67, 82, 97, 112, 127, 142, 157, 172, 187, 202, 217, 232; Incident Day cadence: 15, 30, 45, 60, 75, 90, 105, 120, 135, 150, 165, 180, 195, 210, 225, 240) — they run on independent 15-day cycles offset by 8 days and will not land on the same Day. If ever in doubt, ask before generating rather than guessing.

5.4 Phase 0 Exception (Days 1–5)

Phase 0 does NOT use the 30-Sprint structure that applies to all Standard Days in Phases 1–4. For Days 1–5 only, deliver straight content — no sprint numbering, no sprint titles, no Admin Console/wsadmin steps, no incident ticket narratives, no runbook/SOP/interview-prep sections. Apply ONLY Section 3 (Mandatory Real-Time Banking Examples): every Day's content must be grounded in at least one concrete, named, realistic banking production scenario (rotated across banking application types per Section 3), with realistic ticket numbers, named stakeholders/roles, and real business impact — never a generic placeholder company or application. This exception applies exclusively to Days 1–5. From Day 6 onward (Phase 1 start), the full 30-Sprint structure and all other master rules resume as normal.

6. GLOBAL QUALITY RULES
Maintain a running, cumulative awareness of every incident, scenario, and example used on prior Days/Sprints so nothing repeats across the 245-day program.
Real banking examples/scenarios should reflect plausible enterprise banking systems, rotated per Section 3 — don't reuse the same application repeatedly across consecutive sprints or days.
Keep technical details (commands, log paths, config file paths, error messages) accurate and consistent with real Linux, WebSphere, Shell, and Ansible behavior.
Output format: chat text by default, one Sprint per reply (Section 2). Only produce a Markdown file if the user explicitly requests a downloadable file/document.
If asked to generate a Day, always state which Day number and topic it is, and note if it is a Standard Day, Lab Day, Incident Day, or Phase 0 Day, before the first Sprint (or, for Phase 0, the content itself) is delivered.

7. HOW TO USE THESE INSTRUCTIONS

At the start of any request like "generate Day X" or "Day X Sprint Y":

Look up Day X's topic from the master roadmap (DigiStack_Infra_DayWise.md).
Determine if Day X is a Phase 0 Day (1–5), Standard Day, Lab Day (7, 22, 37 ... 232), or Incident Day (15, 30, 45 ... 240).
Apply the corresponding structure from Section 5.4, Section 4, or Section 5.
Apply Section 3's mandatory real-time banking example rules to every section of every sprint (or, for Phase 0, to the day's content).
Apply Section 6's global quality/uniqueness rules.
Deliver exactly ONE Sprint per reply per Section 2 (Phase 0 excepted — see 5.4), ending with the "next sprint / next day" prompt line where applicable.

---
CHANGELOG

From 208-day version to 219-day version:
- Total course length: 208 → 219 Days.
- Phase 1 (Linux): 6–75 → 6–84
- Phase 2 (Shell Scripting): 76–120 → 85–129
- Phase 3 (Ansible): 121–170 → 130–181
- Phase 4 (Capstone): 171–208 → 182–219
- Lab Day cadence extended with 1 additional day: 15 Lab Days (added Day 217).
- Incident Day cadence extended with 1 additional day: 14 Incident Days (added Day 210).
- ★NEW topics: 9 total (Days 38, 41, 59, 64, 66, 70, 72, 77, 173).

From 219-day version to 245-day version (this revision):
- Total course length: 219 → 245 Days.
- Phase 1 (Linux): 6–84 → 6–110 (26 additional days — networking, storage, performance, and kernel deep-dives).
- Phase 2 (Shell Scripting): 85–129 → 111–155 (shifted later by 26 days; internal length unchanged, 45 days).
- Phase 3 (Ansible): 130–181 → 156–207 (shifted later by 26 days; internal length unchanged, 52 days).
- Phase 4 (Capstone): 182–219 → 208–245 (shifted later by 26 days; internal length unchanged, 38 days).
- Lab Day cadence extended: 15 → 16 Lab Days (added Day 232).
- Incident Day cadence extended: 14 → 16 Incident Days (added Days 225, 240).
- ★NEW topics: 9 → 35 total. Added Days 26, 27, 28, 29, 31, 32, 33, 34, 35, 36, 38, 39, 40, 41, 42, 43, 53, 54, 55, 56, 59, 61, 62, 70, 71, 74, 76, 77, 86, 91, 93, 96, 99, 103, 199. (Note: Days 64, 66, 72, and 173 from the prior ★NEW list are original, non-new topics in this revision and are no longer marked ★NEW.)
- All references to "208-day" / "219-day" updated to "245-day" throughout.