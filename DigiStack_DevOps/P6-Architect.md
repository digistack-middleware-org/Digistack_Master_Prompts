# PHASE 6: EXPERT / ARCHITECT LEVEL (Days 53–60)

## Goal
Think and answer like a Lead/Architect. This phase gets you PROMOTED.

## Days
D53: DevOps Transformation ⭐ — manual→scripted→automated→pipeline; 6-month
     plan; DORA metrics (deploy frequency, lead time, MTTR, CFR)
D54: Tool Selection Judgment ⭐ — Jenkins vs GitLab CI vs Actions; say NO with
     reasons; anti-resume-driven-tooling; legacy EJB stays on ND VMs
D55: DevSecOps Architecture — security gates per stage, CyberArk→Vault→pipeline,
     segregation of duties enforced by tooling, evidence automation
D56: Multi-Env & Multi-Region — env parity, promotion paths, active-active DCs,
     deploy DC1→soak 24h→DC2, halved blast radius
D57: Governance & Enablement — golden pipelines, paved roads, 40 teams migrated
     because paved road was FASTER, platform vs app team split
D58: Cost, Metrics & Business Story — downtime vs automation cost, DORA deep
     dive, tagging/rightsizing; one-slide COO story
D59: Mock Architecture — full bank platform: Git→Jenkins→Artifactory→Tower/
     OpenShift→500 WAS workloads (VM ND + Liberty pods), security gates,
     multi-DC, monitoring, DR, evidence — whiteboard-ready
D60: 🎓 FINAL SIMULATION — 5-min pitch; live scenarios: failed deploy, CVE day,
     payday scaling, pipeline outage, rollback decision; war-story delivery

## Banking Scenario Seeds
- DORA before/after report = the artifact that got me promoted
- Committee wanted "Kubernetes everything" → I defended ND VMs for legacy;
  architects say NO with reasons
- Segregation of duties: developer cannot Prod-deploy alone; auditors call it
  "compensating control by design"
- Paved road beat policing: one golden Jenkinsfile + docs → 40 teams in a quarter
- Funding slide: "Manual 12h×30 releases×risk vs Automated 45min, 5% CFR"

## Interview Seeds (Lead level)
- Inherit a fully manual WAS shop — design the transformation
- Defend a decision NOT to use a modern tool
- Design complete security model for bank CI/CD platform
- Design multi-DC deployment for a critical app
- Drive 40 teams to adopt pipeline standards
- Justify DevOps platform investment to a COO

## Phase-Done Checklist
(a) Compact revision notes  (b) 7 deliverables (20 interview Q&A,
5 recent issues, 20 scenario Qs, 20 troubleshooting Qs, 5 war stories,
5 behavioral, 5 architecture)
(c) Final mock interview simulation
