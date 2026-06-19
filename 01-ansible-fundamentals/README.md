# Part 1 — Ansible Fundamentals

> 30 minutes. Mostly reading, with a couple of ad-hoc commands to try. Get the model first; the playbooks in Part 2 will then feel obvious.

---

## What problem is Ansible solving?

You have 1 server — you SSH in, install nginx, edit a config, start the service. Fine.

Now you have **50 servers**. Doing it by hand is slow, error-prone, and impossible to keep consistent (did server 37 get the same config?). Ansible lets you describe that setup **once**, in YAML, and apply it to all 50 — identically, repeatably, and safely.

---

## Two ideas that define Ansible

### 1. Agentless
Ansible needs **nothing installed** on the servers it manages. It connects over plain **SSH** and runs Python (already on most Linux boxes). No agent to install, update, or secure on every node. The machine you run Ansible *from* is the **control node**; the servers it configures are **managed nodes**.

### 2. Idempotent + declarative
You declare the **state you want** ("nginx installed", "service running"), not the steps. Ansible checks reality and changes only what's needed. So every task reports one of:

- **ok** — already in the desired state, nothing done
- **changed** — it had to make a change to reach the desired state
- **failed** — something went wrong

```
   playbook (desired state)         each task reports:
  ┌────────────────────────┐       ┌──────────────────┐
  │ nginx installed        │ ────► │ ok / changed     │
  │ config in place        │ ────► │ ok / changed     │
  │ service running        │ ────► │ ok / changed     │
  └────────────────────────┘       └──────────────────┘
       run it again → mostly "ok" (nothing left to change)
```

Run the same playbook 10 times → it only *acts* when something is missing or has drifted. That's idempotency, and it's why Ansible is safe to re-run.

---

## The building blocks (bottom up)

- **Inventory** — the list of hosts (and groups) you manage.
- **Module** — a unit of work (`apt`, `copy`, `service`). The *verbs*.
- **Task** — one call to a module ("install nginx with `apt`").
- **Play** — connects a list of tasks to a group of hosts.
- **Playbook** — a YAML file with one or more plays.
- **Role** — a reusable, structured bundle of tasks/vars/templates (Part 2).
- **Facts** — auto-discovered info about each host (OS, IP, RAM).
- **Handler** — a task that runs only when notified (e.g. "restart nginx if config changed").

---

## Inventory — who you're managing

```ini
# inventory.ini
[web]
web1 ansible_host=127.0.0.1 ansible_port=2221
web2 ansible_host=127.0.0.1 ansible_port=2222

[db]
db1 ansible_host=127.0.0.1 ansible_port=2223

[web:vars]
ansible_user=root
```

Hosts go in `[groups]`. You then target a group (`hosts: web`) in a play. `localhost` is always available without an inventory.

---

## Ad-hoc commands — try these now

An ad-hoc command runs a single module without a playbook. Great for quick checks.

```bash
# 1. Can Ansible reach my own machine?
ansible localhost -m ping
# localhost | SUCCESS => { "ping": "pong" }

# 2. Run a command
ansible localhost -m command -a "uptime"

# 3. See everything Ansible knows about a host (facts)
ansible localhost -m setup | head -40
```

- `-m` picks the module, `-a` passes arguments.
- Add `--become` to run as root (sudo) for things like installing packages.

> **Try it:** run `ansible localhost -m setup` and find `ansible_os_family` and `ansible_default_ipv4` in the output. You'll use facts like these in Part 2.

---

## Modules are the verbs (and why to prefer them)

Ansible ships hundreds of modules. The everyday ones:

| Module | Does |
|--------|------|
| `apt` / `yum` / `dnf` | install/remove packages |
| `copy` | copy a static file to the host |
| `template` | render a Jinja2 template to the host |
| `file` | create directories, set permissions |
| `service` / `systemd` | start/stop/enable services |
| `user` / `group` | manage accounts |
| `command` / `shell` | run a raw command (**last resort**) |

**Why avoid `command`/`shell`?** They run *every time* and always report `changed` — they can't tell "already done". Real modules check state first, so they stay idempotent. Reach for `command`/`shell` only when no module fits, and guard them (`creates:`, `when:`).

---

## Where Ansible fits (interview favourite)

> **Terraform provisions** the servers. **Ansible configures** them.

Terraform creates the VM; Ansible then installs the software and lays down the config. Different jobs, often used together. You'll see this exact comparison in interviews — be ready to say it.

---

## ✅ Check yourself (answers below)

1. What does "agentless" mean for Ansible, and why is it an advantage?
2. What are the three results a task can report, and what does each mean?
3. Why should you prefer the `apt` module over `shell: apt install nginx`?
4. One line: Ansible vs Terraform.

<details>
<summary>Answers</summary>

1. No agent software needs to be installed on managed nodes — Ansible connects over SSH and runs Python. Advantage: less to install/maintain/secure on every server.
2. `ok` (already in desired state, nothing changed), `changed` (a change was made to reach the desired state), `failed` (something errored).
3. The `apt` module is idempotent — it checks if nginx is already installed and only acts/report `changed` if needed. `shell: apt install nginx` runs every time and always reports `changed`, so you lose idempotency.
4. Terraform provisions infrastructure (creates it); Ansible configures it (sets it up). Complementary tools.

</details>

---

Next: **[Part 2 — Playbooks & Roles](../02-playbooks-and-roles/README.md)**.
