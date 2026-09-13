# PHASE 1: LINUX FOUNDATIONS (Days 1–10)

## Goal
Navigate Linux confidently; install/setup done; understand how WAS lives on Linux.

## Days
D1: What is Linux — kernel vs shell, RHEL vs CentOS vs SUSE, why banks pick RHEL
    (IBM certification, support, stability)
D2: Filesystem Hierarchy — /, /opt, /var, /etc, /tmp, /proc; where WAS lives
    (/opt/IBM/WebSphere...)
D3: Basic Commands — pwd, ls, cd, mkdir, rm, cp, mv, touch, find, which
D4: Viewing & Search ⭐ — cat, less, head, tail -f, grep (-i,-v,-c,-A,-B,-r),
    pipes, zgrep
D5: Permissions ⭐ — rwx, 755/644, chmod, chown, umask; never 777 in a bank
D6: Users & Sudo — /etc/passwd, /etc/shadow, sudoers, sudo vs su, SIEM logging
D7: Processes ⭐ — ps -ef, top, kill vs kill -9, nohup, finding WAS java PID
D8: Services & systemd — systemctl start/stop/enable, journalctl, WAS auto-start
D9: Packages & Patching — yum/dnf, rpm -qa, subscription manager, WAS admin's
    role in OS patching
D10: ✅ Revision — 10 rapid-fire + 15 commands from memory

## Banking Scenario Seeds (1 per day)
- RHEL 8 chosen because IBM certifies WAS 9.0.5 on it
- /opt and /var on separate LUNs so log flood can't kill app
- find huge logs before nightly batch fills disk
- tail -f + grep during 2 AM payment failure
- junior chmod -R 777 = failed audit + broken keyfiles
- root banned; limited audited sudo for wasadmin
- never kill -9 DMGR blindly
- missing systemctl enable = app down after patch reboot
- patch cycle: stop WAS → patch → reboot → verify

## Interview Seeds
- Which Linux is certified for WAS and why it matters?
- WAS logs vs OS on separate partitions — why?
- find files >100MB modified in last 24h
- ERROR in last 500 lines + gzipped logs
- Why 777 dangerous? Proper WAS profile perms?
- sudo vs su; where are sudo logs?
- kill vs kill -9; find WAS PID + uptime
- Auto-start WAS after reboot
- Your role during OS patching

## Phase-Done Checklist
(a) Revision notes  (b) 7 deliverables (20 interview Q&A, 5 recent issues,
20 scenario Qs, 20 troubleshooting Qs, 5 war stories, 5 behavioral,
5 architecture)
