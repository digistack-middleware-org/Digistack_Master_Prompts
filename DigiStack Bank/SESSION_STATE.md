ID: SESSION01
Version: 1.2
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
folder's own README.md — this file does not duplicate it. Keeping detail
in exactly one place (the PXX/README.md) avoids two files disagreeing
about progress.

---

## Currently Active Folder

Folder: 02_Application_Development/
Part(s) in progress: P01
Version in progress: v1
Sprint in progress: Sprint 1 (next — not yet started)

→ For full detail (completion %, AI Resume Context, Pause/Resume Log),
  open that folder's own README.md. This file only tells you WHICH
  folder to open.

---

## Load Instructions (this session)

Required
--------
- Active folder listed above (all files in it)
- 00_Core/* (all core reference docs — single shared folder, no
  per-folder copies)
- Progress_Log.md

Optional
--------
- Prior Part's folder, only if this session directly references its
  output (e.g. P02 referencing P01's schema)

Do Not Load
-----------
- Any folder not listed as "Currently Active" above
- Any *_Interview_Prep / *_WAS_IQ folder, unless this session is
  explicitly interview-prep work

---

## Switching Parts?

When you move to a new Part/folder:
1. Update "Currently Active Folder" above.
2. Confirm that folder's README.md AI Resume Context section is current
   (update it there, not here).

---

Last Updated
------------
2026-07-29

Change Note (v1.1)
-------------------
Removed all `SYNC_STATUS.md` / cross-account sync references (file
retired — project is maintained from a single account). If multi-account
work resumes in future, a sync-tracking mechanism should be reintroduced
and documented here before relying on it again.

Change Note (v1.2, 2026-07-28)
-------------------------------
Corrected "Sprint in progress" from "Sprint 2 (next)" to "Sprint 1 (next
— not yet started)". This file had drifted out of sync with
01_Application_Development/README.md (source of truth, per the "Switching
Parts?" rule above) and Progress_Log.md's Folder Tracker, both of which
confirm P01 has not been started (0/14 versions complete, no SetupDocs
written). No sprint work has actually begun. Found and corrected during
the 2026-07-28 cross-file audit — see Progress_Log.md's Open Questions
section for the full audit note.
