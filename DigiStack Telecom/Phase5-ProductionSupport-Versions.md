# Phase 5 — Production Support: Versions & Sprints

Goal: Real incident scenarios, batched by failure category. Since this phase IS incident response training, the 6-sprint structure is adapted: instead of "build an app," each version walks through fault injection → diagnosis → full incident lifecycle for that failure category.

Every version follows this 6-sprint structure:
1. Learning Session (Concept)
2. Fault Injection / Reproduce the Failure
3. Administration — diagnostic tools & commands (Console + wsadmin + logs)
4. Incident Response — Symptoms → Investigation → Logs → Commands → Root Cause → Resolution → Prevention
5. Documentation + Interview Questions
6. Hands-on Challenge — a new, unannounced failure to diagnose independently

## Version-1: App/Deployment Failures
1. Concept — why deployments fail: app won't start, plugin sync failure, packaging issues
2. Fault Injection — reproduce an app-won't-start scenario (missing class, bad manifest)
3. Administration — diagnostic tools: SystemOut.log, plugin logs, deployment manager logs
4. Incident Response — full cycle for the app-start failure
5. Documentation + Interview Questions
6. Hands-on Challenge — diagnose a new, unannounced deployment failure

## Version-2: JVM/Memory Failures
1. Concept — hung threads, OutOfMemoryError, GC pauses
2. Fault Injection — reproduce a hung thread / OOM scenario under load
3. Administration — thread dumps, heap dumps, verbose GC logs, javacore analysis
4. Incident Response — full cycle for the JVM/memory incident
5. Documentation + Interview Questions
6. Hands-on Challenge — diagnose a new JVM crash scenario

## Version-3: Data/Messaging Failures
1. Concept — MQ unavailable, JDBC failures, connection pool exhaustion
2. Fault Injection — reproduce MQ-down / JDBC connection failure
3. Administration — MQ diagnostics (runmqsc, FFDC), datasource diagnostics, connection pool monitoring
4. Incident Response — full cycle for the data/messaging incident
5. Documentation + Interview Questions
6. Hands-on Challenge — diagnose a new data/messaging failure

## Version-4: Network/Security/Session Failures
1. Concept — SSL cert expiry, session replication issues, cluster member failures
2. Fault Injection — reproduce SSL expiry / session loss scenario
3. Administration — SSL diagnostics, session manager logs, cluster health checks
4. Incident Response — full cycle for the network/security/session incident
5. Documentation + Interview Questions
6. Hands-on Challenge — diagnose a new network/security/session failure
