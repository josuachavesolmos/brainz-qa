# Playwright Conventions — BrainzLab

Mandatory conventions for all Playwright E2E specs. These rules are non-negotiable when writing or repairing specs.

## Locator Strategy

### Priority Hierarchy

Use the highest-priority locator that reliably identifies the element:

```
1. getByRole()          ← Preferred: semantic, accessible, resilient
2. getByLabel()         ← Form fields with visible labels
3. getByPlaceholder()   ← Inputs with placeholder text
4. getByText()          ← Visible text (use exact: true when needed)
5. getByTestId()        ← data-testid attributes (last resort for dynamic content)
```

### Hierarchical Scoping

Always scope locators within their parent container to avoid ambiguity:

```typescript
// BAD — matches any "Submit" button on the page
await page.getByRole("button", { name: "Submit" }).click();

// GOOD — scoped to the specific form
const form = page.getByRole("form", { name: "Create Query" });
await form.getByRole("button", { name: "Submit" }).click();
```

### Forbidden Patterns

```typescript
// NEVER use raw CSS selectors
page.locator(".btn-primary");              // ❌
page.locator("#submit-button");            // ❌
page.locator("div > span.label");          // ❌

// NEVER use XPath
page.locator("xpath=//div[@class='foo']"); // ❌

// NEVER use hardcoded URLs
await page.goto("https://staging.brainzlab.com/dashboard"); // ❌
await page.goto("/dashboard");             // ✅ uses baseURL from config

// NEVER use waitForTimeout
await page.waitForTimeout(3000);           // ❌
await page.waitForLoadState("networkidle");// ✅
```

## Wait Strategies

### After Navigation

Always wait for network idle after any navigation:

```typescript
await page.goto("/dashboard");
await page.waitForLoadState("networkidle");
```

### Before Interaction

Verify visibility before clicking or filling:

```typescript
const button = page.getByRole("button", { name: "Save" });
await expect(button).toBeVisible();
await button.click();
```

### After Actions That Trigger Network Requests

```typescript
await page.getByRole("button", { name: "Apply Filter" }).click();
await page.waitForLoadState("networkidle");

// Now assert on the filtered results
await expect(page.getByRole("table")).toBeVisible();
```

### Dynamic Content Loading

For content that loads asynchronously:

```typescript
// Wait for specific element to appear
await expect(page.getByRole("heading", { name: "Results" })).toBeVisible({ timeout: 10000 });

// Wait for a specific network response
await Promise.all([
  page.waitForResponse(resp => resp.url().includes("/api/queries") && resp.status() === 200),
  page.getByRole("button", { name: "Search" }).click(),
]);
```

## Test Structure

### File Naming

- `{feature-name}.spec.ts` — kebab-case matching the feature
- Place in `e2e/tests/{service-name}/` directory

### Describe Blocks

```typescript
import { test, expect } from "@playwright/test";

test.describe("Feature: Dashboard Queries", () => {
  test.beforeEach(async ({ page }) => {
    // Use global helpers for auth
    await page.goto("/dashboard/queries");
    await page.waitForLoadState("networkidle");
  });

  test("should display query table with default sorting", async ({ page }) => {
    // Single responsibility: one behavior per test
  });

  test("should filter queries by status when dropdown is changed", async ({ page }) => {
    // Descriptive name: should <behavior> when <condition>
  });
});
```

### Assertions

```typescript
// Prefer specific assertions over generic ones
await expect(page.getByRole("table")).toBeVisible();                    // ✅
await expect(page.getByRole("row")).toHaveCount(10);                   // ✅
await expect(page.getByText("No results")).not.toBeVisible();          // ✅

// Avoid loose assertions
expect(await page.textContent("body")).toContain("something");         // ❌
```

## Global Helpers and Fixtures

### Authentication

Use the global setup from `global.setup.ts` for authentication state:

```typescript
// In playwright.config.ts — storageState from global setup
use: {
  storageState: "e2e/.auth/user.json",
}
```

Do NOT write login flows inside individual specs unless testing auth itself.

### Environment Configuration

```typescript
// Access via playwright.config.ts baseURL
// In test: page.goto("/relative-path") automatically prepends baseURL

// For environment-specific values, use .env or config fixtures
// NEVER hardcode: URLs, ports, usernames, passwords, API keys
```

### Shared Utilities

If a pattern repeats across 3+ specs, extract to a helper:

```typescript
// e2e/helpers/table.helpers.ts
export async function waitForTableLoad(page: Page) {
  await page.waitForLoadState("networkidle");
  await expect(page.getByRole("table")).toBeVisible();
}

export async function getRowCount(page: Page): Promise<number> {
  return await page.getByRole("row").count() - 1; // minus header
}
```

## Error Recovery Patterns

### Handling Flaky Network States

```typescript
// If networkidle doesn't resolve (SSE/WebSocket connections), use domcontentloaded + explicit wait
await page.goto("/realtime-dashboard");
await page.waitForLoadState("domcontentloaded");
await expect(page.getByTestId("dashboard-loaded")).toBeVisible({ timeout: 15000 });
```

### Retries for Known Unstable Elements

```typescript
// Use Playwright's built-in retry via expect with timeout
await expect(async () => {
  await page.getByRole("button", { name: "Refresh" }).click();
  await expect(page.getByRole("cell", { name: "Updated" })).toBeVisible();
}).toPass({ timeout: 30000 });
```
