ID: P10
Version: 1.0
Status: Active

Title: Containerization & Cloud-Native Modernization

Imports:
IDX
STD
ARCH01
ARCH02
STDGAP01
SOE01
CAP01

Exports:
Versions 74-78
Containerization Foundations (Docker)
Kubernetes / Amazon EKS Orchestration
Container Networking & Service Mesh
Container CI/CD & GitOps
Containerization Capstone

Used By:
(none yet — final Part in the current roadmap)

Next:
(none)

---

Purpose
-------
Transform DigiStack Bank's AWS-native platform (P09) from EC2-hosted
WebSphere/IHS/MQ into a containerized, orchestrated platform — Docker,
Kubernetes (Amazon EKS), service mesh, and container-native CI/CD. This
is the natural next step after full cloud-native adoption, per ARCH01's
Future Scalability Considerations ("Containerization — natural next step
after full cloud-native estate").

Standing Principle — Modernize the Runtime, Not the Bank
----------------------------------------------------------------
Consistent with every prior infrastructure-only Part (P04-P09), this Part
changes how DigiStack Bank's 9 applications are packaged, deployed, and
run — never what they do. No new banking functionality is introduced
anywhere in this Part. Any test failure that would require an actual
application/business-logic change is logged as a containerization
blocker, not silently worked around by adding scope.

Fixed/Recurring Deposits — Explicit Non-Scope Note
----------------------------------------------------------
The Progress Log's long-open "Fixed Deposits & Recurring Deposits"
question is explicitly NOT addressed by this Part. This Part is
infrastructure/runtime-only (same restriction as every Part since P04);
introducing a new banking feature here would break that discipline.
Fixed/Recurring Deposits remain permanently unscoped within the current
roadmap (P01-P10) unless a future, explicitly new Part is proposed and
roadmapped specifically for banking feature work — this Part does not
serve as that vehicle.

Prerequisite
------------------
P09 Completion Checkpoint satisfied — DigiStack Bank fully AWS-native
(EC2 → RDS for PostgreSQL, S3, hybrid SQS/SNS+MQ, IAM/Secrets Manager/
KMS/ACM), part9-release tag applied, all three regions cut over with
on-prem fully decommissioned.

Deployment Model
------------------
No new banking features are added anywhere in this Part. Every version
containerizes, orchestrates, or operationally hardens the same 9
applications built through P03 and modernized through P09. WebSphere ND
remains the application server inside each container — this Part does
not replace WebSphere with a different runtime (e.g., Open Liberty),
since that would be an application-server migration, not
containerization, and is explicitly out of scope unless a future Part
proposes it on its own terms.

Numbering note: This Part is newly created (not a renumbered draft), so
no renumbering table is required — Versions 74-78 are assigned fresh,
starting immediately after P09's final version (73), per the same
Version Numbering Freeze discipline used throughout this roadmap.

---

Version 74 — Containerization Foundations
--------------------------------------------------
Objective: Establish Docker fundamentals and produce the first working
containerized version of a DigiStack application, proving the pattern
before scaling it to all 9.

Banking Features Added: None.

Topics Covered: Docker Engine, Dockerfile authoring, multi-stage builds
(EAR/WAR build stage separate from runtime image), base image selection
(IBM-supported WebSphere Liberty/traditional WAS container images vs.
Open Liberty — this project retains traditional WAS per the Deployment
Model note above), image layering and caching, Amazon ECR (Elastic
Container Registry) as the image registry, image tagging/versioning
(reuses P08 v50's immutable-artifact-promotion principle — same EAR
built once, baked into one image, promoted unchanged across
environments), container health checks (reuses P04 v31's /health
endpoints as the container's own HEALTHCHECK).

WebSphere Topics Covered: Containerized WAS traditional profile startup
behavior, JVM tuning inside a resource-constrained container (revisits
P01 v14's heap tuning under container cgroup limits, not host-level
limits).

Enterprise Learning: Container Fundamentals, Image Build Discipline,
Immutable Infrastructure at the Image Layer.

Sprint Deliverable: CBS is containerized first (the highest-value,
most-representative app) — a Dockerfile builds a WAS traditional image
containing the CBS EAR, published to ECR with an immutable tag; the
container starts, passes its HEALTHCHECK via the existing /health
endpoint (P04 v31), and connects successfully to Amazon RDS for
PostgreSQL (P09 v64) from inside the container.

---

Version 75 — Kubernetes / Amazon EKS Orchestration
------------------------------------------------------------
Objective: Stand up Amazon EKS and migrate all 9 containerized
applications onto it, replacing EC2-hosted WebSphere ND clusters (P09
v59) with Kubernetes-orchestrated pods.

Banking Features Added: None.

Topics Covered: Amazon EKS cluster provisioning, node groups (managed
node groups vs. Fargate — this project uses managed EC2 node groups,
consistent with keeping WAS traditional's memory/CPU profile
predictable), Pods, Deployments, ReplicaSets, Services (ClusterIP,
NodePort, LoadBalancer), ConfigMaps and Secrets (externalized
configuration — direct continuation of STD's "no hardcoded values"
Golden Rule, now Kubernetes-native; Secrets backed by AWS Secrets
Manager via the Secrets Store CSI Driver, not native k8s Secrets alone,
preserving P09 v67's Secrets Manager discipline), readiness/liveness
probes (Kubernetes-native equivalent of P04 v31's /health, distinguishing
readiness from liveness explicitly), Horizontal Pod Autoscaler (HPA) —
the container-native equivalent of P01 v5's clustering/horizontal
scaling.

WebSphere Topics Covered: Session replication behavior under Kubernetes
pod rescheduling (re-verifies P01 v9/P05 v36's session-survives-failure
guarantee, now under a pod eviction/reschedule instead of a JVM crash);
Messaging Engine placement (P02 v15, P05 v36) reconsidered under
Kubernetes — SIBus ME singleton behavior does not map cleanly onto
stateless pod scaling, so this version documents the ME's placement
constraint explicitly (pinned to a single replica or externalized)
rather than silently assuming HPA scales it like any other pod.

Enterprise Learning: Container Orchestration, Kubernetes Operations,
Cloud-Native Scaling Patterns.

Sprint Deliverable: All 9 applications run as Kubernetes Deployments on
EKS, each with readiness/liveness probes wired to the existing /health
endpoints; ConfigMaps/Secrets (via Secrets Store CSI Driver → Secrets
Manager) carry all environment-specific configuration, with zero
hardcoded values in any image; a deliberate pod kill on CBS proves
Kubernetes reschedules it and session state survives, mirroring P05
v36's Scenario B outcome; HPA scales the Internet Banking Portal
Deployment under a simulated load spike.

---

Version 76 — Container Networking & Service Mesh
--------------------------------------------------------
Objective: Bring container-to-container communication up to the same
security and observability bar already established for VM-to-VM and
region-to-region traffic (P01 v12, P06 v41).

Banking Features Added: None.

Topics Covered: Kubernetes networking model (CNI — Amazon VPC CNI),
Ingress (AWS Load Balancer Controller, replacing IHS's external-facing
role — IHS/reverse-proxy responsibilities move to Ingress, per the
Standing WebSphere Continuity-style note below), Network Policies
(pod-to-pod traffic restriction — the container-native equivalent of
Security Groups, per P09 v54/v67), service mesh (Istio or AWS App
Mesh — mTLS between services, the container-native continuation of P01
v12's end-to-end SSL and P06 v41's cross-region mTLS), circuit breaking
and retries at the mesh layer (compared explicitly against P03 v25's
Payment Hub retry policy and P08's shared retry constants, so the two
aren't silently duplicated).

Standing Note — IHS's Role Under Containerization
IHS remains the documented reverse-proxy pattern for every non-
containerized environment in this roadmap (P01-P09). Under Kubernetes,
Ingress + the service mesh's sidecar proxies take over IHS's routing and
TLS-termination responsibilities for containerized traffic. This is
called out explicitly here so a fresh reader doesn't mistake IHS's
disappearance from the containerized topology for an oversight — it's
the same "the platform changed, the discipline didn't" pattern P07/P09
already established for WAS-version and infrastructure-substrate
changes.

WebSphere Topics Covered: None directly — this version instruments the
network fabric around the containerized WAS pods, not WAS configuration
itself.

Enterprise Learning: Cloud-Native Networking, Service Mesh Architecture,
Zero-Trust Container Networking.

Sprint Deliverable: Ingress fronts all externally-facing applications
(Internet Banking, Mobile, ATM, Card Portal) with TLS termination; a
Network Policy proves Reporting Service's pod cannot reach CBS's write
path (container-native re-enforcement of the single-writer rule, P03
§Governing Rule); service-mesh mTLS is confirmed active between CBS and
every satellite service's pod, verified via mesh-native traffic
inspection rather than manual packet capture.

---

Version 77 — Container CI/CD & GitOps
--------------------------------------------
Objective: Extend P08's Jenkins/Ansible pipeline (v49-v53) to build,
scan, and deploy container images via GitOps, rather than bolting
container steps onto the existing EAR-deployment pipeline as an
afterthought.

Banking Features Added: None.

Topics Covered: Container image build stage (Jenkins → Docker build →
ECR push, replacing/extending P08 v50's "Publish to Nexus" step with an
equivalent immutable-image-publish step); image vulnerability scanning
(ECR native scanning or a third-party scanner — concept-level scan
policy gate, consistent with this project's "commercial/heavier tooling
= concept" pattern); GitOps (Argo CD or Flux — declarative Kubernetes
manifests stored in Git, reconciled automatically, rather than
imperative `kubectl apply` from Jenkins); Helm charts (templated,
parameterized Kubernetes manifests per application, replacing hand-
written YAML per environment — the container-native equivalent of P08
v51's Ansible group_vars per-region pattern); progressive delivery on
Kubernetes (canary/blue-green via the mesh from v76, extending P02 v21
and P07 v47's canary discipline to the container layer).

WebSphere Topics Covered: None directly.

Enterprise Learning: GitOps Methodology, Declarative Deployment,
Progressive Delivery on Kubernetes.

Sprint Deliverable: A commit to develop triggers Jenkins to build and
scan a container image, publish it to ECR, and update a Helm chart
value in a Git manifest repo; Argo CD detects the manifest change and
reconciles the EKS cluster to match, with zero manual kubectl commands;
a canary rollout of a trivial CBS change shifts traffic 10% → 100%
using the service mesh, with an automatic rollback proven on a
deliberately failed health check — mirroring P07 v47's canary mechanics,
now GitOps-driven.

---

Version 78 — Containerization Capstone
--------------------------------------------
Objective: Confirm DigiStack Bank now runs entirely on a containerized,
orchestrated, GitOps-managed platform — exercising Versions 74-77
together as one governed program, in the same "build, operate, simulate,
validate" shape as every prior capstone in this roadmap (P06 v43, P07
v48, P09 v73).

Banking Features Added: None.

Final Architecture

                      Users
                        │
                     Route 53
                        │
          Application Load Balancer / Ingress
                        │
              Service Mesh (mTLS, circuit breaking)
                        │
          Amazon EKS — Kubernetes Deployments
      (all 9 applications, as containerized WAS pods)
                        │
                 DigiStack CBS (pod)
                        │
          Amazon RDS for PostgreSQL
                        │
            S3 / SQS-SNS-hybrid-with-MQ
                        │
          CloudWatch / Prometheus / Grafana
          (P04 stack, extended to scrape pod/cluster
           metrics — not replaced)
                        │
                Argo CD (GitOps reconciliation)

Migration Success Criteria (mirrors P07 v48 / P09 v73's shape). Not
complete until every item below is true simultaneously:
- No open Critical/High defects across the containerized estate
- All 9 applications running as Kubernetes Deployments, passing
  readiness/liveness probes
- Performance within the baseline established across P04 v33/P07 v45/
  P09 v63/v68 (no unexplained regression under containerization)
- Service mesh mTLS confirmed across all internal service-to-service
  traffic, single-writer rule (P03) re-verified via Network Policy
- GitOps reconciliation confirmed as the sole deployment path — no
  environment shows configuration drift from its Git-declared state
- Full regression pack (all prior TestCases-v1.md-v77.md) passes against
  the containerized platform
- P04's observability stack confirmed extended (not duplicated) to
  cover pod/cluster-level metrics and logs
- EC2-hosted WebSphere estate (P09 v59-v63) formally decommissioned only
  after a defined observation window closes with no rollback triggered

WebSphere Topics Covered: Full-stack re-verification — every WebSphere
topic from P01-P09 re-confirmed functioning identically inside a
containerized, orchestrated runtime.

Enterprise Learning: Full-Lifecycle Container Platform Governance,
Cloud-Native Operating Model Maturity.

Sprint Deliverable: All Migration Success Criteria above are demonstrated
true simultaneously, with real evidence for each; a single retrospective
document captures what changed at the runtime layer across P01→P10 and
confirms the roadmap's standing promise held one final time — DigiStack
Bank's applications never changed; only the platform beneath them did,
all the way from a single EAR on a standalone AppServer (P01 v1) to a
GitOps-managed container on Kubernetes (this version).

---

Completion Checklist
------------------------
□ CBS containerized and proven functional against RDS from inside a
  container, image published to ECR (v74)
□ All 9 applications running as Kubernetes Deployments on EKS, with
  readiness/liveness probes, ConfigMaps/Secrets (via Secrets Manager),
  and HPA proven (v75)
□ Session survival under pod rescheduling proven; SIBus ME placement
  constraint explicitly documented, not silently assumed scalable (v75)
□ Ingress + service mesh mTLS fronting and securing all containerized
  traffic; Network Policy re-enforces the single-writer rule (v76)
□ Container image build, scan, and GitOps (Argo CD/Flux + Helm)
  deployment pipeline operational, replacing manual kubectl (v77)
□ Canary/progressive delivery proven on Kubernetes via the service mesh,
  with automatic rollback on failed health check (v77)
□ Full regression pack passes against the containerized platform (v78)
□ Migration Success Criteria (v78) demonstrated true simultaneously
□ EC2-hosted WebSphere estate formally decommissioned only after
  observation window closes clean (v78)
□ All five versions' TestCases-v74.md-v78.md signed off per Test Case
  Standards
□ Cloud Resource Inventory (P09) extended with EKS cluster, ECR
  repositories, and any new networking resources (VPC CNI, mesh control
  plane)
□ Part promoted Dev → UAT → Prod per Environment Promotion Standards,
  part10-release tag applied, via the P08 v53 pipeline (extended for
  container artifacts per v77)

Application State After This Part
--------------------------------------
Application code: unchanged from P09 (digistack-bank family of 7 EARs +
Mobile/ATM Tomcat WARs, now containerized rather than repackaged) — zero
new banking functionality was added in this Part.

Platform Changes
- All 9 applications containerized (Docker) and orchestrated (Amazon
  EKS), replacing EC2-hosted WebSphere ND clusters (v74-v75)
- Ingress + service mesh (mTLS, circuit breaking) replacing IHS's
  external-facing role for containerized traffic (v76)
- GitOps-managed deployment (Argo CD/Flux + Helm) as the sole deployment
  path, extending P08's pipeline rather than replacing it (v77)
- P04's observability stack extended to cover pod/cluster-level metrics,
  not duplicated (v78)
- EC2-hosted WebSphere estate formally decommissioned (v78)

Explicit transition rule: every future addition to this roadmap assumes
the containerized, GitOps-managed platform (EKS, service mesh, Argo CD)
as the current state. The pre-containerization EC2-hosted estate is
retained only as historical record, per the same explicit transition
rule established at the end of every prior platform-migration Part
(P07, P09).

Carried Forward
---------------------
DigiStack Bank now runs entirely on a containerized, Kubernetes-
orchestrated, GitOps-managed AWS-native platform. This is currently the
final Part in the roadmap — no further Part is scoped or implied beyond
this one. Any future work (e.g., a genuinely new banking feature, or a
further platform evolution) requires a new, explicitly-scoped Part to be
proposed from scratch, per this roadmap's standing discipline.