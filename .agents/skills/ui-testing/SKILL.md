---
name: ui-testing
description: Write UI integration tests for a changed flow and run the existing UI suite as regression. Use when the product has a user-visible UI, or when implementing or reviewing a user-visible surface.
---

# UI testing

When the product has a user-visible UI, a change to that UI is incomplete until a **UI integration test** covers the new or changed flow and the **existing UI suite** passes as regression.

This skill is the procedure. The invariant is `testing`. When Playwright is the harness, also follow `playwright`. Write the new case with the `tdd` skill.

## Does this product have a UI?

Yes when the repo ships pages or components a person uses (browser, native shell, or desktop webview): `.razor`, `.cshtml`, app `.html`, React / Vue / Svelte / Angular / Solid files, Flutter / SwiftUI / WinUI, or an existing UI test project.

No when the tree is catalog-only, docs-only, or an API / worker with none of those surfaces. Stop here.

If the change cannot be seen in the UI, stop.

If the user said not to write tests, stop before the steps below. Do not add a new case. When `testing` binds, still run the existing project suite after a code change. Do not call the work done while that suite is red.

## 1. Name the flow seam

The seam is the user-visible flow: open the route, click, type, submit, navigate. Confirm that seam with the user before writing a test (see `tdd`). A seam already agreed in this conversation or the spec stands; do not ask again.

Name the related surfaces that share the state, data, or components you will touch. Those are the regression set.

Component-only tests (bUnit, shallow RTL) are not this seam. Host HTTP tests still cover authorization and private bytes.

## 2. Use the repo's UI harness

Search the repo for the existing UI integration runner and project (Playwright, Cypress, or whatever the docs already name). Use that. Do not add a second harness.

If the product has a UI and no UI integration harness:

- Use the runner the repo docs name.
- If `testing` binds and the docs are silent, add Playwright and follow `playwright`.
- If neither names a runner, ask. Do not guess.

If Playwright is already the harness, follow `playwright` for locators, assertions, isolation, and stubs.

## 3. Write one integration case for the changed flow

Red → green through the real UI. One flow, one test, then the implementation. Assert what the user can see or do, with expected values from the spec, not from the code.

```typescript
// BAD — component state, no flow
test("save is enabled", () => {
  render(<Editor />);
  expect(screen.getByRole("button", { name: "Save" })).toBeEnabled();
});

// GOOD — integration through the UI
test("author can publish a draft", async ({ page }) => {
  await page.goto("/drafts/new");
  await page.getByLabel("Title").fill("Hello");
  await page.getByRole("button", { name: "Publish" }).click();
  await expect(page.getByRole("heading", { name: "Hello" })).toBeVisible();
});
```

Cover the edge states the change actually touches (empty, error, route or flag variants), not every screen in the app.

If the repo has no case for this flow yet, create it. Missing harness is not a reason to skip the case.

## 4. Regression

After the new case is green, run the existing UI suite (or the full suite if UI cases live there). Also run the cases for the related surfaces you named in step 1.

A red regression is a defect in this change. Fix it and re-run. Do not call the work done while that suite is red.

If the suite cannot run here (no browser, CI-only), still write the case and say what you could not run. Do not skip writing it.

A screenshot of the changed screen is not this step. After tests, a user-visible change still follows `preview`.
