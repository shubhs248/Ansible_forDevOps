# Part 3 — Real Automation & Debugging

> 40 minutes. Build a realistic inventory + facts workflow, configure a service end to end, then fix three broken playbooks — the part that makes you dangerous in interviews and on-call.

---

## Using real "servers" (optional)

Spin up a couple of SSH-able containers to act as managed nodes:

```bash
docker run -d --name web1 -p 2221:22 rastasheep/ubuntu-sshd:18.04
docker run -d --name web2 -p 2222:22 rastasheep/ubuntu-sshd:18.04
# default creds for that image: root / root
```

Then point an inventory at them (see Exercise 1). **No Docker?** Everything also works against `localhost` with `--connection=local`.

---

## Exercises

| Exercise | You'll practise |
|----------|-----------------|
| [1 — Inventory & facts](exercise-1-inventory-and-facts.md) | groups, group vars, gathering and using facts |
| [2 — Install & configure a service](exercise-2-install-configure-service.md) | a realistic end-to-end play (package + config + service + verify) |
| [3 — Fix this playbook](exercise-3-fix-this-playbook.md) | debugging broken playbooks (the interview skill) |

---

## The debugging mindset

When a play fails or misbehaves:

1. **Read the error** — Ansible names the host, the task, and the module.
2. **`--check --diff`** to preview what it *thinks* it will change.
3. **`-vvv`** for verbose connection/module detail.
4. **`ansible <host> -m ping`** first — is it even reachable?
5. A task **`changed` every run**? You probably used `command`/`shell` where a module belongs.
6. **Permission denied** installing/editing system files? You forgot **`become: true`**.

---

That's the lab. Finish with **[INTERVIEW-QUESTIONS.md](../INTERVIEW-QUESTIONS.md)** and rehearse the answers out loud.
