# PHASE 5: Linux Troubleshooting (Day 106–148)
Goal: Own the OS layer — CPU, memory, disk, network, scripts. This is where 10-yr admins separate from 3-yr admins.
Milestone Day 148: 100% CPU → guilty thread in <5 min; df/du mysteries; CLOSE_WAIT forensics; real ops scripts.

## Module 14: Linux Essentials for Middleware (Day 106–112)
| Day | Topic | Lab |
|---|---|---|
| 106 | File permissions, users/groups; why Tomcat must NOT run as root | Create tomcat user; chown/chmod tree; verify |
| 107 | Non-root binding to 80/443: authbind, iptables redirect | Configure iptables 80→8080; verify |
| 108 | systemd deep dive: unit files, Type=forking, Restart | Write tomcat.service from scratch; test |
| 109 | Auto-start on reboot + startup ORDER (Tomcat after DB) | Fake "DB" service; After=/Wants= dependencies; test ordering |
| 110 | EnvironmentFile; ulimits theory: nofile, nproc | LimitNOFILE=65536; verify via /proc/<pid>/limits |
| 111 | ⭐ "Too many open files" — classic Tomcat outage | Set ulimit low on purpose; load Tomcat; watch fail; fix |
| 112 | SELinux basics: why Tomcat won't start after hardening | setsebool/chcon demo; answer "SELinux blocked Tomcat — now what?" |

## Module 15: CPU & Memory Troubleshooting (Day 113–120)
| Day | Topic | Lab |
|---|---|---|
| 113 | top mastery: load average, us/sy/wa/id, RES vs VIRT | Run Tomcat under JMeter; read top live |
| 114 | Load average simply: CPU-bound vs IO-bound vs run-queue | Generate CPU load + IO load; compare |
| 115 | vmstat, free -m, sar, pidstat | Collect 10 min of sar under load; interpret |
| 116 | Java thread counting/mapping: ps -eLf, top -H -p | Correlate TID with thread names during load |
| 117 | ⭐ The 100% CPU investigation: top -H → nid → thread dump | CPU-spinning servlet; find guilty thread — until automatic |
| 118 | Swap: why swapping kills JVM GC; detection & prevention | Big heap on small VM; force swap; observe GC pauses |
| 119 | OOM Killer: oom_score, dmesg evidence | Force OOM kill; find evidence; document RCA |
| 120 | 🏦 Banking: "Tomcat vanished overnight, no logs" | Rehearse OOM-Killer war story aloud, STAR format |

## Module 16: Disk & IO Troubleshooting (Day 121–127)
| Day | Topic | Lab |
|---|---|---|
| 121 | df -h, du -sh, iostat -x | Fill disk with dd; stress IO; read iostat columns |
| 122 | lsof mastery | List Tomcat's open files, sockets, jars |
| 123 | ⭐ Deleted-but-open files: disk full but du shows free | rm catalina.out while running → prove with lsof \| grep deleted → fix |
| 124 | Truncation vs deletion: : > file on live logs | Safe truncation without killing Tomcat |
| 125 | Log cleanup: logrotate, cron, 7-year audit retention | Real logrotate config (compress, copytruncate); test -f |
| 126 | NFS/storage issues; slow IO vs slow app | Correlate access log %D vs iostat await |
| 127 | 🏦 Banking: disk 100% at midnight, audit logs can't be deleted → tiered retention | Write hot/warm/cold retention doc + cleanup script with exclusions |

## Module 17: Network Troubleshooting (Day 128–135)
| Day | Topic | Lab |
|---|---|---|
| 128 | Connectivity toolkit: ping, telnet/nc, curl, dig, traceroute | Test each hop: Apache→Tomcat, Tomcat→DB |
| 129 | netstat -anp / ss — socket states, per-process | Identify LISTEN, ESTABLISHED, TIME_WAIT on Tomcat |
| 130 | ⭐ TCP states: TIME_WAIT (normal), CLOSE_WAIT (bug!), SYN_RECV | Force CLOSE_WAIT pile-up; watch in ss; explain cause |
| 131 | CLOSE_WAIT forensics: whose fault? | Correlate with stuck threads; write RCA answer |
| 132 | Firewall reality: every layer needs openings; ephemeral ports | Simulate blocked port (iptables DROP); reproduce "intermittent" |
| 133 | tcpdump basics: filters, SYN/SYN-ACK/ACK, pcap | tcpdump Tomcat→DB; prove "SYN sent, no response" = firewall |
| 134 | TLS from server's view: openssl s_client, SNI, failures | Debug 4 broken-SSL cases |
| 135 | 🏦 Banking: DC migration → intermittent transfer failures → ephemeral port firewall miss | Write war story (STAR) + firewall-change checklist |

## Module 18: Shell Scripting for Middleware Admins (Day 136–143)
| Day | Topic | Lab |
|---|---|---|
| 136 | Bash: variables, conditionals, loops, exit codes, set -e | Port-check script with proper exit codes |
| 137 | Health-check v1: process + port + HTTP 200 + response time | Test all failure modes |
| 138 | 🏦 The alert script: down 3x → thread dump → restart → alert | Full script: logging, lock file, history |
| 139 | ⚠️ Why auto-restart is DANGEROUS in banking (audit, stuck payments) | Write restart policy doc with safeguards |
| 140 | Log-cleanup + disk-alert scripts (>85% warn, >90% page) | Build both with audit-log exclusion |
| 141 | cron: scheduling, PATH gotchas; fixing app-team scripts | Schedule scripts; break cron (PATH) and fix |
| 142 | Ansible ladder: ad-hoc → playbook → role | Minimal playbook: JDK + Tomcat + WAR on localhost |
| 143 | Python basics: parse access log for 5xx/slow %D; API health checker | Write both; run against your Tomcat |

## ✅ Phase 5 Checkpoints
- Day 112: 🏦 Patching Sunday → reboot → Tomcat dead Monday 8AM. Prevention runbook. "Tomcat must start after DB — how?"
- Day 120: Timed — "Tomcat at 100% CPU: find WHICH thread and WHAT it's doing" (no notes)
- Day 127: "df shows 95% full but du totals only 40% — explain."
- Day 135: "Thousands of CLOSE_WAIT to Tomcat — whose fault is it?"
- Day 143: "Write a Tomcat health-check/restart script — and explain the risks."
