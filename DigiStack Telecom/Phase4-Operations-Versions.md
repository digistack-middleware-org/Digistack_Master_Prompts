# Phase 4 — Operations: Versions & Sprints

Goal: Automate and scale what Phase 2 taught manually — CI/CD, rolling deployments, backup/recovery, SSL/TLS management, security hardening, and performance tuning, now wired into Jenkins and applied fleet-wide.

Every version follows the same 6-sprint structure:
1. Learning Session (Concept)
2. Hands-on Build
3. Administration (Console/CLI + wsadmin + Jenkins automation)
4. Incident Simulation
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-1: Jenkins CI/CD Fundamentals & Pipeline Setup
1. Concept — CI/CD principles, why Jenkins, pipeline as code
2. Hands-on Build — install Jenkins, create first pipeline job
3. Administration — Jenkins job config, plugins, credentials store
4. Incident Simulation — pipeline job fails silently / bad credential
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-2: Automated Build & Deploy Pipeline
1. Concept — wiring wsadmin/Jython scripts into Jenkins stages
2. Hands-on Build — Jenkinsfile: build → package → deploy stages
3. Administration — trigger wsadmin deployment from a Jenkins pipeline
4. Incident Simulation — pipeline deploys the wrong EAR version
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-3: Rolling Deployments & Zero-Downtime — Automated at Fleet Scale
1. Concept — recap the manual rolling deployment from Phase 2; now automate it across all 11 apps via Jenkins
2. Hands-on Build — Jenkins pipeline for rolling deploy across cluster members
3. Administration — orchestrate rolling restart sequencing via wsadmin + Jenkins
4. Incident Simulation — automated rollout stalls mid-cluster
5. Documentation + Interview Questions
6. Hands-on Challenge — automate a zero-downtime deploy across the full fleet

## Version-4: Application Updates & EAR Version Management — Automated at Fleet Scale
1. Concept — versioning strategy across 11 apps, semantic versioning for EARs
2. Hands-on Build — Jenkins job to auto-tag/version EAR builds
3. Administration — automated rollback pipeline (auto-revert on failed health check)
4. Incident Simulation — version mismatch across cluster members after a partial deploy
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-5: Backup & Recovery
1. Concept — backupConfig, pg_dump, Flyway history — why a backup strategy matters
2. Hands-on Build — script backupConfig + pg_dump for all components
3. Administration — schedule automated backups via cron/Jenkins
4. Incident Simulation — restore from backup after config corruption
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-6: SSL/TLS Certificate Management — Automated at Cell Scale
1. Concept — recap manual cert/keystore work from Phase 2; now automate renewal/rotation
2. Hands-on Build — script cert renewal across the cell
3. Administration — Jenkins job for scheduled cert rotation, expiry alerting
4. Incident Simulation — expired cert takes down the cluster mid-automation
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-7: Security Hardening — Automated & Audited at Fleet Scale
1. Concept — recap Phase 2 security config; automate hardening checks + audit logging fleet-wide
2. Hands-on Build — script to validate security config compliance across all apps
3. Administration — Jenkins job for scheduled security audits, secrets rotation automation
4. Incident Simulation — audit reveals a misconfigured app in production
5. Documentation + Interview Questions
6. Hands-on Challenge

## Version-8: JVM & WebSphere Performance Tuning — Automated & Scaled
1. Concept — recap Phase 2 manual tuning; automate tuning-profile application across cluster members
2. Hands-on Build — script/templatize JVM and container tuning configs
3. Administration — Jenkins job to apply tuning profiles fleet-wide, capacity-based scaling triggers
4. Incident Simulation — an automated tuning rollout causes a regression
5. Documentation + Interview Questions
6. Hands-on Challenge
