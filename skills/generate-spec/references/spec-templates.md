# Spec Templates

Fallback templates when no existing specs are available to learn from.

## Playwright TypeScript Template

```typescript
import { test, expect } from "@playwright/test";

test.describe("Feature: <feature-name>", () => {
  test.beforeEach(async ({ page }) => {
    // Authentication and navigation setup
  });

  test("should <expected behavior> when <condition>", async ({ page }) => {
    // Arrange — set up preconditions
    // Act — perform user actions
    // Assert — verify expected outcome
  });

  test("should show validation error when <invalid input>", async ({ page }) => {
    // Validation and error state tests
  });

  test("should handle empty state gracefully", async ({ page }) => {
    // Edge case: no data present
  });
});
```

### Playwright Conventions

- Use `test.describe` for feature grouping
- Use `data-testid` attributes for selectors: `page.getByTestId("submit-btn")`
- Use Playwright locators over raw CSS: `page.getByRole("button", { name: "Submit" })`
- Use `test.beforeEach` for shared setup (auth, navigation)
- Use `expect(locator).toBeVisible()` over `waitForSelector`
- Name tests as `should <behavior> when <condition>`

## RSpec/Capybara Template

```ruby
require "rails_helper"

RSpec.describe "Feature: <feature-name>", type: :system do
  before do
    # Authentication and data setup
    driven_by(:selenium_chrome_headless)
  end

  context "when user is authenticated" do
    before { sign_in(user) }

    it "displays the <element> on the page" do
      visit path
      expect(page).to have_content("Expected text")
    end

    it "allows user to <action>" do
      visit path
      fill_in "Field", with: "value"
      click_button "Submit"
      expect(page).to have_content("Success")
    end
  end

  context "when validation fails" do
    it "shows error messages" do
      visit path
      click_button "Submit"
      expect(page).to have_content("can't be blank")
    end
  end
end
```

### RSpec/Capybara Conventions

- Use `type: :system` for E2E specs
- Group with `context "when <condition>"`
- Use `it "verbs the thing"` for test names
- Use Capybara finders: `have_content`, `have_css`, `have_selector`
- Use FactoryBot for test data setup
- Use `driven_by(:selenium_chrome_headless)` for headless browser testing
