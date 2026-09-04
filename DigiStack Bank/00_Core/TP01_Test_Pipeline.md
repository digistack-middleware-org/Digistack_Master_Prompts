# TP01 — Test Pipeline Standard (Version 8 Onward)

ID: TP01
Version: 1.0
Status: Active
Applies From: P01 v8 onward, every version (v8–v78), every Part (P01–P10)

Authority: IDX → STD → STDGAP01 → TP01 (higher wins on conflict)

---

Purpose
-------
Defines the mandatory multi-environment test pipeline every version must
pass, from Version 8 onward. Sprint 6 (Test Cases) of every version now
executes this pipeline IN ADDITION to the standard TCS01/TCS02 test case
discipline. Versions 1–7 remain as-is (single-environment lab testing).

The pipeline mirrors a real enterprise promotion path. The banking app is
the same tiny EAR throughout — the pipeline is practiced around it.

---

The Pipeline (5 stages, strict order, no stage skipped)

STAGE 1 — DEVELOPMENT
  ├─ Unit / Component Tests
  ├─ Developer Tests (local build verification, developer-run)
  └─ Code Quality / Security Checks
      (no hardcoded credentials — grep check; naming conventions per
      STD v1.10; clean compile; Git commit format per STD)

STAGE 2 — SIT / TEST
  ├─ API Tests (servlet endpoints / context root paths)
  ├─ Integration Tests (code connects to DB/WAS/JNDI after deployment)
  ├─ Database Tests (schema, constraints, migration + rollback scripts)
  ├─ Middleware Tests (WAS config: DataSource, JNDI, Mail Session,
  │   plugin, security roles — whichever this version touched)
  ├─ End-to-End Tests (Browser→IHS→plugin→WAS→DB full chain)
  ├─ Negative Tests (invalid input, blocked actions, wrong role, 403/404/500)
  └─ Regression Tests (Regression Pack: all prior versions' Critical+High)

STAGE 3 — UAT
  ├─ Business Process Tests (Login, Deposit, Withdraw, Freeze/Unfreeze —
  │   and whatever business feature this version adds)
  ├─ Customer Journey Tests (login → view balance → deposit → withdraw →
  │   logout, as a real customer would)
  ├─ Financial / Accounting Validation (balance arithmetic exact; no
  │   lost/duplicated money; deposit +, withdraw −, over-withdraw blocked)
  └─ Business Acceptance (explicit "accepted" recorded before promotion)

STAGE 4 — PRE-PRODUCTION
  ├─ Production-like Smoke (smoke suite run against pre-prod-like config)
  ├─ Performance (only when CAP01 gives a numeric target for this version)
  ├─ Security (auth/authorization constraints, SSL hops per current state)
  ├─ DR / Recovery (VM snapshot restore OR backupConfig restore verified)
  ├─ Operational Readiness (logs reachable, monitoring/PMI as available,
  │   backup discipline current)
  └─ Deployment / Rollback (Update Application deploy + tested rollback)

STAGE 5 — PRODUCTION (lab "prod" = final signed-off state)
  ├─ Smoke Test (minimal go/no-go: login, balance, core transaction)
  ├─ Sanity Test (focused check of THIS version's new feature only)
  ├─ Monitoring Verification (SystemOut/GC/PMI clean, no errors on deploy)
  └─ Business Validation (one real end-to-end business action confirmed)

---

Rules
-----
R1. Stages run in order. A failed stage blocks promotion to the next.
R2. Every stage's result is recorded in TestCases-v<N>.md under a new
    section: "## TP01 Pipeline Results — v<N>" with a stage table:
    Stage | Check | Result (Pass/Fail) | Evidence (log line / screenshot / command output)
R3. Any Critical/High failure at any stage = no promotion, no sign-off.
R4. Medium/Low failures documented, do not block (per TCS01 priority model).
R5. Regression Pack (all prior versions' Critical + High) re-runs at
    Stage 2 every version — this satisfies the sign-off rubric item 4.
R6. "Production" in this lab = the final validated state after Sprint 7
    sign-off. Pre-production = the state just before final smoke.
R7. The pipeline does NOT add sprints — it lives inside Sprint 6 of each
    version, extending the existing 8-sprint structure (per STDGAP01
    TCS02.3, which stays unchanged).
R8. FaultDrill (Sprint 8) is unchanged and remains non-gating.

---

TestCases-v<N>.md Format Addition (from v8 onward)
---------------------------------------------------
After the standard TCS01 test case table, append:

## TP01 Pipeline Results — v<N>

| Stage | Check | Result | Evidence |
|---|---|---|---|
| DEV | Unit/Component | | |
| DEV | Developer Tests | | |
| DEV | Code Quality / Security | | |
| SIT | API | | |
| SIT | Integration | | |
| SIT | Database | | |
| SIT | Middleware | | |
| SIT | End-to-End | | |
| SIT | Negative | | |
| SIT | Regression Pack | | |
| UAT | Business Process | | |
| UAT | Customer Journey | | |
| UAT | Financial/Accounting | | |
| UAT | Business Acceptance | | |
| PRE-PROD | Production-like Smoke | | |
| PRE-PROD | Performance (if CAP01 target) | | |
| PRE-PROD | Security | | |
| PRE-PROD | DR/Recovery | | |
| PRE-PROD | Operational Readiness | | |
| PRE-PROD | Deployment/Rollback | | |
| PROD | Smoke | | |
| PROD | Sanity | | |
| PROD | Monitoring Verification | | |
| PROD | Business Validation | | |

All rows must be Pass (Critical/High) before Sprint 7 sign-off.
