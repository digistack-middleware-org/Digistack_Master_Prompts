ID: SESSION01
Version: 1.4
Status: Active

Title: Session State — Global Pointer

Imports:
IDX
STD

Exports:
Current active folder pointer
Load/Do-Not-Load instructions for this session

Used By:
(every session, every account)

Next:
(none — living tracker, updated every session)

---

Purpose
-------
Tells you (and Claude) which folder to open FIRST in a new session. All
detailed status, version history, and AI Resume Context lives in that
folder's own README.md — this file does not duplicate it.

---

## Currently Active Folder

Folder: 02_Application_Development/
Part(s) in progress: P01
Version in progress: v4
Sprint in progress: Sprint 1 (next — add "v4" label to Home.jsp for lifecycle practice)

→ For full detail (completion %, AI Resume Context, Pause/Resume Log),
  open that folder's own README.md.

---

## Load Instructions (this session)

Required
--------
- Active folder listed above (all files in it)
- 00_Core/* (all core reference docs)
- Progress_Log.md

Optional
--------
- Prior Part's folder, only if this session directly references its output

Do Not Load
-----------
- Any folder not listed as "Currently Active" above
- Any *_Interview_Prep / *_WAS_IQ folder, unless explicitly interview-prep work

---

## Switching Parts?

When you move to a new Part/folder:
1. Update "Currently Active Folder" above.
2. Confirm that folder's README.md AI Resume Context section is current.

---

Last Updated
------------
2026-08-11

Change Note (v1.4, 2026-08-11)
-------------------------------
Full project reset per project owner request. Both the physical lab
environment (VM/WebSphere/PostgreSQL) and prior chat context were lost.
P01 Version 1 — previously signed off 2026-08-07 — is reset to
not-started. Pointer reverted to Version 1, Sprint 1. Prior sign-off row
in Progress_Log.md is struck through, not deleted, per this project's
"never silently edit" discipline (same precedent as the 2026-08-04
reset).

(Prior change notes v1.1–v1.3 retained below for history.)

Change Note (v1.1)
-------------------
Removed all SYNC_STATUS.md / cross-account sync references.

Change Note (v1.2, 2026-07-28)
-------------------------------
Corrected Sprint pointer drift — no sprint work had actually begun.

Change Note (v1.3, 2026-08-07)
-------------------------------
P01 Version 1 signed off (superseded by this v1.4 reset).