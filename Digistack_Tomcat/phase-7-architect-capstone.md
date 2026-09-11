# PHASE 7: Architect + Capstone War Games (Day 172–186)
Goal: Speak and operate at architect level; diagnose any cross-layer P1 in <15 min.
Milestone Day 186: All 10 war games completed and graded.

## Module 23: Automation & Modern Practices (Day 172–180)
| Day | Topic | Lab |
|---|---|---|
| 172 | Ansible: ad-hoc → playbooks (depth from Module 18) | Playbook: install + configure Tomcat on 2 VMs |
| 173 | Ansible roles for multi-instance deployment | Refactor to role; deploy 3 instances identically |
| 174 | Docker: Tomcat image, Dockerfile, volumes, env config | Containerize a Tomcat app |
| 175 | K8s basics: pods, liveness/readiness probes, deployments | Deploy Tomcat in minikube; kill pod; watch self-heal |
| 176 Embedded Tomcat (Spring Boot) vs standalone; migration strategy | Comparison notes + interview answers |
| 177 | 🏦 Banking: 50 legacy apps → K8s migration plan | Phased migration plan with risks |
| 178 | Incident management: P1 process, bridge calls, comms, 5-Why | Full P1 RCA document (use your Day-160 GC freeze) |
| 179 | Runbooks, SOPs, on-call discipline, problem management | 3 runbooks: OOM, disk-full, pool-exhaust |
| 180 | Upgrade strategy: 8→9→10, Jakarta namespace | Write the bank's Tomcat upgrade SOP |

## Module 24: Capstone — War Games (Day 181–186)
| Day | War Game | Layers Tested |
|---|---|---|
| 181 | "Net Banking down 8:30 AM" — Tomcat refuses connections | Tomcat + Linux (ss, saturation, ulimits, systemd) |
| 182 | "Payments failing intermittently since DC migration" | Network + DB (tcpdump, ephemeral ports) |
| 183 | "Site slow daily 9–10 AM" + "Session expired during transfer" | JVM/OS (GC, thread dump) + F5/LB (sticky, false-healthy) |
| 184 | "Tomcat vanished overnight" + "SSL handshake failing at B2B partner" | OOM Killer (dmesg) + Apache certs (openssl s_client) |
| 185 | "App team says Tomcat slow, DBA says DB fine" ⭐ + "15 security findings, fix by Friday" | APM evidence-based blame + prioritization/CAB |
| 186 | "Deploy failed — rollback pending your approval" + Whiteboard: full HA platform from memory, 15 min aloud | Rollback + F5 drain + full architecture |

## ✅ Phase 7 Checkpoint (Day 186)
- Rank your weakest 3 war games — redo them
- Grade each: Speed /10, Method /10, Communication /10
- War stories portfolio: 15+ STAR stories complete
