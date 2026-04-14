---
name: perf-audit
description: >
  Run performance audits and enforce budgets. Use when the user asks to
  "audit performance", "run lighthouse", "check page speed", "performance
  budget", "load time", "core web vitals", or needs help with performance
  testing setup and optimization.
metadata:
  version: "0.1.0"
---

Run performance audits, analyze results, and suggest optimizations for BrainzLab services.

## Lighthouse Audit

Run Lighthouse via CLI if available:

```bash
npx lighthouse <url> --output=json --output-path=./lighthouse-report.json --chrome-flags="--headless --no-sandbox"
```

If Lighthouse is not installed, guide the user through setup or use Playwright's built-in performance APIs.

### Key Metrics to Extract

| Metric | Budget | Description |
|--------|--------|-------------|
| First Contentful Paint | < 1.8s | Time to first visible content |
| Largest Contentful Paint | < 2.5s | Time to largest visible element |
| Total Blocking Time | < 200ms | Main thread blocking time |
| Cumulative Layout Shift | < 0.1 | Visual stability score |
| Speed Index | < 3.4s | How quickly content is visually populated |
| Time to Interactive | < 3.8s | Time until page is fully interactive |

### Scoring

- **Green (90-100):** Meets budget, no action needed
- **Yellow (50-89):** Approaching budget, optimize proactively
- **Red (0-49):** Over budget, requires immediate attention

## Performance Analysis

When metrics fail budgets, investigate common causes:

1. **Large bundle size** — Check `npx webpack-bundle-analyzer` or equivalent
2. **Unoptimized images** — Look for images without width/height, missing lazy loading
3. **Render-blocking resources** — CSS/JS in `<head>` without async/defer
4. **Excessive API calls** — Network waterfall analysis
5. **Memory leaks** — Monitor heap size across navigation

## Playwright Performance Integration

Generate Playwright performance tests using the Performance API:

```typescript
test("page loads within performance budget", async ({ page }) => {
  await page.goto("/dashboard");
  const metrics = await page.evaluate(() => JSON.stringify(performance.getEntriesByType("navigation")));
  const nav = JSON.parse(metrics)[0];
  expect(nav.loadEventEnd - nav.startTime).toBeLessThan(3000);
});
```

## Report Format

```
## Performance Audit Report

**URL:** <audited-url>
**Date:** <timestamp>

| Metric | Value | Budget | Status |
|--------|-------|--------|--------|
| FCP    | 1.2s  | 1.8s   | PASS   |
| LCP    | 3.1s  | 2.5s   | FAIL   |
| TBI    | 150ms | 200ms  | PASS   |
| CLS    | 0.05  | 0.1    | PASS   |

### Recommendations
1. Optimize hero image (LCP element): compress and serve in WebP format
2. ...
```
