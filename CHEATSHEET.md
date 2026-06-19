# 🤖 Ansible Cheat Sheet — one page

> Quick reminder of the syntax and the most-used modules. Pair with the exercises.

---

## 🧠 The vocabulary

| Term | What it means |
|------|---------------|
| **Control node** | The machine you run Ansible *from* (must be Linux/macOS/WSL) |
| **Managed node** | A server Ansible configures (over SSH) |
| **Inventory** | The list of managed nodes (a file: `inventory.ini` or `.yml`) |
| **Module** | A unit of work (`apt`, `copy`, `service`…) — the verbs |
| **Task** | One call to a module |
| **Play** | Maps a set of tasks to a set of hosts |
| **Playbook** | A YAML file containing one or more plays |
| **Role** | A reusable, structured bundle of tasks/vars/templates/handlers |
| **Handler** | A task triggered only when `notify`'d (e.g. restart service on config change) |
| **Facts** | Auto-discovered info about a host (OS, IP, memory…) |
| **Idempotent** | Running it again makes no further change if already in the desired state |

---

## 📇 Inventory

```ini
# inventory.ini
[web]
web1 ansible_host=127.0.0.1 ansible_port=2221
web2 ansible_host=127.0.0.1 ansible_port=2222

[web:vars]
ansible_user=root
ansible_ssh_common_args='-o StrictHostKeyChecking=no'

[all:vars]
ansible_python_interpreter=/usr/bin/python3
```

---

## ⚡ Ad-hoc commands (one-off, no playbook)

```bash
ansible all -i inventory.ini -m ping              # connectivity check
ansible web -i inventory.ini -m command -a "uptime"
ansible all -i inventory.ini -m apt -a "name=nginx state=present" --become
ansible all -i inventory.ini -m setup              # dump all facts
```

- `-m` = module, `-a` = arguments, `--become` = sudo (privilege escalation).

---

## 📕 Playbook structure

```yaml
- name: Configure web servers       # a PLAY
  hosts: web
  become: true                      # use sudo
  vars:
    app_port: 8080
  tasks:
    - name: Install nginx           # a TASK (calls the apt module)
      ansible.builtin.apt:
        name: nginx
        state: present
        update_cache: true

    - name: Deploy config from a template
      ansible.builtin.template:
        src: nginx.conf.j2
        dest: /etc/nginx/nginx.conf
      notify: Restart nginx         # trigger the handler IF this changes

    - name: Ensure nginx is running and enabled
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: true

  handlers:
    - name: Restart nginx           # runs only when notified
      ansible.builtin.service:
        name: nginx
        state: restarted
```

Run it:
```bash
ansible-playbook -i inventory.ini site.yml
ansible-playbook -i inventory.ini site.yml --check     # dry run (no changes)
ansible-playbook -i inventory.ini site.yml --diff      # show file changes
ansible-playbook -i inventory.ini site.yml -v          # verbose (-vvv for more)
```

---

## 🧰 Most-used modules

| Module | Does |
|--------|------|
| `ansible.builtin.apt` / `yum` / `dnf` | install/remove packages |
| `ansible.builtin.copy` | copy a file to the host |
| `ansible.builtin.template` | render a Jinja2 template (`.j2`) to the host |
| `ansible.builtin.file` | create dirs, set permissions/ownership |
| `ansible.builtin.service` / `systemd` | start/stop/enable services |
| `ansible.builtin.lineinfile` | ensure a line is present in a file |
| `ansible.builtin.user` / `group` | manage users/groups |
| `ansible.builtin.git` | clone/checkout a repo |
| `ansible.builtin.command` / `shell` | run a command (last resort — not idempotent) |

> Prefer a real module over `command`/`shell`: modules are **idempotent** and report `changed` correctly.

---

## 🔤 Variables, facts, conditionals, loops

```yaml
vars:
  packages: [nginx, curl, git]

tasks:
  - name: Install a list of packages (loop)
    ansible.builtin.apt:
      name: "{{ item }}"
      state: present
    loop: "{{ packages }}"

  - name: Only on Debian-family
    ansible.builtin.apt:
      name: nginx
      state: present
    when: ansible_os_family == "Debian"      # uses a fact

  - name: Show a fact
    ansible.builtin.debug:
      msg: "Host {{ ansible_hostname }} has IP {{ ansible_default_ipv4.address }}"
```

Variable precedence (low → high, simplified): role defaults < inventory vars < playbook vars < `-e` extra vars (highest).

---

## 📦 Role layout (`ansible-galaxy init myrole`)

```
roles/
└── webserver/
    ├── tasks/main.yml        # the tasks
    ├── handlers/main.yml     # the handlers
    ├── templates/            # .j2 templates
    ├── files/                # static files to copy
    ├── vars/main.yml         # high-priority vars
    ├── defaults/main.yml     # low-priority (overridable) vars
    └── meta/main.yml         # role metadata/dependencies
```

Use it:
```yaml
- hosts: web
  become: true
  roles:
    - webserver
```

---

## 🔐 Secrets — Ansible Vault

```bash
ansible-vault create secrets.yml          # create an encrypted file
ansible-vault edit secrets.yml            # edit it
ansible-playbook site.yml --ask-vault-pass   # supply the password at runtime
```
Never commit plaintext secrets; encrypt them with Vault.

---

## 🛟 Debugging

- `--check` (dry run) + `--diff` to preview.
- `-vvv` for verbose connection/module detail.
- `ansible all -m ping` to test connectivity first.
- A task stuck at `changed` every run usually means you used `command`/`shell` instead of a proper module.
- Read the error — Ansible names the host, task, and module that failed.
