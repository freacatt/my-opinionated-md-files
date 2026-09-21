# Initiate Development

Rules to apply when starting development in any project. Do these before writing feature code.

## 1. Graft: optimize tokens

1. Install: `npm install -g @nanonets/graft` (check with `graft version`).
2. Index the repo: `graft build` from the repo root.
3. Query graft before grepping or reading source:
   - `graft ask "<task>" --source` to locate and understand code.
   - `graft grep "<literal>"` to find every occurrence.
   - `graft skeleton <file>` to see a file's API without reading it.
   - `graft callers <symbol> --depth all` before any refactor or rename.
   - `graft map` to orient in an unfamiliar repo.
4. Keep the graph fresh: `graft check` in CI (fails if `graft/` is stale).

## 2. Test-driven first

1. Write the test before the implementation.
2. Minimum bar for every project:
   - Unit tests for logic.
   - A smoke test that starts the app or runs the main path end to end.
3. A change is not done until tests pass.
4. Set a coverage floor (for example 80% on new code).

## 3. Makefile organizes all commands

1. One `Makefile` at the repo root. No command lives only in someone's head.
2. Required targets:
   - `make install`: install dependencies and tools (including graft).
   - `make test`: unit tests.
   - `make smoke`: smoke test.
   - `make run`: start the app.
   - `make lint` / `make fmt`: lint and format.
   - `make graft`: rebuild the graft index.
   - `make release`: version, tag, changelog cut.
   - `make help`: list targets.

## 4. Change log

1. Keep `CHANGELOG.md` at the repo root.
2. Every change adds an entry under `Unreleased` (Added / Changed / Fixed / Removed).
3. Follow Keep a Changelog format.

## 5. Memory

1. Keep persistent project memory so context survives across sessions.
2. Record: decisions and their reasons, user preferences, constraints not visible in code.
3. Don't record what the code or git history already shows.

## 6. Project files

1. `CLAUDE.md` / `AGENTS.md`: commands, conventions, don't-touch areas for the agent.
2. `README.md`: what it is, how to install, how to run. Update it when behavior changes.
3. `.gitignore` and `.env.example`: never commit secrets. Commit a template of the required env vars.
4. Standard layout: `src/`, `tests/`, `docs/`.
5. Reproducible environment: lockfile plus `.tool-versions` or a devcontainer.

## 7. Quality gates

1. One linter and one formatter per language, run through `make lint` and `make fmt`.
2. Pre-commit hooks run lint, format and tests before every commit.
3. CI runs `make test`, `make smoke`, lint and `graft check` on every push.
4. Definition of done: tests pass, lint clean, changelog updated, graft rebuilt, README updated if behavior changed.
5. Run `/code-review` before merge.

## 8. Git conventions

1. Branch naming: `type/short-description`.
2. Conventional Commits, small commits.
3. Commit only when asked.

## 9. Process

1. Plan before code: write a short spec or plan for anything bigger than about 30 lines.
2. Decision log: `docs/decisions/` (ADRs) for why X was picked over Y.
3. Scope discipline: no unrequested refactors or features.

## 10. Dependencies and security

1. Pin versions. Justify each new dependency.
2. Secret scanning and dependency audit in CI.
3. No secrets in logs.
4. Structured logs. No silent failures.

## 11. Zero-bug development

Goal: catch bugs early and never see the same one twice.

### Prove correctness
1. Reproduce first: every bug fix starts with a failing test that reproduces it.
2. Strict types: strict mode on, no `any`, warnings as errors.
3. Make illegal states unrepresentable: enums and validated types, not loose strings and flags.
4. Validate at boundaries: check all external input (user, API, file) on entry and fail fast.
5. Assertions and invariants: state what must always be true and check it in code.
6. Run it for real: launch the app and verify the change works. Passing tests are not enough.

### Stronger tests
7. Integration and end-to-end tests for the paths where units meet.
8. Property-based tests (Hypothesis, fast-check) for logic with many edge cases.
9. Mutation testing (mutmut, Stryker) to check that tests catch real changes.
10. Deterministic tests: seeded randomness, frozen time, no network. Fix or delete a flaky test within a day.

### Automated bug hunters
11. Static analysis (Semgrep, CodeQL) in CI.
12. Fuzzing for parsers and anything that takes untrusted input.
13. Contract tests for API and schema compatibility between services.
14. Second review: run `/code-review` and `/security-review` on every diff.

### Safe delivery
15. Small PRs: under about 300 lines, one purpose each.
16. CI blocks merge: no red build reaches main.
17. Migration safety: every DB or schema migration is reversible and tested against a copy.
18. Feature flags and staged rollout: ship dark, enable gradually, keep a rollback path.
19. Staging environment: a production-like copy where smoke tests run before release.

### Reliability
20. Timeouts, retries and idempotency on every external call.
21. Observability: error tracking (Sentry), metrics, health checks and alerts.
22. Load and performance tests: set a baseline and fail CI on regressions.

### Learn from bugs
23. Bug log: root cause plus the test that now prevents it.
24. Postmortem for every bug that reaches production: why did the tests miss it, and what guard is now added.

Applies to every project: 1–6, 10, 11, 14–16, 21, 23, 24.
Apply when relevant: 7–9 (complex logic), 12 (untrusted input), 13 (multi-service), 17 (databases), 18–19 (deployed services), 20 (external calls), 22 (performance-sensitive code).
