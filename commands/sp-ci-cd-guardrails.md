# Superpowers: CI/CD Guardrails Check

Use this command after every feature build as a safety pass to verify CI/CD completeness and prevent regressions.

## Primary Objective

Ensure each feature delivery includes:
1. New or updated E2E coverage for the changed behavior.
2. Regression protection for critical existing flows.
3. Zero destructive changes to existing CI/CD unless explicitly approved.

## Verify Infrastructure Is Present (Run First)

Before running the per-feature checklist, confirm all four scaffold artifacts exist:

- `.husky/pre-push` — local gate hook
- `.github/pull_request_template.md` — E2E attestation template
- `.github/workflows/e2e-coverage.yml` — path-diff CI job
- `scripts/setup-branch-protection.sh` — branch protection provisioning

If **any** are missing, stop and run `/scaffold-ci-cd` first. Do not proceed with feature-level guardrails until the infrastructure baseline is in place.

## Mandatory Safety Rules

1. **Do not modify existing workflows by default.**
   - Treat current `.github/workflows/*.yml` files as stable contracts.
   - Prefer additive changes only (new jobs, new steps, new tags, new tests).
2. **Do not remove or rename existing jobs, required checks, tags, or scripts** unless the user explicitly requests it.
3. **Do not weaken gates** (lint, typecheck, unit/integration, E2E) to make failing changes pass.
4. If an existing check must change, require explicit approval and document:
   - why the change is necessary,
   - which historical features are affected,
   - how regressions are still prevented.

## Checklist (Run Every Feature)

1. Confirm changed feature scope (routes, APIs, shared modules, data contracts).
2. Confirm current CI required checks and workflow triggers.
3. Add or update `@feature` E2E tests for new behavior.
4. Add or update `@regression-core` tests when shared components/routes/contracts changed.
5. Ensure artifacts upload on E2E failure (trace/video/report/logs).
6. Verify local gates:
   - lint
   - typecheck
   - unit/integration tests
   - `test:e2e:feature`
   - `test:e2e:regression`
7. Verify CI config remains additive and non-breaking.

## Alignment With /scaffold-ci-cd

When baseline from `/scaffold-ci-cd` exists:
- Reuse existing scripts and workflow structure.
- Extend only the minimum required suite/tag coverage for the current feature.
- Keep naming and tagging consistent with existing pipeline conventions.

When baseline does not exist:
- Run `/scaffold-ci-cd` first.
- Then re-run this command to validate feature-specific additions.

## Output Format

Return:
1. Existing workflow files inspected
2. Additive CI/CD changes proposed or applied
3. E2E tests added/updated (`feature` and `regression-core`)
4. Verification commands executed + outcomes
5. Explicit statement confirming no existing guardrails were weakened
