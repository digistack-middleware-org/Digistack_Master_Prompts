# PHASE 1: Tomcat Foundations (Day 1–25)
Goal: Install, deploy, tune, and troubleshoot Tomcat like a banking middleware admin.
Milestone Day 25: Connectors/pools/SSL tuned + survive disk-full incident.

## Module 1: Middleware & Installation (Day 1–10)
| Day | Topic | Lab |
|---|---|---|
| 1 | Middleware: web server vs app server vs web container; Tomcat's role in a bank | Draw F5→Apache→Tomcat→DB stack on paper |
| 2 | Tomcat vs WebSphere/WebLogic/JBoss — features, cost, why banks use each | Comparison table from memory; 2 answers aloud |
| 3 | Architecture: Server→Service→Connector→Engine→Host→Context | Draw from memory — must be perfect |
| 4 | JVM basics: JDK vs JRE, JAVA_HOME, JAVA_OPTS | Install JDK 17 on Linux VM; verify java -version |
| 5 | Directory structure: bin, conf, logs, webapps, temp, work, lib | Install Tomcat from tar.gz; explore every folder |
| 6 | startup.sh / shutdown.sh / catalina.sh internals | Trace startup via ps + logs |
| 7 | CATALINA_HOME vs CATALINA_BASE; multi-instance concept | Create 2 instances on one server, different ports |
| 8 | 🏦 Banking: credit card app request flow end-to-end | Trace one request through all components; write the story |
| 9 | systemd service for Tomcat | Create tomcat.service; enable auto-start; test reboot |
| 10 | Deploying WAR: exploded vs WAR vs hot deploy | Deploy sample WAR 3 ways; break it; fix it |

## Module 2: Core Configuration (Day 11–21)
| Day | Topic | Lab |
|---|---|---|
| 11 | server.xml structure: Server, Service, Connector, Engine, Host | Annotate every line of server.xml |
| 12 | HTTP Connector: maxThreads, minSpareThreads, acceptCount, connectionTimeout, maxConnections | Change each param; observe via JConsole |
| 13 | AJP Connector; NIO vs APR; why AJP for Apache backend | Apache + mod_proxy_ajp → Tomcat lab |
| 14 | Ports: shutdown, HTTP, AJP; port conflicts | Run 4 Tomcat instances simultaneously, no conflicts |
| 15 | Virtual Hosts, Context, autoDeploy — why disable in PROD | Configure 2 vhosts; test autoDeploy behavior |
| 16 | web.xml: global vs app level; context.xml | Compare conf/web.xml vs WEB-INF/web.xml |
| 17 | JNDI DataSource theory + pool params (maxTotal, maxWait, validationQuery, testOnBorrow) | Install MySQL/Oracle-XE; configure JNDI pool |
| 18 | DB pool tuning & thread pile-up behavior | JMeter load; exhaust pool; observe maxWait effect |
| 19 | SSL/TLS: keystore, truststore, 1-way vs 2-way | Enable HTTPS self-signed; verify browser + openssl |
| 20 | Mutual TLS for B2B/payment gateway | Configure clientAuth; import client cert into truststore |
| 21 | 🏦 Banking: Net Banking salary-day traffic — threads exhausted | JMeter at maxThreads limit; capture symptoms |

## Module 3: Logging (Day 22–25)
| Day | Topic | Lab |
|---|---|---|
| 22 | Log types: catalina.out, catalina.log, localhost.log, access_log | Identify each in your instance; know what goes where |
| 23 | catalina.out unbounded growth + logrotate | Fill a disk on purpose; set up logrotate; fix it |
| 24 | Access logs for audit (RBI/SOX); log levels; Splunk/ELK basics | Enable %D timing in access log; change log levels |
| 25 | 🏦 Banking: auditor asks 6 months of access logs | Write audit-response procedure + retention script |

## ✅ Phase 1 Checkpoint (Day 25)
- catalina.out disk-full incident — do it and fix it yourself
- Mock interview: Modules 1–3 (installation, config, logging)
- War stories so far: minimum 3 (request flow, pool exhaustion, disk-full)
