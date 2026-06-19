# Exercise 2 — Install & Configure a Service (End to End)

## Goal
Write one realistic playbook that does the full job a sysadmin would: install a service, create a user and a directory for it, lay down its config from a template, ensure it's running, and verify it responds. This is what "configuration management" actually looks like.

## The brief
Create `deploy-web.yml` targeting `web`, with `become: true`, that:

1. Installs `nginx` (and `curl`).
2. Creates a system user `webadmin` (`ansible.builtin.user`).
3. Creates the directory `/var/www/app` owned by `webadmin`, mode `0755` (`ansible.builtin.file`).
4. Renders `templates/index.html.j2` → `/var/www/app/index.html`.
5. Renders a site config → `/etc/nginx/conf.d/app.conf` (root `/var/www/app`, a `listen_port` var), and **notifies** a `Restart nginx` handler.
6. Ensures nginx is `started` + `enabled`.
7. **Verifies**: use `ansible.builtin.uri` (or `command: curl -s localhost:{{ listen_port }}`) to check the page returns HTTP 200. Register the result and `debug` it.

## Hints
- Order matters: install → user → dir → files → service → verify.
- `ansible.builtin.uri: { url: "http://localhost:{{ listen_port }}", status_code: 200 }`.
- `register: result` then `debug: var=result.status`.
- Keep the handler at the play level.

## How to check you succeeded
- First run: lots of `changed`, handler fires, verify task passes (200).
- Second run: mostly `ok`, handler does **not** fire (idempotent).

Compare with [`solutions/deploy-web.yml`](solutions/deploy-web.yml).

## Bonus
Make the playbook safe to preview: run it with `--check --diff` and read what it *would* change.
