# Templates & Scaffolds — DigiStack Telecom Enterprise
*(Upload only when actively filling one of these out. Not needed for normal sprint work.)*

## A. Developer Handoff Package (one per app, fill out once app is built)
```
## App: [name]
**Purpose:** [one line]
**Cluster:** [Customer / Business / Integration]
**Context root:** [/path]

### Tech
- Package: com.digistack.telecom.[app].*
- DB schema: [name]
- MQ resources used: [queue names, connection factories]

### Deployment
- EAR file name:
- Required datasources:
- Required JMS resources:
- Environment variables / JNDI names:

### Endpoints
| Method | Path | Purpose |
|---|---|---|

### Known Issues / Limitations
-

### Contacts
- Built by: Venkatesh (self-study)
- Admin owner: Venkatesh
```

## B. Test Case Suite (fill out per app)
| Test ID | Description | Steps | Expected Result | Actual Result | Status |
|---|---|---|---|---|---|
| TC-[APP]-001 | | | | | Not Run |

Categories to cover: Functional, Negative, Integration, Failover (cluster member down mid-request), Performance (basic load/burst).

## C. Lab Challenge Bank
Filled in per version, only once that version's Hands-on Challenge sprint is actually reached (so difficulty matches what's genuinely been taught).
```
### [Phase].[Version]: [Version Name]
- Beginner: [task]
- Intermediate: [task]
- Advanced: [task]
- Expert: [task]
```
Index — Phase 1–6: (empty, fill as completed)

## D. Sprint / Calendar Plan
**Status:** pacing not yet decided.
| Phase | Versions | Sprints (6 each) | Total |
|---|---|---|---|
| 1 Foundation | 12 | 6 | 72 |
| 2 App Dev | 11 | 6 | 66 |
| 3 WAS Admin | 7 | 6 | 42 |
| 4 Operations | 8 | 6 | 48 |
| 5 Prod Support | 4 | 6 | 24 |
| 6 Enterprise Ops | 7 | 6 | 42 |
| **Total** | **49** | — | **294** |
Open: sprints/day-week rhythm, target completion date. Original DigiStack Bank project used a 15-day cycle over ~464 days — not yet confirmed here.
