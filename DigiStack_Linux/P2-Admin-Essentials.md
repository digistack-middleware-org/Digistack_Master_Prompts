# PHASE 2: ADMIN ESSENTIALS (Days 11–22)

## Goal
Daily-driver skills: disk, archives, vi, limits, network, SSH, cron.

## Days
D11: Disk Space ⭐⭐ — df -h, du -sh, df -i, LVM intro; #1 cause of outages
D12: Inodes & File Floods — df -i, "No space left" with free space, file floods
D13: Archive & Transfer — tar -czvf/-xzvf, gzip, scp, sftp, rsync; pre-change
     backup pattern
D14: vi/vim — modes, i/esc/:wq/:q!, dd, yy, /search, :%s/old/new/g — 2 AM survival
D15: Environment Variables — export, PATH, /etc/profile vs ~/.bashrc, JAVA_HOME
     for WAS
D16: ulimits ⭐ — ulimit -a, nofile/nproc, /etc/security/limits.conf, soft vs
     hard; "Too many open files"
D17: Networking Basics — IP, /etc/hosts, /etc/resolv.conf; WAS ports
     9043/9443/9080/2809, Oracle 1521
D18: Network Troubleshooting ⭐ — ping, telnet, nc, curl, ss -tulpen,
     traceroute; prove network vs DB issue
D19: Firewall & SELinux — firewall-cmd, iptables concept, SELinux modes;
     port matrix discipline
D20: SSH Deep Dive — ssh-keygen, authorized_keys, sshd_config, tunnels
     (ssh -L for admin console)
D21: cron — syntax, crontab -e, /etc/cron.d; cleanup + health-check jobs
D22: ✅ Revision — simulate "disk full on prod node" end-to-end

## Banking Scenario Seeds
- WAR STORY: /opt 100% full — OOM heap dumps ate 40GB, payments down 9 AM
- 500k tiny javacores → inode exhaustion, juniors panic
- tar backup before every fixpack = rollback insurance
- vi over slow VPN at 2 AM
- wrong java in PATH → wsadmin cryptic errors
- nofile 1024 default → "Too many open files" under peak load
- stale /etc/hosts entry after VM migration = federation failure
- telnet dbhost 1521 → 2-min firewall query vs 2-hr team war
- firewalld missing 9443 = 90% of "new node unreachable"
- bad cron deleted profile logs = audit finding

## Interview Seeds
- /opt 100% full on Prod — walk through actions
- Disk 50% free but writes fail — check what?
- Backup+restore command before WAS change
- Too many open files — root cause + permanent fix
- Ports needed: WAS↔DMGR, IHS↔WAS
- Commands proving network vs DB problem
- Secure admin console access when not exposed
- Cron: health check 5 AM Mon–Fri with log

## Phase-Done Checklist
(a) Revision notes  (b) 7 deliverables (20 interview Q&A, 5 recent issues,
20 scenario Qs, 20 troubleshooting Qs, 5 war stories, 5 behavioral,
5 architecture)

