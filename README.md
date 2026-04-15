# brainz-qa

QA automation toolkit for BrainzLab. Provides E2E spec generation, test execution and reporting, PR quality gates, flaky test detection, performance audits, and security scanning.

## Skills

| Skill | Trigger phrases | Description |
|-------|----------------|-------------|
| **generate-spec** | "generate specs", "create E2E tests", "write playwright tests", "write rspec tests" | Generates Playwright TS or RSpec/Capybara specs by learning from existing test conventions |
| **run-tests** | "run tests", "run the test suite", "execute specs" | Executes test suites, parses output, and produces structured pass/fail reports |
| **quality-gate** | "check quality gate", "validate PR test coverage", "are there missing tests" | Checks a PR branch for test coverage gaps and missing specs for changed files |
| **flaky-detective** | "flaky tests", "intermittent failures", "tests that sometimes fail" | Identifies flaky tests from CI history, analyzes root causes, and suggests fixes |
| **perf-audit** | "audit performance", "run lighthouse", "check page speed" | Runs Lighthouse audits, checks Core Web Vitals against budgets, suggests optimizations |
| **fix-e2e-docs** | "fix this E2E test", "test failed after UI change", "repair spec and update docs" | Repairs broken Playwright specs after intentional UI changes and updates companion Markdown documentation following Docs-as-Code philosophy |
| **security-scan** | "security scan", "check for vulnerabilities", "OWASP check" | Scans dependencies for CVEs and source code for OWASP Top 10 vulnerabilities |

## Agents

| Agent | Trigger | Description |
|-------|---------|-------------|
| **ci-triage** | "CI is red", "build failed", "debug CI failure" | Fetches GitHub Actions logs, identifies root cause, and suggests fixes |

## Supported Frameworks

- **Playwright** (TypeScript) — E2E browser testing
- **RSpec / Capybara** (Ruby) — Rails system tests
- GitHub Actions for CI integration

## Setup

No additional configuration required. The plugin uses tools already available in your environment:

- `gh` CLI for GitHub Actions integration (ci-triage agent)
- `npm` / `npx` for Playwright and Lighthouse
- `bundle` for RSpec and bundler-audit
- `npm audit` / `bundle audit` for dependency scanning

## Usage

Trigger any skill by describing what you need in natural language. Examples:

- "Generate Playwright specs for the users controller"
- "Run the full test suite and summarize failures"
- "Check if my PR has enough test coverage"
- "We have a flaky test in login.spec.ts, can you investigate?"
- "Run a performance audit on the dashboard page"
- "Scan the codebase for security vulnerabilities"
- "This E2E test broke after the last deploy, fix it and update the docs"
- "CI failed on my PR, can you check why?"
