# PHASE 4: CI/CD + IaC INTEGRATION (Days 33–42) ⭐ INTERVIEW CENTERPIECE

## Goal
Wire the full toolchain; master deployment strategies, DevSecOps, and rollback.

## Days
D33: Full Pipeline ⭐⭐ — Git→Jenkins→Artifactory→Ansible/Tower→WAS→health→
     notify; where each tool belongs
D34: Deployment Strategies ⭐ — big-bang vs rolling vs blue-green vs canary;
     DB backward-compat one release ahead
D35: IaC Mindset — declarative/idempotent/versioned; Terraform (VMs) vs
     Ansible (WAS config) division
D36: Tower in Pipelines — Jenkins calls Tower workflow; approval nodes with
     change-ticket check
D37: Testing Gates — test pyramid, smoke suite (login/balance/mini transfer,
     90 sec, catches 80% of bad deploys)
D38: DevSecOps ⭐ — SAST, SCA, image + secrets scanning, CVE SLAs;
     Log4Shell 30-min impact answer
D39: Monitoring ⭐ — Prometheus/Grafana, deploy markers on dashboards,
     catch bad release before customers
D40: ChatOps & Evidence — auto-post version/approver/ticket/smoke to Teams;
     audit evidence = export, zero manual collection
D41: Failure & Rollback ⭐ — failure modes, automated vs manual rollback,
     "roll back first, debug calmly" rule
D42: ✅ Revision — whiteboard full flow WITH failure paths

## Banking Scenario Seeds
- 12-hr manual runbook → 45-min pipeline, 2 approval gates, failure 30%→5%
- Blue-green payments: swap router in seconds, blue warm for instant rollback
- Painful 2 AM rollback: DB migration wasn't backward-compatible
- DR cell built by playbooks = infrastructure as code, not tribal knowledge
- Deploy without smoke = incident review lesson
- Rollback decision <10 min or automated — the rule that saved weekends
- Grafana deploy lines = RCA gold

## Interview Seeds
- Design complete delivery pipeline for a banking WAS app
- Blue-green vs canary for payments; the database question
- What is IaC? Terraform vs Ansible boundary
- Jenkins + Tower cooperation in a bank pipeline
- What tests gate a Prod deploy and why?
- Critical CVE drops — DevSecOps response end-to-end
- Know a deployment went bad before customers do
- Release evidence for RBI audit
- Deploy failed halfway across 6 nodes — what happens next, exactly?

## Phase-Done Checklist
(a) Compact revision notes  (b) 7 deliverables (20 interview Q&A,
5 recent issues, 20 scenario Qs, 20 troubleshooting Qs, 5 war stories,
5 behavioral, 5 architecture)