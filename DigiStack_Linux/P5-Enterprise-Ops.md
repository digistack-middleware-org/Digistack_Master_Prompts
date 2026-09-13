# PHASE 5: ENTERPRISE OPERATIONS (Days 46–52)

## Goal
Run storage, backups, boot recovery, and cross-team work like a senior.

## Days
D46: LVM & Storage — PV/VG/LV, lvextend -r, resize2fs/xfs_growfs, /etc/fstab;
     sizing math for storage requests
D47: NFS & Shared Storage — mounts, fstab, autofs; stale NFS → WAS hang,
     df -h hangs (classic diagnosis)
D48: Backup & Restore — what to back up (profiles/configs/certs), 3-2-1,
     90-day retention for audit, quarterly restore drills
D49: Boot Process & GRUB — BIOS→GRUB→kernel→systemd, rescue/emergency targets,
     bad fstab recovery
D50: Time Sync, DNS & Hostname — chrony, A/PTR records, hostnamectl;
     NTP mandatory for SSL/SSO/audit
D51: Working with Other Teams — evidence bundle (df, top, logs, telnet) before
     any ticket; ticket writing; who owns what
D52: ✅ Revision — NFS hang + LVM extend + boot recovery simulations

## Banking Scenario Seeds
- /opt full → storage LUN extended → lvextend -r in 5 min, with sizing math
- stale NFS mount froze WAS log writes — commands hanging = NFS suspect
- 3-2-1 backups, pre-change tars kept 90 days for audit
- fstab typo after kernel patch → emergency boot → console recovery
- NTP mandatory: audit + SSL + SSO + batch ordering
- evidence-first tickets halved resolution time

## Interview Seeds
- Commands hang on server — NFS? Confirm safely?
- Server won't boot after fstab change — recovery
- Why strict NTP in banks?
- What do you send the storage team when app is slow?

## Phase-Done Checklist
(a) Revision notes  (b) 7 deliverables (20 interview Q&A, 5 recent issues,
20 scenario Qs, 20 troubleshooting Qs, 5 war stories, 5 behavioral,
5 architecture)

