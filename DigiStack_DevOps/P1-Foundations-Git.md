# PHASE 1: DEVOPS FOUNDATIONS + GIT (Days 1–10)

## Goal
Understand DevOps in a bank; master Git; own build→artifact→version discipline.

## Days
D1: What is DevOps — silos, CI/CD concept, toolchain map (Git→Jenkins→
    Artifactory→Ansible→WAS), DevSecOps, why banks adopt it
D2: Git Basics ⭐ — init/clone/add/commit/push/pull, staging vs repo, .gitignore
D3: Branching & Merging ⭐ — checkout -b, merge, conflicts, git diff; bank
    strategy: feature→UAT→main(=Prod)
D4: Enterprise Git — PRs, 2-approvals rule, tags (v1.2.3), git blame,
    revert vs reset
D5: GitOps for WAS ⭐ — config-as-code, drift detection, console read-only
    in Prod rule
D6: CI/CD Concepts ⭐ — CI vs Delivery vs Deployment, pipeline stages,
    quality gates, Dev→UAT→Prod promotion
D7: Maven — pom.xml, lifecycle, mvn clean package, WAR structure; no laptop builds
D8: Artifactory/Nexus ⭐ — SNAPSHOT vs RELEASE, "build once deploy many",
    checksum verification, immutability
D9: Versioning & Release Strategy — semver, rollback planning (previous
    artifact stays deployable)
D10: ✅ Revision — draw full CI/CD flow from memory + Git hands-on

## Banking Scenario Seeds
- Release failure 30% → <5% after CI/CD; auto-linked change tickets passed RBI audit
- All WAS config (server.xml templates, Jython, Ansible) in Git — "lost config" dead
- Unmerged-to-main mistake pushed untested context-root change to Prod
- Git history = change history auditors read directly
- Prod console click on DataSource timeout → 3 AM mystery → GitOps rule
- Laptop-built WAR = no traceability = firing offense
- Rebuilt WAR for Prod = untested = audit violation
- v3.2.1 smoke fail → rollback to v3.1.9 in 8 min

## Interview Seeds
- What is DevOps? Why harder in banks than startups?
- git pull vs git fetch
- Branching strategy for regulated environments
- Git history in audits; revert vs reset on shared branches
- Prevent config drift Git vs live WAS
- CI vs CD vs CD with banking example
- Why CI builds only?
- Guarantee Prod binary = UAT binary
- Design rollback strategy for banking release

## Phase-Done Checklist
(a) Compact revision notes  (b) 7 deliverables (20 interview Q&A,
5 recent issues, 20 scenario Qs, 20 troubleshooting Qs, 5 war stories,
5 behavioral, 5 architecture)
