# Exercise 3 — Fix This Playbook

## Goal
Debug a broken playbook. This is the skill interviewers probe: can you read a play, spot what's wrong, and explain *why*?

## The situation
The file [`broken/playbook.yml`](broken/playbook.yml) is supposed to install nginx, drop a config file, and make sure the service is running. But it has **four** problems — it fails to run, or behaves wrong (e.g. reports `changed` on every run, or can't write system files).

## Your task
1. Read `broken/playbook.yml` and find all four issues.
2. Fix them.
3. For each fix, be ready to say *why* it was wrong.

## Hints (no spoilers)
- One problem stops the YAML from parsing. Look at **indentation**.
- One task tries to install a package but gets **permission denied** — what's missing at the play level?
- One task uses `shell` to install nginx, so it reports **changed every run** — there's a proper module for that.
- A handler is **notified** but the handler **name doesn't match** — so the restart never happens.

## How to check you succeeded
- The playbook parses and runs.
- The install task uses an idempotent module → second run reports `ok`, not `changed`.
- The handler actually fires when the config changes.

Compare your fix with [`solutions/fixed-playbook.yml`](solutions/fixed-playbook.yml) — and read the "What was wrong" notes at the bottom of that file.
