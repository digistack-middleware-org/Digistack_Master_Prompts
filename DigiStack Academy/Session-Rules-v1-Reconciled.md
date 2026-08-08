# SESSION RULES — RECONCILED (v1)

**Purpose:** This file resolves conflicts between the original pasted mentoring
rules (which referenced a 208-Day plan with a generic "every 7th / every 15th"
Lab/Incident pattern) and the two verified project files:

- `Progress-Log-v5-Verified.md`
- `WebSphere-222-Day-Plan-v5-Verified.md`

Add this file to the Project's knowledge alongside those two, so future
sessions don't need to re-derive these resolutions from scratch.

---

## 1. Authoritative day count: 222 Days, not 208

The program is **222 Days / 6,660 Sprints** (30 Sprints × 222 Days), per the
v5-Verified plan. There is no verified 208-day version in this Project. Any
future reference to "208 days" in a prompt should be treated as stale/incorrect
unless a new, explicitly verified plan file superseding v5 is uploaded.

## 2. Lab Day / Incident Day placement: Authority Table overrides the generic pattern

The original rule text ("Day 7 = Lab Day," "Day 15 = Incident Day," recurring
every 7th/15th day) is a **simplification only** and is superseded by the
**Day-Tag Authority Table** in `Progress-Log-v5-Verified.md`. Applying the
generic modulo rule directly would collide with Closeout Days and with the
actual staggered Lab/Incident placement. Use this table as ground truth:

- **Lab Days (20 total):** 8, 15, 29, 36, 50, 64, 85, 92, 99, 120, 127, 134,
  141, 148, 155, 162, 169, 190, 204, 218
- **Incident Days (11 total):** 40, 55, 70, 100, 115, 130, 145, 160, 175, 205,
  220
- **Closeout Days (18 total — absorb the Incident Day requirement via 5
  production scenarios already built into that day; no separate Incident Day
  needed):** 9, 18, 24, 34, 47, 62, 71, 73, 84, 91, 101, 109, 119, 128, 154,
  171, 180, 191
- **All other Days:** Standard 30-Sprint template.

Note the resulting overlap point that trips people up: **Day 15 is a Lab Day,
not an Incident Day** — the generic rule's "Day 15 = Incident Day" does not
apply here.

## 3. Lab Day content scope: no standalone Linux curriculum

v5 removed the standalone Linux Administration and Networking Fundamentals
block entirely (21 days removed from v4's 243-day plan). Lab Days therefore do
**not** include general Linux administration (LVM, systemd, SSH/cron, package
management, kernel tuning, etc.).

What survives, and is in-scope for Lab Days and any sprint referencing "Linux
commands":
- Basic OS-level **verification** commands only — `df`, `ps`, `grep`, `tail`,
  etc. — used specifically for WAS log and process checks.
- This is *not* a general Linux curriculum item; treat it as a WebSphere
  operational skill, not a networking/OS-admin topic.

Sprint 8 (Admin Console + wsadmin) N/A logic is unaffected: it still applies
only to non-WebSphere-console-related topics, but "pure Linux/networking day"
no longer exists as a category in v5 — so this exception will rarely, if
ever, trigger going forward.

## 4. Incident Day sub-template: confirmed, no conflict

The original rule (six new, unique-across-the-program incidents per Incident
Day; each incident fully closed — investigation through lessons-learned —
using the same running-ticket narrative structure) matches the v5 Incident Day
sub-template exactly. No changes needed here.

## 5. Lab Environment versions (locked, from Day 4 onward)

| Component | Version |
|---|---|
| WebSphere Application Server ND | 9.0.5.21 |
| Java | IBM Java 8 (SDK 8.0) |
| IBM HTTP Server (IHS) | 9.0.5.21 |
| Web Server Plug-ins | 9.0.5.21 |
| IBM Installation Manager | 1.9.x |
| IBM MQ | 9.3.x or 9.4.x |
| OS | RHEL 8.x / Rocky Linux 8.x |
| Database | PostgreSQL 13+ (lab) / Oracle 19c (production-representative) |

Day 1 is exempt (carried forward unchanged from v4) and is not tied to this
version table.

## 6. Current position (as of this reconciliation)

- **Last completed:** Day 1, Sprint 10 (Investigation — Incident 1)
- **Next:** Day 1, Sprint 11 (Troubleshooting — Incident 1)
- **Day 1 template type:** Standard Day
- **Day 1 topic:** Enterprise IT overview, banking IT org structure, roles,
  ITIL basics, ServiceNow/Jira/Confluence

## 7. All other original session rules — unchanged, confirmed compatible

The following rules from the original prompt were checked against the
verified files and require **no changes**:

- Fixed 30-Sprint template, in exact order, every Standard Day.
- Sprint 1 structure: Theory (50%) → Real-time Examples (40%) → Interview
  Notes (10%).
- Mandatory real, specific, rotating banking examples in *every* sprint, not
  just the "Real Banking Example" sprint — named systems (Core Banking, Loan
  Origination, Payments Gateway, Credit Card Processing, Online/Mobile
  Banking Portal, Wire/SWIFT, ATM Switch, Fraud
  Detection Engine, etc.), realistic ticket numbers, timestamps, log
  snippets, and named stakeholder roles.
- Sprint 8 always includes both Admin Console click-path and wsadmin (Jython)
  script for WebSphere-specific topics.
- Incident sub-sprints (9–14, 15–20 on Standard Days; the 6-incident
  structure on Incident Days) must each be a distinct, realistic, unique
  banking incident, never reused across the 222-day program.
- One Sprint (or one Day) delivered per turn, in full long-form detail, never
  condensed — wait for explicit "next sprint" / "next day" / "Day-X
  Sprint-Y" before continuing.
- Every reply ends with a "Say 'next sprint' for Sprint [Y+1], or 'next day'
  to move to Day [X+1]" prompt.

---

**How to use this file:** Keep it alongside the two v5-Verified files in this
Project's knowledge. If a future edition (v6, a real 208-day cut, etc.) is
produced, this reconciliation file should be superseded/updated at the same
time so the three files never drift out of sync again.
