# Contributing

Thanks for wanting to make this lab better! 🙌

This repo is a learning lab, so the best contributions make a concept **clearer** or give people **more realistic practice**.

## Ways to contribute

- **Add an exercise** — a new playbook scenario (a module, a pattern, a role) with a brief and a solution.
- **Add a broken playbook** — an intentionally broken `.yml` for the "fix-it" style, with the fix in `solutions/`.
- **Add an interview question** — a real one you were asked, with a short, plain-English answer.
- **Fix a bug or typo** — clarity fixes are very welcome.

## How to add an exercise

1. Put the brief as `exercise-N-short-name.md` in the relevant part folder.
2. Put the working answer (the playbook/inventory/role) in that part's `solutions/` folder.
3. Keep the explanation in **plain English** and focus on the *why* (especially idempotency).
4. Make sure the playbook actually runs: test with `ansible-playbook --syntax-check` and, ideally, `--check`. Run `ansible-lint` if you can.

## Style

- Short explanations, real examples, no walls of theory.
- Prefer a proper module over `command`/`shell` (and explain when raw commands are justified).
- Keep practice free/local (localhost or Docker containers) — no paid cloud required.
- One idea per exercise.

## Submitting

1. Fork the repo and create a branch.
2. Make your change.
3. Open a pull request describing what you added and why.

Be kind, keep it beginner-friendly, and assume the reader is learning. Thank you! ⭐
