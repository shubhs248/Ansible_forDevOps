# Exercise 2 — Variables, Loops, Templates & Handlers

## Goal
Make the playbook flexible and smart: install a *list* of packages with a loop, render a config from a template using a variable, and restart the service **only when the config changes** using a handler.

## The brief
Create `webserver.yml` that:

1. Defines vars: a list `packages: [nginx, curl]` and `listen_port: 8080`.
2. Installs every package in `packages` using a **loop**.
3. Renders a Jinja2 template `templates/index.html.j2` to `/var/www/html/index.html`, passing `listen_port`.
4. Renders `templates/nginx-site.conf.j2` to `/etc/nginx/conf.d/site.conf`, and **notifies** a handler `Restart nginx` if it changes.
5. Has a handler `Restart nginx` that restarts the service.

## Why a handler here?
You only want to restart nginx if the config **actually changed**. A handler triggered by `notify` does exactly that — it runs once, at the end, and only if something notified it. Restarting on every run would be wasteful and disruptive.

## Hints
- Loop: `loop: "{{ packages }}"` with `name: "{{ item }}"`.
- Template: `ansible.builtin.template: { src: ..., dest: ... }` then `notify: Restart nginx`.
- In the `.j2` file, reference `{{ listen_port }}`.
- Handlers go in a `handlers:` block at the play level.

## How to check you succeeded
- First run: template tasks are `changed` and the handler fires ("RUNNING HANDLER ... Restart nginx").
- Second run: templates are `ok` and the handler does **not** fire.

Compare with [`solutions/webserver.yml`](solutions/webserver.yml) and the templates in [`solutions/templates/`](solutions/templates/).
