# PHASE 6: EXPERT / ARCHITECT LEVEL (Days 53–60)

## Goal
Think like a Lead/Architect. This phase gets you PROMOTED.

## Days
D53: Capacity Planning — heap×instances + native + OS overhead; TPS vs cores;
     scale-up vs scale-out; 500 TPS sizing doc
D54: Advanced Tools — lsof -p <pid>, /proc/<pid>/fd leak tracking,
     strace/perf (concept)
D55: Security Architecture — least privilege, sudo granularity, CVE process:
     assess→plan→remediate in SLA→evidence pack (you OWN it)
D56: Automation Integration — golden RHEL image + Ansible role (ulimits,
     sysctl, chrony, sudoers) → WAS playbook; new node in 60 min
D57: HA from OS Side — NIC bonding, multipathing, graceful vs hard failover;
     switch died, bonding saved the day
D58: OS Tuning for WAS — sysctl (somaxconn, swappiness=10, tcp time_wait),
     THP OFF for java, WHY each setting exists
D59: Mock Architecture — golden image → Ansible → monitoring → HA → patching
     → DR parity; whiteboard-ready
D60: 🎓 FINAL SIMULATION — 5-min pitch; live scenarios: disk full, OOM kill,
     too many open files, slow app, port unreachable; war story delivery

## Banking Scenario Seeds
- New payment app 500 TPS → 4 nodes × 8GB heap × 8 cores + 40% headroom
- /proc/<pid>/fd count trending up = fd leak = planned restart before crash
- CVE drops → 7-day SLA remediation with evidence pack
- Golden image + Ansible = identical nodes, zero drift
- NIC bonding saved the day when a switch died mid-day
- Build sheet items (THP off, swappiness 10) each trace to real incidents

## Interview Seeds (Lead level)
- Design the Linux platform for 500+ WAS servers end-to-end
- Critical OS CVE on WAS estate — end-to-end response
- OS-level HA protecting your WAS estate
- OS tuning for WAS and the why behind each
- fd leak — confirm and act

## Phase-Done Checklist
(a) Revision notes  (b) 7 deliverables (20 interview Q&A, 5 recent issues,
20 scenario Qs, 20 troubleshooting Qs, 5 war stories, 5 behavioral,
5 architecture)
(c) Final mock interview simulation
