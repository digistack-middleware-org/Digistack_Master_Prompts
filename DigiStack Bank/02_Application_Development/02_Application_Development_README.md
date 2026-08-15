# 01_Application_Development

**Status:** ⏳ Not Started
**Depends On:** 00_Core
**Feeds Into:** 02_Interview_Prep, 03_Observability

---

## Contents
| File | Purpose |
|---|---|
| P01_Foundation.md | P01 roadmap (Versions 1–14) |
| P01_Sprint_Plan.md | P01 consolidated sprint plan |
| P02_Middleware.md | P02 roadmap (Versions 15–22) |
| P02_Sprint_Plan.md | P02 consolidated sprint plan |
| P03_Banking_Systems.md | P03 roadmap (Versions 23–30) |
| P03_Sprint_Plan.md | P03 consolidated sprint plan |

---

## P01 — Foundation

### Version History
Latest working version: v3
Latest approved version: v3
Next planned version: v4

### Completion
Source:
- P01_Foundation.md

Progress:
- 3 / 14 versions complete

Remaining:
- v4 through v14

### AI Resume Context
Current Objective: Version 3 signed off (2026-08-11). Beginning Version 4 — EAR Update, Rollback & Application Lifecycle.
Current Milestone: v3 complete — Deposit/Withdraw working with Controller→Service→DAO layering, overdraft prevention proven.
Next Task: Begin Version 4, Sprint 1 — trivial UI change ("v4" label) to observe Update Application mechanics.
---

## P02 — WebSphere / Enterprise Middleware Integration

### Version History
First version: v15
Latest working version: —
Latest approved version: —
Next planned version: v15

### Completion
Source:
- P02_Middleware.md

Progress:
- 0 / 8 versions complete

Remaining:
- v15 through v22 (all)

### AI Resume Context
Current Objective: Not started — depends on P01 completion.
Current Milestone: —
Next Task: —

---

## P03 — Enterprise Banking Systems

### Version History
First version: v23
Latest working version: —
Latest approved version: —
Next planned version: v23

### Completion
Source:
- P03_Banking_Systems.md

Progress:
- 0 / 8 versions complete

Remaining:
- v23 through v30 (all)

### AI Resume Context
Current Objective: Not started — depends on P02 completion.
Current Milestone: —
Next Task: —

---

### Required (upload every session)
- [ ] `00_Core/*` — all core reference docs (IDX, STD, ARCH01, ARCH02,
      STDGAP01, SOE01, CAP01, RACI01)
- [ ] `Progress_Log.md`
- [ ] `SESSION_STATE.md`
- [ ] This README (you are reading it)
- [ ] The active Part's roadmap file and sprint plan (currently:
      P01_Foundation.md + P01_Sprint_Plan.md)

### Optional (load only if this session directly references it)
- [ ] The prior Part's roadmap file — e.g., P01_Foundation.md when
      working in P02, to cross-reference a schema or endpoint it produced

### Do Not Load
- Any Part file for a Part not yet started
- `02_Interview_Prep/` folder or any P03.1 / P03.2 file
- Any Phase-2 or Phase-3 folder (03_Observability onward)
- Any `*_Interview_Prep` or `*_WAS_IQ` folder

---
## Folder Freeze Checklist

Move this folder to 🔒 Frozen only when **all** of the following are true.
Once frozen, reopen only for a documented correction — log it in
Progress_Log.md's post-freeze correction table.

- [ ] P01: All 14 versions signed off per TCS01 §2.7 (every Critical +
      High test case passed, no open Critical/High defects, regression
      pack re-run, reviewer + date recorded)
- [ ] P02: All 8 versions (v15–v22) signed off per TCS01 §2.7
- [ ] P03: All 8 versions (v23–v30) signed off per TCS01 §2.7
- [ ] P01, P02, P03 Completion Checklists (bottom of each roadmap file)
      fully checked
- [ ] Cross-Part Dependency Chain in Progress_Log.md has one row per
      version for all 30 versions in this folder
- [ ] Promotion tag `part3-release` applied in Git (P03 is the last Part
      in this folder; its tag governs the folder-level freeze)
- [ ] Progress_Log.md Folder Tracker row for `01_Application_Development`
      updated to 🔒 Frozen in the same edit as the tag

---

## Notes / Deviations

*Log anything that differs from the roadmap as originally written — first
entry in the version whose SetupDoc introduced the deviation, mirrored
here for folder-level visibility (per STDGAP01 §7).*

| Date | Version | Deviation | Why |
|---|---|---|---|
| | | | |

---

## Pause / Resume Log

| Date Paused | Paused At (Version + Sprint) | Reason | Exact Resume Point |
| 2026-08-11 | v1, pre-Sprint-1 | Lab VM + chat session lost | Full reset — resume at Version 1 Sprint 1, VM provisioning, from a blank machine. See Progress_Log.md Open Questions for full reset note. |

*"Exact Resume Point" must be specific enough for a cold-start chat to
pick up without re-reading everything — e.g., "P01 v3 Sprint 4: DAO layer
built, Controller not yet wired; next step is Sprint 4 in P01_Sprint_Plan.md."*
