ID: ARCHVIZ08
Version: 0.1 (scaffold)
Status: Not Populated

Title: Deployment Architecture

Imports:
ARCH02 (§2a Maven Project Structure)
STD (§Deployables)

Populate At: P01 v1 (first EAR), extended every version that changes
packaging or targets

---

## What this will show
EAR/WAR module layout (per ARCH02 §2a's Maven structure), deployment
targets, context root/virtual host mapping.

## Deployment Layout (as of P01 v1)

```
digistack-bank-parent/          (pom, parent POM)
├── digistack-bank-web/         (war — servlets/JSPs, controller,
│                                 DAO; per ARCH02 §2, no dao/ split
│                                 out yet, app is still tiny)
└── digistack-bank-ear/         (ear — assembles the WAR above)

`mvn clean package` at digistack-bank-parent/ produces
digistack-bank-v1.ear — first EAR deployment, per P01 v1's Sprint
Deliverable.
```

No `-ejb/` module exists yet — per ARCH02 §2a that module is CBS-only
and doesn't appear until the P03 CBS split; P01's app is plain
servlet/JSP, no session beans.

Deployment target: single standalone AppServer on dsb-dmgr (no cluster
until v5, no DMgr/cell federation until v5/v6 — dsb-dmgr today is a
lone AppServer profile). Context root and virtual host mapping are not
yet formally pinned in any standard document — flagging as open rather
than inventing a value; expect this settled by SetupDoc-v1.md.

## Status
**Reset — 2026-08-25 (full project reset #2).** The module layout above
is still the correct v1 target, but no EAR is actually deployed
anywhere right now — the lab VM (dsb-dmgr) was lost the same day this
was drawn. Treat as a rebuild target until `digistack-bank-v1.ear` is
actually redeployed and re-verified. Extend at any version that
changes packaging or targets.