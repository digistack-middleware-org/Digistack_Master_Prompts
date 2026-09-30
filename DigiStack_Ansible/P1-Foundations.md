# 🚀 Phase 1: ANSIBLE FOUNDATIONS (Days 1–10)
---

## 📅 Day-by-Day Plan

| Day | Topic | Key Outcomes |
|-----|-------|--------------|
| D1 | What is Ansible | Agentless, push model, idempotency, control vs managed nodes |
| D2 | Installation | RHEL install, ansible.cfg search order |
| D3 | Inventory | INI & YAML formats, groups, ranges |
| D4 | SSH & Auth | Keys, become, remote_user vs become_user |
| D5 | Ad-Hoc Commands | ping, shell, command, copy, service |
| D6 | YAML | Indentation, lists, dicts, common mistakes |
| D7–8 | First Playbook | copy/template/yum/service, --check, --limit |
| D9 | Variables | Precedence, host_vars, group_vars, extra-vars |
| D10 | ✅ Revision | 10 rapid-fire Qs + 5-task playbook from memory |

---

## 📚 D1: What is Ansible?

### Core Concepts
- **Configuration Management Tool** — automates provisioning, configuration, deployment
- **Agentless** — no daemon on managed nodes; uses SSH (Linux) / WinRM (Windows)
- **Push Model** — control node pushes config (vs Puppet/Chef pull model)
- **Idempotency** — same playbook run multiple times = same result, no duplicate changes
- **Control Node** — machine where Ansible is installed (must be Linux)
- **Managed Nodes** — target servers listed in inventory

### Idempotency Examples
| Idempotent ✅ | Non-Idempotent ⚠️ |
|---------------|-------------------|
| `yum: name=httpd state=present` | `shell: yum install -y httpd` (runs every time) |
| `copy:` (skips if unchanged) | `shell: echo x >> /etc/hosts` (appends every run) |

---

## 📚 D2: Installation

### RHEL Install
```bash
sudo dnf install ansible-core        # RHEL 8/9
# or
sudo dnf install epel-release
sudo dnf install ansible

ansible --version
```

### ansible.cfg Search Order (First Found Wins 🏆)
1. `$ANSIBLE_CONFIG` (environment variable)
2. `./ansible.cfg` (current working directory)
3. `~/.ansible.cfg` (home directory)
4. `/etc/ansible/ansible.cfg` (system default)

```ini
# Sample ansible.cfg
[defaults]
inventory = ./inventory
host_key_checking = False
remote_user = svc-ansible
log_path = ./ansible.log
```

---

## 📚 D3: Inventory

### INI Format
```ini
[web]
web01.bank.com
web02.bank.com

[db]
db01.bank.com

[app:children]
web
db

[web:vars]
http_port=8080
```

### YAML Format
```yaml
all:
  children:
    web:
      hosts:
        web01.bank.com:
        web02.bank.com:
    db:
      hosts:
        db01.bank.com:
    app:
      children:
        web:
        db:
```

### Host Ranges
```ini
was[01:12].bank.com     # was01 through was12
app[a:e].bank.com       # appa through appe
```

### Host Variables (INI)
```ini
web01.bank.com ansible_host=10.0.1.5 ansible_user=admin
```

---

## 📚 D4: SSH & Auth

```bash
ssh-keygen -t rsa -b 4096
ssh-copy-id svc-ansible@web01.bank.com
```

### remote_user vs become_user
| Directive | Meaning |
|-----------|---------|
| `remote_user` | SSH login user (connection identity) |
| `become` | Enable privilege escalation (sudo) |
| `become_user` | User to become after escalation (default: root) |

```yaml
- hosts: was
  remote_user: svc-ansible
  become: true
  become_user: wasadmin
```

### Host Key Checking
```ini
[defaults]
host_key_checking = False   # lab only; keep True in prod
```

---

## 📚 D5: Ad-Hoc Commands

```bash
ansible all -i inventory -m ping
ansible web -m shell -a "df -h"
ansible web -m command -a "uptime"
ansible web -m copy -a "src=/etc/motd dest=/etc/motd"
ansible web -m service -a "name=httpd state=started enabled=yes"
ansible db -m yum -a "name=nc state=present"
```

> ⚠️ `command` vs `shell`: `shell` supports pipes, redirects, env vars; `command` does not — and is safer.

### Banking Example — Disk Check on 60 Nodes (1 command)
```bash
ansible was[01:60].bank.com -m shell -a "df -h /tmp /var" --limit "was[01:60]"
```

---

## 📚 D6: YAML Essentials

```yaml
# List
packages:
  - httpd
  - git
  - vim

# Dict (mapping)
user:
  name: wasadmin
  uid: 2001

# List of dicts
tasks:
  - name: Install httpd
    yum:
      name: httpd
      state: present
```

### Common Mistakes ❌
- Tabs instead of spaces (YAML forbids tabs)
- Inconsistent indentation (always 2 spaces)
- Missing space after colon (`key:value` ❌ → `key: value` ✅)
- Unquoted values starting with special chars (`*`, `!`, `{`)

### Validate
```bash
ansible-playbook playbook.yml --syntax-check
python3 -c "import yaml; yaml.safe_load(open('file.yml'))"
```

---

## 📚 D7–8: First Playbook

```yaml
---
- name: Deploy HTTPD on web servers
  hosts: web
  become: true
  tasks:
    - name: Install httpd
      yum:
        name: httpd
        state: present

    - name: Deploy custom index
      template:
        src: index.html.j2
        dest: /var/www/html/index.html

    - name: Copy audit banner
      copy:
        content: "Authorized access only\n"
        dest: /etc/motd

    - name: Start and enable httpd
      service:
        name: httpd
        state: started
        enabled: true
```

### Useful Flags
```bash
ansible-playbook site.yml --check             # dry-run
ansible-playbook site.yml --limit web01.bank.com
ansible-playbook site.yml --syntax-check
ansible-playbook site.yml -C -D               # check + show diff
```

---

## 📚 D9: Variables

### Directory Layout
```
inventory/
├── group_vars/
│   ├── prod.yml      # prod heap/db settings
│   └── uat.yml
├── host_vars/
│   └── web01.yml
```

### Precedence (Low → High) — Simplified Top 10
1. Role defaults — **LOWEST**
2. Inventory vars (host_vars / group_vars)
3. Host facts / cached facts
4. Play vars / play vars_files
5. Role params
6. Block vars
7. Task vars
8. set_facts / registered vars
9. include_params
10. Extra vars (`-e`) — **HIGHEST**

```bash
ansible-playbook site.yml -e "app_port=8443"   # overrides everything
```

---

## 📚 D10: ✅ Revision Day

### Rapid-Fire Qs (Answer Without Notes)
1. Is Ansible agentless? What does it use to connect?
2. Which ansible.cfg wins if three exist?
3. Push or pull — which model does Ansible use?
4. `command` vs `shell` — one difference?
5. remote_user vs become_user?
6. Which flag gives a dry-run?
7. What does idempotent mean? Give one example.
8. Highest precedence variable source?
9. How do you target 5 of 60 hosts?
10. What breaks YAML most often?

### Memory Playbook (Write From Memory — 5 Tasks)
> Install package → template config → ensure service running → copy audit banner → verify with shell command.

---

## 🏦 Banking Scenario Seeds (Pick 1 Per Day)

| # | Scenario | Lesson |
|---|----------|--------|
| 1 | Config drift between UAT & Prod caused failed launch; audit finding | group_vars per environment |
| 2 | Offline RPM install on restricted bastion server | copy .rpm + `yum: name=/tmp/x.rpm` |
| 3 | Prod inventory in encrypted Git, lead-only write access | ansible-vault + Git permissions |
| 4 | wasadmin via limited audited sudoers; root banned | become_user + sudoers rules |
| 5 | Check disk on 60 nodes before nightly batch — 1 command | ad-hoc shell module |
| 6 | Bad YAML = failed 2 AM deployment | --syntax-check discipline |
| 7 | First week task: install/start IHS on 10 nodes | loops + service module |
| 8 | group_vars/prod.yml vs uat.yml for heap/db settings | env-based variable separation |

---

## 🎤 Interview Q&A

**Q1: Ansible vs Chef vs Puppet vs Terraform?**
A: Ansible — agentless, push, YAML, procedural. Chef/Puppet — agent-based, pull, DSL. Terraform — IaC for provisioning infra, not config management.

**Q2: Which ansible.cfg wins?**
A: First found in order: `$ANSIBLE_CONFIG` env var → cwd → `~/.ansible.cfg` → `/etc/ansible/ansible.cfg`.

**Q3: command vs shell?**
A: `shell` runs via `/bin/sh` — supports pipes, redirection, env vars. `command` does not — safer, preferred default.

**Q4: remote_user vs become_user?**
A: `remote_user` = SSH connection user. `become_user` = target user after sudo escalation (root, wasadmin, etc.).

**Q5: Idempotent vs non-idempotent example?**
A: Idempotent: `yum state=present` (installs once, then ok). Non-idempotent: `shell "echo x >> file"` (appends every run).

**Q6: Variable precedence top 3?**
A: Extra vars (`-e`) > set_fact/registered > play vars > inventory/group_vars > role defaults (lowest).

**Q7: How does Ansible connect to Linux targets?**
A: SSH (OpenSSH or paramiko) — no agent required.

**Q8: Control node requirements?**
A: Linux with Python and Ansible installed. Managed nodes only need Python for most modules.

**Q9: What is --check mode?**
A: Dry-run — shows what would change without applying changes.

**Q10: What is a group-of-groups?**
A: `[app:children]` in inventory — a group containing other groups.

---

## ✅ Phase-Done Checklist

- [ ] (a) Compact revision notes written
- [ ] (b) 7 deliverables:
  - [ ] 20 interview Q&A
  - [ ] 5 recent issues
  - [ ] 20 scenario Qs
  - [ ] 20 troubleshooting Qs
  - [ ] 5 war stories
  - [ ] 5 behavioral answers
  - [ ] 5 architecture answers
- [ ] Write a 5-task playbook from memory ✍️
- [ ] Ad-hoc disk check across 60 nodes done hands-on
- [ ] ansible.cfg search order memorized
- [ ] Variable precedence list memorized

---

**➡️ Next: Phase 2 — Core Modules, Handlers, Loops, Conditionals, Templates**
