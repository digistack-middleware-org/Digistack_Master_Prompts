# PHASE 3: PERFORMANCE & TROUBLESHOOTING (Days 23–35) ⭐ HEART MODULE

## Goal
Diagnose any slow/hung/crashed WAS server like a 10-year veteran.

## Days
D23: Memory — free -h (available ≠ free), top fields, swap; 4 JVMs×4GB on 16GB
D24: CPU ⭐ — top, load average, vmstat, mpstat, iowait vs user vs system
D25: java Process Deep Dive — WAS java cmdline flags, ps -T, top -H, thread-ID
     correlation with javacore
D26: OOM & Swap ⭐ — OOM killer in /var/log/messages + dmesg, swappiness=10
D27: Disk I/O — iostat -x, await, %util; transaction logs on slow LUN story
D28: Logrotate — rotation policies, zless/zgrep; 5GB/day SystemOut app
D29: System Logs — /var/log/messages, /var/log/secure (brute force), dmesg,
     journalctl
D30: Baseline & Monitoring ⭐ — sar history, threshold script (disk>85%,
     CPU>90%) → email/SNOW before users notice
D31: Troubleshooting Methodology ⭐ — identify→isolate(app/OS/net/DB/storage)
     →measure→fix→verify→document; the 10-yr framework
D32: WAS-OS Clinic #1 — too many open files, zombies, time drift breaking
     SSL/SSO, semaphores
D33: WAS-OS Clinic #2 — /tmp full → JSP compile 500s, core dumps 4–8GB,
     sticky bits
D34: Security Hardening — unused services, SSH hardening, CIS scans,
     remediation without breaking app
D35: ✅ Capstone — "payment app down" full simulation from memory

## Banking Scenario Seeds
- iowait 40% → storage problem, correct team paged (wrong call = 3 hrs wasted)
- top -H thread 0x4f2a at 100% CPU → javacore correlation
- OOM killer silently killed java at 3 AM — found in /var/log/messages
- slow transaction-log storage = sluggish month-end payments → fast LUN fix
- time drift 3 min = intermittent SSL/SSO failures that "come and go"
- /var/log/secure: 10,000 failed SSH/night = security escalation
- 5 AM proactive alerting: fix before 9 AM, business sees nothing

## Interview Seeds
- Explain free -h; is high "used" bad?
- Load 20 on 8 cores — meaning + next steps?
- WAS java 100% CPU — step-by-step
- Java died with no SystemOut — find the killer
- Slow app, CPU/mem fine — next check?
- "App is slow" no other info — full diagnostic flow
- Intermittent SSL errors — overlooked cause?
- Random 500s, no app change — first check?
- Disk monitoring script with alerting

## Phase-Done Checklist
(a) Revision notes  (b) 7 deliverables (20 interview Q&A, 5 recent issues,
20 scenario Qs, 20 troubleshooting Qs, 5 war stories, 5 behavioral,
5 architecture)

