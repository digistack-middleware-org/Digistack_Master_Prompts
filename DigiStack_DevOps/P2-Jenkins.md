# PHASE 2: JENKINS — THE PIPELINE ENGINE (Days 11–20)

## Goal
Build, secure, and run production-grade WAS deployment pipelines.

## Days
D11: Jenkins Basics — install (via Ansible), UI, jobs vs pipelines, master/agents
D12: Freestyle → Pipelines — why pipeline-as-code won, Jenkinsfile intro
D13: Declarative Pipeline ⭐ — pipeline{agent,stages,steps}, post{success/
     failure}, readable-by-auditor design
D14: WAS Deploy Stages ⭐⭐ — checkout→build→backup→deploy(wsadmin/Ansible)→
     restart→health check→auto-rollback
D15: Credentials & Secrets ⭐ — credentials plugin, masking, CyberArk-backed
     store; pipeline USES but nobody READS
D16: Triggers — webhooks vs polling, cron triggers, nightly CVE scans,
     upstream/downstream
D17: Parallel & Retry — parallel stages, timeout, retry, fail-fast;
     6 nodes in parallel, DB step run-once
D18: Shared Libraries — custom steps (wasDeploy()), one approved deploy path
     for 40 teams
D19: Security/RBAC/Audit — folder perms, input-step approvals recorded,
     1-year build log retention
D20: ✅ Revision — write full WAS-deploy Jenkinsfile from memory

## Banking Scenario Seeds
- Plugin security review before install (malicious plugin = bank backdoor)
- Freestyle jobs broke after migration → everything as Jenkinsfile in Git
- Deploy fails health check → AUTO-rollback + page on-call; downtime 40→6 min
- Juniors run UAT; Prod requires lead's input-step approval; logs kept 1 year for RBI
- Nightly 1 AM regression + CVE scan, findings ready at login
- DB schema step runs ONCE before 6-node parallel deploy

## Interview Seeds
- Why restrict Jenkins plugins? Master vs agents?
- Freestyle vs Pipeline — why pipelines in enterprises?
- Write declarative skeleton; what does post do?
- End-to-end WAS deployment pipeline stage by stage
- Secrets in Jenkins for a bank
- Webhook vs polling — which and why?
- Speed up multi-node deploys safely
- Enforce deployment standards across 40 teams
- Design Jenkins RBAC: junior/senior/lead

## Phase-Done Checklist
(a) Compact revision notes  (b) 7 deliverables (20 interview Q&A,
5 recent issues, 20 scenario Qs, 20 troubleshooting Qs, 5 war stories,
5 behavioral, 5 architecture)