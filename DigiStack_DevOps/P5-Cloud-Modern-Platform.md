# PHASE 5: CLOUD & MODERN PLATFORM (Days 43–52)

## Goal
Operate the modern bank platform: cloud VMs, OpenShift, hybrid estates, SRE.

## Days
D43: Cloud Basics — IaaS/PaaS/SaaS, regions/AZs, responsibility model;
     private-cloud-first + RBI data residency
D44: VMs in Cloud — provisioning from golden images, tagging, security groups,
     Terraform basics; Terraform→Ansible→Tower chain (60-min node)
D45: tWAS vs Liberty Containers ⭐ — ND for legacy EJB/MQ, Liberty containers
     for microservices; pragmatic split
D46: OpenShift Basics — routes, build configs/image streams, projects+RBAC;
     why banks pick it (support, RBAC, SELinux, audit)
D47: Liberty on OpenShift — deployment configs, routes+TLS, probes, quotas,
     S2S; dev self-service in 30 min
D48: Hybrid Estate ⭐ — 100 ND VMs + 300 pods, one team; single control plane,
     ONE dashboard/alerting/patch process
D49: Auto-Scaling — HPA metrics, cluster autoscaler, payday 6→20 pods story,
     40% infra cost saving
D50: Cloud Security — network policies (default-deny), pod security, admission
     control, encryption, API audit logging
D51: SRE ⭐ — SLI/SLO/error budgets, blameless postmortems, toil reduction;
     99.95% payment SLO + freeze-on-budget-burn
D52: ✅ Revision — design hybrid platform (VMs+OpenShift) with scaling,
     security, SLOs from memory

## Banking Scenario Seeds
- New node: Terraform builds VM (tags app=payments,env=prod) → Ansible
  configures → Tower registers — 60 min, auditable, zero clicks
- OpenShift over vanilla K8s: passes procurement AND security review
- Old job (2-day VM provisioning) becoming museum history — self-service
- Before cloud: hardware bought for peak 365 days; now elastic
- Default-deny network policy: compromised pod can't roam
- Blameless RCA: 2 AM mistake became a pipeline guard, not a fired colleague

## Interview Seeds
- Can a bank use public cloud? What governs it?
- Automated WAS node provisioning end-to-end
- Should a bank containerize ALL WAS workloads? Defend.
- OpenShift vs K8s — why banks prefer OpenShift
- OpenShift vs traditional ND deployment differences
- Operate a mixed VM + container estate
- Auto-scaling design for monthly spikes
- Container equivalent of firewall discipline?
- Error budget; how SLOs change bank operations

## Phase-Done Checklist
(a) Compact revision notes  (b) 7 deliverables (20 interview Q&A,
5 recent issues, 20 scenario Qs, 20 troubleshooting Qs, 5 war stories,
5 behavioral, 5 architecture)
