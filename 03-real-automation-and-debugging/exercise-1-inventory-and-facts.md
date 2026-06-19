# Exercise 1 — Inventory & Facts

## Goal
Write a real inventory with groups and group variables, then write a small playbook that reads **facts** and prints host-specific info.

## The brief
1. Create `inventory.ini` with:
   - a `[web]` group containing `web1` and `web2` (use the Docker containers on ports 2221/2222, or just `localhost`),
   - a `[web:vars]` section setting `ansible_user`,
   - an `[all:vars]` section setting `ansible_python_interpreter=/usr/bin/python3`.
2. Create `facts.yml` with a play targeting `web` that prints, for each host:
   - the OS family (`ansible_os_family`),
   - the default IPv4 address (`ansible_default_ipv4.address`),
   - total memory (`ansible_memtotal_mb`).

## Hints
- Facts are gathered automatically at the start of a play (unless you set `gather_facts: false`).
- Use the `debug` module with a multi-line `msg`.
- Discover available fact names with: `ansible web -i inventory.ini -m setup`.

## How to check you succeeded
- `ansible-playbook -i inventory.ini facts.yml` prints correct, host-specific values for each host.
- Running `ansible web -i inventory.ini -m ping` succeeds first (proves connectivity).

Compare with [`solutions/inventory.ini`](solutions/inventory.ini) and [`solutions/facts.yml`](solutions/facts.yml).
