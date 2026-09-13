# PHASE 1: JBOSS FOUNDATIONS (Days 1–10)

## Goal
Install, run, and control EAP confidently; understand standalone.xml and
classloading — the foundation every real issue sits on.

## Days
D1: What is JBoss/WildFly — EAP vs WildFly (RHEL vs Fedora analogy), Jakarta
    EE, EAP 7.x versions, Red Hat subscription; why banks run EAP alongside WAS
D2: Install & Directory Structure — JDK prereqs, zip vs RPM, $JBOSS_HOME
    (bin, standalone, domain, modules), apps NEVER inside install tree
D3: Standalone vs Domain Mode ⭐ — standalone.sh/domain.sh, DC, host controller,
    server groups, profiles; when each fits (bank: mostly standalone+Ansible)
D4: Start/Stop & jboss-cli.sh ⭐⭐ — systemd, connect, :shutdown, deploy,
    :read-attribute(name=server-state); graceful vs hard shutdown
D5: standalone.xml ⭐⭐ — extensions, management interfaces, profiles,
    subsystems, interfaces, socket-binding-groups; XML vs CLI change discipline
D6: Subsystems — datasource, undertow, ejb, transactions, logging, security,
    infinispan; profile = subsystem collection; the 6 to master first
D7: Deployments — deployments folder vs CLI deploy, exploded vs archive,
    managed vs unmanaged, overlays; folder deploy FORBIDDEN in bank prod
D8: Interfaces & Socket Bindings — 9990/9993/8080/8443/ajp, localhost vs
    0.0.0.0, port offsets (100/200/300), bank port matrix
D9: Modules & Classloading ⭐ — module.xml, jboss-deployment-structure.xml,
    isolation, ClassNotFound vs NoClassDefFound, custom JDBC driver module
D10: ✅ Revision — 10 rapid-fire + start/stop/deploy/health from memory

## Banking Scenario Seeds
- $2M/yr WAS renewal killed by moving 30 new apps to EAP; WAS kept for core
- Deploy inside install tree → fixpack wiped the app
- Wrong mode choice = years of operational pain
- :read-attribute = 2-second health check during incidents
- Manual XML edits by juniors → 3 outages in one year → CLI/Ansible only rule
- Unregistered exposed 9990 failed a security scan
- Custom module for newer JDBC driver; jar-hell over Jackson version (2 days)

## Interview Seeds
- EAP vs WildFly — why banks choose EAP?
- What must never live inside $JBOSS_HOME?
- Standalone vs domain for a 12-node payment cluster?
- Check server health without logs; graceful vs hard shutdown
- Walk through standalone.xml; edit while running = what breaks?
- What is a subsystem? Which 6 first?
- Managed vs unmanaged deploy — why banks mandate managed?
- Run 3 instances on one server; which ports firewall?
- Explain modular classloading; fix ClassNotFoundError

## Phase-Done Checklist
(a) Compact revision notes  (b) 7 deliverables (20 interview Q&A,
5 recent issues, 20 scenario Qs, 20 troubleshooting Qs, 5 war stories,
5 behavioral, 5 architecture)
