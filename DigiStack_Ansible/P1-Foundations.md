# PHASE 1: ANSIBLE FOUNDATIONS (Days 1–10)

## Goal
Understand what Ansible is, set it up, and write your first working playbooks.

## Days
D1: What is Ansible — config management, agentless, push vs pull, idempotency,
    control node vs managed nodes
D2: Installation — RHEL install, ansible.cfg search order (env→cwd→home→/etc),
    ansible --version
D3: Inventory — INI & YAML formats, groups, group-of-groups, host vars,
    ranges was[01:12].bank.com
D4: SSH & Auth — key-based auth, become/become_user, remote_user vs become_user,
    host key checking
D5: Ad-Hoc Commands — ping, shell, command, copy, service modules
D6: YAML — indentation, lists, dicts, kv pairs; common mistakes
D7–8: First Playbook — hosts, tasks, modules (copy/template/yum/service),
    --check dry-run, --limit
D9: Variables — play vars, inventory vars, host_vars/, group_vars/,
    extra-vars precedence
D10: ✅ Revision — 10 rapid-fire + write 5-task playbook from memory

## Banking Scenario Seeds (pick 1 per day)
- Config drift between UAT & Prod caused failed launch; audit finding
- Offline RPM install on restricted bastion server
- Prod inventory in encrypted Git, lead-only write access
- wasadmin via limited audited sudoers; root banned
- Check disk on 60 nodes before nightly batch — 1 command
- Bad YAML = failed 2 AM deployment
- First week task: install/start IHS on 10 nodes
- group_vars/prod.yml vs uat.yml for heap/db settings

## Interview Seeds
- Ansible vs Chef vs Puppet vs Terraform
- Which ansible.cfg wins?
- command vs shell module
- remote_user vs become_user
- Idempotency: idempotent vs non-idempotent example
- Variable precedence top 10

## Phase-Done Checklist
(a) Compact revision notes  (b) 7 deliverables (20 interview Q&A,
5 recent issues, 20 scenario Qs, 20 troubleshooting Qs, 5 war stories,
5 behavioral, 5 architecture)
