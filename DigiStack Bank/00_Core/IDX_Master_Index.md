ID: IDX
Version: 1.4
Status: Active

Title: Master Index

Imports:
None

Exports:
Part sequence
Phase/folder structure
Dependency matrix
File map

Used By:
STD
ARCH01
ARCH02
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
STD

---

File Map
--------
IDX      Master Index (this file)
STD      Standing Rules
STDGAP01 Consolidated Standing Standards (Test Case/Environment Promotion/
         DB Deployment/Setup Doc/Certificate Inventory standards)
SOE01    Standard Operating Environment — Golden Image Specification
CAP01    Capacity & Sizing Standard
RACI01   Operational RACI — Production Support Ownership Matrix
Progress_Log.md    Project progress tracker — version log, dependency chain,
                    cutover status, environment notes (not a dependency of
                    any Part; replaces the earlier "PROG" placeholder)
ARCHVIZ  Architecture Diagram Set (01_Architecture/) — Network, VM
         Layout, Request Flows, Cluster, MQ, DB ER, Security,
         Deployment, DR diagrams. Companion to ARCH01/ARCH02 (which
         remain the text/prose architecture source of truth); ARCHVIZ
         is the visual layer, populated incrementally.
ARCH01   Enterprise Architecture
ARCH02   Solution Architecture
P01      Foundation
P02      WebSphere / Enterprise Middleware Integration
P03      Enterprise Banking Systems (Applications)
P03.1    Interview Preparation — Interview Drills (suffix Part — see
         Placement Note)
P03.2    Interview Preparation — Interview Book (Phase-1 companion
         reference; suffix to P03.1, no versioned deployables)
P04      Enterprise Observability, SRE & Production Operations
P05      Enterprise HA, DR & Business Continuity
P06      Multi-Region Enterprise Banking & Middleware Architecture
P07      Enterprise WebSphere Migration & Modernization
P07.1    WAS Migration Interview Book (companion reference; suffix to P07,
         no versioned deployables)
P08      Enterprise DevOps & End-to-End Automation
P08.1    DevOps & Automation Interview Book (companion reference; suffix
         to P08, no versioned deployables)
P09      Enterprise Hybrid Cloud & AWS Migration
P09.1    Cloud Migration Interview Book (companion reference; suffix to
         P09, no versioned deployables)
P10      Containerization & Cloud-Native Modernization
P10.1    Containerization Interview Book (companion reference; suffix to
         P10, no versioned deployables)

---

Folder / Phase Structure (Authoritative)
------------------------------------------
This project runs across 8 phases, one top-level folder per phase (Phase-1
is the sole multi-Part folder — P01, P02, P03 share one folder since they
form a single continuous application build):

00_Core/                          ← Shared reference docs, no Part number
                                     (IDX, STD, STDGAP01, SOE01, CAP01,
                                     RACI01, ARCH01, ARCH02, Progress_Log.md)
                                     Loaded alongside whichever Phase
                                     folder is active — never duplicated.

01_Architecture/                  ← Cross-cutting diagram set (no Part
                                     number, not phase-sequenced — same
                                     "load alongside whichever phase is
                                     active, when that diagram is
                                     relevant" treatment as 00_Core)
  README.md (index + fill-in trigger table)
  01_Network_Diagram.md
  02_VM_Layout.md
  03_Request_Flows.md
  04_Cluster_Architecture.md
  05_MQ_Architecture.md
  06_Database_ER_Diagram.md
  07_Security_Architecture.md
  08_Deployment_Architecture.md
  09_DR_Architecture.md
  (Enterprise Architecture itself is NOT duplicated here — see ARCH01/
  ARCH02 in 00_Core, which remain the text source of truth.)

02_Application_Development/       ← Phase-1 (P01 – P03)
  P01_Foundation.md / P01_Sprint_Plan.md
  P02_Middleware.md / P02_Sprint_Plan.md
  P03_Banking_Systems.md / P03_Sprint_Plan.md

03_Interview_Prep/                ← Phase-1 Companion (P03.1, P03.2)
  P03.1_Interview_Drills.md
  P03.2_Interview_Book.md

04_Observability/                 ← Phase-2 (P04)
  P04_Observability.md

05_HA_DR/                         ← Phase-3 (P05)
  P05_HA_DR.md

06_Multi_Region/                  ← Phase-4 (P06)
  P06_Multi_Region.md

07_WAS_Migration/                 ← Phase-5 (P07, P07.1)
  P07_WebSphere_Migration.md
  P07.1_Interview_Book.md

08_Automation/                    ← Phase-6 (P08, P08.1)
  P08_DevOps_Automation.md
  P08.1_Interview_Book.md

09_Cloud_Migration/                ← Phase-7 (P09, P09.1)
  P09_AWS_Migration.md
  P09.1_Interview_Book.md

10_Containerization/               ← Phase-8 (P10, P10.1)
  P10_Containerization.md
  P10.1_Interview_Book.md
  P10.1_Interview_Book.md

Rule: each folder from 03_Observability onward is a single-Part phase —
unlike Phase-1, there is no bundling. 02_Interview_Prep is a *companion*
folder to Phase-1, not a phase in its own numbered sequence — it sits
between Phase-1 and Phase-2 in the Sequence below, same as each `.1`
Interview Book sits alongside its own parent Part inside that Part's own
folder (06_WAS_Migration, 07_Automation, 08_Cloud_Migration,
09_Containerization) rather than in a separate companion folder — this is
a deliberate structural difference from P03.1/P03.2, which do get their
own folder. If this asymmetry is ever revisited, document the change here
first, per P-10.

Sequence
--------
P01 → Foundation
 ↓
P02 → WebSphere / Middleware Integration
 ↓
P03 → Banking Applications (CBS, Payments, Channel Simulators, Loans)
 ↓
P03.1 → Interview Preparation — Interview Drills (suffix Part, own folder
         02_Interview_Prep — no version numbers renumbered; see P03.1
         file's Placement Note)
 ↓
P03.2 → Interview Preparation — Interview Book (suffix Part, same folder
         as P03.1)
 ↓
P04 → Observability, SRE & Production Operations
 ↓
P05 → HA, DR & Business Continuity
 ↓
P06 → Multi-Region Banking & Middleware Architecture
 ↓
P07 → WebSphere Migration & Modernization
 ↓ (P07.1 — WAS Migration Interview Book, same folder as P07)
P08 → DevOps & End-to-End Automation
 ↓ (P08.1 — DevOps & Automation Interview Book, same folder as P08)
P09 → Hybrid Cloud & AWS Migration
 ↓ (P09.1 — Cloud Migration Interview Book, same folder as P09)
P10 → Containerization & Cloud-Native Modernization
 ↓ (P10.1 — Containerization Interview Book, same folder as P10)

Note: PROG (Progress_Log.md) runs alongside all Parts (updated after each
version), lives in 00_Core, and is not part of the sequence above. P10 is
currently the final Part in the roadmap — no further Part is scoped or
implied beyond it. P03.1 and P03.2 are suffix Parts (documentation/
preparation artifacts only, no versioned deployables) — neither carries a
version number in the v1-v78 sequence and neither shifts any Part's
numbering. The same is true of P07.1, P08.1, P09.1, and P10.1 — each is a
companion to its parent Part and is recommended to be completed before
the next phase begins.

Phase Structure
---------------
Phase-1 — Application Development (motive: learn WebSphere fundamentals
by building the bank) — folder: 02_Application_Development/
  P01    Foundation
  P02    WebSphere / Enterprise Middleware Integration
  P03    Enterprise Banking Systems (CBS, Payments, Channel Simulators, Loans)

Phase-1 Companion — folder: 03_Interview_Prep/
  P03.1  Interview Preparation — Interview Drills (readiness gate, not a
         technical dependency; recommended before Phase-2)
  P03.2  Interview Preparation — Interview Book (companion to P03.1)

Phase-2 — Enterprise Observability, SRE & Production Operations —
folder: 04_Observability/
  P04    Enterprise Observability, SRE & Production Operations

Phase-3 — Enterprise HA, DR & Business Continuity —
folder: 05_HA_DR/
  P05    Enterprise HA, DR & Business Continuity

Phase-4 — Multi-Region Enterprise Banking & Middleware Architecture —
folder: 06_Multi_Region/
  P06    Multi-Region Enterprise Banking & Middleware Architecture

Phase-5 — Enterprise WebSphere Migration & Modernization —
folder: 07_WAS_Migration/
  P07    Enterprise WebSphere Migration & Modernization
  P07.1  WAS Migration Interview Book (companion to P07)

Phase-6 — Enterprise DevOps & End-to-End Automation —
folder: 08_Automation/
  P08    Enterprise DevOps & End-to-End Automation
  P08.1  DevOps & Automation Interview Book (companion to P08)

Phase-7 — Enterprise Hybrid Cloud & AWS Migration —
folder: 09_Cloud_Migration/
  P09    Enterprise Hybrid Cloud & AWS Migration
  P09.1  Cloud Migration Interview Book (companion to P09)

Phase-8 — Containerization & Cloud-Native Modernization —
folder: 10_Containerization/
  P10    Containerization & Cloud-Native Modernization
  P10.1  Containerization Interview Book (companion to P10)

Rule: P03 is the application feature-completion point. No new banking
modules are introduced from P04 onward unless a later WebSphere topic
makes one strictly unavoidable — and if so, it must be explicitly called
out and justified in that Part's file, not silently added. The one
existing exception: P05 v38 adds idempotency-key handling on Fund
Transfer, documented there as a resilience/safety property retrofitted
onto an existing transaction, not a new module. P03.1/P03.2 introduce no
banking module either — they are documentation/preparation only, as are
P07.1/P08.1/P09.1/P10.1.

P10 is currently the final versioned Part in the roadmap. Fixed Deposits
& Recurring Deposits remain permanently unscoped within P01-P10 — see the
Progress Log's Resolved Decisions (this was an open question, closed
2026-07-22 once P10 confirmed it would not absorb the feature). Any
future banking-feature work or platform evolution beyond P10 requires a
new, explicitly-scoped Part proposed from scratch, with its own new
top-level folder — nothing is assumed to automatically follow P10.

Downstream planning documents (Design Documents, SDLC Blueprint, Sprint
Planning) are generated per-phase, not once for all Parts — Phase-1's
versions reference P01-P03 only (P03.1/P03.2 are excluded, since they
have no versions and produce no design/SDLC-relevant artifacts); each of
Phase-2 through Phase-8's versions reference only that phase's single
Part (P04 / P05 / P06 / P07 / P08 / P09 / P10 respectively, each
excluding its own `.1` companion for the same reason). Phase-1's
application design is frozen once Phase-2 begins (per P09's Standing
Architectural Note: "the applications never changed; only the platform
did"), so Phase-1 planning docs are not regenerated later — the same
freeze applies to every earlier phase once the next one begins.

Dependency Matrix
-----------------
Part  | IDX | STD | ARCH01 | ARCH02 | STDGAP01 | SOE01 | CAP01 | RACI01
P01   |  ✅ |  ✅ |   ❌    |   ❌    |    ✅     |   ✅   |   ✅   |   ❌
P02   |  ✅ |  ✅ |   ❌    |   ✅    |    ✅     |   ❌   |   ❌   |   ✅  ← changed
P03   |  ✅ |  ✅ |   ✅    |   ✅    |    ✅     |   ❌   |   ❌   |   ✅  ← changed
P03.1 |  ✅ |  ✅ |   ✅    |   ✅    |    ✅     |   ❌   |   ❌   |   ✅
P03.2 |  ✅ |  ✅ |   ✅    |   ✅    |    ✅     |   ❌   |   ❌   |   ✅
P04   |  ✅ |  ✅ |   ❌    |   ✅    |    ✅     |   ✅   |   ✅   |   ✅
P05   |  ✅ |  ✅ |   ❌    |   ✅    |    ✅     |   ❌   |   ✅   |   ✅
P06   |  ✅ |  ✅ |   ❌    |   ✅    |    ✅     |   ❌   |   ✅   |   ✅
P07   |  ✅ |  ✅ |   ✅    |   ✅    |    ✅     |   ❌   |   ❌   |   ✅  ← changed
P07.1 |  ✅ |  ✅ |   ✅    |   ✅    |    ✅     |   ❌   |   ❌   |   ✅
P08   |  ✅ |  ✅ |   ❌    |   ✅    |    ✅     |   ✅   |   ❌   |   ✅  ← changed
P08.1 |  ✅ |  ✅ |   ❌    |   ✅    |    ✅     |   ❌   |   ❌   |   ✅
P09   |  ✅ |  ✅ |   ✅    |   ✅    |    ✅     |   ✅   |   ✅   |   ✅  ← changed
P09.1 |  ✅ |  ✅ |   ✅    |   ✅    |    ✅     |   ❌   |   ✅   |   ✅
P10   |  ✅ |  ✅ |   ✅    |   ✅    |    ✅     |   ✅   |   ✅   |   ✅  ← changed
P10.1 |  ✅ |  ✅ |   ✅    |   ✅    |    ✅     |   ✅   |   ✅   |   ✅

Dependency Matrix correction (2026-08-05): RACI01 column flipped to ✅
for P02, P03, P07, P08, P09, P10. Trigger: STD's own Import Rule marks
RACI01 conditional on "the file exercises incident response, escalation,
or ownership during a failure/drill." As of STD v1.9's PIS01 addendum,
every version from P02 onward (v15-v78) runs a Sprint 7 incident-
response drill — the condition is now met project-wide for these Parts,
matching the standard their `.1` interview-book companions already met.
P01 is unaffected (PIS01 doesn't apply retroactively to v1-v14).

Legacy "doc NN" Reference Map — Resolved (2026-07-22, updated
2026-07-24 to remove superseded duplicate table)
------------------------------------------------------------------------------
Versions across P03–P10 and the Progress Log frequently cite documents by
an informal number ("doc 01," "doc 04 §6," "doc 07 §7," etc.) that
predates this project's current ID-based file system (IDX, STD, ARCH01,
ARCH02, DD01, DES01, FE01, P01–P10, P03.1, P03.2, STDGAP01). Per STDGAP01
Section 6, the mapping is resolved in full below — this is the single
authoritative version of this table. (A prior revision of this file
carried an earlier, pre-STDGAP01 draft of this table further down the
page that still listed doc 03/04/05 as unwritten; that draft is now
removed as superseded — this table replaces it.)

| Referenced As | Subject Matter | Actual Current Location |
|---|---|---|
| doc 01 | VM Setup Standards | STD §VM Hostnames / §Environments |
| doc 02 | Git Standards | STD §Git / §Commit Format |
| doc 03 | Test Case Standards | STDGAP01, Section 2 (TCS01) |
| doc 04 | Environment Promotion Standards | STDGAP01, Section 3 (EPS01) |
| doc 05 | DB Deployment Standards | STD §SQL Migration (naming convention) + STDGAP01, Section 4 (DBS01) (Connection & Credentials, DDL/DML separation) |
| doc 06 | End-to-End Setup Documentation Standards | STDGAP01, Section 1 (SDD01) — now carries a proper ID/metadata block |
| doc 07 | Configuration & Cross-Cutting Standards | STD §Ports (Port Matrix, §6) + STDGAP01, Section 5 (CI01) (Certificate Inventory, §7) |
| doc 14 | Gap-analysis behind P04's Observability rewrite | Folded into P04 directly; no standalone file, none expected |

This project's "Known Gap" note (previously standing since the original
audit) is closed. STDGAP01, SOE01, CAP01, and RACI01 are registered above
in the File Map and Dependency Matrix — no file in this project now
exists outside this Master Index's own tracking, and no stale/contradicting
copy of this table remains elsewhere in this file.