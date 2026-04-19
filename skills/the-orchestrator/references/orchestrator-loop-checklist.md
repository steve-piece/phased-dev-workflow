<!-- skills/the-orchestrator/references/orchestrator-loop-checklist.md -->
<!-- Per-stage gate the-orchestrator walks before advancing to the next stage. Mirrors sp-feature-delivery/references/completion-checklist.md but at the orchestrator level. -->

# Orchestrator Per-Stage Loop Checklist

`the-orchestrator` walks this checklist between **every** stage. Do **not** advance to the next stage until every box is `[x]`. If any item fails, surface the failure to the user and stop the loop.

## 1. Stage Runner Returned Cleanly

- [ ] `stage-runner` returned a structured summary (not an exception, not a partial response).
- [ ] Summary includes `stage_n`, `branch`, `pr_url`, `pr_merged`, `tests_green`, `completion_checklist_all_checked`.
- [ ] `stage-runner` reported `pr_merged: true`.
- [ ] `stage-runner` reported `tests_green: true`.
- [ ] `stage-runner` reported `completion_checklist_all_checked: true`.

## 2. PR Reviewer Verdict

- [ ] `pr-reviewer` was dispatched with the stage's PR URL.
- [ ] `pr-reviewer` returned `verdict: pass`.
- [ ] `ci_status: all_green` on the merged head SHA.
- [ ] `required_checks_failed` is empty.
- [ ] `scope_drift` is empty (or any drift was explicitly approved by the user).

## 3. Git State Invariant — Clean Main

- [ ] Current branch is `main`.
- [ ] `git status --short` is empty.
- [ ] Local `main` is up to date with `origin/main` (`git pull --ff-only` succeeds with no changes).
- [ ] `git branch --list` shows only `main` (and any pre-existing long-lived branches the user explicitly listed).
- [ ] No leftover slice branches from this stage exist locally.
- [ ] No leftover slice branches exist remotely (or they were auto-deleted by the merge).
- [ ] No leftover git worktrees for this stage's slice.

## 4. Master Checklist Updated

- [ ] `docs/plans/00_master_checklist.md` row for the just-completed stage shows `Status: Completed`.
- [ ] Per-task `[x]` flips for the stage are present (done by `sp-feature-delivery` / `sp-ci-cd-scaffold`, verified by orchestrator).
- [ ] No other stage's status was accidentally edited.

## 5. Skill Completion Checklist Verified

- [ ] If the stage ran `sp-feature-delivery`: every box in `sp-feature-delivery/references/completion-checklist.md` is `[x]` for this slice.
- [ ] If the stage ran `sp-ci-cd-scaffold` (stage 1): every box in `sp-ci-cd-scaffold/references/scaffold-completion-checklist.md` is `[x]`.

## 6. Ready to Advance

- [ ] Next stage in `00_master_checklist.md` is well-formed and has a corresponding `docs/plans/stage_<n+1>_*.md` file.
- [ ] User has not interjected with an instruction to stop the orchestrator.
- [ ] No model-availability issue would prevent dispatching the next `stage-runner` on `claude-4.6-opus-high-thinking`.

## Done Criteria

The orchestrator may advance to the next stage **only** when:

1. All six sections above are fully checked.
2. The orchestrator session is on `main` with a clean working tree.
3. The just-merged PR is reachable from `main`'s `git log`.

If any item is unchecked, **stop**. Surface the failing item to the user before doing anything else.
