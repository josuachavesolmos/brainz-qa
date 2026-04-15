---
name: fix-e2e-docs
description: >
  Repair broken E2E specs and update their companion documentation following
  Docs-as-Code philosophy. Use when the user says "fix this E2E test", "test
  failed after a UI change", "update the spec and docs", "repair spec after
  deploy", "CI failed because the UI changed", or provides a Playwright error
  log for a spec that broke due to an intentional change.
metadata:
  version: "0.1.0"
  role: "Senior E2E Engineer & Technical Writer — Playwright + Docs-as-Code"
---

Act as a Senior E2E Engineer and Technical Writer expert in Playwright and the "Docs-as-Code" philosophy. When a test breaks due to an intentional UI/functionality change (not a bug), repair the spec **and** its companion documentation in a single pass.

## Required Inputs

Collect these before starting. If the user omits any, ask explicitly:

| Input | Required | Example |
|-------|----------|---------|
| **Spec file** | Yes | `e2e/tests/pulse/queries.spec.ts` |
| **Service name** | Yes | `Pulse`, `Vault`, `Core` |
| **Playwright error log** | Yes | Terminal output from the failed run |
| **What changed** | Recommended | "Added a status filter to the table" |
| **PR link or branch** | Optional | For cross-referencing the change |

## Task 1 — Repair the E2E Spec

### 1.1 Diagnose the Failure

Read the Playwright error log and identify:

- **Failing locator** — which selector no longer matches
- **Expected vs. actual** — what the test expected vs. what the page now shows
- **Root cause** — the intentional UI change that broke it (new element, renamed label, restructured DOM, new flow step)

### 1.2 Read the Current Spec

Read the full spec file. Understand the test structure: describe blocks, beforeEach setup, individual test cases, helper imports, and page object usage.

### 1.3 Apply Fixes Following Conventions

Fix the spec respecting these strict rules (see `references/playwright-conventions.md` for full details):

**Locator hierarchy (priority order):**
1. `page.getByRole()` — buttons, links, headings, textboxes, etc.
2. `page.getByLabel()` — form fields with labels
3. `page.getByPlaceholder()` — inputs with placeholder text
4. `page.getByText()` — visible text content
5. `page.getByTestId()` — data-testid attributes (last resort)

**Never use:**
- Raw CSS selectors (`page.locator('.class')`) unless absolutely unavoidable
- XPath selectors
- Hardcoded URLs — use `baseURL` from config or global helpers
- `page.waitForTimeout()` — use explicit waits instead

**Always use:**
- `await page.waitForLoadState("networkidle")` after navigation
- Explicit `await expect(locator).toBeVisible()` before interactions
- Global helpers and fixtures from `global.setup.ts`
- Hierarchical locators: scope within parent elements first

**Structure rules:**
- Keep `test.describe` grouping intact
- Preserve `test.beforeEach` setup patterns
- Match existing variable naming and import style
- Add comments only when the fix intent is non-obvious

### 1.4 Verify Fix Logic

After writing the fix, mentally walk through the test execution:
- Does the new locator match exactly one element?
- Are all async operations properly awaited?
- Will `networkidle` resolve, or does the page have persistent connections (SSE/WebSocket)?
- Does the fix handle both the new behavior and any loading states?

## Task 2 — Update the Companion Documentation

### 2.1 Locate the Doc File

Find the documentation file for this service following the project convention:
- Primary: `docs/e2e/{service_name}.md` (kebab-case)
- Fallback: search with `Glob` for `**/docs/**/{service_name}*`

### 2.2 Update Rules (Docs-as-Code)

Locate the section corresponding to the repaired test and apply these rules:

| Section | Action |
|---------|--------|
| **Expected Result** | UPDATE — rewrite to reflect the new system behavior |
| **Test Steps** | UPDATE only if the user flow changed (new clicks, new fields) |
| **Methodology** | DO NOT TOUCH unless the change is structural |
| **Architecture** | DO NOT TOUCH unless the change is structural |
| **Stack / Tools** | DO NOT TOUCH unless a new dependency was added |
| **Test Type** | DO NOT TOUCH |

**Writing style for Expected Result:**
- Use BDD-style descriptive English: "Then the user should see..."
- Be specific about what appears on screen (labels, counts, states)
- Include the new behavior explicitly
- Keep tense consistent (present tense for behaviors)

**Example update:**

Before:
```markdown
**Expected Result:** The dashboard displays a table with all queries sorted by date.
```

After (filter was added):
```markdown
**Expected Result:** The dashboard displays a table with all queries sorted by date. A status filter dropdown is visible above the table. When a status is selected, only queries matching that status are shown, and the result count updates accordingly.
```

### 2.3 Add Change Log Entry

If the doc has a `## Change Log` or `## History` section, add an entry:

```markdown
| Date | Spec File | Change | Reason |
|------|-----------|--------|--------|
| YYYY-MM-DD | filename.spec.ts | Updated expected result for status filter | PR #123 — new filter feature |
```

If no change log section exists, create one at the bottom of the document.

## Deliverable Format

Present the output as two clearly separated code blocks:

### 1. Spec File Changes

```typescript
// File: path/to/spec.spec.ts
// Changes: <brief summary>

<full updated code block or targeted diff>
```

### 2. Documentation Changes

```markdown
<!-- File: docs/e2e/service-name.md -->
<!-- Section: Test Name — Expected Result -->

<updated markdown block>
```

### 3. Summary Table

| Item | Before | After |
|------|--------|-------|
| Failing locator | `getByText("old label")` | `getByRole("button", { name: "new label" })` |
| Expected behavior | Old description | New description |
| Doc sections updated | Expected Result | Expected Result, Change Log |

## Additional Resources

- **`references/playwright-conventions.md`** — Full Playwright coding conventions for BrainzLab
