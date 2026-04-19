<!-- skills/the-orchestrator/references/per-stage-prompt-template.md -->
<!-- Exact prompt the-orchestrator sends to each stage-runner subagent. Mirrors what the user has been pasting manually into a fresh Opus 4.7 chat for each stage. -->

# Per-Stage Prompt Template

`the-orchestrator` fills in the four variables below and passes the result to the `stage-runner` subagent (alongside the full body of `agents/stage-runner.md`).

## Variables

- `{STAGE_N}` — the stage number, e.g. `2`.
- `{STAGE_FILE_PATH}` — workspace-relative path to the stage file, e.g. `docs/plans/stage_2_database_schema.md`.
- `{STAGE_GOAL}` — the goal sentence from the stage file's `**Goal:**` line.
- `{SKILL_TO_LOAD}` — `sp-ci-cd-scaffold` for stage 1, `sp-feature-delivery` for every other stage.

## Template

```
You are the stage-runner for Stage {STAGE_N} of the active phased plan.

Stage file: @{STAGE_FILE_PATH}
Stage goal: {STAGE_GOAL}
Skill to load: /{SKILL_TO_LOAD}
Master checklist: docs/plans/00_master_checklist.md

Complete all steps in @{STAGE_FILE_PATH} using /{SKILL_TO_LOAD}.

Hard requirements you must honor:

1. You are running on Opus 4.7 (claude-4.6-opus-high-thinking). Confirm this
   before doing anything. If you are not on Opus 4.7, stop and report.
2. Switch to plan mode before invoking the skill.
3. Run the loaded skill end-to-end (Phases 0..6). Let it dispatch its own
   Sonnet 4.6 subagents — do not re-implement that pipeline.
4. Open and merge the PR. Wait for CI. Patch failures on the same branch
   until every required check is green.
5. After merge, sync local main, delete the slice branch (local + remote
   if not auto-deleted), prune any worktrees, and confirm clean tree on main.
6. Walk the loaded skill's completion checklist end-to-end and confirm
   every box is [x] before returning.
7. Return the structured summary from the stage-runner output contract.
   Do NOT advance to Stage {STAGE_N}+1 — that is the orchestrator's job.

The orchestrator will:
- Dispatch a pr-reviewer subagent against your PR after you return.
- Walk its own per-stage loop checklist.
- Update the master checklist's stage-level status.
- Then dispatch the next stage-runner.
```

## Stage 1 Override

For `STAGE_N == 1`, `{SKILL_TO_LOAD}` MUST be `sp-ci-cd-scaffold`. The stage file is the canned `docs/plans/stage_1_ci_cd_scaffold.md` — it has no task body, only a delegation note. The skill is the source of truth for the work.

## Notes

- Do not paraphrase this prompt. The wording matches what the user has been doing manually; consistency makes the stage-runner's behavior predictable.
- Variables are filled by the orchestrator using `StrReplace`-style substitution before the prompt is sent.
- The orchestrator passes the full body of `agents/stage-runner.md` alongside this filled prompt — the subagent gets both.
