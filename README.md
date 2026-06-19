# 🤖 Ansible for DevOps — Practice Lab

> A **clone-and-go** lab to actually _understand_ Ansible — the tool that configures your servers *after* something like Terraform creates them. You'll write playbooks, use variables and handlers, build a role, run things against real containers, and (most importantly) **fix broken playbooks**. Every step explains the _why_. It runs on your laptop with **no cloud, no real servers, and no bill**.

Made with Ansible · License: MIT · PRs Welcome

---

## 🎯 Why this repo?

Most Ansible tutorials want you to spin up cloud VMs (and pay for them) just to run a playbook. This lab teaches the exact same skills — inventory, plays, tasks, modules, variables, handlers, templates, roles, idempotency — using **localhost and a couple of Docker containers** as your "managed servers". Nothing to pay for, nothing to clean up.

This lab is built the way interviews and real work actually test you:

* **Every concept is explained in plain English first**, with the mental model.
* **You write the playbooks yourself** from a spec, then compare with a working solution.
* **A whole section is about things going wrong** — the broken playbooks you'll actually hit.

> This pairs naturally with the [Terraform lab](https://github.com/shubhs248/Terraform_forDevOps). The classic line: **Terraform _provisions_ infrastructure, Ansible _configures_ it.** This lab is the "configures it" half.

## 🧠 The one mental model to keep

Ansible is **declarative and idempotent**. You don't write "run this command". You describe the **state you want** — "nginx is installed", "this config file exists", "the service is running" — and Ansible checks reality and only changes what's needed.

That's why you can run the same playbook 10 times and it only *does* something the first time (or when something has drifted). Each run reports `ok` (already correct), `changed` (it fixed something), or `failed`. Keep that `ok / changed / failed` loop in your head — it's the whole tool.

It's also **agentless**: Ansible just connects over **SSH** (and runs Python on the target). There's no agent to install on the servers — that's a big reason people pick it.

## 🗂️ What's inside

```
ansible-practice-lab/
├── README.md                           ← you are here
├── CHEATSHEET.md                       ← modules + playbook + inventory syntax on one page
├── CONTRIBUTING.md
├── INTERVIEW-QUESTIONS.md              ← the questions interviewers actually ask
├── 01-ansible-fundamentals/            ← the model: inventory, modules, ad-hoc, idempotency
│   └── README.md
├── 02-playbooks-and-roles/             ← write playbooks: vars, handlers, templates, roles
│   ├── README.md
│   ├── exercise-1-first-playbook.md
│   ├── exercise-2-variables-and-handlers.md
│   ├── exercise-3-roles.md
│   └── solutions/
└── 03-real-automation-and-debugging/   ← inventory & facts, configure a service, FIX broken plays
    ├── README.md
    ├── exercise-1-inventory-and-facts.md
    ├── exercise-2-install-configure-service.md
    ├── exercise-3-fix-this-playbook.md
    ├── broken/                         ← intentionally broken playbooks to diagnose & fix
    └── solutions/
```

## ✅ Requirements

* **Ansible** — install with `pip install ansible` (or your OS package manager). Check: `ansible --version`.
* **Python 3** — Ansible needs it (it almost certainly is already there).
* *(Optional)* **Docker** — to use real containers as "managed nodes" for the hands-on parts. You have it from the Docker lab.

> 💡 **On Windows:** run Ansible inside **WSL** — the Ansible control node must be Linux/macOS (it doesn't run natively on Windows). Everything then behaves like a real Linux box.
>
> 💡 **No Docker?** You can run every playbook against `localhost` with `--connection=local` and still learn all the concepts.

## 🚀 Quick start

```bash
# 1. Get the code
git clone https://github.com/shubhs248/ansible-practice-lab.git
cd ansible-practice-lab

# 2. Confirm Ansible is working — an ad-hoc "ping" to your own machine
ansible localhost -m ping
# localhost | SUCCESS => { "ping": "pong" }

# 3. Learn the model, then start writing playbooks
cat 01-ansible-fundamentals/README.md
cat 02-playbooks-and-roles/exercise-1-first-playbook.md
```

*(Optional) spin up two containers to use as practice "servers":*
```bash
docker run -d --name web1 -p 2221:22 rastasheep/ubuntu-sshd:18.04
docker run -d --name web2 -p 2222:22 rastasheep/ubuntu-sshd:18.04
# then target them from an inventory (see Part 3)
```

## 🧭 Suggested path (about 2 hours)

| # | Part | What you practise | Time |
|---|------|-------------------|------|
| 1 | [Ansible Fundamentals](01-ansible-fundamentals/README.md) | inventory, modules, ad-hoc commands, idempotency, the model | 30 min |
| 2 | [Playbooks & Roles](02-playbooks-and-roles/README.md) | plays, tasks, variables, handlers, templates, roles | 50 min |
| 3 | [Real Automation & Debugging](03-real-automation-and-debugging/README.md) | inventory/facts, install+configure a service, **fix broken plays** | 40 min |

## 📝 How each part works

* **Part 1** is a guided read: the model and the vocabulary, so the YAML later makes sense.
* **Part 2**: read the brief, **write the playbook yourself**, run it, then compare with `solutions/`.
* **Part 3**: build a realistic automation, then debug three broken playbooks (the part that makes you dangerous in interviews and on-call).

---

## 🎤 Prepping for an interview?

Open **[INTERVIEW-QUESTIONS.md](INTERVIEW-QUESTIONS.md)** — the Ansible questions interviewers actually ask (idempotency, agentless, playbook vs role, handlers, "Ansible vs Terraform?", "how do you handle secrets?"), in plain English with short answers you can say out loud.

---

## ⭐ Found this useful?

* **Star** ⭐ the repo so more people discover it.
* **Fork** 🍴 it and practise on your own copy.
* **Share** 🔗 it on LinkedIn and tag me — I would love to see your progress.
* **Contribute** 🤝 a new exercise or fix. See [CONTRIBUTING.md](CONTRIBUTING.md).

## 👋 About the author

Made with care by **Shubham Sharma**.

* GitHub: [github.com/shubhs248](https://github.com/shubhs248)
* LinkedIn: [linkedin.com/in/shubhs248](https://www.linkedin.com/in/shubhs248)

## 📜 License

MIT — free to use, fork, teach with, and share. A star ⭐ or a tag on LinkedIn is always appreciated!
