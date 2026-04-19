<!-- skills/sp-ci-cd-scaffold/references/scaffold-artifact-templates.md -->
<!-- Canonical file templates for every artifact sp-ci-cd-scaffold writes: husky pre-push, PR template, ci.yml, e2e.yml, e2e-coverage.yml, branch-protection script. Lift verbatim and adjust paths for the detected stack. -->

# CI/CD Scaffold Artifact Templates

Lift these verbatim into the repo, adjusting only:
- Package manager (`pnpm` → `npm` / `yarn` / `bun` if detected).
- App paths (`apps/web` → detected app dir).
- Node version (default 20, override if repo specifies).

All templates assume a Turborepo-style monorepo with `apps/**` and `packages/**`. For single-app repos, drop the `apps/**` prefix.

---

## `.husky/pre-push`

```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

pnpm lint && \
pnpm typecheck && \
pnpm test && \
pnpm test:e2e:feature && \
pnpm test:e2e:regression
```

Make executable: `chmod +x .husky/pre-push`.

---

## `.github/pull_request_template.md`

```markdown
## Changes

[description]

## CI/CD checklist

- [ ] E2E test added or updated for every changed behavior
- [ ] regression-core suite still passes locally
- [ ] No existing CI workflow jobs removed or weakened
- [ ] Husky pre-push hook ran green locally
```

---

## `.github/workflows/ci.yml`

```yaml
name: CI

on:
  pull_request:
    branches: [main]
  push:
    branches: [main]

concurrency:
  group: ci-${{ github.ref }}
  cancel-in-progress: true

jobs:
  lint:
    name: lint
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v3
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: pnpm
      - run: pnpm install --frozen-lockfile
      - run: pnpm lint

  typecheck:
    name: typecheck
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v3
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: pnpm
      - run: pnpm install --frozen-lockfile
      - run: pnpm typecheck

  unit-tests:
    name: unit-tests
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v3
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: pnpm
      - run: pnpm install --frozen-lockfile
      - run: pnpm test
```

---

## `.github/workflows/e2e.yml`

```yaml
name: E2E

on:
  pull_request:
    branches: [main]
  push:
    branches: [main]

concurrency:
  group: e2e-${{ github.ref }}
  cancel-in-progress: true

jobs:
  feature:
    name: feature
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v3
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: pnpm
      - run: pnpm install --frozen-lockfile
      - run: pnpm exec playwright install --with-deps chromium
      - run: pnpm test:e2e:feature
      - if: failure()
        uses: actions/upload-artifact@v4
        with:
          name: playwright-report-feature
          path: playwright-report/
          retention-days: 7

  regression-core:
    name: regression-core
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v3
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: pnpm
      - run: pnpm install --frozen-lockfile
      - run: pnpm exec playwright install --with-deps chromium
      - run: pnpm test:e2e:regression
      - if: failure()
        uses: actions/upload-artifact@v4
        with:
          name: playwright-report-regression
          path: playwright-report/
          retention-days: 7
```

---

## `.github/workflows/e2e-coverage.yml`

```yaml
name: E2E

on:
  pull_request:
    branches: [main]

jobs:
  coverage-check:
    name: coverage-check
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Detect changed product files
        id: changed
        run: |
          BASE=${{ github.event.pull_request.base.sha }}
          HEAD=${{ github.event.pull_request.head.sha }}
          CHANGED=$(git diff --name-only "$BASE" "$HEAD")
          PRODUCT=$(echo "$CHANGED" | grep -E '^(apps|packages)/' | grep -vE '\.(md|css|png|jpg|jpeg|gif|svg|webp|woff2?|ttf)$' | grep -vE '\.json$' || true)
          TESTS=$(echo "$CHANGED" | grep -E '\.(spec|e2e)\.ts$' || true)

          echo "product<<EOF" >> $GITHUB_OUTPUT
          echo "$PRODUCT" >> $GITHUB_OUTPUT
          echo "EOF" >> $GITHUB_OUTPUT

          echo "tests<<EOF" >> $GITHUB_OUTPUT
          echo "$TESTS" >> $GITHUB_OUTPUT
          echo "EOF" >> $GITHUB_OUTPUT

      - name: Enforce E2E coverage
        run: |
          if [ -n "${{ steps.changed.outputs.product }}" ] && [ -z "${{ steps.changed.outputs.tests }}" ]; then
            echo "::error::Product files changed without any *.spec.ts or *.e2e.ts changes."
            echo "Changed product files:"
            echo "${{ steps.changed.outputs.product }}"
            exit 1
          fi
          echo "E2E coverage check passed."
```

---

## `scripts/setup-branch-protection.sh`

```bash
#!/usr/bin/env bash
set -euo pipefail

REPO="${1:-$(gh repo view --json nameWithOwner -q .nameWithOwner)}"
BRANCH="${2:-main}"

echo "Configuring branch protection on $REPO@$BRANCH..."

gh api \
  --method PUT \
  -H "Accept: application/vnd.github+json" \
  "/repos/$REPO/branches/$BRANCH/protection" \
  -f required_status_checks.strict=true \
  -f required_status_checks.contexts[]='CI / lint' \
  -f required_status_checks.contexts[]='CI / typecheck' \
  -f required_status_checks.contexts[]='CI / unit-tests' \
  -f required_status_checks.contexts[]='E2E / feature' \
  -f required_status_checks.contexts[]='E2E / regression-core' \
  -f required_status_checks.contexts[]='E2E / coverage-check' \
  -f enforce_admins=false \
  -f required_pull_request_reviews.required_approving_review_count=1 \
  -f required_pull_request_reviews.dismiss_stale_reviews=true \
  -f restrictions= \
  -f allow_force_pushes=false \
  -f allow_deletions=false

echo "Branch protection configured. Required checks:"
gh api "/repos/$REPO/branches/$BRANCH/protection/required_status_checks" \
  --jq '.contexts[]'
```

Make executable: `chmod +x scripts/setup-branch-protection.sh`.

---

## Baseline Playwright Specs

### `e2e/feature/smoke.spec.ts`

```ts
import { test, expect } from '@playwright/test';

test.describe('@feature smoke', () => {
  test('home page loads without errors', async ({ page }) => {
    const errors: string[] = [];
    page.on('pageerror', (e) => errors.push(e.message));
    page.on('console', (m) => {
      if (m.type() === 'error') errors.push(m.text());
    });

    await page.goto('/');
    await expect(page).toHaveTitle(/.+/);
    expect(errors, `console/page errors: ${errors.join(', ')}`).toHaveLength(0);
  });
});
```

### `e2e/regression/sentinel.spec.ts`

```ts
import { test, expect } from '@playwright/test';

test.describe('@regression-core sentinel', () => {
  test('app responds with 200 on /', async ({ request }) => {
    const res = await request.get('/');
    expect(res.status()).toBe(200);
  });
});
```

### `playwright.config.ts` (root)

```ts
import { defineConfig } from '@playwright/test';

export default defineConfig({
  testDir: 'e2e',
  fullyParallel: true,
  reporter: [['html', { open: 'never' }], ['list']],
  use: {
    baseURL: process.env.PLAYWRIGHT_BASE_URL ?? 'http://localhost:3000',
    trace: 'retain-on-failure',
    video: 'retain-on-failure',
    screenshot: 'only-on-failure',
  },
  projects: [
    { name: 'feature', grep: /@feature/ },
    { name: 'regression-core', grep: /@regression-core/ },
  ],
});
```

---

## `package.json` Script Additions

```json
{
  "scripts": {
    "test:e2e": "playwright test",
    "test:e2e:feature": "playwright test --project=feature",
    "test:e2e:regression": "playwright test --project=regression-core"
  }
}
```

---

## `.gitignore` Additions

```gitignore
# Playwright
playwright-report/
test-results/
.playwright/
```
