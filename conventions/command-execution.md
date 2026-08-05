# Convention — Running commands (build and test)

Command output enters the agent's context and costs tokens: verbosity must be kept to a minimum.

## Rules

- Run builds and tests in the **least verbose variant** available; if `.archi` records the quiet
  commands, use those.
- Examples of quiet variants:
  - Maven → `mvn -q`
  - Gradle → `gradle -q`
  - Vitest → `vitest run --reporter=dot`
  - Jest → `jest --silent`
  - Karma/Angular → `ng test --watch=false` with a minimal reporter
  - Playwright → `--reporter=dot` (or `line`)
  - npm → `npm --silent run <script>`
- On failure → do NOT re-run everything in verbose mode: re-run **only the failing part** (the
  single test or module) with the detail you need.
- If the output is still long → filter it (e.g. `| tail`, `grep` on the errors) instead of reading
  it whole.

## Scope of execution: the module first, the global run only on the last batch

- While working on a module → run **only that module's tests** (idiomatic filters: `mvn -q
  -Dtest=...`, a path or pattern for Vitest/Jest, `--grep` for Playwright).
- The **global run** of the whole suite (all modules) happens **once only, at the end of the plan**
  → **on the last batch only**, as the non-regression verdict over the entire plan. Never as an
  iteration loop.
- On **intermediate batches** → no global run: the batch closes with the tests of the batch (or of
  the modules it touched) green. Non-regression across batches is verified all at once at the end.
- Which one is the last batch is decided and communicated by the **orchestrator** (`/sdd-dev`): the
  subagent does not infer it on its own.
- If the global run (last batch) finds a failure outside the current module → it is a regression:
  fix it in a narrow scope and close with a new green global run.
