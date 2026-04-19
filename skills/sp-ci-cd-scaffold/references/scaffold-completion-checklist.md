<!-- skills/sp-ci-cd-scaffold/references/scaffold-completion-checklist.md -->
<!-- Concise end-of-scaffold verification checklist confirming all four CI/CD artifacts exist, local + CI gates passed, the chore/ci-cd-scaffold branch was merged, and the orchestrator finished on a clean main. -->

# CI/CD Scaffold Completion Checklist

Run this checklist at the end of every `sp-ci-cd-scaffold` run, in order. Do **not** consider the scaffold "done" until every box is `[x]`. If any step fails, return to the failing phase before moving on.

## 1. Scaffold Artifacts Present

- [ ] `.husky/pre-push` exists and is executable.
- [ ] `.github/pull_request_template.md` exists with the E2E attestation checklist.
- [ ] `.github/workflows/ci.yml` exists (lint + typecheck + unit/integration jobs).
- [ ] `.github/workflows/e2e.yml` exists (`@feature` + `@regression-core` jobs with artifact upload on failure).
- [ ] `.github/workflows/e2e-coverage.yml` exists (path-diff job named `E2E / coverage-check`).
- [ ] `scripts/setup-branch-protection.sh` exists and is executable.
- [ ] `.gitignore` excludes `playwright-report/`, `test-results/`, `.playwright/`.

## 2. Test Suites and Scripts Present

- [ ] `package.json` (root) has scripts: `test:e2e`, `test:e2e:feature`, `test:e2e:regression`.
- [ ] At least one `@feature`-tagged smoke spec exists.
- [ ] At least one `@regression-core`-tagged sentinel spec exists.
- [ ] Playwright (or detected E2E framework) is installed and lockfile updated.
- [ ] If a monorepo task runner exists (`turbo.json` / `nx.json`), the new E2E tasks are wired into it.

## 3. Local Gates Green

- [ ] `pnpm lint` (or detected equivalent) passes.
- [ ] `pnpm typecheck` passes.
- [ ] `pnpm test` (unit/integration) passes.
- [ ] `pnpm test:e2e:feature` passes locally.
- [ ] `pnpm test:e2e:regression` passes locally.
- [ ] Husky `pre-push` hook fires on push (verify with a dry-run or trial push).

## 4. PR Created and Submitted

- [ ] All work happened on branch `chore/ci-cd-scaffold` — never on `main`.
- [ ] PR opened via `git-commit-push-pr` / `new-branch-and-pr` skill or `gh pr create`.
- [ ] PR description lists every artifact created and links to `references/scaffold-completion-checklist.md`.
- [ ] PR is targeted at `main` and is **not** draft.

## 5. CI/CD Passing on the PR

- [ ] All required GitHub Actions checks have completed (no `pending` / `queued`).
- [ ] Every required check is **green**. No skipped checks counted as passing.
- [ ] If any check failed:
  - [ ] Read failing job logs.
  - [ ] Patch the cause on `chore/ci-cd-scaffold` (do **not** open a new PR for the fix).
  - [ ] Push the patch and wait for CI to re-run.
  - [ ] Repeat until all checks pass.
- [ ] Final CI run reflects the latest commit on the PR head, not a stale SHA.

## 6. Branch Cleanup and Return to Main

Only after CI is fully green and the PR is merged.

- [ ] PR merged into `main`.
- [ ] Local `main` updated: `git checkout main && git pull --ff-only origin main`.
- [ ] Confirm scaffold commits are present on `main` (`git log --oneline | head`).
- [ ] Local `chore/ci-cd-scaffold` branch deleted: `git branch -d chore/ci-cd-scaffold`.
- [ ] Remote `chore/ci-cd-scaffold` deleted: `git push origin --delete chore/ci-cd-scaffold` (skip if auto-deleted).
- [ ] If a worktree was used: `git worktree remove <path>` and `git worktree prune`.
- [ ] Final `git status` shows clean tree on `main`.
- [ ] User reminded once to run `scripts/setup-branch-protection.sh` to enable required status checks.
- [ ] If invoked as Stage 1 of a phased plan, `docs/plans/00_master_checklist.md` Stage 1 row flipped to `Completed`.

## Done Criteria

The scaffold is considered delivered **only** when:

1. All six sections above are fully checked.
2. The orchestrator is back on `main` with a clean working tree.
3. All four scaffold artifacts are present and committed on `main`.
4. CI passed on the merged PR head SHA.

If any item is unchecked, the scaffold is **not done** — surface the gap to the user before claiming completion.
