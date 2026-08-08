ID: STDGAP01
Version: 1.1
Status: Active

Title: Consolidated Standing Standards — Resolving the Master Index "doc 01-07" Gap Table

Imports:
IDX
STD

Exports:
SDD01
TCS01
EPS01
DBS01
CI01

Used By:
P01
P02
P03
P03.1
P03.2
P04
P05
P06
P07
P07.1
P08
P08.1
P09
P09.1
P10
P10.1

Next:
(none — resolves the Master Index's standing "Known Gap" note in full)

---

Cover Note
----------
The Master Index's Legacy "doc NN" Reference Map flagged five real
content gaps — items cited constantly across P03-P10 as authoritative but
never actually written anywhere in the project — plus one naming gap (doc
06 lacking a proper ID/metadata block). This single file resolves all six
in one place, in dependency order. Each section below is a complete,
standalone standard in its own right and should be treated as such by any
future file that cites it by ID (SDD01 / TCS01 / EPS01 / DBS01 / CI01).

===============================================================================
# SECTION 1 — SDD01: End-to-End Setup Documentation Standards
(resolves doc 06's naming gap — content unchanged from the original file,
now given a formal ID/metadata block per STD's own Metadata Block Standard)
===============================================================================

**Applies to:** Every version gets its own `SetupDoc-v<N>.md` — a complete, self-contained runbook that lets you (or anyone) build that version's environment and deploy that version's code from a blank VM, using only that document.

## Why This Matters
This is the actual deliverable that fills your 10-year gap: real WebSphere admins live by exactly this kind of setup documentation. If `SetupDoc-v<N>.md` can't stand alone, it's not done.

## Standard Template — `SetupDoc-v<N>.md`

```markdown
# Setup Documentation — Version <N>: <Title>

**Part:** <Part number and title>
**Prerequisite versions completed:** v1...v<N-1>
**Estimated setup time:** <realistic estimate>

---

## 1. Overview
What this version adds, in 2-3 sentences. Link back to the roadmap entry.

## 2. VM Setup
(Per STD §VM Hostnames/§Environments; reference it, don't repeat it verbatim; call out anything version-specific)
- VM(s) involved:
- New packages to install:
- Ports to open:

## 3. Pre-Deployment Checklist
- [ ] Previous version's SetupDoc completed and verified
- [ ] VM snapshot taken (pre-v<N>)
- [ ] Git branch `feature/v<N>-<desc>` created from latest `develop`

## 4. Step-by-Step Configuration

### 4.1 WebSphere Admin Console Steps
Numbered, exact clicks/fields, with **expected result shown inline** after each step — no "run and paste back."

### 4.2 wsadmin / Command-Line Steps (if applicable)
Exact commands and expected console output shown inline.

### 4.3 Database Changes (if applicable)
Reference the specific migration script: `V<N>__<description>.sql` (per DBS01, Section 4 below). Show the exact command to run it and expected confirmation output.

### 4.4 Application Deployment
EAR/WAR build and deploy steps, referencing the artifact naming convention (`digistack-<app>-v<N>.ear`/`.war`, per STD).

## 5. Verification Steps
How to confirm the version actually works — cross-references `TestCases-v<N>.md` (TCS01, Section 2 below), doesn't duplicate it.

## 6. Rollback Procedure
Exact steps to undo this version's changes if something breaks — VM snapshot restore, or manual undo steps if snapshot isn't available (e.g., DB rollback script from `/db/rollback/`, EAR redeploy of previous version).

## 7. Known Issues / Troubleshooting
Populated as issues are actually found during your build — this section grows over time and becomes genuinely valuable.

## 8. Sign-off
- [ ] Setup completed successfully
- [ ] All verification steps passed
- [ ] Documentation reviewed for accuracy (i.e., you actually followed it start to finish, not just skimmed)

---
```

## Documentation Discipline Rules
1. **Write it as you go, not after.** The setup doc for v<N> is drafted *during* that version's sprint, refined during testing, finalized at approval — not reconstructed from memory afterward.
2. **No skipped steps.** If a step was "just click around until it worked," that's a documentation gap — go back and capture the actual click path.
3. **Screenshots optional but encouraged** for Admin Console-heavy steps (SSL, Security, Clustering).
4. **Cross-reference, don't duplicate.** SetupDoc references TestCases-v<N>.md and the DB migration script by filename rather than repeating their content.

## File Location
```
/docs
    /setup
    /testcases
    /incidents      ← IncidentSim-v<N>.md files
    /faultdrills    ← FaultDrill-v<N>.md files
    ...
```
Committed to Git alongside the code for that version (per STD §Git).

===============================================================================
# SECTION 2 — TCS01: Test Case Standards (doc 03)
===============================================================================

Every version from P01 v1 onward references `TestCases-v<N>.md` (e.g.,
regression packs in P07 v45, smoke-test subsets in P08 v52, negative
tests in P03 v23) without a standing definition of what that file
contains or how a test case is scored. This section is that definition.

## 2.1 File Location & Naming
```
/docs/testcases/TestCases-v<N>.md
```
One file per version, committed alongside that version's code. Never
merged across versions — each version's test cases are frozen once
approved, consistent with the Version Numbering Freeze discipline used
throughout this project.

## 2.2 Test Case ID Convention
```
TC-v<N>-<seq>
```
Example: `TC-v23-07` = the 7th test case for Version 23. Negative tests
(proving something is correctly blocked) use the same numbering — a
negative test is not a separate category, just a test case whose expected
result is a failure/rejection.

## 2.3 Standard Template — `TestCases-v<N>.md`

```markdown
# Test Cases — Version <N>: <Title>

**Part:** <Part number and title>
**Prerequisite versions' regression pack:** TestCases-v1.md ... v<N-1>.md

## Test Case Table

| ID | Description | Type | Priority | Steps | Expected Result | Actual Result | Status | Notes |
|---|---|---|---|---|---|---|---|---|
| TC-v<N>-01 | | Functional/Negative/Regression | Critical/High/Medium/Low | | | | Pass/Fail/Blocked | |

## Sign-off

| Item | Status |
|---|---|
| All Critical cases passed | |
| All High cases passed | |
| No open Critical/High defects | |
| Regression subset re-run (per §2.6 below) | |
| Reviewed by | |
| Approved date | |
```

## 2.4 Priority / Severity Model

| Priority | Definition | Sign-off Rule |
|---|---|---|
| Critical | Core money-movement or single-writer-rule integrity (Fund Transfer, balance update, CBS write path) | Must pass — zero exceptions, at every promotion gate |
| High | Primary feature path for the version | Must pass — zero open High defects before UAT promotion |
| Medium | Secondary path, edge case, non-blocking UX | Documented if failed; doesn't block promotion |
| Low | Cosmetic, nice-to-have | Documented if failed; never blocks promotion |

This model is the one EPS01's Prod Promotion Checklist and P07/P09/P10's
"Migration Success Criteria" ("no open Critical/High defects") both point
back to — defined once, here.

## 2.5 Test Case Types
- **Functional** — proves a feature works as specified.
- **Negative** — proves something is correctly rejected/blocked (e.g.,
  P03 v23's write-access negative tests, P06 v41's mismatched-cert test).
- **Regression** — re-run of a prior version's test case against the
  current build.
- **Smoke** — the minimal subset (login, balance, Fund Transfer) used as
  an automated go/no-go gate, not a full regression pass.

## 2.6 Regression Pack Convention
A "full regression pack" = every prior version's Critical + High test
cases, re-executed against the current build. This is what P07 v45, P08
v53, P09 v63/v68/v73, and P10 v78 all mean when they say "regression pack
passes" — defined once, here.

## 2.7 Sign-off Rubric
A version is signed off when, and only when:
1. Every Critical test case: Pass.
2. Every High test case: Pass.
3. No open Critical or High defect remains (Medium/Low may remain open, documented).
4. The relevant Regression Pack (§2.6) has been re-run and passes.
5. Reviewer/approver and date recorded in the Sign-off table.

===============================================================================
# SECTION 3 — EPS01: Environment Promotion Standards (doc 04)
===============================================================================

The single most cross-referenced missing document in the project — cited
from P03 onward for Dev/UAT/Prod checklists, and explicitly by the
Progress Log's "Migration Cutover Status" and "AWS Migration Cutover
Status" tables. This section supplies that content.

## 3.1 Standard Promotion Path
Per STD: `DEV → TEST → UAT → PROD → DR`. This standard governs the gates
between each stage.

## 3.2 Dev → UAT Checklist
- [ ] All Critical + High test cases in `TestCases-v<N>.md` pass (TCS01)
- [ ] No open Critical/High defect
- [ ] SetupDoc-v<N>.md (SDD01) complete and followed start-to-finish
- [ ] Code reviewed / PR approved (P08 v49's branch protection once live; manual self-review checklist before then)
- [ ] Rollback procedure documented and dry-run tested
### 3.2a Solo Review Discipline
Since this is a solo build, formal PR review isn't available until P08
v49's branch protection goes live. Self-review against TCS01's sign-off
rubric (§2.7) substitutes for peer review in the interim — "simulate the
artifact even solo." Every version's sign-off table still requires
"Reviewed by" and "Approved date," filled in as a genuine self-review
checkpoint, not skipped as N/A.

## 3.3 UAT → Prod Checklist ("Prod Promotion Checklist")
- [ ] Dev → UAT checklist items all still true
- [ ] Full Regression Pack (TCS01 §2.6) passes in UAT
- [ ] Change Request raised and approved (§3.6 below)
- [ ] Backup taken immediately before promotion (per STD §Backup)
- [ ] Smoke test subset ready to run immediately post-deploy
- [ ] Maintenance/deployment window communicated in advance

## 3.4 Post-Promotion Verification (any environment)
- [ ] Smoke test passes in the target environment
- [ ] Application/infra health confirmed (Grafana/health endpoints once P04 exists; manual Admin Console check before then)
- [ ] Version tag applied in Git

## 3.5 Multi-Region Promotion Rules (governs P06 onward)
Once a Part spans more than one region (P06 v39+), promotion follows a
**one-region-at-a-time** discipline, not a simultaneous multi-region push:
1. Promote Dev → UAT → Prod in the first region (India) using §3.2-3.4.
2. Confirm post-promotion verification holds for a defined observation
   window before starting the next region.
3. Repeat for Singapore, then Dubai.
4. **No two-of-three mostly-promoted state.** A Part is not tagged
   `part<N>-release` until every region has independently completed the
   full Prod Promotion Checklist.

## 3.6 Migration Part Promotion (governs P07 — WebSphere Migration)
- While cutover is in progress, the Progress Log's Current Status Summary
  table must show **`Cutover in-flight`** — not "In progress," not blank.
- A per-region cutover state table (Migration Cutover Status) tracks:
  Platform State, Canary %, Post-Cutover Observation Window Closed?, Old
  Platform Decommissioned? — independently per region.
- The Part is not tagged `part<N>-release` until **every region** reads
  "Cut over — decommissioned" **and** the Current Status Summary is
  updated to `part<N>-release` in the same edit.
- Old platform decommissioning only happens after a defined post-cutover
  observation window closes cleanly with no rollback triggered.

## 3.7 Phased Cloud Migration Promotion (governs P09 — AWS Migration)
- **Phase capstones (v58, v63, v68) are Dev-only gates** — not UAT/Prod
  promotion events in their own right.
- **Actual UAT/Prod promotion happens once, at the end** — after v73 —
  across all three regions, using §3.3 plus §3.5.
- The AWS Migration Cutover Status table's columns are updated
  **incrementally**, per region, as each phase/region reaches that
  milestone — not held back until v73.
- `part9-release` is applied only once every region reads "Cut over — old
  on-prem decommissioned" **and** every region has independently
  completed the Prod Promotion Checklist.

## 3.8 Change Request / CAB Convention
- **Routine promotion**: promoted via §3.2-3.4 without a separate CR.
- **Change Request required** when: a DB schema migration is involved
  (per DBS01 §4.4), a cross-cutting infra change is involved (WAS
  version, cert rotation per CI01, network change), or the change touches
  more than one application.
- **CAB Approval** (simulated): required before any Migration Window or
  Migration Freeze Window opens. **Emergency CAB** (expedited, post-hoc
  documented) is available for a genuine production incident fix.
- **Change Freeze**: no unrelated infra changes permitted on any
  environment touched by an active migration window.
- **Code Freeze**: no new commits to `develop` for applications being
  migrated, for the duration of a migration window.

## 3.9 Environment Config & Secrets (cross-reference)
Every environment's config differences are injected via externalized,
per-environment `.properties` files (STD §Config; DBS01 §2) — promotion
never triggers a rebuild of the artifact per environment (formalized by
P08 v50's Immutable Artifact Promotion Principle once that Part exists;
the same discipline applies manually before then).

===============================================================================
# SECTION 4 — DBS01: DB Deployment Standards (doc 05)
===============================================================================

STD §SQL Migration already defines the migration file naming convention
(`V<N>__<description>.sql`, rollback in `/db/rollback/`) — that part was
never missing. What was missing is the Connection & Credentials Standard
and the DDL-vs-DML separation rule, both cited elsewhere without ever
being written down. Supplied here.

## 4.1 SQL Migration Convention (cross-reference, not restated)
Owned by STD §SQL Migration — see STD directly.

## 4.2 Connection & Credentials Standard
- **No hardcoded connection strings, usernames, or passwords** anywhere
  in code, config committed to Git, Jenkinsfiles, or SetupDoc-v<N>.md.
  Every credential resolves at runtime via a JNDI-bound resource.
- **JAAS Auth Alias** is the sole mechanism by which a WAS-managed
  DataSource resolves DB credentials (P01 v7's foundation). The alias
  name is committed to config; the credential value it resolves to is
  never committed anywhere.
- **Per-environment `.properties` files** hold JNDI names and connection
  targets that differ by environment/region — never credential values.
  Convention: `application-<env>-<region>.properties`.
- **Backing store evolution**: local JAAS Auth Alias credential store
  (P01-P08) → AWS Secrets Manager as backing store once P09 v67 is
  reached. The JAAS Auth Alias abstraction in WAS itself is unchanged —
  only what backs it changes.
- **Least-privilege DB accounts**: CBS's `CBSDataSource` connects with
  read/write on `banking`/`audit`/`reporting` schemas within
  `digistack_cbs` only. Every other application's DB-facing account
  (where one exists, e.g. Reporting Service's accepted direct-read
  tradeoff) is granted read-only, never write — enforced via the
  negative tests required at P03 v23.
- **Pipeline credentials** (once P08 v50 exists): stored in the Jenkins
  Credentials Store, never in a Jenkinsfile or console log; the Jenkins
  service account is least-privilege — deploy rights only, cannot drop
  `digistack_cbs`.

## 4.3 DDL-vs-DML Separation Rule
- **DDL** (schema changes) is delivered **exclusively** through numbered
  migration scripts (`V<N>__<description>.sql`), applied in sequence,
  never ad hoc against Prod.
- **DML** (routine data changes) happens exclusively through the
  application's own JDBC/DAO layer at runtime — never a manually run
  script against a live environment.
- **One-time data migrations** (e.g., P03 v23's
  `V23__migrate_existing_data_to_cbs.sql`) are the one case where DML
  appears inside a numbered migration script — permitted because it's a
  one-time, versioned, reviewed, rollback-planned cutover step, not
  routine data manipulation. Still numbered, committed, and
  rollback-scripted like any other migration.
- **Rule of thumb**: changes what a table looks like → migration script.
  Changes what's in a table as part of normal running → application's job.
- **The SQL that runs in Prod must be byte-identical to what was tested
  in UAT** — no environment-specific hand-editing of a migration script;
  environment differences belong in `.properties` files (§4.2), never in
  the SQL itself.

## 4.4 Schema Change → Change Request Trigger (cross-reference)
Any DDL change (§4.3) automatically requires a Change Request under EPS01
§3.8 — this is the specific trigger EPS01 refers to.

===============================================================================
# SECTION 5 — CI01: Certificate Inventory (doc 07 §7)
===============================================================================

STD §Ports already supplies the Port Matrix (doc 07 §6); the Certificate
Inventory (§7) — referenced by P06 v41, P07 v46, P09 v67, and P05 v38's
backup inventory — never existed as an actual table. Supplied here, and
extended incrementally by every version that introduces a new cert, same
drift-prevention rule STD's Port Matrix already follows.

## 5.1 Certificate Naming Convention
```
digistack-<scope>-<purpose>-<region?>.crt/.key
```
Examples: `digistack-ihs-webtier.crt` (P01 v11), `digistack-mtls-internal-hop.crt`
(P01 v12), `digistack-mtls-crossregion-in-sg.crt` (P06 v41).

## 5.2 Certificate Inventory Table

| Cert | Scope | Introduced | Type | Renewal Cadence | Notes |
|---|---|---|---|---|---|
| `digistack-ihs-webtier.crt` | IHS HTTPS (browser-facing) | P01 v11 | Self-signed | Annual | HTTP→HTTPS redirect enforced |
| `digistack-mtls-internal-hop.crt` | WAS↔DB or IHS↔plugin internal hop | P01 v12 | Self-signed | Annual | At least one internal hop, per NFR matrix |
| `digistack-mq-chlauth.crt` | MQ channel auth (CHLAUTH+TLS) | P02 v19 | Self-signed | Annual | External payment leg |
| `digistack-mtls-crossregion-<r1>-<r2>.crt` | Cross-region CBS↔CBS mTLS | P06 v41 | Self-signed, regional CA-issued | Annual | One pair per region-to-region link; expiry/mismatch proven to fail closed |
| `digistack-ltpa-keyset` | LTPA token signing (not a TLS cert, tracked here for completeness) | P06 v42 | Symmetric key set | Synchronized across cells, not independently renewed | Must match across India/Singapore/Dubai |
| Migrated equivalents of the above | Same scopes, new keystores | P07 v46 | Imported (not regenerated) | Same as original | Imported into new keystores during platform migration, not reissued unless already expired |
| `digistack-acm-alb-<region>.cert` | AWS ACM-managed, fronts AWS ALB/IHS tier | P09 v67 | ACM-issued/managed | Auto-renewed by ACM | New row added at v67 per that version's Sprint Deliverable |

## 5.3 Renewal / Expiry Tracking Rule
- Every cert above carries an explicit renewal cadence — "renew when it
  breaks" is not a valid entry.
- Expiry tracking is checked as part of P04 v35's Production Reporting
  (Weekly Operations Report) once that Part exists; before then, tracked
  manually against this table.
- **Migration behavior (P07 v46)**: certs are imported into new keystores
  during a platform migration, never blindly regenerated — regeneration
  only happens if a cert is already expired at time of migration.
- **AWS ACM behavior (P09 v67)**: once a cert's scope moves to an
  ACM-fronted ALB, it is removed from manual renewal tracking (ACM
  auto-renews) but the row stays, marked "ACM-managed."
- **Re-audit (P09 v72)**: this table is checked again in full at P09
  v72's formal security/compliance pass.

===============================================================================
# Master Index Update -- Completed
===============================================================================
Done. The Master Index's Legacy "doc NN" Reference Map has been updated
to point doc 03 -> TCS01 (Section 2), doc 04 -> EPS01 (Section 3), doc 05 ->
DBS01 (Section 4), doc 06 -> SDD01 (Section 1), and doc 07 section 7 -> CI01
(Section 5), all within this single file (STDGAP01), rather than five
separate files -- and its "Known Gap" note is marked resolved. See IDX's
own "Legacy 'doc NN' Reference Map -- Resolved" section for the live copy
of this table; this section is no longer an open instruction.

===============================================================================
# SECTION 6 — Addendum: Updated Master Index Reference Map
===============================================================================

The Master Index's Legacy "doc NN" Reference Map table currently reads
several rows as "not written anywhere in the project." As of this file,
that is no longer accurate. The Master Index's table should be corrected
to read:

| Referenced As | Subject Matter | Actual Current Location |
|---|---|---|
| doc 01 | VM Setup Standards | STD §VM Hostnames / §Environments |
| doc 02 | Git Standards | STD §Git / §Commit Format |
| doc 03 | Test Case Standards | **STDGAP01, Section 2 (TCS01)** |
| doc 04 | Environment Promotion Standards | **STDGAP01, Section 3 (EPS01)** |
| doc 05 | DB Deployment Standards | STD §SQL Migration (naming) + **STDGAP01, Section 4 (DBS01)** (Connection & Credentials, DDL/DML separation) |
| doc 06 | End-to-End Setup Documentation Standards | **STDGAP01, Section 1 (SDD01)** — now carries a proper ID/metadata block |
| doc 07 | Configuration & Cross-Cutting Standards | STD §Ports (Port Matrix, §6) + **STDGAP01, Section 5 (CI01)** (Certificate Inventory, §7) |
| doc 14 | Gap-analysis behind P04's Observability rewrite | Folded into P04 directly; no standalone file, none expected |

The Master Index's "Known Gap" note is resolved as of this file and
should be marked closed, not left standing.

===============================================================================
# SECTION 7 — Addendum: Environment Notes Field Guidance
===============================================================================

The Progress Log's "Environment Notes" section is intentionally blank
until an actual build exists — it isn't a documentation gap the way doc
03-07 were, since there's nothing to document before P01 v1 is built. To
prevent it becoming a silent gap once building starts, the fields are
formalized here so whoever fills them in knows exactly what's expected
and where the value comes from:

| Field | Filled in at | Source of truth |
|---|---|---|
| WAS ND version installed | P01 v1 | Recorded in SetupDoc-v1.md (SDD01 §4.1), then mirrored here |
| Profile(s) created so far | P01 v1 onward, updated every version that adds one | SetupDoc-v<N>.md's §4.1/§4.2 |
| Node/Cell/Cluster names in use | P01 v5/v6 (DMgr + federation) onward | SetupDoc-v5.md / v6.md, per STD §VM Hostnames naming |
| Database (PostgreSQL version / host / port / DB name) | P01 v1 (initial shared DB), updated at P03 v23 (digistack_cbs cutover) | SetupDoc-v1.md and SetupDoc-v23.md; **no password ever recorded here — see DBS01 §4.2 (Section 4)** |
| IBM HTTP Server installed (Y/N) | P01 v8 | SetupDoc-v8.md |
| Any deviations from the roadmap so far | Ongoing, any version | Whichever version's SetupDoc first introduces the deviation, cross-referenced here |

Rule: this table is a **pointer/summary**, not a second source of truth —
the authoritative detail always lives in that version's SetupDoc-v<N>.md
(SDD01), per SDD01's own "cross-reference, don't duplicate" discipline.
If this summary and a SetupDoc ever disagree, the SetupDoc governs and
this table must be corrected to match — same precedent as STD's own
Dependency Matrix / IDX sync rule.

---

*This is STDGAP01. Companion/parent standard: STD (Standing Rules — owns
the conventions each section here cross-references rather than
duplicates). This file closes the Master Index's standing gap table in
full, including its own reference-map correction (Section 6) and the
Progress Log's Environment Notes field discipline (Section 7).*
