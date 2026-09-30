# Phase 0 — Environment Setup

## Goal
Raw infrastructure before any code. Learn Tomcat internals, systemd, non-root user.

## Tomcat Skills
- #1 Fundamentals & Architecture
- #5 Security basics (non-root user)
- #9 Production Operations (systemd)

## Checklist
- [ ] Install Java 17
- [ ] Install Oracle 21c XE
- [ ] Install Kafka (single broker)
- [ ] Install Nginx
- [ ] Install Tomcat 9 Instance 1 (tarball) — port 8080 default
- [ ] Install Tomcat 9 Instance 2 (tarball) — change to port 8081
- [ ] Instance 2: change shutdown port 8005 → 8105
- [ ] Explore directory structure: bin, logs, webapps, temp, work, conf
- [ ] Create non-root user: tomcat
- [ ] Set ownership: chown -R tomcat:tomcat /opt/tomcat*
- [ ] Write systemd unit for Instance 1
- [ ] Write systemd unit for Instance 2
- [ ] Verify both instances start independently

## Key Configs

### Instance 2 — server.xml port changes
File: /opt/tomcat2/conf/server.xml
Change shutdown port:   8005 → 8105
Change HTTP port:       8080 → 8081

### systemd unit template
File: /etc/systemd/system/tomcat1.service
```
[Unit]
Description=Apache Tomcat 9 - Instance 1
After=network.target

[Service]
Type=forking
User=tomcat
Group=tomcat
Environment=JAVA_HOME=/usr/lib/jvm/java-17
Environment=CATALINA_HOME=/opt/tomcat1
Environment=CATALINA_PID=/opt/tomcat1/temp/tomcat.pid
ExecStart=/opt/tomcat1/bin/startup.sh
ExecStop=/opt/tomcat1/bin/shutdown.sh
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

### systemd unit — Instance 2
File: /etc/systemd/system/tomcat2.service
```
[Unit]
Description=Apache Tomcat 9 - Instance 2
After=network.target

[Service]
Type=forking
User=tomcat
Group=tomcat
Environment=JAVA_HOME=/usr/lib/jvm/java-17
Environment=CATALINA_HOME=/opt/tomcat2
Environment=CATALINA_PID=/opt/tomcat2/temp/tomcat.pid
ExecStart=/opt/tomcat2/bin/startup.sh
ExecStop=/opt/tomcat2/bin/shutdown.sh
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

```bash
# After writing both unit files:
systemctl daemon-reload
systemctl enable tomcat1 tomcat2
systemctl start tomcat1 tomcat2
```

## Verification Commands

```bash
# Java
java -version

# Tomcat instances
curl http://localhost:8080   # Instance 1
curl http://localhost:8081   # Instance 2

# systemd
systemctl status tomcat1
systemctl status tomcat2

# Process check
ps aux | grep tomcat
```

## Expected Log Line (catalina.out)
INFO [main] org.apache.catalina.startup.Catalina.start Server startup in [XXX] milliseconds


## Phase-End Interview Questions
1. What is the difference between CATALINA_HOME and CATALINA_BASE?
2. Why should Tomcat never run as root user?
3. What happens if two Tomcat instances share the same shutdown port (8005)?
4. What are the 5 main directories in a Tomcat installation and what does each do?
5. In the DigiStack setup, Instance 2 runs on port 8081 and shutdown port 8105 — why was 8005 changed?

## Status
- [ ] Not started
- [ ] In progress
- [ ] Completed

## Notes
(paste your errors or observations here)