# 🎤 Ansible — Interview Questions

> Ansible is the most-asked configuration-management tool in DevOps interviews. Plain-English answers you can say out loud. Do the exercises first, then use this to **explain** what you did.

**How to use this file:** cover the answers, read a question, answer out loud, then check. For most answers, the magic words are **idempotent** and **agentless**.

---

## 🟢 Fundamentals

**1. What is Ansible and what problem does it solve?**
Ansible is an open-source **configuration management and automation** tool. You describe the desired state of your servers in YAML and Ansible makes them match — installing packages, deploying config, managing services — consistently across many machines, replacing manual SSH-and-fiddle work.

**2. What does "agentless" mean and why does it matter?**
Ansible doesn't require any software/agent installed on the managed servers. It connects over **SSH** and runs Python. That means less to install, maintain, and secure on every node — a big reason teams pick it over agent-based tools.

**3. What is idempotency and why is it central to Ansible?**
An operation is idempotent if running it again doesn't change anything once the desired state is reached. Ansible modules check current state and only act if needed, so you can re-run a playbook safely — it reports `ok` (already correct), `changed` (fixed something), or `failed`. This makes automation predictable and safe to repeat.

**4. What's the difference between Ansible and Terraform?**
**Terraform provisions** infrastructure (creates VMs, networks, load balancers) and is declarative + state-driven. **Ansible configures** what's inside/on top (installs software, sets up services) and is agentless config management. They're complementary: Terraform builds the servers, Ansible configures them.

**5. Ansible vs Puppet/Chef?**
Puppet and Chef are typically **agent-based** and use a pull model (agents pull config from a server). Ansible is **agentless** and **push-based** (control node pushes over SSH), with a gentler learning curve (YAML, no Ruby DSL). Ansible's simplicity is its main draw.

---

## 🔵 Core building blocks

**6. Explain inventory, play, task, module, and playbook.**
- **Inventory** — the list of hosts to manage (grouped).
- **Module** — a unit of work (`apt`, `copy`, `service`) — the verbs.
- **Task** — one call to a module.
- **Play** — maps a set of tasks to a set of hosts.
- **Playbook** — a YAML file of one or more plays.

**7. What is an ad-hoc command and when do you use it?**
A one-off command without writing a playbook: `ansible all -m ping` or `ansible web -m apt -a "name=nginx state=present" --become`. Good for quick checks or a single action across hosts; playbooks are for repeatable, multi-step automation.

**8. What is a module — give common examples.**
A reusable unit that does one thing idempotently: `apt`/`yum` (packages), `copy`/`template` (files), `service`/`systemd` (services), `file` (dirs/permissions), `user` (accounts), `git` (repos). Prefer modules over raw `command`/`shell` because modules are idempotent.

**9. `command` vs `shell` module — and why avoid them?**
`command` runs a binary without a shell; `shell` runs through a shell (so pipes/redirects/`$VARS` work). Avoid both when a real module exists, because they're **not idempotent** (they run every time and report `changed` always) and `shell` can be a security/injection risk. Use `creates:`/`when:` to guard them if you must.

**10. What are facts?**
Auto-discovered information about a host (OS, IP, memory, CPU…) gathered at the start of a play. You reference them like `ansible_os_family` or `ansible_default_ipv4.address` to make playbooks adapt to the host. Disable gathering with `gather_facts: false` for speed when not needed.

---

## 🟣 Playbooks, variables, handlers, templates

**11. What is a handler and how is it different from a task?**
A handler is a task that runs **only when notified** by another task, and only **once** at the end of the play. Classic use: a config-file task `notify`s a "restart nginx" handler — so the service restarts *only if the config actually changed*, not on every run.

**12. How do variables work and what's precedence?**
Variables parameterise playbooks (`{{ var }}`). They come from many places; simplified precedence low→high: role `defaults` < inventory vars < playbook vars < extra vars (`-e`, highest). When in doubt, `-e` wins.

**13. What is a template and how is it different from copy?**
`copy` puts a static file on the host as-is. `template` renders a **Jinja2** template (`.j2`) first — substituting variables/facts/loops — then writes the result. Use template whenever the file content depends on variables (configs, with host-specific values).

**14. What's a loop and a conditional in Ansible?**
A loop repeats a task over items: `loop: "{{ packages }}"` with `{{ item }}`. A conditional runs a task only when true: `when: ansible_os_family == "Debian"`. Together they keep playbooks DRY and host-aware.

**15. What does `become` do?**
Privilege escalation — it runs the task as another user, usually root via sudo (`become: true`). Equivalent of `sudo` for your tasks (install packages, edit system files).

---

## 🟠 Roles & structure

**16. What is a role and why use it?**
A role is a **reusable, standardised bundle** of tasks, handlers, templates, files, and variables in a fixed directory layout. It's like a function for configuration: write `webserver` once, apply it to many hosts/projects. Roles keep large playbooks organised and shareable (via Ansible Galaxy).

**17. What's the standard role directory layout?**
`tasks/`, `handlers/`, `templates/`, `files/`, `vars/`, `defaults/`, `meta/` — each with a `main.yml` where relevant. `defaults/` holds low-priority (overridable) vars; `vars/` holds higher-priority ones. Scaffold one with `ansible-galaxy init <role>`.

**18. defaults vs vars in a role?**
`defaults/main.yml` are the **lowest-precedence** variables — sensible defaults the user can easily override. `vars/main.yml` are **higher precedence** and harder to override. Put "knobs" users should change in defaults.

---

## 🔴 Real-world & "senior"

**19. How do you handle secrets in Ansible?**
**Ansible Vault** — encrypt sensitive files/variables (`ansible-vault create/edit`), and supply the password at runtime (`--ask-vault-pass` or a vault password file/secret manager). Never commit plaintext secrets. You can also pull secrets from an external manager (Vault, cloud secret stores).

**20. How do you test/preview a playbook before running it for real?**
`--check` (dry run — reports what *would* change without changing it) combined with `--diff` (shows file content changes). Also `ansible-lint` for best-practice linting and `--syntax-check`. Run against a staging inventory first.

**21. How do you make sure a playbook is safe to re-run?**
Use idempotent modules (not `command`/`shell`), guard non-idempotent commands with `creates:`/`when:`, and test with `--check`. A well-written playbook should report mostly `ok` on the second run.

**22. How does Ansible connect and run on the targets?**
The control node SSHes into each managed node, copies the module code over, runs it with Python on the target, captures the result (JSON), and removes it. No persistent agent. (For Windows targets it uses WinRM instead of SSH.)

**23. How do you target only some hosts or limit a run?**
Group hosts in inventory and set `hosts: web`, or use `--limit web1` on the command line. Use `tags` on tasks/roles and `--tags`/`--skip-tags` to run only parts of a playbook.

**24. Your playbook shows `changed` every single run for one task. What's likely wrong?**
You probably used `command`/`shell` for something a proper module should do (so it can't detect "already done"), or a template/file that's being rewritten with differing content (e.g. a timestamp). Switch to an idempotent module or add `creates:`/`changed_when:`.

**25. How does Ansible fit a CI/CD pipeline?**
A pipeline can run `ansible-playbook` as the **configure/deploy** step — after Terraform provisions and an image is built, Ansible configures the servers or pushes the release. Often combined with `--check` in a PR for a safe preview.

---

## 🧠 Hands-on prompts to rehearse

All of these are in this lab.

1. **Write a playbook** that installs nginx and ensures it's running.
2. **Use a variable + a loop** to install a list of packages.
3. **Add a handler** that restarts a service only when its config changes.
4. **Render a config from a Jinja2 template** with a host-specific value.
5. **Turn a playbook into a role** with the standard layout.
6. **Fix a broken playbook** (bad indentation, non-idempotent command, missing become).

---

## ⭐ Found this useful?
If this helped your prep, please **star** ⭐, **fork** 🍴, and **share** 🔗 the repo on LinkedIn. Got an Ansible question from a real interview? Add it via [CONTRIBUTING.md](CONTRIBUTING.md).

Made by **Shubham Sharma** · [GitHub](https://github.com/shubhs248) · [LinkedIn](https://www.linkedin.com/in/shubhs248)
