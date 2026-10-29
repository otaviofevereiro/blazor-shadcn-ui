---
name: playwright
description: >
  Playwright UI integration invariants from the official guide: isolated cases,
  user-facing locators, web-first assertions, no third-party or production hits.
  Use when Playwright is the UI harness.
---

# Playwright

**When:** Playwright is the UI harness (`playwright.config.*`, a `*UiTests*` project, or this change adds it).

Write UI integration tests the way Playwright's own guide does: user-visible behavior, isolated cases, locators with auto-wait, web-first assertions. Same rules on `@playwright/test` and `Microsoft.Playwright`. A generated test is a draft — rewrite it to this skill before you keep it.

- One flow per test. Each case gets a fresh browser context (the built-in `page` fixture). Do not share cookies, storage, or leftover data across cases. Shared login goes in `beforeEach` or a setup project the repo already has — not in test order.
- Locators: `getByRole` with an accessible name, then `getByLabel` / `getByText`, then `getByTestId` as an explicit contract. Chain and `filter` to make a locator unique. No CSS or XPath. No `.first()` / `.nth()` to dodge a strict-mode hit.
- Assert with web-first matchers (`toBeVisible`, `toHaveURL`, `toHaveText`). Do not `expect(await locator.isVisible()).toBe(true)`. Do not `waitForTimeout` or `Thread.Sleep`.
- Stub third-party HTTP with `page.route`. Do not click through to a site you do not control. Do not hit production. Test data is data you own.
- Use the repo's Playwright config, `baseURL`, and project layout. Do not add a second config, a page-object kit, or a browser the repo does not run.
- On CI: traces on first retry, not on every test. Install only the browsers the config uses.

```typescript
// BAD — CSS, sleep, snapshot assertion
await page.locator(".btn-primary").click();
await page.waitForTimeout(2000);
expect(await page.locator("#ok").isVisible()).toBe(true);

// GOOD — role locator, web-first assert
await page.getByRole("button", { name: "Publish" }).click();
await expect(page.getByRole("heading", { name: "Hello" })).toBeVisible();
```

The procedure is still `ui-testing`. This skill is how the Playwright cases are written.
