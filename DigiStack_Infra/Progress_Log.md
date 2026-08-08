# Progress Log — WebSphere Banking Infra Roadmap (245 Days)

**Started on:** ____________
**Last updated:** ____________

---

## Lab Environment — Software Versions

| Component | Version | Notes |
|---|---|---|
| WebSphere Application Server ND | 9.0.5.21 | Stable enterprise fix pack, production-like |
| Java | IBM Java 8 (SDK 8.0) | Standard for WAS 9.0.5.x |
| IBM HTTP Server (IHS) | 9.0.5.21 | Fix pack matched to WAS |
| Web Server Plug-ins | 9.0.5.21 | Fix pack matched to IHS and WAS |
| IBM Installation Manager | 1.9.x | For install, updates, and maintenance |
| IBM MQ | 9.3.x or 9.4.x | JMS and enterprise messaging integration |
| OS | RHEL 8.x / Rocky Linux 8.x | Enterprise OS, WAS 9.0.5.x supported |
| Database | PostgreSQL 13+ (Lab) / Oracle 19c (Enterprise) | PostgreSQL for labs; Oracle 19c for enterprise context |

---

## Quick Status

| Field | Value |
|---|---|
| Current Day | |
| Current Phase | |
| Current Topic | |
| Day Type (Standard / Lab / Incident) | |
| Status (Not Started / In Progress / Done) | |

---

## Phase Completion Tracker

| Phase | Days | Status | Completed On |
|---|---|---|---|
| Phase 0 – Foundation | 1–5 | ☐ Not Started ☐ In Progress ☐ Done | |
| Phase 1 – Linux | 6–110 | ☐ Not Started ☐ In Progress ☐ Done | |
| Phase 2 – Shell Scripting | 111–155 | ☐ Not Started ☐ In Progress ☐ Done | |
| Phase 3 – Ansible | 156–207 | ☐ Not Started ☐ In Progress ☐ Done | |
| Phase 4 – Capstone | 208–245 | ☐ Not Started ☐ In Progress ☐ Done | |

---

## Day-by-Day Log

| Day | Topic | Type | Status | Date Completed | Notes |
|---|---|---|---|---|---|
| 1 | Course orientation | Foundation (Simplified) | ☐ | | |
| 2 | Enterprise banking architecture overview | Foundation (Simplified) | ☐ | | |
| 3 | Banking application environments (DEV/SIT/UAT/PRE-PROD/PROD) | Foundation (Simplified) | ☐ | | |
| 4 | Infrastructure overview | Foundation (Simplified) | ☐ | | |
| 5 | Learning methodology & lab preparation | Foundation (Simplified) | ☐ | | |
| 6 | Linux Fundamentals – History & Distributions | Standard | ☐ | | |
| 7 | Enterprise Lab Day | Lab Day | ☐ | | |
| 8 | Linux Fundamentals – Boot Process & Kernel | Standard | ☐ | | |
| 9 | Linux Fundamentals – Shell Basics & CLI Navigation | Standard | ☐ | | |
| 10 | File System – Hierarchy (FHS) | Standard | ☐ | | |
| 11 | File System – Types (ext4, xfs) & Mounting | Standard | ☐ | | |
| 12 | File System – Inodes & Links | Standard | ☐ | | |
| 13 | File & Directory Management – Basic Ops | Standard | ☐ | | |
| 14 | File & Directory Management – Advanced (find, archiving) | Standard | ☐ | | |
| 15 | Banking Production Incident Day | Incident Day | ☐ | | |
| 16 | Users & Groups – Management | Standard | ☐ | | |
| 17 | Users & Groups – Password Policies & PAM basics | Standard | ☐ | | |
| 18 | Permissions – Basic (rwx, chmod, chown) | Standard | ☐ | | |
| 19 | Permissions – Special (SUID/SGID/Sticky bit) | Standard | ☐ | | |
| 20 | ACLs – Access Control Lists | Standard | ☐ | | |
| 21 | Processes – Fundamentals (ps, top) | Standard | ☐ | | |
| 22 | Enterprise Lab Day | Lab Day | ☐ | | |
| 23 | Processes – Signals & Priority | Standard | ☐ | | |
| 24 | Services (systemd) – Unit files & Targets | Standard | ☐ | | |
| 25 | Services (systemd) – Managing Services | Standard | ☐ | | |
| 26 | Networking Fundamentals – OSI Model & TCP/IP Model Overview | Standard (NEW) | ☐ | | |
| 27 | Networking Fundamentals – IPv4 Addressing, Private vs Public IP, Subnet Mask, Default Gateway & MAC Address | Standard (NEW) | ☐ | | |
| 28 | Hostname Resolution – Hostname, FQDN, /etc/hosts & DNS Forward/Reverse Lookup | Standard (NEW) | ☐ | | |
| 29 | TCP/IP Communication – TCP, UDP, Three-Way Handshake & Sockets | Standard (NEW) | ☐ | | |
| 30 | Banking Production Incident Day | Incident Day | ☐ | | |
| 31 | Ports Deep-Dive – Well-Known/Dynamic/Listening Ports & WebSphere ND Port Reference (9060/9043/9080/9443/8879/2809/9352) + netstat/ss/lsof | Standard (NEW) | ☐ | | |
| 32 | HTTP & HTTPS Fundamentals – Requests, Responses, Status Codes, Headers, Cookies & Sessions | Standard (NEW) | ☐ | | |
| 33 | SSL/TLS Handshake Flow – Browser → IBM HTTP Server → WebSphere | Standard (NEW) | ☐ | | |
| 34 | DNS Deep-Dive – A Record, CNAME, PTR Record & Name Resolution Tools (nslookup, dig, host) | Standard (NEW) | ☐ | | |
| 35 | Firewall Concepts for WebSphere ND – Rules, Allow/Deny, Source/Destination & Port Opening | Standard (NEW) | ☐ | | |
| 36 | Load Balancer Concepts – Layer 4 vs Layer 7, Sticky Sessions, Health Checks, Failover, Round Robin (F5/HAProxy/NGINX) | Standard (NEW) | ☐ | | |
| 37 | Enterprise Lab Day | Lab Day | ☐ | | |
| 38 | Reverse Proxy Deep-Dive – IBM HTTP Server, Apache HTTP Server & plugin-cfg.xml | Standard (NEW) | ☐ | | |
| 39 | SSL/TLS for WebSphere – Certificates, CSR, CA, Self-Signed Certs, TrustStore/KeyStore, PKCS12/JKS | Standard (NEW) | ☐ | | |
| 40 | SSL/TLS Tooling – ikeyman & keytool + Certificate Renewal Workflow | Standard (NEW) | ☐ | | |
| 41 | Network Troubleshooting Commands – ping, traceroute, tracepath, curl, telnet, nc, netstat, ss, ip addr, ip route | Standard (NEW) | ☐ | | |
| 42 | Enterprise Network Architecture for WebSphere ND – Full Traffic Flow (Internet → Firewall → LB → IHS → Plugin → Cluster → App/DB/MQ) | Standard (NEW) | ☐ | | |
| 43 | Networking Production Scenarios Lab – DMGR Unreachable, Node Federation SOAP-Port Block, DNS Misresolution, SSL Handshake Failures, IHS-to-Cluster Connectivity, Node-Specific App Failures, LB Routing to Unhealthy Nodes, JDBC/MQ Port Blocks, HTTP 503 Patterns | Standard (NEW) | ☐ | | |
| 44 | Package Management – RPM/YUM/DNF | Standard | ☐ | | |
| 45 | Banking Production Incident Day | Incident Day | ☐ | | |
| 46 | Package Management – APT & Repositories | Standard | ☐ | | |
| 47 | Storage – Disk Partitioning | Standard | ☐ | | |
| 48 | Storage – LVM Basics | Standard | ☐ | | |
| 49 | Storage – LVM Advanced | Standard | ☐ | | |
| 50 | Storage – RAID Concepts | Standard | ☐ | | |
| 51 | NFS / Shared Storage Mounts | Standard | ☐ | | |
| 52 | Enterprise Lab Day | Lab Day | ☐ | | |
| 53 | Mount Points, fstab & Persistent Storage Configuration | Standard (NEW) | ☐ | | |
| 54 | CIFS/SMB Shares – Cross-Platform Storage Integration | Standard (NEW) | ☐ | | |
| 55 | Storage Arrays Deep-Dive – SAN/NAS/LUN Concepts & Storage Snapshots | Standard (NEW) | ☐ | | |
| 56 | Storage – SAN Multipathing (DM-Multipath, multipath -ll) & Enterprise Backup Tools Overview (TSM/Commvault/NetBackup) | Standard (NEW) | ☐ | | |
| 57 | Logging – syslog & rsyslog | Standard | ☐ | | |
| 58 | Logging – journald | Standard | ☐ | | |
| 59 | System Diagnostics – dmesg, strace & lsof for Production Troubleshooting | Standard (NEW) | ☐ | | |
| 60 | Banking Production Incident Day | Incident Day | ☐ | | |
| 61 | Log Rotation Deep-Dive – logrotate Configuration & Enterprise Retention Policy | Standard (NEW) | ☐ | | |
| 62 | Log Aggregation/SIEM Basics – Shipping Logs to Splunk/ELK (Forwarder Config, Index Routing) | Standard (NEW) | ☐ | | |
| 63 | Scheduling (cron) | Standard | ☐ | | |
| 64 | SSH – Key-based Authentication | Standard | ☐ | | |
| 65 | SSH – Hardening & Tunneling | Standard | ☐ | | |
| 66 | Performance Monitoring – CPU & Memory | Standard | ☐ | | |
| 67 | Enterprise Lab Day | Lab Day | ☐ | | |
| 68 | Performance Monitoring – Disk & I/O | Standard | ☐ | | |
| 69 | Performance Monitoring – Network | Standard | ☐ | | |
| 70 | Performance Tools Deep-Dive – top, htop, vmstat, free & mpstat | Standard (NEW) | ☐ | | |
| 71 | Performance Tools Deep-Dive – iostat, sar & pidstat | Standard (NEW) | ☐ | | |
| 72 | Kernel/sysctl Tuning – TCP & ulimit | Standard | ☐ | | |
| 73 | Kernel/sysctl Tuning – Shared Memory for WAS | Standard | ☐ | | |
| 74 | Kernel Parameters – Swap Management & Configuration | Standard (NEW) | ☐ | | |
| 75 | Banking Production Incident Day | Incident Day | ☐ | | |
| 76 | Kernel Parameters – Huge Pages for JVM/WebSphere Performance | Standard (NEW) | ☐ | | |
| 77 | ulimits Deep-Dive – Soft/Hard Limits & Per-Process Resource Tuning | Standard (NEW) | ☐ | | |
| 78 | JVM/Java Troubleshooting – Process-level checks | Standard | ☐ | | |
| 79 | JVM/Java Troubleshooting – Thread & Heap Dumps | Standard | ☐ | | |
| 80 | Time Synchronization (NTP/chrony) | Standard | ☐ | | |
| 81 | SSL/TLS Fundamentals | Standard | ☐ | | |
| 82 | Enterprise Lab Day | Lab Day | ☐ | | |
| 83 | Certificate Management (openssl, keystores) | Standard | ☐ | | |
| 84 | Firewall – firewalld | Standard | ☐ | | |
| 85 | Firewall – iptables | Standard | ☐ | | |
| 86 | Network Security Zones & DMZ Architecture for Banking Infra | Standard (NEW) | ☐ | | |
| 87 | Security Basics – Hardening Fundamentals | Standard | ☐ | | |
| 88 | SELinux Basics | Standard | ☐ | | |
| 89 | Patch Management | Standard | ☐ | | |
| 90 | Banking Production Incident Day | Incident Day | ☐ | | |
| 91 | Patch/Vulnerability Management – Enterprise Tooling (Red Hat Satellite/Uyuni, Qualys/Nessus Scanning) | Standard (NEW) | ☐ | | |
| 92 | CIS Hardening / Compliance Basics | Standard | ☐ | | |
| 93 | Compliance/Regulatory Basics for Infra Teams (PCI-DSS, RBI/SEBI – Log Retention, Encryption-at-Rest, Access Reviews) | Standard (NEW) | ☐ | | |
| 94 | IBM HTTP Server (IHS) Basics | Standard | ☐ | | |
| 95 | DB Connectivity Troubleshooting from OS Level | Standard | ☐ | | |
| 96 | Database Connectivity – Connection Pooling & Timeout Troubleshooting at OS/Network Level (netstat/ss/tcpdump, TCP Keepalive) | Standard (NEW) | ☐ | | |
| 97 | Enterprise Lab Day | Lab Day | ☐ | | |
| 98 | Load Balancer / Reverse Proxy Concepts | Standard | ☐ | | |
| 99 | Load Balancer Deep-Dive – Hands-on HAProxy/NGINX/F5 Config for Banking App Tier | Standard (NEW) | ☐ | | |
| 100 | High Availability / Clustering at OS Level | Standard | ☐ | | |
| 101 | Containers Basics (Docker) | Standard | ☐ | | |
| 102 | Kubernetes Overview | Standard | ☐ | | |
| 103 | Container Security – Image Scanning & Docker/K8s Hardening | Standard (NEW) | ☐ | | |
| 104 | Privileged Access Management Concepts | Standard | ☐ | | |
| 105 | Banking Production Incident Day | Incident Day | ☐ | | |
| 106 | Troubleshooting – Systematic Methodology | Standard | ☐ | | |
| 107 | Troubleshooting – Common Production Issues | Standard | ☐ | | |
| 108 | Linux Automation – Basics | Standard | ☐ | | |
| 109 | Linux Automation – Advanced Scripting Integration | Standard | ☐ | | |
| 110 | ITIL Basics & Major Incident Bridge Communication | Standard | ☐ | | |
| 111 | Shell Basics – Shebang, Script Execution | Standard | ☐ | | |
| 112 | Enterprise Lab Day | Lab Day | ☐ | | |
| 113 | Shell Basics – Comments & Quoting | Standard | ☐ | | |
| 114 | Shell Basics – Command Substitution | Standard | ☐ | | |
| 115 | Variables – Declaration & Scope | Standard | ☐ | | |
| 116 | Variables – Environment Variables | Standard | ☐ | | |
| 117 | Operators – Arithmetic & Comparison | Standard | ☐ | | |
| 118 | Operators – Logical & String | Standard | ☐ | | |
| 119 | Input & Output – read, echo, printf | Standard | ☐ | | |
| 120 | Banking Production Incident Day | Incident Day | ☐ | | |
| 121 | Input & Output – Redirection & Pipes | Standard | ☐ | | |
| 122 | Conditional Statements – if/else | Standard | ☐ | | |
| 123 | Conditional Statements – case | Standard | ☐ | | |
| 124 | Loops – for | Standard | ☐ | | |
| 125 | Loops – while & until | Standard | ☐ | | |
| 126 | Functions – Basics | Standard | ☐ | | |
| 127 | Enterprise Lab Day | Lab Day | ☐ | | |
| 128 | Functions – Return Values & Scope | Standard | ☐ | | |
| 129 | Arrays – Indexed Arrays | Standard | ☐ | | |
| 130 | Arrays – Associative Arrays | Standard | ☐ | | |
| 131 | File Handling – Reading Files | Standard | ☐ | | |
| 132 | File Handling – Writing & Appending | Standard | ☐ | | |
| 133 | Text Processing – grep | Standard | ☐ | | |
| 134 | Text Processing – awk | Standard | ☐ | | |
| 135 | Banking Production Incident Day | Incident Day | ☐ | | |
| 136 | Text Processing – sed | Standard | ☐ | | |
| 137 | Text Processing – Combining grep/awk/sed | Standard | ☐ | | |
| 138 | Error Handling & Debugging (trap, set -e, set -x) | Standard | ☐ | | |
| 139 | JSON/XML Parsing (jq, xmllint) | Standard | ☐ | | |
| 140 | Process Automation – Basics | Standard | ☐ | | |
| 141 | Process Automation – Advanced | Standard | ☐ | | |
| 142 | Enterprise Lab Day | Lab Day | ☐ | | |
| 143 | Monitoring Scripts – System Health Checks | Standard | ☐ | | |
| 144 | Monitoring Scripts – WebSphere Health Checks | Standard | ☐ | | |
| 145 | Parsing WebSphere Logs (SystemOut/SystemErr) | Standard | ☐ | | |
| 146 | Sending Metrics/Logs to Monitoring Stacks | Standard | ☐ | | |
| 147 | Alerting/Notifications from Scripts | Standard | ☐ | | |
| 148 | Backup Scripts – Basics | Standard | ☐ | | |
| 149 | Backup Scripts – Advanced (rotation, retention) | Standard | ☐ | | |
| 150 | Banking Production Incident Day | Incident Day | ☐ | | |
| 151 | Deployment Scripts – Basics | Standard | ☐ | | |
| 152 | Deployment Scripts – Advanced (WAS EAR/WAR) | Standard | ☐ | | |
| 153 | Shell Script Version Control Basics (git) | Standard | ☐ | | |
| 154 | Log Analysis – Techniques | Standard | ☐ | | |
| 155 | Log Analysis – Advanced Correlation | Standard | ☐ | | |
| 156 | Introduction – What is Ansible & Architecture | Standard | ☐ | | |
| 157 | Enterprise Lab Day | Lab Day | ☐ | | |
| 158 | Introduction – Agentless Model & SSH Basis | Standard | ☐ | | |
| 159 | Installation – Control Node Setup | Standard | ☐ | | |
| 160 | Inventory – Static Inventory Basics | Standard | ☐ | | |
| 161 | Inventory – Groups & Variables | Standard | ☐ | | |
| 162 | Inventory – Host Patterns | Standard | ☐ | | |
| 163 | Dynamic Inventory | Standard | ☐ | | |
| 164 | Ad-hoc Commands – Basics | Standard | ☐ | | |
| 165 | Banking Production Incident Day | Incident Day | ☐ | | |
| 166 | Ad-hoc Commands – Advanced Use Cases | Standard | ☐ | | |
| 167 | Modules – Command & Shell Modules | Standard | ☐ | | |
| 168 | Modules – File & Copy Modules | Standard | ☐ | | |
| 169 | Modules – Package & Service Modules | Standard | ☐ | | |
| 170 | Playbooks – Structure & Syntax | Standard | ☐ | | |
| 171 | Playbooks – Tasks & Plays | Standard | ☐ | | |
| 172 | Enterprise Lab Day | Lab Day | ☐ | | |
| 173 | Playbooks – Conditionals in Playbooks | Standard | ☐ | | |
| 174 | Playbooks – Loops in Playbooks | Standard | ☐ | | |
| 175 | Playbooks – Multi-play Playbooks | Standard | ☐ | | |
| 176 | Variables – Precedence | Standard | ☐ | | |
| 177 | Variables – Vars Files & Prompts | Standard | ☐ | | |
| 178 | Facts – Gathering & Custom Facts | Standard | ☐ | | |
| 179 | Facts – Using Facts in Playbooks | Standard | ☐ | | |
| 180 | Banking Production Incident Day | Incident Day | ☐ | | |
| 181 | Templates (Jinja2) – Basics | Standard | ☐ | | |
| 182 | Templates (Jinja2) – Advanced Filters & Loops | Standard | ☐ | | |
| 183 | Templates (Jinja2) – WebSphere Config Templates | Standard | ☐ | | |
| 184 | Handlers – Basics | Standard | ☐ | | |
| 185 | Handlers – Notify & Listen | Standard | ☐ | | |
| 186 | Roles – Structure | Standard | ☐ | | |
| 187 | Enterprise Lab Day | Lab Day | ☐ | | |
| 188 | Roles – Creating Custom Roles | Standard | ☐ | | |
| 189 | Roles – Role Dependencies | Standard | ☐ | | |
| 190 | Roles – Testing Roles | Standard | ☐ | | |
| 191 | Roles – WebSphere Role Example | Standard | ☐ | | |
| 192 | Ansible Galaxy & Collections | Standard | ☐ | | |
| 193 | Idempotency Deep-Dive | Standard | ☐ | | |
| 194 | Tags – Basics & Use Cases | Standard | ☐ | | |
| 195 | Banking Production Incident Day | Incident Day | ☐ | | |
| 196 | Vault – Encrypting Secrets | Standard | ☐ | | |
| 197 | Vault – Managing Vault in Teams | Standard | ☐ | | |
| 198 | Vault – Vault in CI/CD | Standard | ☐ | | |
| 199 | Enterprise Secrets Management – CyberArk/HashiCorp Vault Overview & Ansible Integration Patterns | Standard (NEW) | ☐ | | |
| 200 | Error Handling – Blocks & Rescue | Standard | ☐ | | |
| 201 | Error Handling – Ignore Errors & Failed When | Standard | ☐ | | |
| 202 | Enterprise Lab Day | Lab Day | ☐ | | |
| 203 | IBM WebSphere-Specific Ansible Content | Standard | ☐ | | |
| 204 | CI/CD Integration (Jenkins) | Standard | ☐ | | |
| 205 | AWX / Ansible Tower Overview | Standard | ☐ | | |
| 206 | Enterprise Automation – Patterns & Standards | Standard | ☐ | | |
| 207 | Enterprise Automation – Scaling Playbooks | Standard | ☐ | | |
| 208 | Multi-server Infrastructure Setup – Planning & Design | Standard | ☐ | | |
| 209 | Multi-server Infrastructure Setup – Implementation | Standard | ☐ | | |
| 210 | Banking Production Incident Day | Incident Day | ☐ | | |
| 211 | Multi-server Infrastructure Setup – Validation | Standard | ☐ | | |
| 212 | Health Check Automation – Design | Standard | ☐ | | |
| 213 | Health Check Automation – Implementation | Standard | ☐ | | |
| 214 | Health Check Automation – Integration with Monitoring | Standard | ☐ | | |
| 215 | Log Collection and Analysis – Centralized Logging Setup | Standard | ☐ | | |
| 216 | Log Collection and Analysis – Automated Analysis Scripts | Standard | ☐ | | |
| 217 | Enterprise Lab Day | Lab Day | ☐ | | |
| 218 | Backup and Restore Automation – Design | Standard | ☐ | | |
| 219 | Backup and Restore Automation – Implementation | Standard | ☐ | | |
| 220 | Backup and Restore Automation – Restore Testing | Standard | ☐ | | |
| 221 | Server Provisioning – Baseline OS Setup | Standard | ☐ | | |
| 222 | Server Provisioning – Automated Provisioning with Ansible | Standard | ☐ | | |
| 223 | Configuration Management – Standardizing Configs | Standard | ☐ | | |
| 224 | Configuration Management – Drift Detection & Remediation | Standard | ☐ | | |
| 225 | Banking Production Incident Day | Incident Day | ☐ | | |
| 226 | Deployment Automation – Application Deployment Pipeline | Standard | ☐ | | |
| 227 | Deployment Automation – Rollback Strategy | Standard | ☐ | | |
| 228 | Deployment Automation – Blue-Green/Canary Concepts | Standard | ☐ | | |
| 229 | CI/CD-Integrated Deployment Pipeline – Design | Standard | ☐ | | |
| 230 | CI/CD-Integrated Deployment Pipeline – Implementation | Standard | ☐ | | |
| 231 | Disaster Recovery Simulation – Planning | Standard | ☐ | | |
| 232 | Enterprise Lab Day | Lab Day | ☐ | | |
| 233 | Disaster Recovery Simulation – Execution | Standard | ☐ | | |
| 234 | Disaster Recovery Simulation – Post-DR Review | Standard | ☐ | | |
| 235 | Performance Tuning – JVM & WebSphere Tuning | Standard | ☐ | | |
| 236 | Performance Tuning – OS-Level Tuning | Standard | ☐ | | |
| 237 | Performance Tuning – Load Testing & Validation | Standard | ☐ | | |
| 238 | Certificate Renewal Automation – Design | Standard | ☐ | | |
| 239 | Certificate Renewal Automation – Implementation | Standard | ☐ | | |
| 240 | Banking Production Incident Day | Incident Day | ☐ | | |
| 241 | Certificate Renewal Automation – End-to-End Testing | Standard | ☐ | | |
| 242 | Operational Documentation – Runbooks | Standard | ☐ | | |
| 243 | Operational Documentation – SOPs | Standard | ☐ | | |
| 244 | Operational Documentation – Knowledge Base Articles | Standard | ☐ | | |
| 245 | Final Capstone Review & Career/Interview Readiness | Standard | ☐ | | |

---

## Incident Log (for uniqueness tracking)

| Incident # | Day Used | Banking Application | Short Description | Ticket # |
|---|---|---|---|---|
| | | | | |

*(Every Incident Day (15, 30, 45, 60, 75, 90, 105, 120, 135, 150, 165, 180, 195, 210, 225, 240) introduces 6 new unique incidents — add all 6 rows here as each Incident Day is generated, so uniqueness can be checked against this log going forward. Standard Days also introduce 2 incidents each (Sprints 9 and 15) — log those here too.)*

*(Lab Day cadence: 7, 22, 37, 52, 67, 82, 97, 112, 127, 142, 157, 172, 187, 202, 217, 232 — 16 Lab Days total.)*

---

## How to Update

Each time you finish a day, just tell Claude:
> "Mark Day X as done, topic was Y, date Z"

Or update this file directly and re-upload it to the project so future sessions know where you left off.