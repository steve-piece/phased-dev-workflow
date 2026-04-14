---
name: prd-generator
description: Generate a complete PRD (Product Requirements Document) from a free-form project brief. Use when the user provides a project description and wants a structured PRD, says "create a PRD", "write a PRD", "generate a PRD", "build a PRD", or "document requirements". Follows the Phased PRD Template v1 with 7 top-level sections (0–6) and a phased implementation plan. References bundled defaults for tech stack, architecture, and delivery conventions.
---

# PRD Generator

Generate a complete, canonical PRD from a free-form project brief.

## Inputs

Collect before generating:

1. **Project brief** (required) — free-form description of the product: what it does, who it's for, why it exists.
2. **Explicit overrides** (optional) — deliberate divergences from the default stack, architecture, or conventions.
3. **Scope signals** (optional) — MVP vs full, known phases, key personas, required integrations.

If the brief is present but specifics are missing, infer from [references/project-defaults.md](references/project-defaults.md). Do not ask for information that can be reasonably inferred from context.

If critical ambiguities remain after inference (auth model, multi-tenancy, domain name, known external APIs), ask **at most 3 focused questions** before generating.

## Reference Files

Read both before filling any section:

| File | Used For |
|---|---|
| [references/prd-template-v1.md](references/prd-template-v1.md) | Section structure, headings, and prompts |
| [references/project-defaults.md](references/project-defaults.md) | Default tech stack, services, and conventions (sections 0, 4, 5) |

## Generation Workflow

1. Read both reference files.
2. Map the project brief to each template section.
3. For every section not addressed by the brief:
   - Apply defaults from `project-defaults.md` where applicable (tech stack, infra, integrations).
   - Mark genuinely unknown specifics as `[TBD — needs input]` rather than inventing them.
4. Fill **section 6.5 (Phased Implementation Plan)** with phase tables:
   - One `#### Phase N: Name` block per phase.
   - Feature rows with columns: Feature / Work Item | Priority (P0/P1/P2) | Dependencies | Notes.
   - A Gate block after each table: testable criteria that must be true before moving on.
5. Fill **section 6.6 (Linear Implementation Mapping)** with workspace/team, project/label naming per phase, and issue creation rules from the 6.5 tables.
6. Write the complete PRD as a single markdown file.

## Output

- **File**: `docs/prd-[project-slug].md`, or `docs/prd.md` if no slug is obvious.
- **Header**: Two lines at top — (1) relative path, (2) one-line semantic description.
- **Structure**: Follow [references/prd-template-v1.md](references/prd-template-v1.md) exactly. All sections 0–6 and all subsections must be present, even if sparse. Never omit a section.
- **Section 0** must open with this verbatim note:
  > Unless explicitly overridden in this document, use the **Phased Default Project Setup** for all tech stack, architecture, testing, CI/CD, and coding standards.

## Handling Missing Information

| Situation | Action |
|---|---|
| Tech stack not specified | Apply full defaults from `project-defaults.md` |
| Integrations not mentioned | List defaults (Supabase, Stripe, Resend); mark optional ones `[Include if required]` |
| Data model not described | Enumerate entities implied by the brief; mark attributes `[TBD]` |
| Personas vague | Draft 1–2 personas from context; flag each for review |
| Phases not given | Propose a 3–4 phase breakdown based on scope |
| Domain/DNS unknown | Mark `[TBD — to be assigned]` |
| Overrides from defaults | Document explicitly in section 0: what is overridden and why |

## Downstream Skills

This skill produces a PRD file. It does not chain automatically.

Once the PRD is confirmed by the user:
- **`prd-to-phased-plans`** — decomposes section 6.5 into a master checklist and per-stage implementation files under `docs/plans/`.
- **`sp-feature-delivery`** command — executes each stage from those plan files.
