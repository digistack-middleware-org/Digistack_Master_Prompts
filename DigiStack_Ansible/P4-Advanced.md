# PHASE 4: ADVANCED ANSIBLE (Days 36–45)

## Goal
Operate Ansible as an enterprise platform: Tower/AAP, governance, CI/CD.

## Days
D36: Tower/AAP ⭐ — RBAC, credential vaulting, scheduling, workflows,
     audit logs, self-service
D37: Tower Deep Dive — Projects (Git sync), Job Templates, Surveys,
     Credential types, Execution Environments, Notifications
D38: Workflows & Approvals — workflow templates, approval nodes,
     convergence nodes
D39: Dynamic Inventory — VMware/ServiceNow CMDB sources, custom scripts,
     constructed inventory
D40: Performance — forks, pipelining, strategy:free, fact caching (Redis),
     gathering:smart
D41: Custom Modules & Plugins — module structure, filter/lookup/callback plugins
D42: GitOps — branching (feature→UAT→main=Prod), PR reviews, tagged releases
D43: Testing — --check/--diff, ansible-lint, yamllint, Molecule
D44: CI/CD — Jenkins/GitLab triggering playbooks, credentials binding, stages
D45: ✅ Revision — design complete bank Tower setup: teams, RBAC,
     credentials, workflows

## Banking Scenario Seeds
- Juniors run patching but can't see Prod passwords; every run logged
- Survey: env+app+change ticket; Prod needs lead approval
- Workflow: Deploy UAT → QA lead approves → Prod → smoke → Teams notify
- Servers auto-appear from CMDB; no manual inventory
- 60-node patch: 2 hrs → 20 min after tuning
- Callback plugin sends run results to ServiceNow as audit evidence
- No code to Prod without PR + 2 approvals + change ticket in commit
- CI blocks bad code via lint+dry-run on sandbox cell
- Dev merges → Jenkins WAR → Ansible UAT deploy → smoke → approval → Prod

## Interview Seeds
- Tower vs open-source Ansible; explain RBAC
- Design Tower self-service portal for app teams
- Build release workflow with approvals
- 500 servers: static vs dynamic inventory — why?
- Fix a 4-hour/100-server playbook
- Custom module vs role — when?
- Ansible code through ITIL change management
- Safe pre-Prod testing strategy
- Where does Ansible sit in CI/CD?

## Phase-Done Checklist
(a) Compact revision notes  (b) 7 deliverables (20 interview Q&A,
5 recent issues, 20 scenario Qs, 20 troubleshooting Qs, 5 war stories,
5 behavioral, 5 architecture)
