---
name: run-tests
description: >
  Execute test suites and produce structured reports. Use when the user asks to
  "run tests", "run the test suite", "execute specs", "run playwright tests",
  "run rspec", or needs test results parsed and summarized.
metadata:
  version: "0.1.0"
---

Execute test suites for BrainzLab services, parse output, and produce structured reports.

## Framework Detection

Detect the test framework from the project:

- **Playwright** — `playwright.config.ts` or `package.json` scripts containing `playwright`
- **RSpec** — `Gemfile` with `rspec-rails`, `.rspec` file, or `spec/` directory
- **Jest/Vitest** — `jest.config.*` or `vitest.config.*`

## Execution

Run the appropriate command based on framework and user request:

| Framework  | Full suite            | Single file               | Tagged/filtered           |
|------------|-----------------------|---------------------------|---------------------------|
| Playwright | `npx playwright test` | `npx playwright test <path>` | `npx playwright test --grep "<pattern>"` |
| RSpec      | `bundle exec rspec`   | `bundle exec rspec <path>` | `bundle exec rspec --tag <tag>` |

Pass through any additional flags the user specifies (e.g., `--headed`, `--workers=1`, `--fail-fast`).

## Output Parsing

Parse the test runner output and produce a structured report:

### Summary Table

| Metric | Value |
|--------|-------|
| Total  | N     |
| Passed | N     |
| Failed | N     |
| Skipped| N     |
| Duration | Xs  |

### Failed Tests Detail

For each failure, extract and present:

1. **Test name** — full describe/it path
2. **File and line** — clickable path
3. **Error message** — the assertion or error that failed
4. **Relevant snippet** — the failing line with 2-3 lines of context
5. **Suggested fix** — brief assessment of likely cause

### Flaky Detection Flag

If a test passes on retry but failed initially, flag it as potentially flaky and recommend adding it to the flaky-detective watchlist.

## Re-run Failures

If the user asks, re-run only the failed tests:

- Playwright: `npx playwright test --last-failed`
- RSpec: `bundle exec rspec --only-failures`
