# Exercise 1 — Your First Playbook

## Goal
Write a playbook that installs nginx and makes sure it's running — then prove it's idempotent by running it twice.

## The brief
Create `site.yml` with one play that targets `web` (or `localhost`), uses `become`, and has tasks to:

1. Update the apt cache and install `nginx` (state present).
2. Ensure the `nginx` service is **started** and **enabled** (starts on boot).
3. Use the `debug` module to print a message: `nginx is set up on {{ ansible_hostname }}`.

## Hints
- `ansible.builtin.apt` with `update_cache: true` and `state: present`.
- `ansible.builtin.service` with `state: started` and `enabled: true`.
- `ansible.builtin.debug` with `msg:`.

## Run it
```bash
ansible-playbook -i inventory.ini site.yml
# or against your own machine:
ansible-playbook site.yml --connection=local -e "ansible_hostname=$(hostname)"
```

## How to check you succeeded
- First run: the install task is **changed**.
- **Run it again** → the install/service tasks now report **ok** (nothing to do). That's idempotency — the whole point.

Compare with [`solutions/site.yml`](solutions/site.yml).
