---
name: quality-gate
description: >
  Check a PR branch for test coverage gaps and missing specs. Use when the user
  asks to "check quality gate", "validate PR test coverage", "are there missing
  tests", "check if specs exist for my changes", or wants to ensure adequate
  test coverage before merging.
metadata:
  version: "0.1.0"
---

Analyze a PR branch to identify test coverage gaps and enforce quality standards before merge.

## Analysis Steps

### 1. Identify Changed Files

Run `git diff --name-only main...HEAD` (or the base branch the user specifies) to get all changed files.

Categorize changes:
- **App code** — controllers, models, services, components, views
- **Test code** — spec files, test helpers, fixtures
- **Config/infra** — CI config, build files, dependencies
- **Docs** — README, docs, comments only

### 2. Spec Coverage Check

For each changed app code file, check whether a corresponding spec file exists:

| App file pattern | Expected spec location |
|-----------------|----------------------|
| `app/controllers/*_controller.rb` | `spec/system/*_spec.rb` or `e2e/tests/**/*.spec.ts` |
| `app/models/*.rb` | `spec/models/*_spec.rb` |
| `app/services/*.rb` | `spec/services/*_spec.rb` |
| `src/components/*.tsx` | `e2e/tests/**/*.spec.ts` or `__tests__/*.test.tsx` |

Flag any app code file that was changed but has no corresponding spec.

### 3. New Feature Detection

If new routes, controllers, or pages were added, verify that E2E specs cover them. Check:
- New route entries in `config/routes.rb`
- New controller actions
- New page components or views

### 4. Test-to-Code Ratio

Calculate the ratio of test lines changed to app lines changed. Flag if below 0.5 (less than 1 test line per 2 app lines changed).

## Report Format

Present findings as a quality gate report:

```
## Quality Gate Report

**Branch:** feature/xyz
**Files changed:** N app, N test, N other

### Coverage Gaps
- [ ] `app/controllers/users_controller.rb` — no system spec found
- [x] `app/models/user.rb` — covered by `spec/models/user_spec.rb`

### New Routes Without E2E Coverage
- POST /api/v1/widgets — no E2E spec

### Metrics
- Test-to-code ratio: 0.7 (OK)
- Missing specs: 2 files

### Verdict: NEEDS WORK
Add specs for the 2 uncovered files before merging.
```

Use verdict values: **PASS**, **NEEDS WORK**, or **FAIL** (for zero test changes on significant app changes).
