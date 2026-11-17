---
name: ui-testing
description: Write a UI integration test for a changed flow. The suite runs once at the end of the initiative, after the user agrees. Use when the product has a user-visible UI, or when implementing a user-visible surface.
---

# UI testing

When the product has a user-visible UI, write a **UI integration test** for the new or changed flow during the issue. Do not execute UI tests in that issue.

The initiative runs the **existing UI suite** once, after the last issue, and only after the user agrees. If they decline, leave the suite unrun and say so. A red suite at that run is a defect: fix it and re-run before the pull request.

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

## 4. Leave the suite for the initiative

Write the case. Do not run the UI suite in this issue. The initiative asks once, then runs that suite once with all project tests, before the pull request.

If the suite cannot run there (no browser, CI-only), still write the case and say what could not run.

A screenshot of the changed screen is not this step. A user-visible change still follows `preview` in QA.
