ID: ARCHVIZ04
Version: 0.1 (scaffold)
Status: Not Populated

Title: Cluster Architecture

Imports:
CAP01 (§7 Cluster Sizing)

Populate At: P01 v5 (cluster stood up), deepened at P01 v6 (DMgr/
federation internals)

---

## What this will show
DMgr (devdsbindmgr01 profile), Node01 (devdsbinnode01), Node02
(devdsbinnode02), AppCluster (devdsbinappcluster01), cluster members
— actual topology once built, not the generic diagram.

Per STD naming (CONTEXT_PACK §Naming Conventions):
  Cell:         devdsbincell01
  DMgr profile: devdsbindmgr01          (v5+)
  Node1:        devdsbinnode01          (v5+, on dsb-dmgr VM)
  Node2:        devdsbinnode02          (v5+, on dsb-node02 VM)
  AppCluster:   devdsbinappcluster01    (v5+)
  Server name:  server1                 (no prefix — naming exception per STD)

At v5: cluster stood up, session replication proven (basic topology).
At v6: DMgr/federation internals, node synchronization, wsadmin deep-dive.
At P03: 9-application multi-EAR topology deployed to same 2-member
cluster (no new cluster members added in P02 or P03).

## Status
Not yet populated. P01 is currently at Version 2 — clustering doesn't
exist yet (that's Version 5).