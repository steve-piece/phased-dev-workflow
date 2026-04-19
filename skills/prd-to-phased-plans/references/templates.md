# Plan Document Templates

## Master Checklist Template

```markdown
<!-- docs/plans/00_master_checklist.md -->
<!-- Master checklist tracking all features and subtasks across N development stages -->

# [Project Name] — Master Checklist

[One-sentence description of what is being tracked.]

---

## Stage 1: CI/CD Scaffolding (canned)
**Status:** Not Started
**Ship-blocking:** Yes

### Feature: CI/CD + E2E Baseline
- [ ] Run `sp-ci-cd-scaffold` skill end-to-end

**Exit criteria:** Every box in `sp-ci-cd-scaffold/references/scaffold-completion-checklist.md` is `[x]`.

---

## Stage 2: [Stage Name]
**Status:** Not Started
**Ship-blocking:** Yes | No

### Feature: [Feature Name]
- [ ] Subtask description

**Exit criteria:** [Testable condition.]

---

<!-- Repeat for all stages -->

## Ship-Blocking Summary (MVP)

| Stage | Requirement |
|-------|-------------|
| 1     | [What must be true] |
| 2     | [What must be true] |

## Phase 2 (Post-MVP)

| Feature | Stage |
|---------|-------|
| [Feature name] | N |
```

## Stage Plan Template

```markdown
<!-- docs/plans/stage_N_short_name.md -->
<!-- Stage N: [Brief semantic description for search] -->

# Stage N: [Stage Name] Implementation Plan

**Goal:** [One sentence describing the deliverable.]

**Architecture:** [How this stage fits into the overall system. 1-2 sentences.]

**Tech Stack:** [Frameworks, libraries, tools used in this stage.]

---

## Rules Reference

From `.cursor/rules/[relevant-rule].mdc`:
- [Key constraint from the rule]
- [Key constraint from the rule]

---

## Tasks

### Task 1: [Task Title]

**Files:**
- Create: `path/to/new-file.ts`
- Modify: `path/to/existing-file.ts`

**Step 1: [Step description]**

[Explanation of what to do.]

\`\`\`ts
// path/to/file.ts
// Full implementation code
\`\`\`

**Step 2: [Step description]**

\`\`\`bash
[command to run]
\`\`\`

**Commit:**
\`\`\`bash
git commit -m "feat: [description]"
\`\`\`

---

### Task 2: [Task Title]

[Same structure as Task 1]

---

**Exit criteria:** [Testable conditions — commands to run, expected output.]
```

## Stage 1 (Canned) Template

`docs/plans/stage_1_ci_cd_scaffold.md` is **always written by the `prd-to-phased-plans` orchestrator skill itself** — never by the `phased-plan-writer` subagent. Its body is fixed and simply delegates to the `sp-ci-cd-scaffold` skill. Lift verbatim:

```markdown
<!-- docs/plans/stage_1_ci_cd_scaffold.md -->
<!-- Stage 1: bootstrap CI/CD + E2E baseline via sp-ci-cd-scaffold skill -->

# Stage 1: CI/CD Scaffolding

**Goal:** Bootstrap CI/CD + E2E baseline before any feature work begins.

**Architecture:** Establishes Playwright suites (`@feature`, `@regression-core`), GitHub Actions workflows (`ci.yml`, `e2e.yml`, `e2e-coverage.yml`), Husky `pre-push`, PR template, and the branch-protection setup script. Every later stage depends on this baseline.

**Tech Stack:** Playwright, GitHub Actions, Husky, `gh` CLI.

**Owner:** Run the [`sp-ci-cd-scaffold`](../../.cursor/skills/sp-ci-cd-scaffold/SKILL.md) skill end-to-end.

---

## Tasks

This stage's task list is intentionally not duplicated here. The single task is:

- Run the `sp-ci-cd-scaffold` skill in full.

The skill's `SKILL.md` (Phases 0–6) is the source of truth for the work. Its `references/scaffold-completion-checklist.md` is the source of truth for "done".

---

**Exit criteria:** Every box in `sp-ci-cd-scaffold/references/scaffold-completion-checklist.md` is `[x]`. The PR for `chore/ci-cd-scaffold` is merged into `main`, CI is green on the merged head SHA, and the working tree is clean on `main` with no leftover branches.
```

## Naming Conventions

- Checklist: `00_master_checklist.md`
- Stage plans: `stage_N_short_name.md` where `short_name` is lowercase with underscores (e.g., `database_schema`, `ui_components`, `admin_portal`)
- Stage numbers are sequential starting at 1
- All files go in `docs/plans/`

## Header Comment Convention

Every generated plan file starts with two HTML comment lines:
1. Relative file path
2. Brief description optimized for semantic search

```markdown
<!-- docs/plans/stage_2_database_schema.md -->
<!-- Stage 2: Supabase schema for industries, questionnaires, and submissions with RLS -->
```

## TodoWrite Convention

After generating all plan files, create todos in this format:

```
id: stage-N-short-name
content: "Stage N: [description of implementation work] (see docs/plans/stage_N_short_name.md)"
status: pending
```

Keep one todo per stage. The stage plan file contains the detailed subtasks.
