ID: ARCHVIZ03
Version: 0.1 (scaffold)
Status: Not Populated

Title: Request Flows

Imports:
ARCH02 (§5 Solution-Level Component Diagram)

Populate At: P01 v1 (browser → AppServer → DB, basic path), extended at
P01 v7 (JNDI), P01 v8 (IHS reverse proxy), P01 v12 (mTLS hops)

---

## What this will show
Sequence-diagram style walkthrough of one real request (e.g. a Deposit)
end-to-end through every tier that actually exists at the time it's
drawn — not the finished-system version (that's ARCH02 §5's job).

## Request Flow (as of P01 v1)

**Flow 1 — Home page load**
```
Browser --GET /-------------------> AppServer (dsb-dmgr:9080)
                                          │
                                          │ JDBC (direct, no JNDI pool yet — v7)
                                          ▼
                                    PostgreSQL (dsb-db:5432)
                                    SELECT * FROM app_config
                                          │
Browser <--Home.jsp rendered----------- AppServer
```

This is the only flow that exists at v1 — public/pre-login landing page
(hero banner, marketing copy, feature-teaser tiles linking to Login,
"Login"/"Open an Account" buttons — the latter two non-functional,
per P01 v1's UI note). No Login, no Deposit/Withdraw, no session yet —
those arrive at v2 and v3 respectively.

No IHS/reverse proxy (v8), no connection pool/JNDI DataSource (v7), no
SSL (v11) — browser talks straight to the AppServer over HTTP on 9080.

## Status
**Reset — 2026-08-25 (full project reset #2).** The flow diagrammed
above is still the correct v1 target design, but nothing pictured is
physically running right now — the lab VM (dsb-dmgr, dsb-db) was lost
the same day this was drawn. Treat as a rebuild target, not a live
system, until P01 v1 is actually redeployed and re-verified.
Extend at P01 v2 (Login flow added), v7 (JNDI pool replaces direct
JDBC), v8 (IHS hop added), v12 (mTLS hops).