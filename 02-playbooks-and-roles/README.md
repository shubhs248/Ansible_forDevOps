# Part 2 — Playbooks & Roles

> 50 minutes. You'll **write playbooks yourself**, run them, then compare with `solutions/`. By the end you'll have turned a flat playbook into a reusable role.

---

## A playbook, anatomically

```yaml
- name: Configure web servers     # ── a PLAY (tasks + the hosts they target)
  hosts: web
  become: true                    # run as root (sudo)
  vars:
    app_port: 8080
  tasks:
    - name: Install nginx         # ── a TASK (one module call)
      ansible.builtin.apt:
        name: nginx
        state: present
    - name: Start nginx
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: true
```

- A **play** says *which hosts* and *what tasks*.
- A **task** calls one **module** with arguments.
- `become: true` is sudo. `vars:` parameterise the play.

Run it:
```bash
ansible-playbook -i inventory.ini site.yml
ansible-playbook -i inventory.ini site.yml --check --diff   # preview, change nothing
```

> **No servers?** Run against your own machine: set `hosts: localhost` and add `--connection=local`. Everything in this part works that way too.

---

## Exercises

| Exercise | You'll practise |
|----------|-----------------|
| [1 — Your first playbook](exercise-1-first-playbook.md) | plays, tasks, modules, idempotency, `--check` |
| [2 — Variables & handlers](exercise-2-variables-and-handlers.md) | vars, loops, templates, `notify` + handlers |
| [3 — Roles](exercise-3-roles.md) | turning a playbook into a reusable role |

Write first, then peek at `solutions/`.

---

## Key things to remember

- **Indentation is everything** (YAML) — and tabs are illegal.
- A **handler** runs only when `notify`'d, and only once, at the end — perfect for "restart service if config changed".
- Prefer **`template`** over `copy` when content depends on variables.
- Use a **loop** to avoid repeating a task per package.
- Re-run your playbook — a good one reports mostly **`ok`** the second time (idempotency).

Next: **[Part 3 — Real Automation & Debugging](../03-real-automation-and-debugging/README.md)**.
