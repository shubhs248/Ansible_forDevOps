# Exercise 3 — Turn It Into a Role

## Goal
Take the playbook from Exercise 2 and restructure it as a reusable **role**. This is how real Ansible projects are organised — and a very common interview topic.

## Why roles?
A flat playbook is fine for one job. But when you reuse "set up a web server" across projects and teams, you want it **packaged**: tasks, handlers, templates, and default variables in a standard layout you can drop into any playbook with one line. That package is a role.

## The brief
1. Scaffold a role: `ansible-galaxy init roles/webserver` (or create the folders by hand).
2. Move your logic into the role:
   - tasks → `roles/webserver/tasks/main.yml`
   - handler → `roles/webserver/handlers/main.yml`
   - templates → `roles/webserver/templates/`
   - default vars (`packages`, `listen_port`) → `roles/webserver/defaults/main.yml`
3. Create a tiny top-level `site.yml` that just applies the role to `web`.

## Hints
- The standard role directories: `tasks/ handlers/ templates/ files/ vars/ defaults/ meta/`.
- Inside the role, templates are referenced by name only (`src: index.html.j2`) — Ansible looks in the role's `templates/` automatically.
- Put overridable knobs in `defaults/main.yml` (lowest precedence, easy to override).

## The top-level playbook should be this short:
```yaml
- hosts: web
  become: true
  roles:
    - webserver
```

## How to check you succeeded
- `ansible-playbook -i inventory.ini site.yml` produces the same result as Exercise 2.
- You can override a default at runtime: `-e "listen_port=9090"`.

Compare with the layout in [`solutions/roles/webserver/`](solutions/roles/webserver/) and [`solutions/site-with-role.yml`](solutions/site-with-role.yml).
