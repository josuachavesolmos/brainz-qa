---
name: flaky-detective
description: >
  Identify and fix flaky tests from CI history. Use when the user asks about
  "flaky tests", "intermittent failures", "tests that sometimes fail",
  "unstable specs", "test reliability", or needs help diagnosing tests that
  pass locally but fail in CI.
metadata:
  version: "0.1.0"
---

Investigate flaky tests, identify root causes, and suggest targeted fixes.

## Investigation Process

### 1. Gather Evidence

Collect information about the flaky test:

- Ask the user for the test name/file or search CI logs with `gh run list --status failure`
- Check recent CI runs: `gh run view <run-id> --log-failed`
- Look for the test in local history: run it multiple times with `--repeat-each=5` (Playwright) or `--order random` (RSpec)

### 2. Common Flaky Patterns

Analyze the test code and failure logs against these known patterns:

| Pattern | Symptoms | Fix |
|---------|----------|-----|
| **Race condition** | Passes locally, fails in CI; timing-dependent | Add explicit waits, use `waitFor` / `have_css` with wait |
| **Shared state** | Fails when run after specific other tests | Isolate test data, reset state in `beforeEach`/`before` |
| **Animation/transition** | Element found but click fails | Disable animations in test config or wait for stable state |
| **Network timing** | API calls timeout intermittently | Mock external APIs, increase timeout, add retry |
| **Date/time dependent** | Fails at month/year boundaries | Freeze time with `Timecop` or `page.clock` |
| **Order dependent** | Only fails in certain run orders | Run with `--shard` or random order to reproduce |
| **Resource leak** | Fails after many tests run | Check for unclosed connections, memory leaks in setup |

### 3. Reproduce Locally

Attempt to reproduce the flakiness:

- Playwright: `npx playwright test <file> --repeat-each=10 --workers=1`
- RSpec: `for i in $(seq 1 10); do bundle exec rspec <file> --order random; done`

### 4. Root Cause Analysis

Read the test file and identify:
- Missing `await` or missing Playwright auto-waiting
- Selectors that match multiple elements
- Setup/teardown that doesn't fully reset state
- Hard-coded delays (`sleep`, `waitForTimeout`) that are too short
- Assertions on non-deterministic data (timestamps, random IDs)

## Output

Present findings as:

1. **Test:** name and file path
2. **Flaky pattern:** which pattern from the table above
3. **Evidence:** specific lines or log output showing the issue
4. **Fix:** concrete code change with before/after diff
5. **Confidence:** High/Medium/Low that this fix resolves the flakiness
