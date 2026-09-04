# DigiStack Bank — Progress Log

**Instructions:** Update this file yourself after each version is approved (or ask Claude to update it at the end of a sprint). Upload this file — along with the MASTER INDEX and the relevant Part file — at the start of every new chat so Claude knows exactly where to resume.

---

## Folder Status Legend

| Status | Meaning |
|---|---|
| 🔒 Frozen | All versions in this folder signed off (per TCS01 §2.7 rubric). No further edits except a documented correction, logged below. |
| 🔓 In Progress | Actively being built. Only one folder should carry this status at a time. |
| ⏳ Not Started | No work begun. Files may exist as roadmap-only (unbuilt) drafts. |
| ⏸️ Paused | Was In Progress, deliberately set aside — note why and the resume point in that folder's own README. |

---

## Folder Tracker

| # | Folder | Status | Last Completed Version | Next Version | Current Focus (AI Resume one-liner) |
|---|---|---|---|---|---|
| 00 | Core | 🔒 Frozen | — | — | — |
| 02 | Application_Development | 🔓 In Progress | v5 (P01) | v6 (P01) | v5 (WAS Clustering) signed off 2026-09-01 — 80/80 tests pass. Next: v6 Sprint 1 (Application Administration). dsb-dmgr, dsb-node02, dsb-ihs, dsb-db all ON. Mirrors Multi-Part Folder Detail row below. |
| 03 | Interview_Prep | ⏳ Not Started | — | Interview-1 (P03.1) | Not started — depends on P03 completion |
| 04 | Observability | ⏳ Not Started | — | v31 (P04) | Not started — depends on P03 completion |
| 05 | HA_DR | ⏳ Not Started | — | v36 (P05) | Not started — depends on P04 completion |
| 06 | Multi_Region | ⏳ Not Started | — | v39 (P06) | Not started — depends on P05 completion |
| 07 | WAS_Migration | ⏳ Not Started | — | v44 (P07) | Not started — depends on P06 completion |
| 08 | Automation | ⏳ Not Started | — | v49 (P08) | Not started — depends on P07 completion |
| 09 | Cloud_Migration | ⏳ Not Started | — | v54 (P09) | Not started — depends on P08 completion |
| 10 | Containerization | ⏳ Not Started | — | v74 (P10) | Not started — depends on P09 completion |

> The "Current Focus" column is a one-line mirror of that folder's own README's **AI Resume Context → Next Task**. Update both together — if they ever disagree, the folder's own README governs (same precedent as SetupDoc vs. Environment Notes).

---

## Folder Freeze Rule

A folder moves to 🔒 Frozen only when:
1. Every version's `TestCases-v<N>.md` sign-off rubric (TCS01 §2.7) is satisfied.
2. The Part's own Completion Checklist (in its roadmap file) is fully checked.
3. This file's Cross-Part Dependency Chain has a row for every version in the folder.
4. Promotion tag applied (per EPS01 §3.5/3.6/3.7 as applicable) — `part<N>-release`.

Once frozen, a folder is only reopened for a documented correction — never silently edited. Log any post-freeze correction here:

| Date | Folder | Version | What Changed | Why |
|---|---|---|---|---|
| | | | | |

---

## Multi-Part Folder Detail

> The Folder Tracker above shows one row per folder. Folders `01_Application_Development` and `02_Interview_Prep` each contain multiple Parts with independent lifecycles — tracked in full detail inside that folder's own README, summarized here for a project-wide glance.

### 02_Application_Development

| Part | Status | Last Approved Version | Next Version |
|---|---|---|---|
| P01 — Foundation | 🔓 In Progress | v5 | v6 |
| P02 — Middleware | ⏳ Not Started | — | v15 |
| P03 — Banking Systems | ⏳ Not Started | — | v23 |

### 03_Interview_Prep

| Part | Status | Last Approved Section/Version | Next Section/Version |
|---|---|---|---|
| P03.1 — Interview Drills | ⏳ Not Started | — | Interview-1 |
| P03.2 — Interview Book | ⏳ Not Started | — | Version 1 (Ch. 1) |

### 07_WAS_Migration

| Part | Status | Last Approved Version | Next Version |
|---|---|---|---|
| P07 — WebSphere Migration | ⏳ Not Started | — | v44 |
| P07.1 — Interview Book | ⏳ Not Started | — | Version 1 (Ch. 1) |

### 08_Automation

| Part | Status | Last Approved Version | Next Version |
|---|---|---|---|
| P08 — DevOps Automation | ⏳ Not Started | — | v49 |
| P08.1 — Interview Book | ⏳ Not Started | — | Version 1 (Ch. 1) |

### 09_Cloud_Migration

| Part | Status | Last Approved Version | Next Version |
|---|---|---|---|
| P09 — AWS Migration | ⏳ Not Started | — | v54 |
| P09.1 — Interview Book | ⏳ Not Started | — | Version 1 (Ch. 1) |

### 10_Containerization

| Part | Status | Last Approved Version | Next Version |
|---|---|---|---|
| P10 — Containerization | ⏳ Not Started | — | v74 |
| P10.1 — Interview Book | ⏳ Not Started | — | Version 1 (Ch. 1) |

---
> **Note on Part 3.1 (Interview Preparation):** unlike every other Part,
> this one produces no versioned deployables (no EAR/WAR, no SQL
> migration, no infrastructure) — its "Next Version" entry in the Folder
> Tracker and in `02_Interview_Prep`'s own row above refers to its six
> Interview sub-sections (Interview-1 through Interview-6), not a
> numbered version. Track its progress as a simple checklist against its
> own Completion Checklist rather than the Detailed Version Log /
> Cross-Part Dependency Chain tables below, which assume a numbered
> version with concrete build artifacts.
---
## Interview Preparation (Part 3.1) — Section Completion Status

| Section | Deliverable | Completed? | Score (if applicable) | Notes |
|---|---|---|---|---|
| Interview-1 — Project Walkthrough | 5-min unscripted walkthrough | Not started | — | |
| Interview-2 — WebSphere Administration | ≥80% on random 25-Q draw | Not started | — | |
| Interview-3 — Production Support | 1-page runbook, defended | Not started | — | |
| Interview-4 — Troubleshooting | 3 random scenarios, live | Not started | — | |
| Interview-5 — Banking Production Environment | 10 domain Qs answered | Not started | — | |
| Interview-6 — Mock Interview | Full mock run, scored ≥4/5 all sections | Not started | — | |

---
## Standing Reference Documents (non-versioned — tracked here for completeness)

| Doc ID | Title | Version | Status |
|---|---|---|---|
| IDX | Master Index | 1.4 | Active |
| STD | Standing Rules | 1.14 | Active |
| SOE01 | Golden Image Specification | 1.10 | Active |
| ARCH01 | Enterprise Architecture | 1.0 | Active |
| ARCH02 | Solution Architecture | 1.1 | Active |
| STDGAP01 | Consolidated Standing Standards | 1.1 | Active |
| CAP01 | Capacity & Sizing Standard | 1.1 | Active |
| RACI01 | Operational RACI | 1.1 | Active |


Note: these are reference standards, not versioned Parts — they don't
carry a v<N> in the P01-P78 sequence and aren't subject to the Version
Numbering Freeze. Updated in place (with a version bump + change note,
per STD's Metadata Block Standard) when their content changes.

Sprint Plan Files (not reference standards — no ID/version block):
  P01_Sprint_Plan.md — all 8 sprints for v1–v14 (P01)
  P02_Sprint_Plan.md — all 8 sprints for v15–v22 (P02)
  P03_Sprint_Plan.md — all 8 sprints for v23–v30 (P03)
SESSION_STATE.md's Load Instructions refer to "CURRENT_SPRINT.md"
as a manually uploaded file each session. In practice, the relevant
Part's Sprint Plan file (above) is the source — copy the active
version's 8 sprints from it into CURRENT_SPRINT.md before each session,
or upload the full Sprint Plan file directly.
---

## Detailed Version Log

> Add one row per version as it's completed. Keep the most recent entry at the bottom (or top — your preference, just stay consistent).

| Date | Part | Version | Feature | Status (Started / Dev Done / Deployed to WAS / Tested / Approved) | Notes / Issues |
|---|---|---|---|---|---|

| ~~2026-07-30~~ | ~~P01~~ | ~~v1~~ | ~~Project Setup & Enterprise Architecture~~ | **RESET 2026-08-04** | Entry reset per project owner request — P01 v1 is not started. Original row (Approved 2026-07-30) struck through rather than deleted, per this project's "never silently edit" discipline. See Open Questions section below for the full reset decision log. |
| 2026-08-07 | P01 | v1 | Project Setup & Enterprise Architecture | Approved | Signed off per TCS01 §2.7 — 13/13 test cases pass. WAS ND 9.0.5.28 confirmed on dsb-dmgr; PostgreSQL 16 confirmed on dsb-db (digistack_bank DB, digistack_app user). SetupDoc-v1.md is the source record. |
| 2026-08-08 | P01 | v2 | Login & Session | Approved | Signed off per TCS01 §2.7 — 14/14 test cases pass. users table created (SHA-256+salt hashing), Login/Logout with HttpSession working, digistack-bank-v2.ear redeployed over v1. SetupDoc-v2.md is the source record. |

| 2026-08-09 | P01 | v3 | Basic Transaction (Deposit & Withdraw) | Approved | Signed off per TCS01 §2.7 — 17/17 test cases pass. accounts table (FK to users), full Controller->Service->DAO->DB layering, overdraft rejection enforced, ClassLoader policy documented. SetupDoc-v3.md is the source record. |

| ~~2026-08-07~~ | ~~P01~~ | ~~v1~~ | ~~Project Setup & Enterprise Architecture~~ | **RESET 2026-08-11** | Full project reset per project owner request — both lab VM and chat context lost. Entry struck through rather than deleted, per this project's "never silently edit" discipline. WAS ND/PostgreSQL version pins in STD reverted to placeholder. |

| ~~2026-08-11~~ | ~~P01~~ | ~~v2~~ | ~~Login & Session~~ | **RESET 2026-08-25** | Entry reset per full project reset #2 — lab VM + chat context lost again, confirmed with project owner 2026-08-25. Struck through rather than deleted, per this project's "never silently edit" discipline. |

| ~~2026-08-11~~ | ~~P01~~ | ~~v3~~ | ~~Basic Transaction (Deposit & Withdraw)~~ | **RESET 2026-08-25** | Entry reset per full project reset #2 — same event as the v2 reset row above. |
| 2026-09-01 | P01 | v5 | WAS Clustering | Approved | 80/80 test cases pass. 2-member cluster (devdsbinappcluster01), DMgr devdsbindmgr01, both nodes federated/synchronized, memory-to-memory session replication, live failover test passed. digistack-bank-v5.ear deployed to cluster target. Environment corrections applied from this version: WAS path /apps/IBM/WebSphere/AppServer/, DB password Wasadmin@951951. SetupDoc-v5.md + FaultDrill-v5.md complete. |
| 2026-09-01 | P01 | v5 | WAS Clustering | Not Started |Next version — begins Sprint 1. dsb-node02 VM provisioning required (first new VM since dsb-ihs). |
---

## Cross-Part Dependency Chain

> Per Engineering Standards §8. Add one row per version **as it is actually implemented** — do not pre-fill with guesses at roadmap-design time. `Depends On` = what prior version(s)/artifact(s) this version required to exist first. `Produces` = the concrete artifact(s) this version adds (code module, EAR, table, endpoint, queue, etc.). `Used By` = later version(s) that consume what this version produced (fill in retroactively once that later version is built, or note "known future consumer" if the roadmap already documents the link).

| Version | Depends On | Produces | Used By |
|---|---|---|---|
| ~~V2~~ | ~~V1 (app_config table, EAR skeleton)~~ | ~~users table, Login/Logout servlets, HttpSession creation, Dashboard.jsp~~ | ~~V3, V5, V10~~ |
| ~~V3~~ | ~~V2 (users table, session mechanism)~~ | ~~accounts table (FK to users), AccountDao/Service/Controller layers, Deposit/Withdraw UI, InsufficientFundsException~~ | ~~V4, V5, V6~~ |

Rows above struck through per the 2026-08-25 full reset #2 — these
describe artifacts from the pre-reset build (lost along with the lab
VM), not artifacts currently on disk. Re-add un-struck rows once each
version is actually rebuilt and signed off again.

| V5 | V4 (digistack-bank-v4.ear), V1 (WAS profile) | DMgr devdsbindmgr01, federated nodes devdsbinnode01/02, cluster devdsbinappcluster01, memory-to-memory session replication, digistack-bank-v5.ear | V6 (DMgr/federation deep-dive, Freeze/Unfreeze via wsadmin), V7 (JNDI DataSource across cluster), V8 (plugin-cfg.xml regenerated for cluster) |

---

## Migration Cutover Status (populate only once Part-7 v47 is reached)

> Per doc 04's "Migration Part Promotion" section: while Part-7's cutover is in progress, the **Folder Tracker**'s `06_WAS_Migration` row (and its status field in **Multi-Part Folder Detail**) must show **`Cutover in-flight`** — not "In progress" or left blank — and this detailed per-region table must show explicit per-region state underneath it. The Part cannot be marked `part7-release` until every region reads "Cut over — decommissioned" here **and** the Folder Tracker/Multi-Part Folder Detail entries for `06_WAS_Migration` are updated to `part7-release` in the same edit.

| Region | Platform State | Canary % | Post-Cutover Observation Window Closed? | Old Platform Decommissioned? |
|---|---|---|---|---|
| India | Not started | — | — | — |
| Singapore | Not started | — | — | — |
| Dubai | Not started | — | — | — |

---

## AWS Migration Cutover Status (populate only once Part-9 v60 is reached)

> Added per the 2026-07-19 cross-file audit (Finding F4). Per doc 04's "Phased Cloud Migration Promotion" section: Part-9's progressive cutover is at least as complex as Part-7's — it spans four phase capstones (v58, v63, v68, v73) and region-by-region decommissioning that can happen well before the Part-level UAT/Prod promotion at the very end. Track each region's AWS migration state independently here. Part-9 is not `part9-release` until every region reads "Cut over — on-prem decommissioned" below **and** the **Folder Tracker**'s `08_Cloud_Migration` row (and its status field in **Multi-Part Folder Detail**) is updated to `part9-release` in the same edit — mirroring the Migration Cutover Status table's own rule for Part-7.
>
> **Reminder:** unlike the promotion event itself (which happens once, at the end, per doc 04's Phased Cloud Migration Promotion section), the columns below can and should be updated incrementally as each phase/region reaches that milestone — don't wait until v73 to start filling this in.

| Region | On-Prem/AWS Split (v58) | Lift-and-Shift Complete? (v63) | Platform Modernized? (v68) | Final Cutover % (v73) | Old On-Prem Decommissioned? |
|---|---|---|---|---|---|
| India | Not started | — | — | — | — |
| Singapore | Not started | — | — | — | — |
| Dubai | Not started | — | — | — | — |

---
## Environment Notes

- **WAS ND version installed:** 9.0.5.28 — confirmed at P01 v5 sign-off (2026-09-01). Install path: /apps/IBM/WebSphere/AppServer/ (corrected this version).
- **Profile(s) created so far:** None.
- **Database (PostgreSQL):** 16 — confirmed at P01 v5 sign-off. Runs on dsb-db (192.168.10.30), digistack_app password rotated at v5 (Wasadmin@951951). Active through P02 v22 only; decommissioned at P03 v23 Sprint 4. Runs on dsb-db (192.168.10.30). Active P01 v1 through P02 v22 only; decommissioned at P03 v23 Sprint 4.
- **Database (Oracle):** Not yet provisioned — dsb-oracle VM not built. Oracle 21c XE target/placeholder pin (v22.5 sign-off confirms). IP: 192.168.10.32. Powers on at P02 v22.5. NEVER co-hosted with PostgreSQL on dsb-db.
- **IBM HTTP Server installed:** No — planned v8.
- **Any deviations from the roadmap so far:** OS confirmed as RHEL 8.x on dsb-dmgr (P01 v1 Sprint 2) — 
SOE01/CONTEXT_PACK referenced Rocky Linux 8.x as the expected OS; 
RHEL 8.x is fully compatible, no technical change, 
documentation corrected to reflect actual installed OS.
---

## Open Questions / Decisions Pending

Resolved — Database engine change: PostgreSQL → Oracle 21c XE
from v22.5 onward, 2026-08-28.

digistack_bank (PostgreSQL 16) remains the database engine for
P01 through v22. Version 22.5 (new, inserted between v22 and v23)
migrates all existing data to Oracle 21c XE DIGISTACK_CBS PDB via
a JDBC-based Java migration utility deployed inside WAS. From P03
v23 onward, Oracle 21c XE is the sole database engine. PostgreSQL
is decommissioned at P03 v23 Sprint 4 (final pg_dump archived, VM
shut down, snapshotted once, deleted). Oracle 21c XE runs on a NEW
dedicated VM dsb-oracle (2 vCPU / 4 GB RAM / 60 GB disk, Oracle
Linux 8) — it is NEVER installed on dsb-db. dsb-db retains its
original 2 GB RAM sizing throughout its life. No existing version
numbers changed — v22.5 follows the established suffix-slot
convention.

Files updated: CONTEXT_PACK.md, ARCH01, ARCH02, P02_Middleware.md,
P02_Sprint_Plan.md, 01_Network_Diagram.md, 02_VM_Layout.md,
02_VM_Layout.md (dsb-oracle row added), SESSION_STATE.md,
P03_Banking_Systems.md.

Resolved — NDS01 Rule 7 formalised, 2026-08-27.
Standing requirement that every WebSphere configuration task must
include both Admin Console (GUI) steps AND wsadmin (Jython) steps
was previously implicit in SetupDoc §4.1/§4.2 structure. Promoted
to an explicit named rule (NDS01 Rule 7) at project owner's request
during P01 v1 Sprint 4. Effective from Sprint 5 onward — all
remaining sprints in P01–P10 must deliver both paths.

**Resolved — STD v1.9 PIS01/FIS01 Sprint-Count Retroactivity Correction, 2026-08-27.**

STD's v1.9 change note (PIS01/FIS01 addendum) stated the Sprint 7/8
structural change was "effective P02 onward (v15-v78) — NOT retroactive
to P01 (v1-v14), which remains signed off under its original 6-sprint
structure per version." This directly contradicted `P01_Sprint_Plan.md`,
which was already authored with the full 8-sprint structure (Sprint 7
Sign-off, Sprint 8 Fault Injection + Incident) across all 14 versions.

Corrected: STD bumped to v1.14 with a new change note stating the
8-sprint structure applies project-wide, P01 through the final Part
(v1-v78), no exceptions — matching what `P01_Sprint_Plan.md` already
reflects and what STD's own "Applies to" line already claimed elsewhere
in the same section. Since P01 v1-v14 have not actually been built (full
reset #2, confirmed 2026-08-25), no retroactive rework is required —
this is a documentation-consistency fix only, same category as the
2026-07-28 STD/SOE01 port-table correction and the 2026-08-25 STD/SOE01
version-pin revert. No architectural or technical change. Standing
Reference Documents table above updated to reflect STD v1.14.

**Open — Three Unscoped UI Elements (Business toggle, Open an Account,
Forgot Password), logged 2026-08-24, no target version assigned.**

Project owner's public landing page / login page mockup (2026-08-24)
included three elements with no corresponding backend anywhere in
P01-P10:

| Element | Why it's a gap | Decision needed |
|---|---|---|
| "Personal \| Business" toggle | Business/corporate banking not scoped anywhere in ARCH01 or any Part (retail-only project) | Scope a Business Banking module (new Part?) or drop permanently — undecided |
| "Open an Account" (self-service) | Accounts are currently only created via SQL seed scripts, not a customer-facing flow. Related to the Admin/Teller account-opening gap already logged (2026-08-24, tied to P03 v29 Branch Portal) | Decide: self-service (customer-facing) vs. admin-assisted (Teller, P03 v29) vs. both — undecided |
| "Forgot Password?" | No password-reset flow anywhere in P01 v2 or v10 | Decide which version builds it (candidate: P01 v10, Administrative Security) — undecided |

Decision (2026-08-24): render all three as disabled/"Coming soon" in the
UI rather than removing them or silently wiring them to nothing — visible
so the roadmap gap stays honest, non-functional so no false capability is
implied. Revisit and formally scope each once a decision is made — not
before. Per NDS01/Roadmap Discipline, none of this is built ahead of
schedule; P01 v5 (WAS Clustering) continues uninterrupted.

**Open — Dashboard Full Layout, logged 2026-08-24, updated 2026-08-24 (ATM tile removed).**

| Section | Behavior | Target Version |
|---|---|---|
| 1. View Balance (toggle, hidden by default) | In-page reveal, no redirect | **P01 v3 Sprint 4** — `Dashboard.jsp` retrofit |
| 2. Payments & Transfers tile | In-app Fund Transfer | **P02 v15** (Fund Transfer live) |
| 3. Last 10 Transactions list | In-page list | **P02 v16** (SOAP Account Statement/Transaction History) |
| 4. Cards summary tile | Redirects to card.digistack.cloud | **P03 v28** |
| 5. Loans section | In-page summary, omitted if no active loan | **P03 v30** |
| 6. Last login timestamp | Persistent header, not just post-login flash | **P01 v2/v3 retrofit** |
| 7. Frozen-account banner | Shown when `is_frozen` = true | **P01 v6 retrofit** |
| 8. Notification bell | Withdraw email (v13) → extended to Fund Transfer (v15) | **P01 v13 / P02 v15** |
| 9. Multi-account switcher | "Your Accounts" becomes a list once 2+ accounts possible | **P02 v15** |
| 10. Download Statement link | Next to Recent Transactions, reuses v16's SOAP service | **P02 v16** |

No new backend logic introduced by any of these — all reuse data/events
already produced by the version they're attached to. Per NDS01/Roadmap
Discipline, none of this is built ahead of schedule; P01 v5 (WAS
Clustering) continues uninterrupted.

Decision: do NOT build ahead of schedule (NDS01/Roadmap Discipline). P01 v5
continues uninterrupted. ATM tile (previously logged 2026-08-24) removed
the same day — project owner agreed ATM is a physical channel, not
something a customer launches from online banking; ATM Simulator (P03
v27) remains a standalone kiosk app with no Dashboard entry point.

> Anything you were mid-discussion on when a chat ended, so it isn't lost.
**Resolved — Full Project Reset #2, 2026-08-25.**

Project owner's lab VM (dsb-dmgr, dsb-db, etc.) and prior chat session
were both lost a second time. Confirmed explicitly with project owner
(2026-08-25) that this is a genuine restart, mirroring the 2026-08-11
event. Reset executed:
- SESSION_STATE.md pointer reverted to P01 v1, Sprint 1 (not started);
  header version bumped 1.4 → 1.5, correcting a duplicate-"v1.4"
  change-note labeling bug found in the same pass
- This file's Folder Tracker row for `02_Application_Development`
  reverted to ⏳ Not Started (was stuck showing "v3 signed off,
  In Progress" — itself stale against both this reset and the prior
  2026-08-11 one)
- Detailed Version Log: the 2026-08-11 v2 and v3 "Approved" rows
  struck through (not deleted), per this project's "never silently
  edit" discipline
- Cross-Part Dependency Chain: V2/V3 rows struck through for the same
  reason
- Environment Notes updated to reflect the second loss
- STD's WAS ND / PostgreSQL version pins reverted from CONFIRMED back
  to target/placeholder (STD v1.13), SOE01's mirrored pins likewise
  (SOE01 v1.10) — this also closes a real drift found during this
  pass: the 2026-08-11 reset entry below already claimed this same
  revert had been done, but STD/SOE01's actual pins were never edited
  at that time and still read CONFIRMED (dated 2026-08-07) until now



Physical rebuild (VM provisioning, WAS ND install, PostgreSQL install)
begins fresh at P01 v1 Sprint 1.

**Resolved — Full Project Reset, 2026-08-11.**

Project owner's lab VM (dsb-dmgr, dsb-db, etc.) and prior chat session
were both lost. Confirmed explicitly with project owner (2026-08-11) that
this is a genuine restart, not a documentation drift correction like the
2026-07-28/2026-08-04 events. Reset executed:
- SESSION_STATE.md pointer reverted to P01 v1, Sprint 1 (not started)
- Progress_Log.md's Folder Tracker, Multi-Part Folder Detail, Detailed
  Version Log (old row struck through, not deleted), and Environment
  Notes all reset to reflect zero completed work
- 02_Application_Development_README.md's P01 section reset to 0/14
  versions, AI Resume Context reset to Sprint 1 start
- STD's WAS ND / PostgreSQL version pins reverted from CONFIRMED back to
  target/placeholder, unconfirmed (SOE01's mirrored pins likewise)
Physical rebuild (VM provisioning, WAS ND install, PostgreSQL install)
begins fresh at P01 v1 Sprint 1.

**Resolved — 2026-07-29 Standing Reference Documents sync check.**

Progress_Log.md's Standing Reference Documents table had drifted out of
date against two files' actual metadata blocks: STD showed 1.5 (actual
file: 1.7, already carrying the NDS01 and Build Tool change notes) and
SOE01 showed 1.4 (actual file: 1.5, already carrying the 9.0.5.21
target-pin change note). Both corrected in the table above. Separately,
ARCH02's own file had undocumented content drift — §2a (Maven Project
Structure) and the Maven CI/CD row existed in the file but the metadata
block was never bumped past 1.0 and no change note recorded the
addition; ARCH02 corrected to 1.1 with a change note in the same pass.

**Resolved — 2026-07-28 cross-file audit (premature WAS ND version-pin
correction + stale sync drift).**

A 2026-07-27 edit to STD had “corrected” the WebSphere ND pin from a 9.0.3
placeholder to 9.0.5.21, citing P01 v1's SetupDoc-v1.md as an actual
install record. That document does not exist — this project's own
Folder Tracker and 01_Application_Development's README both confirm
P01 is still “Not Started” (0/14 versions, no SetupDocs, no Pause/Resume
or Deviation entries logged). Confirmed with the project owner
(2026-07-28) that nothing has actually been built yet. Corrected:
- STD's WAS ND/Java SDK pins reverted to the 9.0.3 placeholder (STD v1.5)
- SOE01 §9's mirrored pin reverted to match (SOE01 v1.4)
- This file's own Environment Notes line (previously asserted “9.0.3
  recorded in SetupDoc-v1.md §4.1” — itself premature, since that file also
  doesn't exist yet) reworded to reflect “not yet installed, placeholder
  target only”
- SESSION_STATE.md's Sprint pointer corrected from “Sprint 2 (next)” to
  “Sprint 1 (next — not yet started)”, matching this file's Folder Tracker
- This file's own Standing Reference Documents table was also out of
  sync on STD (showed 1.3, actual file was already 1.4 pre-revert) and
  SOE01 (showed 1.2, actual file was already 1.3 pre-revert) — both
  corrected to the current post-revert versions (1.5 / 1.4)
- Two Ports table gaps between STD and SOE01 also fixed in the same
  pass: STD was missing port 22 (SSH), which SOE01's firewall table
  already opened; SOE01's firewall table was missing port 8080
  (Tomcat), which STD already defines

Re-validate the WAS ND pin against SetupDoc-v1.md §4.1 once P01 v1 is
actually built and signed off — only then promote it back to “confirmed.”

**Resolved — RACI01 / P04 v35 / P05 v38 cross-reference gap (flagged
2026-07-22, resolved 2026-07-23).**

RACI01 (Operational RACI, §4) introduced a named **Incident Commander**
role specifically to resolve an ambiguity in two already-frozen files:

- **P04 v35** (Production Operations, Capacity Planning & Reporting)
  defined the Incident Management Scope and Support Process without
  naming who holds cross-team authority when an incident spans more than
  one team.
- **P05 v38** (Business Continuity & Application Resilience) referenced
  "Management Approval (simulated)" in its DR Runbook and "Incident
  Response" in its Business Continuity section (§7), again without
  naming who actually approves or coordinates.

**Resolution executed:** both P04 v35 and P05 v38 now carry an explicit
one-line cross-reference to RACI01 §4 ("Management Approval → see RACI01
§4, Incident Commander"), added as a documented in-place note — the same
style used for this project's other post-hoc corrections (e.g., STD's
Dependency Matrix note citing the 2026-07-22 audit). This is a
documentation cross-reference only; no technical scope in either P04 or
P05 changed, consistent with the Version Numbering Freeze discipline
(Engineering Standards §7).

This item is now closed — both P04 and P05 explicitly cite RACI01 §4
inline, per the closure condition originally set out when this gap was
first logged.



*(Resolved items below.)*

**Resolved:**
- **Fixed Deposits & Recurring Deposits — Resolved (superseded), 2026-07-22.** Previously tracked as an open question with a default assumption to defer scoping to Part-10 "once roadmapped." Part-10 (Containerization & Cloud-Native Modernization) has since been fully scoped, and its file explicitly declines to absorb this feature (see P10's "Fixed/Recurring Deposits — Explicit Non-Scope Note": this Part is infrastructure/runtime-only, and introducing a banking feature here would break that discipline). Since P10 is currently the final Part in the roadmap with no further Part scoped or implied, Fixed Deposits & Recurring Deposits remain **permanently unscoped within P01–P10**. Any future addition requires a new, explicitly-proposed and scoped Part — this is not assumed to happen automatically.
- Part-2, Version 22 — **Resolved: PostgreSQL selected as the standard database for the DigiStack Bank Enterprise project across all environments.** Original source material referenced MySQL for this version's end-to-end flow; standardized to PostgreSQL project-wide. See MASTER INDEX's "Open Decisions" section for full context.
- Part-8 scope — **Resolved: Part-8 is Enterprise DevOps & End-to-End Automation** (Versions 49–53, renumbered from an initial 48–52 draft that collided with Part-7's frozen 44–48). Former "AWS Migration" placeholder content bumped to Part-9; Containerization bumped to Part-10. See MASTER INDEX's "Open Decisions" section for full context.
- Part-9 scope — **Resolved: Part-9 is Enterprise Hybrid Cloud & AWS Migration** (Versions 54–73, renumbered from an initial 53–72 draft that collided with Part-8's frozen 49–53). Source draft's MySQL references corrected to PostgreSQL/Amazon RDS for PostgreSQL, consistent with the project-wide standard. Containerization remains proposed as Part-10. See MASTER INDEX's "Open Decisions" section for full context.
- Part-6 title conflict — **Resolved: Multi-Region Enterprise Banking & Middleware Architecture is Part-6** (Versions 39–43, offset from an initial 38–42 draft). WebSphere Migration deferred to Part-7. See MASTER INDEX's "Open Decisions" section for full context.
- **Progress Tracker / Master Index sync drift (2026-07-19 cross-file audit) — Resolved.** Parts 2, 3, and 4's "Next Version To Build" columns were blank/inconsistent between this file and the Master Index. Both files now consistently show Version 15 (Part 2), Version 23 (Part 3), and Version 31 (Part 4). Part-3's title in the (then-named) Current Status Summary table was also corrected to match the canonical title used in the Master Index and the Part-3 file itself. *(Note, added post-restructure: this Current Status Summary table has since been superseded by the Folder Tracker / Multi-Part Folder Detail tables above; the historical fix described here still stands, just under the new table names.)*
- **Part-9 promotion model ambiguity (2026-07-19 cross-file audit) — Resolved.** doc 04 previously had no explicit promotion model for a Part that is simultaneously multi-region, phased, and pipeline-automated. Added a "Phased Cloud Migration Promotion" section to doc 04 clarifying that Part-9's phase capstones (v58, v63, v68) are Dev-only gates, while actual UAT/Prod promotion happens once, at the end (post-v73), across all three regions — with regional on-prem decommissioning tracked independently via the "AWS Migration Cutover Status" table above.
- Phase structure — Resolved: Project split into Phase-1 (P01-P03, plus
  suffix Part P03.1 for interview preparation), Phase-2 (P04-P09,
  Enterprise Operations), and Phase-3 (P10, Containerization & Cloud-
  Native Modernization). Confirmed no new banking modules exist after P03
  except P05 v38's idempotency-key addition (documented there as a
  resilience retrofit, not a feature) — P03.1 and P10 introduce no
  banking modules either. Design Documents / SDLC Blueprint / Sprint
  Planning are generated once per phase (P03.1 excluded, as it has no
  versions and produces no design-relevant artifacts), not once for the
  whole project — later phases' versions are deferred until that phase's
  implementation actually begins.


---

*Re-upload this file (updated) alongside the MASTER INDEX and current Part file at the start of every new chat.*