# Session Discipline — DigiStack Bank Project

## Starting Any New Session

Before loading any Part folder or answering a question, follow this order:

1. Open `SESSION_STATE.md` — identifies which folder/Part/Version/Sprint is currently active.
2. Open that active folder's own `README.md` — contains full status, AI Resume Context, and its own "what to upload" checklist.

Do not load folders/Parts marked "Not Started" or "Do Not Load" in SESSION_STATE.md or the active README. Scope each session down to only what's needed — this project has 28+ documents; loading everything risks hitting token limits before real work starts.

## What Gets Uploaded Each Session

1. **Root files**: root `README.md`, `SESSION_STATE.md`, `Progress_Log.md`
2. **Core files**: everything in `00_Core/` (IDX, STD, STDGAP01, SOE01, CAP01, RACI01, ARCH01, ARCH02)
3. **Active Part files**: the roadmap + sprint plan for whichever Part is currently in progress (per SESSION_STATE.md)

If token usage is a concern, upload in that same priority order (Root → Core → active Part) rather than all at once.

## Update Triggers — What to Refresh When Work Finishes

**Finished a Sprint** → update:
1. `<Part>/README.md`
2. `SESSION_STATE.md`

**Finished a Version** → update:
1. `<Part>/README.md`
2. `SESSION_STATE.md`
3. `Progress_Log.md`

**Finished a Part** → update:
1. `<Part>/README.md`
2. `SESSION_STATE.md`
3. `Progress_Log.md`
4. Root `README.md`

Always update the *listed* files for whatever level of work just completed — don't skip layers (e.g. finishing a Version means all three of README/SESSION_STATE/Progress_Log get touched, not just Progress_Log).

## Sync-of-Truth Rule
If a folder's own README and `Progress_Log.md`/`SESSION_STATE.md` ever disagree, the folder's own README governs — flag the mismatch and correct the other file(s) to match.

`00_Core/` is single and shared — never duplicate a core file inside a Part folder.