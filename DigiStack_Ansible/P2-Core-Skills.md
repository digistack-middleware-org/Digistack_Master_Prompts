# PHASE 2: CORE SKILLS (Days 11–22)

## Goal
Master everything inside playbooks — the skills used in 90% of real work.

## Days
D11: Facts — setup module, gather_facts:false, custom facts
D12: Conditionals — when, ==, in, is defined, AND/OR
D13: Loops — loop, with_items, loop_control (label/pause), dict loops
D14: Jinja2 Templates ⭐ — template module, filters (default/upper),
     loops+conditionals in templates
D15: Handlers — notify, listen, run conditions, force handlers
D16: Error Handling — ignore_errors, failed_when, changed_when,
     block/rescue/always, any_errors_fatal
D17: Tags — --tags, --skip-tags, always
D18: Vault ⭐ — create/encrypt/decrypt/rekey, vault-password-file, !vault
D19: Roles ⭐ — full directory structure, ansible-galaxy init, dependencies
D20: Galaxy & Collections — requirements.yml, community.general, ibm collection,
     ansible-core vs ansible
D21–22: Strategy/Delegation — serial, max_fail_percentage, delegate_to,
     run_once, async+poll, throttle
D22 end: ✅ Revision — full role: WAR deploy to cluster with health check+rollback

## Banking Scenario Seeds
- RHEL7 vs RHEL8 node check before fixpacks
- DB2 RRS config only on specific nodes via when
- 15 JVM custom props × 20 servers via loop
- Generate server.xml/IHS configs per env from ONE template
- Config changed → restart; unchanged → no Prod restart
- Patching failure → auto-rollback (rescue) → page on-call (always)
- CIS audit: show only SSL tasks via tags
- DB/J2C/keystore passwords — Vault or fail RBI audit
- Roles: was_install, was_fixpack, ihs_install, was_hardening
- Rolling restart serial:5 — never all 20 at once

## Interview Seeds
- How to see all facts of one host?
- when inside loop behavior
- loop vs with_*
- Template server.xml for 5 environments
- 5 tasks notify same handler — runs how many times?
- block/rescue/always rollback example
- always tag meaning
- Vault-based credential storage in a bank
- Why roles? Directory structure
- Zero-downtime rolling restart design

## Phase-Done Checklist
(a) Compact revision notes  (b) 7 deliverables (20 interview Q&A,
5 recent issues, 20 scenario Qs, 20 troubleshooting Qs, 5 war stories,
5 behavioral, 5 architecture)
