# PHASE 5: SECURITY & OPERATIONS IN A BANK (Days 47–53)

## Goal
Run JBoss the way banks demand: secure, patched, backed up, automated, evidence-driven.

## Days
D47: Elytron Deep Dive ⭐ — security domains, LDAP/Kerberos/JDBC realms,
     role mapping; what Elytron replaced (legacy security) and why;
     LDAP filter misconfig incident (test account saw prod balances)
D48: CVE Response ⭐ — RHSA advisories, impact assessment, 7-day SLA;
     process: inventory(1h) → UAT patch(d2) → regression(d3) → rolling
     zero-downtime prod(d4–5) → evidence pack(d6)
D49: Hardening — management interface on internal VLAN, remove welcome web
     root, disable HTTP mgmt in prod, file perms 750, CIS-style checks;
     scan-clean = audit-pass
D50: Backup & DR ⭐ — back up configs/deployments/keystores/modules, DR cell,
     RTO/RPO; drill restored prod from Git+backups in 45 min; untested DR = no DR
D51: Automation ⭐ — jboss-cli in Ansible, community.general jboss modules,
     config-as-code, golden builds; 25-min node build; drift story (node ran
     old logging config for MONTHS)
D52: Teamwork & Evidence Rule — middleware vs app/DBA/network boundaries;
     no ticket without evidence bundle (thread dump + pool stats + JDBC timing)
     → DBA responded in 30 min vs 2-day blame war
D53: ✅ Revision — simulate: CVE response + DR failover + hardening remediation

## Banking Scenario Seeds
- Elytron ldap-realm → AD group role mapping (tellers vs managers see
  different data); filter fix + testing policy born
- Tuesday CVE, Friday evidence pack — the CISO presentation
- Security scan flagged exposed console + default page → internal VLAN +
  custom error pages
- Old DR doc (5 yrs untested) would've taken 2 days of guessing
- Ansible-built nodes identical every time; manual builds drifted
- Evidence bundle culture killed the "works fine on our side" wars

## Interview Seeds
- Configure LDAP auth for a JBoss app; Elytron vs legacy — why the change?
- Critical EAP CVE drops — end-to-end response plan
- Harden prod JBoss without breaking the app — checklist
- Backup + DR design for 40 servers; prove your RTO
- Automate a 10-node cluster build end-to-end; why did drift happen before?
- App team says "JBoss is slow," you suspect DB — what exactly do you send?

## Phase-Done Checklist
(a) Compact revision notes  (b) 7 deliverables (20 interview Q&A,
5 recent issues, 20 scenario Qs, 20 troubleshooting Qs, 5 war stories,
5 behavioral, 5 architecture)
