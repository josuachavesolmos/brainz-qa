---
name: generate-spec
description: >
  Generate E2E test specs for BrainzLab services. Use when the user asks to
  "generate specs", "create E2E tests", "write playwright tests", "write rspec tests",
  "generate test coverage for this feature", or needs Playwright TypeScript or
  RSpec/Capybara specs created from application code.
metadata:
  version: "0.1.0"
---

Generate E2E test specifications by analyzing application code and matching existing test conventions.

## Framework Detection

Determine the target framework based on the user's request or project context:

- **Playwright TypeScript** — when the project has `playwright.config.ts`, an `e2e/` directory, or the user mentions Playwright
- **RSpec/Capybara** — when the project has `spec/` directory with `*_spec.rb` files, or the user mentions RSpec/system tests

## Convention Learning

Before generating specs, analyze existing test files to learn the team's patterns:

1. Read 2-3 existing spec files in the target directory
2. Extract patterns: file naming, describe/context nesting, helper usage, selector strategies, setup/teardown patterns, assertion styles
3. Note any shared helpers, custom matchers, or page objects
4. Identify authentication/session setup patterns

If no existing specs are found, fall back to the templates in `references/spec-templates.md`.

## Spec Generation Process

1. **Analyze the target code:**
   - For Rails: read routes (`config/routes.rb`), controllers, and views/components
   - For frontend: read components, pages, and API integrations
   - Identify user-facing flows, form submissions, navigation paths, and edge cases

2. **Plan test scenarios:**
   - Happy path for each major user flow
   - Validation error cases
   - Authentication/authorization boundaries
   - Edge cases (empty states, pagination, long inputs)

3. **Generate the spec file:**
   - Match the learned conventions exactly (naming, structure, helpers)
   - Include descriptive test names that explain the behavior being tested
   - Use data-testid selectors over CSS selectors when available
   - Add comments only where the test intent is non-obvious

4. **Write the spec** to the appropriate directory following existing naming conventions.

## Output

Present a summary of what was generated:
- File path
- Number of test scenarios
- Flows covered
- Any scenarios that need manual review or additional setup

## Additional Resources

- **`references/spec-templates.md`** — Fallback templates for Playwright TS and RSpec/Capybara
