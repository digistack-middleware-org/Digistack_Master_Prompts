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
targets, context root/virtual host mapping. Full EAR/WAR roadmap
(redraw trigger at each):

P01 (single EAR throughout):
  digistack-bank-vN.ear          (v1–v14, standalone AppServer → cluster)

P02 (still single EAR):
  digistack-bank-vN.ear          (v15–v22, cluster, IHS, MQ integrated)

P02 v22.5 (infrastructure only — no new EAR):
  Oracle JDBC Provider + shared library added to WAS

P03 (EAR split — 7 WAS EARs + 2 Tomcat WARs by end of Part):
  digistack-portal-vN.ear        (v23 — Internet Banking Portal, WAS)
  digistack-cbs-vN.ear           (v23 — Core Banking System, WAS)
  digistack-notificationservice-vN.ear (v23 — Notification Service, WAS)
  digistack-reportingservice-vN.ear    (v23 — Reporting Service, WAS)
  digistack-paymenthub-vN.ear    (v25 — Payment Hub, WAS)
  digistack-mobile-vN.war        (v26 — Mobile Banking, Tomcat)
  digistack-atmsim-vN.war        (v27 — ATM Simulator, Tomcat)
  digistack-cardportal-vN.ear    (v28 — Card Portal, WAS)
  digistack-branchportal-vN.ear  (v29 — Branch Portal, WAS)

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

At P03 v23, the CBS Maven structure becomes (per ARCH02 §2a):
  cbs-parent/
  ├── cbs-ejb/     (EJB session beans, MDBs, Timer Service — CBS only)
  ├── cbs-web/     (REST/SOAP controller layer, JAX-RS/JAX-WS)
  └── cbs-ear/     (assembles cbs-ejb + cbs-web into digistack-cbs-vN.ear)

All satellite EARs (Portal, PaymentHub, NotificationService,
ReportingService, BranchPortal, CardPortal) use the no-ejb layout:
  <app>-parent/
  ├── <app>-web/
  └── <app>-ear/

Tomcat apps (Mobile, ATM) use WAR-only layout:
  <app>-parent/
  └── <app>-web/   (packaging: war — Tomcat deploys WARs directly)

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