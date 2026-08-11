# Convention — Development branch

No development happens directly on `main`: every piece of work — **evolution or bugfix, no exception
for its size** — lives on its own branch, opened from `main` and reintegrated at the end.

## Opening

- Branch created **from `main`**, up to date, before the first line of the work.
- One branch per plan → all the plan's batches go on the same branch.
- Name → `<type>/<slug of the plan>`, where the type is `feat` for an evolution and `fix` for a
  bugfix, e.g. `feat/library_management`, `fix/login_timeout`.
- Branch already existing for that plan (an interrupted run) → switch to it, do not open a second
  one.
- Uncommitted changes in the working tree when opening → report them to the user and stop: it is
  their call how to handle them.

## During the work

- Every commit of the workflow lands on this branch → convention `commit.md`.
- `main` stays untouched until the reintegration.

## Reintegration

- Only when the work is **complete**: for a plan, all its batches `certified`.
- The orchestrator **proposes** the reintegration into `main` and waits for the human's yes: the
  merge is never automatic.
- Work left incomplete → no reintegration: the branch stays as it is, and the user is told what is
  missing.
