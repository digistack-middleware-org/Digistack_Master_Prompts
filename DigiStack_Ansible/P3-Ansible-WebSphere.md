# PHASE 3: ANSIBLE + WEBSPHERE (Days 23–35) ⭐ HEART MODULE

## Goal
Automate the full WAS lifecycle. This is the module that wins banking jobs.

## Days
D23: WAS Install Automation — IBM Installation Manager silent (imcl),
     response files, repository.config
D24: Profile Automation — manageprofiles.sh silent, DMGR + custom profiles,
     federation
D25: wsadmin + Ansible — wsadmin.sh -f jython.py from playbook, output capture,
     failed_when on wsadmin output
D26: DataSource Automation ⭐ — JDBC provider → DS → J2C alias → custom props →
     test connection; Vault'd credentials
D27: JVM/Cluster Automation — server create, cluster+members, heap settings,
     scale-out with plugin regen
D28: SSL/Certificates ⭐ — keystores/truststores, CA import, keytool,
     expiry monitoring + auto-import
D29: IHS & Plugin — install, plugin-cfg.xml generation/propagation,
     httpd.conf templates, virtual hosts
D30: App Deployment — AdminApp.install, context roots, versioned releases,
     stop→deploy→start→health→smoke
D31: Fixpacks/iFix — imcl install, repo setup, prereq checks, rollback,
     serial:1 node-by-node
D32: Hardening/Compliance — CIS tasks, admin console auth, session security,
     --check mode audit reports
D33: Health Checks — isAlive, serverStatus.sh, DS test connection,
     email/ServiceNow integration
D34: Backup/DR — backupConfig/restoreConfig, config-as-code in Git,
     DR cell from playbooks
D35: ✅ Capstone — one playbook: full cell provision (install→profiles→
     cluster→DS→app→IHS)

## Banking Scenario Seeds
- New cell in 45 min vs 2 days manual
- New microservice needs Oracle DS in 4 envs: 15 min, identical
- Festival peak: add 2 cluster members, plugin auto-updated
- WAR STORY: expired cert killed card payments → monthly cert playbook
- loans.mybank.com: template+plugin propagation, zero clicks
- Release night: single command deploy with -e app=payments ver=8.2
- CVE flagged, RBI 7-day deadline: serial:1 patching with smoke tests
- Quarterly ITGC: --check compliance report as audit evidence
- 5 AM pre-batch health playbook: "ALL GREEN" email
- DR drill: rebuild cell in 3 hrs from playbooks vs 3 days manual

## Interview Seeds
- End-to-end WAS ND install automation walkthrough
- DMGR+custom profile+federation automation
- Why Jython-in-Ansible vs pure modules?
- Automate Oracle XA DS for UAT+Prod — approach
- Cluster scaling script design
- SSL cert management across 200 servers
- plugin-cfg.xml in automated deployments
- Zero-touch banking release pipeline
- Patch 60 nodes zero downtime
- Ansible for bank audits — killer answer
- Fully automated WAS DR design

## Phase-Done Checklist
(a) Compact revision notes  (b) 7 deliverables (20 interview Q&A,
5 recent issues, 20 scenario Qs, 20 troubleshooting Qs, 5 war stories,
5 behavioral, 5 architecture)
