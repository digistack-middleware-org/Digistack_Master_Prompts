# Phase 6 — Performance Tuning

## Goal
Load test with JMeter. Tune connectors, JVM. Practice thread/heap dump analysis.

## Tomcat Skills
- #3 Connector tuning (most interview-heavy)
- #6 jstack, jmap, MAT heap analysis
- #3 JVM heap tuning

## JMeter Test Plan
- 200 concurrent users
- Endpoint: POST /cbs/transfer/external
- Duration: 5 minutes
- Ramp-up: 30 seconds

## Connector Tuning (server.xml)
```xml
<!-- Shared Executor -->
<Executor name="tomcatThreadPool"
          namePrefix="catalina-exec-"
          maxThreads="200"
          minSpareThreads="25"
          maxQueueSize="100"/>

<!-- HTTP Connector using Executor -->
<Connector executor="tomcatThreadPool"
           port="8090"
           protocol="org.apache.coyote.http11.Http11NioProtocol"
           connectionTimeout="20000"
           maxConnections="1000"
           acceptCount="100"
           compression="on"
           compressionMinSize="2048"/>
```

## JVM Tuning (setenv.sh)
```bash
# Create file: /opt/tomcat-cbs/bin/setenv.sh
# This file is auto-sourced by catalina.sh on startup

cat > /opt/tomcat-cbs/bin/setenv.sh << 'EOF'
export JAVA_OPTS="-Xms512m -Xmx512m \
  -XX:+HeapDumpOnOutOfMemoryError \
  -XX:HeapDumpPath=/opt/tomcat-cbs/logs/heapdump.hprof \
  -Xlog:gc:/opt/tomcat-cbs/logs/gc.log"
EOF

chmod +x /opt/tomcat-cbs/bin/setenv.sh

# Verify it's picked up — after restart look for:
# grep "Xmx" /opt/tomcat-cbs/logs/catalina.out
```

## Exercises
1. Trigger OOM: set -Xmx512m + load test → heapdump → analyze in MAT
2. Stuck thread: add Thread.sleep(60000) endpoint → jstack → find it
3. Access log with %D: find slowest requests
4. BIO vs NIO connector: measure difference under load

## Access Log Valve (server.xml)
```xml
<Valve className="org.apache.catalina.valves.AccessLogValve"
       directory="logs"
       prefix="access_log"
       suffix=".txt"
       pattern="%h %l %u %t &quot;%r&quot; %s %b %D"/>
```
Note: %D = request processing time in milliseconds

## Diagnosis Commands
```bash
# jstack — find stuck threads
jstack <PID> | grep -A 20 "BLOCKED\|WAITING"

# jmap — trigger heap dump manually
jmap -dump:format=b,file=/tmp/heap.hprof <PID>

# Watch thread count via JMX
# (covered in Phase 9 with Prometheus)
```

## Phase-End Interview Questions
1. In the DigiStack JMeter test (200 users, POST /cbs/transfer/external): maxThreads=200, acceptCount=100, maxConnections=1000 — what happens to the 301st concurrent request?
2. How do you find a stuck thread in tomcat-cbs production right now — exact command sequence?
3. What does %D in the AccessLogValve pattern capture — and how do you find the slowest CBS transfer request from the log?
4. After setting -Xmx512m and running the JMeter test, CBS crashes with OOM — what is the exact path of the heap dump file in DigiStack?
5. What is the difference between -Xms and -Xmx — why should they be equal in production Tomcat?

## Status
- [ ] Not started
- [ ] In progress
- [ ] Completed

## Notes