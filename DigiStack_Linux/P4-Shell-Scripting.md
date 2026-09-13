# PHASE 4: SHELL SCRIPTING FOR WAS ADMINS (Days 36–45)

## Goal
Write production-safe scripts that run your daily work automatically.

## Days
D36: Bash Basics — shebang, variables, running scripts, server_info.sh
D37: Conditionals — if/elif, file tests (-f,-d,-z), exit codes; health→restart
     +alert
D38: Loops — for/while over server lists; disk check across 60 nodes
D39: Functions & Structure — log(), alert(), check_disk(); args, set -e/-x
D40: Text Power Tools ⭐ — awk (fields/sums), sed (substitute), cut, sort,
     uniq, wc; error-count-per-hour one-liner
D41: Real Scripts: Log & Disk — cleanup javacores>7d, /opt>85% alert,
     archive+compress; deploy via cron+Ansible
D42: Real Scripts: Health Check ⭐ — WAS up, ports listening, DS reachable,
     disk, cert expiry → 5 AM "ALL GREEN" email
D43: Real Scripts: Deploy Helper — stop→backup→deploy→start→verify with
     auto-rollback trigger
D44: Script Safety ⭐ — dry-run flags, prod confirmation prompts, quote
     variables, shellcheck; rm -rf war story
D45: ✅ Revision — write health-check + cleanup + disk report from memory

## Banking Scenario Seeds
- for host in $(cat prod_servers.txt) — 60-node disk check in 1 min
- awk error counts → peak failure window for RCA
- sed bulk host change across configs after DB migration
- 5 AM health script = interview showpiece, describe end-to-end
- deploy helper: 90-min runbook → 15 min, colored output, auto-rollback
- WAR STORY: rm -rf $DIR/ with empty DIR nearly erased a server —
  quote variables, validate, dry-run

## Interview Seeds
- File tests: log exists AND non-empty
- Loop over servers, handle unreachable hosts
- Count ERRORs per hour — one-liner
- Design daily health-check script for payment cluster
- Make scripts safe for Prod

## Phase-Done Checklist
(a) Revision notes  (b) 7 deliverables (20 interview Q&A, 5 recent issues,
20 scenario Qs, 20 troubleshooting Qs, 5 war stories, 5 behavioral,
5 architecture)

