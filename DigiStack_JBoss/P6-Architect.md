# PHASE 6: EXPERT / ARCHITECT LEVEL (Days 54–60)

## Goal
Think and answer like a Lead/Architect. This phase gets you PROMOTED.

## Days
D54: JBoss on OpenShift ⭐ — s2i images, operators, containers vs VMs
     judgment, JBoss vs Quarkus (concept); memo: "containerize what's
     stateless and new" — saying WHERE NOT is architect work
D55: Capacity Planning — vertical vs horizontal economics, session-state
     constraints, festival/salary peaks, predictive sizing from Grafana
     baselines; 2.5× load → +4 nodes in 1 hour via automation
D56: Full Platform Architecture ⭐ — reference architecture: LB layer,
     clustering, DB HA, security, monitoring, automation, patch cadence, DR;
     every new app inherits it; onboarding 3 days not 3 weeks
D57: Incident Command & RCA ⭐ — who does what, comms cadence to business,
     blameless RCA, problem management; 47-min payments outage: timeline,
     evidence, 3 corrective actions, recurrence stopped
D58: Cost & Vendor — EAP subscriptions (sockets vs cores), vs WAS/Tomcat/
     Quarkus paths, Red Hat ticket strategy; right-sizing 20→12 servers saved
     ₹2 crore/yr — middleware drives P&L
D59: Mock Architecture — bank JBoss platform: 60 apps, 40 VMs + 20 OpenShift
     services, mod_cluster + F5, RAC/PG HA, Elytron+LDAP, GitOps config,
     Ansible builds, Grafana, CVE process, DR cell — whiteboard, defend all
D60: 🎓 FINAL SIMULATION — 5-min pitch "My JBoss journey"; live scenarios:
     Monday pool exhaustion, OOM Thursday, cert expiry, CVE day, node silent
     in balancer, heuristic transaction; war-story bank (situation→action→
     result→lesson)

## Banking Scenario Seeds
- Legacy EJB apps stayed on VMs; new REST microservices → EAP-on-OpenShift
- Reference architecture memo approved because it showed cost AND risk
- Biggest outage led: RCA corrective actions = validation config + monitoring
  threshold + runbook → recurrence stopped; leadership = closing the loop
- Licensing renegotiation via capacity data proved admin value beyond uptime

## Interview Seeds (Lead level)
- Move EAP estate to OpenShift? Phased judgment
- Festival load 2.5× — capacity response design
- Present a reference architecture for all JBoss hosting in a bank
- Worst outage you led — timeline, decisions, RCA, prevention
- Reduce middleware licensing cost without risk

## Phase-Done Checklist
(a) Compact revision notes  (b) 7 deliverables (20 interview Q&A,
5 recent issues, 20 scenario Qs, 20 troubleshooting Qs, 5 war stories,
5 behavioral, 5 architecture)
(c) Final mock interview simulation
