Phase 0 – Foundation (Days 1–5)
Day	Content
1	Course orientation
2	Enterprise banking architecture overview
3	Banking application environments (DEV/SIT/UAT/PRE-PROD/PROD)
4	Infrastructure overview
5	Learning methodology & lab preparation

★ SPECIAL DELIVERY RULE — PHASE 0 ONLY (Days 1–5):
Phase 0 does NOT use the 30-Sprint structure that applies to all Standard Days in Phases 1–4.
For Days 1–5 only, deliver straight content — no sprint numbering, no sprint titles, no Admin
Console/wsadmin steps, no incident ticket narratives, no runbook/SOP/interview-prep sections.
Apply ONLY Rule/Section 3 (Mandatory Real-Time Banking Examples): every Day's content must be
grounded in at least one concrete, named, realistic banking production scenario (rotated across
banking application types per Section 3 — Core Banking, Payments, LOS, Credit Card, Mobile
Banking, ATM Switch, Fraud Detection, Wire Transfer/SWIFT, etc.), with realistic ticket numbers,
named stakeholders/roles, and real business impact — never a generic placeholder company or
application. This exception applies exclusively to Days 1–5. From Day 6 onward (Phase 1 start),
the full 30-Sprint structure and all other master rules resume as normal.

Phase 1 – Linux (Days 6–110)
Day	Content
6	Linux Fundamentals – History & Distributions
7	🔧 Enterprise Lab Day
8	Linux Fundamentals – Boot Process & Kernel
9	Linux Fundamentals – Shell Basics & CLI Navigation
10	File System – Hierarchy (FHS)
11	File System – Types (ext4, xfs) & Mounting
12	File System – Inodes & Links
13	File & Directory Management – Basic Ops
14	File & Directory Management – Advanced (find, archiving)
15	🚨 Banking Production Incident Day
16	Users & Groups – Management
17	Users & Groups – Password Policies & PAM basics
18	Permissions – Basic (rwx, chmod, chown)
19	Permissions – Special (SUID/SGID/Sticky bit)
20	ACLs – Access Control Lists
21	Processes – Fundamentals (ps, top)
22	🔧 Enterprise Lab Day
23	Processes – Signals & Priority
24	Services (systemd) – Unit files & Targets
25	Services (systemd) – Managing Services
26	★NEW: Networking Fundamentals – OSI Model & TCP/IP Model Overview
27	★NEW: Networking Fundamentals – IPv4 Addressing, Private vs Public IP, Subnet Mask, Default Gateway & MAC Address
28	★NEW: Hostname Resolution – Hostname, FQDN, /etc/hosts & DNS Forward/Reverse Lookup
29	★NEW: TCP/IP Communication – TCP, UDP, Three-Way Handshake & Sockets
30	🚨 Banking Production Incident Day
31	★NEW: Ports Deep-Dive – Well-Known/Dynamic/Listening Ports & WebSphere ND Port Reference (9060/9043/9080/9443/8879/2809/9352) + netstat/ss/lsof
32	★NEW: HTTP & HTTPS Fundamentals – Requests, Responses, Status Codes, Headers, Cookies & Sessions
33	★NEW: SSL/TLS Handshake Flow – Browser → IBM HTTP Server → WebSphere
34	★NEW: DNS Deep-Dive – A Record, CNAME, PTR Record & Name Resolution Tools (nslookup, dig, host)
35	★NEW: Firewall Concepts for WebSphere ND – Rules, Allow/Deny, Source/Destination & Port Opening
36	★NEW: Load Balancer Concepts – Layer 4 vs Layer 7, Sticky Sessions, Health Checks, Failover, Round Robin (F5/HAProxy/NGINX)
37	🔧 Enterprise Lab Day
38	★NEW: Reverse Proxy Deep-Dive – IBM HTTP Server, Apache HTTP Server & plugin-cfg.xml
39	★NEW: SSL/TLS for WebSphere – Certificates, CSR, CA, Self-Signed Certs, TrustStore/KeyStore, PKCS12/JKS
40	★NEW: SSL/TLS Tooling – ikeyman & keytool + Certificate Renewal Workflow
41	★NEW: Network Troubleshooting Commands – ping, traceroute, tracepath, curl, telnet, nc, netstat, ss, ip addr, ip route
42	★NEW: Enterprise Network Architecture for WebSphere ND – Full Traffic Flow (Internet → Firewall → LB → IHS → Plugin → Cluster → App/DB/MQ)
43	★NEW: Networking Production Scenarios Lab – DMGR Unreachable, Node Federation SOAP-Port Block, DNS Misresolution, SSL Handshake Failures, IHS-to-Cluster Connectivity, Node-Specific App Failures, LB Routing to Unhealthy Nodes, JDBC/MQ Port Blocks, HTTP 503 Patterns
44	Package Management – RPM/YUM/DNF
45	🚨 Banking Production Incident Day
46	Package Management – APT & Repositories
47	Storage – Disk Partitioning
48	Storage – LVM Basics
49	Storage – LVM Advanced
50	Storage – RAID Concepts
51	NFS / Shared Storage Mounts
52	🔧 Enterprise Lab Day
53	★NEW: Mount Points, fstab & Persistent Storage Configuration
54	★NEW: CIFS/SMB Shares – Cross-Platform Storage Integration
55	★NEW: Storage Arrays Deep-Dive – SAN/NAS/LUN Concepts & Storage Snapshots
56	★NEW: Storage – SAN Multipathing (DM-Multipath, multipath -ll) & Enterprise Backup Tools Overview (TSM/Commvault/NetBackup)
57	Logging – syslog & rsyslog
58	Logging – journald
59	★NEW: System Diagnostics – dmesg, strace & lsof for Production Troubleshooting
60	🚨 Banking Production Incident Day
61	★NEW: Log Rotation Deep-Dive – logrotate Configuration & Enterprise Retention Policy
62	★NEW: Log Aggregation/SIEM Basics – Shipping Logs to Splunk/ELK (Forwarder Config, Index Routing)
63	Scheduling (cron)
64	SSH – Key-based Authentication
65	SSH – Hardening & Tunneling
66	Performance Monitoring – CPU & Memory
67	🔧 Enterprise Lab Day
68	Performance Monitoring – Disk & I/O
69	Performance Monitoring – Network
70	★NEW: Performance Tools Deep-Dive – top, htop, vmstat, free & mpstat
71	★NEW: Performance Tools Deep-Dive – iostat, sar & pidstat
72	Kernel/sysctl Tuning – TCP & ulimit
73	Kernel/sysctl Tuning – Shared Memory for WAS
74	★NEW: Kernel Parameters – Swap Management & Configuration
75	🚨 Banking Production Incident Day
76	★NEW: Kernel Parameters – Huge Pages for JVM/WebSphere Performance
77	★NEW: ulimits Deep-Dive – Soft/Hard Limits & Per-Process Resource Tuning
78	JVM/Java Troubleshooting – Process-level checks
79	JVM/Java Troubleshooting – Thread & Heap Dumps
80	Time Synchronization (NTP/chrony)
81	SSL/TLS Fundamentals
82	🔧 Enterprise Lab Day
83	Certificate Management (openssl, keystores)
84	Firewall – firewalld
85	Firewall – iptables
86	★NEW: Network Security Zones & DMZ Architecture for Banking Infra
87	Security Basics – Hardening Fundamentals
88	SELinux Basics
89	Patch Management
90	🚨 Banking Production Incident Day
91	★NEW: Patch/Vulnerability Management – Enterprise Tooling (Red Hat Satellite/Uyuni, Qualys/Nessus Scanning)
92	CIS Hardening / Compliance Basics
93	★NEW: Compliance/Regulatory Basics for Infra Teams (PCI-DSS, RBI/SEBI – Log Retention, Encryption-at-Rest, Access Reviews)
94	IBM HTTP Server (IHS) Basics
95	DB Connectivity Troubleshooting from OS Level
96	★NEW: Database Connectivity – Connection Pooling & Timeout Troubleshooting at OS/Network Level (netstat/ss/tcpdump, TCP Keepalive)
97	🔧 Enterprise Lab Day
98	Load Balancer / Reverse Proxy Concepts
99	★NEW: Load Balancer Deep-Dive – Hands-on HAProxy/NGINX/F5 Config for Banking App Tier
100	High Availability / Clustering at OS Level
101	Containers Basics (Docker)
102	Kubernetes Overview
103	★NEW: Container Security – Image Scanning & Docker/K8s Hardening
104	Privileged Access Management Concepts
105	🚨 Banking Production Incident Day
106	Troubleshooting – Systematic Methodology
107	Troubleshooting – Common Production Issues
108	Linux Automation – Basics
109	Linux Automation – Advanced Scripting Integration
110	ITIL Basics & Major Incident Bridge Communication

Phase 2 – Shell Scripting (Days 111–155)
Day	Content
111	Shell Basics – Shebang, Script Execution
112	🔧 Enterprise Lab Day
113	Shell Basics – Comments & Quoting
114	Shell Basics – Command Substitution
115	Variables – Declaration & Scope
116	Variables – Environment Variables
117	Operators – Arithmetic & Comparison
118	Operators – Logical & String
119	Input & Output – read, echo, printf
120	🚨 Banking Production Incident Day
121	Input & Output – Redirection & Pipes
122	Conditional Statements – if/else
123	Conditional Statements – case
124	Loops – for
125	Loops – while & until
126	Functions – Basics
127	🔧 Enterprise Lab Day
128	Functions – Return Values & Scope
129	Arrays – Indexed Arrays
130	Arrays – Associative Arrays
131	File Handling – Reading Files
132	File Handling – Writing & Appending
133	Text Processing – grep
134	Text Processing – awk
135	🚨 Banking Production Incident Day
136	Text Processing – sed
137	Text Processing – Combining grep/awk/sed
138	Error Handling & Debugging (trap, set -e, set -x)
139	JSON/XML Parsing (jq, xmllint)
140	Process Automation – Basics
141	Process Automation – Advanced
142	🔧 Enterprise Lab Day
143	Monitoring Scripts – System Health Checks
144	Monitoring Scripts – WebSphere Health Checks
145	Parsing WebSphere Logs (SystemOut/SystemErr)
146	Sending Metrics/Logs to Monitoring Stacks
147	Alerting/Notifications from Scripts
148	Backup Scripts – Basics
149	Backup Scripts – Advanced (rotation, retention)
150	🚨 Banking Production Incident Day
151	Deployment Scripts – Basics
152	Deployment Scripts – Advanced (WAS EAR/WAR)
153	Shell Script Version Control Basics (git)
154	Log Analysis – Techniques
155	Log Analysis – Advanced Correlation

Phase 3 – Ansible (Days 156–207)
Day	Content
156	Introduction – What is Ansible & Architecture
157	🔧 Enterprise Lab Day
158	Introduction – Agentless Model & SSH Basis
159	Installation – Control Node Setup
160	Inventory – Static Inventory Basics
161	Inventory – Groups & Variables
162	Inventory – Host Patterns
163	Dynamic Inventory
164	Ad-hoc Commands – Basics
165	🚨 Banking Production Incident Day
166	Ad-hoc Commands – Advanced Use Cases
167	Modules – Command & Shell Modules
168	Modules – File & Copy Modules
169	Modules – Package & Service Modules
170	Playbooks – Structure & Syntax
171	Playbooks – Tasks & Plays
172	🔧 Enterprise Lab Day
173	Playbooks – Conditionals in Playbooks
174	Playbooks – Loops in Playbooks
175	Playbooks – Multi-play Playbooks
176	Variables – Precedence
177	Variables – Vars Files & Prompts
178	Facts – Gathering & Custom Facts
179	Facts – Using Facts in Playbooks
180	🚨 Banking Production Incident Day
181	Templates (Jinja2) – Basics
182	Templates (Jinja2) – Advanced Filters & Loops
183	Templates (Jinja2) – WebSphere Config Templates
184	Handlers – Basics
185	Handlers – Notify & Listen
186	Roles – Structure
187	🔧 Enterprise Lab Day
188	Roles – Creating Custom Roles
189	Roles – Role Dependencies
190	Roles – Testing Roles
191	Roles – WebSphere Role Example
192	Ansible Galaxy & Collections
193	Idempotency Deep-Dive
194	Tags – Basics & Use Cases
195	🚨 Banking Production Incident Day
196	Vault – Encrypting Secrets
197	Vault – Managing Vault in Teams
198	Vault – Vault in CI/CD
199	★NEW: Enterprise Secrets Management – CyberArk/HashiCorp Vault Overview & Ansible Integration Patterns
200	Error Handling – Blocks & Rescue
201	Error Handling – Ignore Errors & Failed When
202	🔧 Enterprise Lab Day
203	IBM WebSphere-Specific Ansible Content
204	CI/CD Integration (Jenkins)
205	AWX / Ansible Tower Overview
206	Enterprise Automation – Patterns & Standards
207	Enterprise Automation – Scaling Playbooks

Phase 4 – Enterprise Capstone (Days 208–245)
Day	Content
208	Multi-server Infrastructure Setup – Planning & Design
209	Multi-server Infrastructure Setup – Implementation
210	🚨 Banking Production Incident Day
211	Multi-server Infrastructure Setup – Validation
212	Health Check Automation – Design
213	Health Check Automation – Implementation
214	Health Check Automation – Integration with Monitoring
215	Log Collection and Analysis – Centralized Logging Setup
216	Log Collection and Analysis – Automated Analysis Scripts
217	🔧 Enterprise Lab Day
218	Backup and Restore Automation – Design
219	Backup and Restore Automation – Implementation
220	Backup and Restore Automation – Restore Testing
221	Server Provisioning – Baseline OS Setup
222	Server Provisioning – Automated Provisioning with Ansible
223	Configuration Management – Standardizing Configs
224	Configuration Management – Drift Detection & Remediation
225	🚨 Banking Production Incident Day
226	Deployment Automation – Application Deployment Pipeline
227	Deployment Automation – Rollback Strategy
228	Deployment Automation – Blue-Green/Canary Concepts
229	CI/CD-Integrated Deployment Pipeline – Design
230	CI/CD-Integrated Deployment Pipeline – Implementation
231	Disaster Recovery Simulation – Planning
232	🔧 Enterprise Lab Day
233	Disaster Recovery Simulation – Execution
234	Disaster Recovery Simulation – Post-DR Review
235	Performance Tuning – JVM & WebSphere Tuning
236	Performance Tuning – OS-Level Tuning
237	Performance Tuning – Load Testing & Validation
238	Certificate Renewal Automation – Design
239	Certificate Renewal Automation – Implementation
240	🚨 Banking Production Incident Day
241	Certificate Renewal Automation – End-to-End Testing
242	Operational Documentation – Runbooks
243	Operational Documentation – SOPs
244	Operational Documentation – Knowledge Base Articles
245	Final Capstone Review & Career/Interview Readiness