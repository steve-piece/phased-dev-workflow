# Plan Document Templates

## Master Checklist Template

```markdown
<!-- docs/plans/00_master_checklist.md -->
<!-- Master checklist tracking all features and subtasks across N development stages -->

# [Project Name] — Master Checklist

[One-sentence description of what is being tracked.]

---

## Stage 1: [Stage Name] ([Owner])
**Status:** Not Started
**Ship-blocking:** Yes | No

### Feature: [Feature Name]
- [ ] Subtask description
- [ ] Subtask description

**Exit criteria:** [Testable condition.]

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
