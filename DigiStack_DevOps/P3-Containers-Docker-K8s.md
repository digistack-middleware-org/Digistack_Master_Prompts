# PHASE 3: CONTAINERS & DOCKER/K8s FOR WAS (Days 21–32) ⭐ MODERN HEART

## Goal
Containerize WAS Liberty apps; deploy and troubleshoot on Kubernetes/OpenShift.

## Days
D21: VM vs Containers — kernel sharing, images vs containers, Docker
     architecture; 3× density story
D22: Docker Basics — run/ps/stop/rm/logs/exec/inspect, -p ports
D23: Images & Dockerfile ⭐ — FROM/COPY/RUN/ENV/EXPOSE/CMD, layers, caching;
     pin base image versions (no :latest in a bank)
D24: WAS Liberty Image ⭐ — Dockerfile: scanned base + WAR + server.xml from
     Git; image traceable to commit; docker history
D25: Networking & Volumes — bridge, port mapping, volumes for logs
     (stateless app + stateful logs pattern)
D26: Docker Compose — Liberty + Oracle + MQ sandbox in one file
D27: Registries & Scanning ⭐ — private registry, Trivy/Twistlock, signing;
     no deploy without scan pass; CVE gate blocks pipeline
D28: Why Kubernetes ⭐ — orchestration problem, master/nodes/kubelet/etcd,
     declarative reconciliation; "OS of the modern datacenter"
D29: Pod/Deployment/Service ⭐ — ReplicaSets, rolling updates, ClusterIP/
     NodePort/LoadBalancer, labels; payday scale 6→10
D30: ConfigMaps & Secrets — one image many configs; secrets synced from
     CyberArk/Vault, never baked in
D31: kubectl Essentials ⭐ — get/describe/logs/exec/apply/rollout, events,
     requests/limits, liveness/readiness probes; CrashLoopBackOff flow
D32: ✅ Revision — containerize+deploy design + kubectl troubleshooting sim

## Banking Scenario Seeds
- 1 VM per app = 60% waste; containers = 3× density, cost committee noticed
- 2-min Liberty container to test Jython vs 5-day VM request
- FROM ubuntu:latest rejected in review — supply chain discipline
- Logs on volumes: container dies, RCA evidence survives
- Payday surge: kubectl scale one command, rolling update = zero-downtime
- Payment pod OOMKilled: describe → raise limit + tune JVM heap to fit
  container (heap ≠ container memory — native counts)

## Interview Seeds
- VM vs container pros/cons for banking
- docker run vs exec; live container logs
- Docker layers; why pin base versions?
- Walk through containerizing a Liberty app
- Persist container logs for RCA
- Local sandbox design WAS+DB+MQ
- Secure container supply chain in a bank
- What problem does K8s solve? Architecture simply
- Rolling update + payday scaling explained
- Env-specific config for containers in a bank
- CrashLoopBackOff — full diagnostic flow

## Phase-Done Checklist
(a) Compact revision notes  (b) 7 deliverables (20 interview Q&A,
5 recent issues, 20 scenario Qs, 20 troubleshooting Qs, 5 war stories,
5 behavioral, 5 architecture)
