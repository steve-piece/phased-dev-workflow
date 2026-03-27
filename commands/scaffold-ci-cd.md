# Scaffold CI/CD

Use this command to bootstrap a production-grade CI/CD and E2E baseline in the current repository.

## Objectives

- Add deterministic CI checks on pull requests.
- Add feature and regression E2E suites.
- Enforce test gates before merge or deployment.
- Keep setup compatible with monorepos and single-app repos.

## Discovery

1. Detect package manager (`pnpm`, `npm`, `yarn`, `bun`).
2. Detect app framework/runtime (Next.js, React, Node API, etc.).
3. Detect monorepo tooling (Turborepo/Nx) and workspace layout.
4. Detect existing workflows and test tooling.
5. Confirm target app(s) for E2E smoke/regression.

## Clarifying Questions (Plan Mode Required)

Before making changes, switch to Plan Mode and ask:
1. Which app(s) are critical paths for E2E?
2. Should CI run on PR only, or PR + main pushes?
3. Which suites are required blockers (`feature`, `regression-core`, full)?
4. Are deployments gated on green E2E checks?
5. Any restricted environments/secrets needed in CI?

## Branching

- Create a dedicated branch: `chore/ci-cd-scaffold`.
- Never scaffold CI directly on `main`/`master`.
- Keep first PR minimal: baseline quality + smoke E2E only.

## Implementation Checklist

### A. Test Framework & E2E Suites

1. Add test framework for E2E (prefer Playwright for web apps).
2. Add scripts:
   - `test:e2e`
   - `test:e2e:feature`
   - `test:e2e:regression`
3. Add baseline E2E tests:
   - smoke test tagged `@feature`
   - regression sentinel tagged `@regression-core`
4. Add/extend task runner config (e.g., `turbo.json`) for E2E tasks.

### B. CI Workflows

5. Add `.github/workflows/ci.yml`:
   - install
   - lint
   - typecheck
   - unit/integration tests
6. Add `.github/workflows/e2e.yml`:
   - install
   - browser dependencies
   - run `feature` and `regression-core`
   - upload traces/videos/reports on failure
7. Add `.github/workflows/e2e-coverage.yml`:
   - Single job that diffs changed files in the PR against `main`.
   - Fails if any file under `apps/**` or `packages/**` was changed without a corresponding `*.spec.ts` or `*.e2e.ts` file being added or modified in the same PR.
   - Exempt paths (no E2E required): `*.md`, `*.json` (config-only), `*.css`, image/font assets.
   - Job name: `E2E / coverage-check`.
8. Update `.gitignore` for E2E artifacts.

### C. Husky Pre-Push Hook

9. Install Husky: `pnpm add -D husky && pnpm exec husky init`.
10. Create `.husky/pre-push` with the full local gate chain:
    ```bash
    pnpm lint && pnpm typecheck && pnpm test && pnpm test:e2e:feature && pnpm test:e2e:regression
    ```
11. Commit `.husky/pre-push` to the repo so every contributor and agent gets the hook automatically.

### D. PR Template

12. Generate `.github/pull_request_template.md`:
    ```markdown
    ## Changes
    [description]

    ## CI/CD checklist
    - [ ] E2E test added or updated for every changed behavior
    - [ ] regression-core suite still passes locally
    - [ ] No existing CI workflow jobs removed or weakened
    ```

### E. Branch Protection Provisioning

13. Generate `scripts/setup-branch-protection.sh` using the `gh` CLI.
    The script must set these required status checks on `main`:
    - `CI / lint`
    - `CI / typecheck`
    - `CI / unit-tests`
    - `E2E / feature`
    - `E2E / regression-core`
    - `E2E / coverage-check`

    The script is run once by the project owner after repo creation. It is not executed automatically.

## Quality Gates

Before opening PR, run locally:
- lint
- typecheck
- unit/integration tests
- `test:e2e:feature`
- `test:e2e:regression`

The Husky `pre-push` hook enforces these gates automatically. If the hook is bypassed (e.g., `--no-verify`), CI will catch failures via required status checks.

Do not claim completion unless:
- All local gates pass.
- `.husky/pre-push` exists and is committed.
- `.github/pull_request_template.md` exists and is committed.
- `scripts/setup-branch-protection.sh` exists and is committed.
- `.github/workflows/e2e-coverage.yml` exists and is committed.

## Subagent-Oriented Execution

When implementing non-trivial scaffolding:
1. Use one implementer subagent per independent task.
2. Run spec compliance review after each task.
3. Run code quality review after spec compliance passes.
4. Fix review findings before moving to next task.

## Final Output Format

Return:
1. Files created/updated
2. Scripts/commands added
3. CI triggers and required checks
4. Local verification commands and outcomes
5. Infrastructure artifacts confirmed present:
   - `.husky/pre-push`
   - `.github/pull_request_template.md`
   - `.github/workflows/e2e-coverage.yml`
   - `scripts/setup-branch-protection.sh`
6. Recommended next E2E flows to add
7. Reminder: run `scripts/setup-branch-protection.sh` to enable required status checks on `main`
