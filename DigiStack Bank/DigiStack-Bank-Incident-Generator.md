# DigiStack Bank — Production Incident Sprint
# Version: 2.0
# Trigger Phrase: "Incident Sprint" OR "Generate Incident" OR "Continue Sprint"
#
# HOW THIS WORKS:
# After completing any version sprint, say:
#   "Incident Sprint" or "Generate Incident" or "Continue Sprint"
#
# Claude will automatically:
#   - Detect which version was just completed from conversation context
#   - Auto-populate VERSION, VERSION TOPIC, ENVIRONMENT
#   - Execute STEP 1 (plant the break) immediately — no manual fill-in needed
#   - Wait for you to confirm BROKEN
#   - Then generate the full incident ticket
#
# You never need to fill in anything manually.
# Just trigger the phrase and Claude does the rest.

---

## SPRINT DEFINITION — INCIDENT SPRINT

Sprint Name  : Incident Sprint
Sprint Type  : Production Incident Simulation
Trigger      : "Incident Sprint" / "Generate Incident" / "Continue Sprint"
Runs After   : Every version sprint completion (v1 through v14 in P01)
Duration     : Self-paced — ends when student types INCIDENT CLOSED

---

## AUTO-DETECTION RULES FOR CLAUDE

When this sprint is triggered, Claude MUST:

1. Read the conversation context to identify:
   - VERSION      → the version just completed (e.g. v8)
   - VERSION TOPIC → the WebSphere topic of that version (from VERSION REFERENCE table below)
   - ENVIRONMENT  → the infra stack active at that version (from ENVIRONMENT STATE table below)

2. Do NOT ask the student to fill in anything.
   Auto-populate all three fields from context before proceeding.

3. Confirm auto-detection at the top of STEP 1 output:
   > Auto-detected: VERSION = vN | TOPIC = <topic> | ENV = <env summary>
   > Proceeding with Incident Sprint.

4. Then execute STEP 1 immediately.

---

## STEP 1 — PLANT THE BREAK

Generate ONE realistic misconfiguration or error condition for the student
to apply manually to their environment before the incident ticket is raised.

Rules:
- Misconfiguration MUST relate ONLY to the technology/topic of the detected version
- Provide: exact file path / config block / command — no ambiguity
- Format: FIND block → CHANGE TO block (copy-paste ready)
- Include: the restart or reload command to activate the break
- Include: one curl or browser check to confirm the break is live
- Do NOT explain why this breaks anything
- Do NOT hint at the fix
- Each version must produce a DIFFERENT misconfiguration — never repeat across versions
- End STEP 1 with exactly:
  "Confirm the break is live, then reply: BROKEN"

---

## STEP 2 — GENERATE THE INCIDENT TICKET

Trigger: Student replies BROKEN (or "broken" / "it's broken" / "confirmed broken")

Generate EXACTLY ONE realistic production incident in this format:

════════════════════════════════════════════════════════
INCIDENT ID:          INC-<YYYY>-<MMDD>-<NNN>
SEVERITY:             <SEV-1 / SEV-2 / SEV-3>
TIME:                 <IST timestamp — realistic for lab session>
APPLICATION/SERVICE:  DigiStack Bank — <affected component>
BUSINESS IMPACT:      <one or two sentences — banking business language>

CUSTOMER/BUSINESS SYMPTOM:
<What the customer or business sees — zero technical jargon>

INITIAL ALERT/TICKET:
<NOC/monitoring alert text that triggered this incident>

OBSERVED ERROR:
<Exact log output — WAS SystemOut.log / IHS error_log / plugin.log /
 DB log / MQ log / browser error — with realistic timestamps.
 Must exactly match the misconfiguration planted in STEP 1.>

SCOPE:
<Exactly what is broken — be specific>

NOT AFFECTED:
<What still works — helps narrow the investigation>

RECENT CHANGE:
<CHG reference + plausible description. Must NOT reveal root cause.
 Omit this section entirely if no recent change is realistic.>

STARTING EVIDENCE:
<Only what is visible at incident creation — monitoring dashboard,
 Admin Console status, first log lines. Nothing the engineer
 has not yet looked at.>
════════════════════════════════════════════════════════

STOP HERE. Wait for the student to investigate.

NEVER provide after STEP 2:
- Root cause
- Fix or solution
- Troubleshooting steps or commands
- Configuration changes
- Hints or nudges
- Expected diagnosis
- RCA

Student investigates using:
WAS Admin Console | wsadmin | IHS logs | WAS SystemOut.log |
App logs | DB tools | MQ tools | Linux commands

---

## STEP 3 — RCA REVIEW (Only On Explicit Request)

Trigger: Student types exactly — RCA REVIEW

Then and ONLY then, provide:
- Confirmed root cause (mapped to the STEP 1 misconfiguration)
- Why it caused the observed symptoms
- Full resolution steps
- Prevention / permanent fix recommendation
- Lessons learned (1–3 bullet points, WAS admin perspective)

---

## STEP 4 — CLOSE THE INCIDENT

Trigger: Student types — INCIDENT CLOSED

Output a brief closure summary:

INCIDENT CLOSED
───────────────
Incident ID   : INC-<same ID>
Version       : vN — <topic>
Break Planted : <one line — what was misconfigured>
Root Cause    : <one line — only after RCA REVIEW was given, else PENDING>
Resolution    : <one line summary>
Duration      : <student-reported or "not recorded">
Logged to     : Incident Log (update the table at bottom of this file)

---

## VERSION REFERENCE TABLE (P01 — Claude uses this for auto-detection)

| Version | WebSphere Topic                              | Sprint Deliverable Summary                        |
|---------|----------------------------------------------|---------------------------------------------------|
| v1      | First EAR Deployment                         | EAR deployed, context root live, PostgreSQL read  |
| v2      | Login & Session                              | Login/logout, session attribute, EAR redeploy     |
| v3      | Basic Transactions (Deposit & Withdraw)      | Controller→DAO→DB end-to-end, Dashboard.jsp       |
| v4      | EAR Update, Rollback & App Lifecycle         | Rollback to v3 verified, v4 redeployed            |
| v5      | WAS Clustering                               | 2-member cluster, session replication, failover   |
| v6      | Application Administration (wsadmin)         | Freeze/Unfreeze via UI + wsadmin script           |
| v7      | WAS JDBC (DataSources, JNDI, Pool)           | All DB access via JNDI DataSource, no hardcoding  |
| v8      | IBM HTTP Server (IHS)                        | IHS front door, plugin-cfg.xml, custom 404/500    |
| v9      | Session Management                           | Sticky sessions, timeout, memory-to-memory repl   |
| v10     | Users, Groups & Administrative Security      | Customer/Administrator roles enforced             |
| v11     | SSL — HTTPS at Web Tier                      | Self-signed cert, HTTPS on IHS, HTTP redirect     |
| v12     | WAS SSL End-to-End (mTLS)                    | SSL full hop chain, mTLS on one internal hop      |
| v13     | Notifications (JavaMail / JNDI Mail Session) | Withdraw triggers real email via WAS Mail Session |
| v14     | Reports & JVM Heap Tuning                    | Large report, no OOM, before/after PMI/GC proof   |

---

## ENVIRONMENT STATE TABLE (P01 — Claude uses this for auto-detection)

| Version | Active Infrastructure                                                        |
|---------|------------------------------------------------------------------------------|
| v1      | Standalone AppServer, PostgreSQL, no cluster, no IHS, no SSL                |
| v2      | Standalone AppServer, PostgreSQL, no cluster, no IHS, no SSL                |
| v3      | Standalone AppServer, PostgreSQL, no cluster, no IHS, no SSL                |
| v4      | Standalone AppServer, PostgreSQL, no cluster, no IHS, no SSL                |
| v5      | DMgr, 2-member cluster (node1/node2), PostgreSQL, no IHS, no SSL            |
| v6      | DMgr, 2-member cluster, PostgreSQL, wsadmin, no IHS, no SSL                 |
| v7      | DMgr, 2-member cluster, PostgreSQL via JNDI DataSource, no IHS, no SSL      |
| v8      | DMgr, 2-member cluster, PostgreSQL via JNDI, IHS installed, no SSL          |
| v9      | DMgr, 2-member cluster, PostgreSQL via JNDI, IHS, no SSL                    |
| v10     | DMgr, 2-member cluster, PostgreSQL via JNDI, IHS, file/LDAP registry        |
| v11     | DMgr, 2-member cluster, PostgreSQL via JNDI, IHS with HTTPS, SSL at web tier|
| v12     | DMgr, 2-member cluster, PostgreSQL via JNDI, IHS, SSL end-to-end + mTLS     |
| v13     | All of v12 + WAS Mail Session (JNDI), SMTP configured                       |
| v14     | All of v13 + JVM heap tuned, PMI/GC monitoring active                       |

---

## INCIDENT LOG — P01 (Update after each version incident sprint)

| Version | Incident ID       | Misconfiguration Summary       | RCA Given | Status    |
|---------|-------------------|--------------------------------|-----------|-----------|
| v8      | INC-2026-0826-007 | plugin-cfg.xml wrong port 9083 | No        | Completed |
|         |                   |                                |           |           |
|         |                   |                                |           |           |
|         |                   |                                |           |           |
|         |                   |                                |           |           |
|         |                   |                                |           |           |
|         |                   |                                |           |           |
|         |                   |                                |           |           |
|         |                   |                                |           |           |
|         |                   |                                |           |           |
|         |                   |                                |           |           |
|         |                   |                                |           |           |
|         |                   |                                |           |           |
|         |                   |                                |           |           |

*(One row per version. Update Status: In Progress / Completed. RCA Given: Yes / No)*

