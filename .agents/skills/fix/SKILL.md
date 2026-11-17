---
name: fix
description: >
  Fix a bug-class issue: reproduce with a failing test, patch via tdd, keep the regression
  guard. If reproduction cannot be automated, document why then patch. Does not review,
  document, or commit.
---

# Fix

Fix the bug in the issue or report. Do not review, update docs, or commit.

## Default path

1. Confirm bug-class and the reported failure (inputs, expected, actual).
2. Agree seams if not already agreed (`tdd`).
3. Write a **reproducing test** that fails on current behavior and matches the report (or an agreed narrowing).
4. Run it; confirm red.
5. Patch via `tdd` until green. Keep the test as the **regression guard**.
6. Before done: run all project tests except the UI suite (`testing`). Red = defect; fix and re-run.

## Documented exception

If reproduction cannot be automated at an agreed seam: write why in the issue and/or commit message, then patch without a reproducing test. Still run project tests before done.

## UI

If UI files change, use `frontend-design` before writing that UI. HIG review stays in `apple-design`.

If the product has a user-visible UI, use `ui-testing` to write a UI integration case for the changed flow. Do not run the UI suite. The initiative runs it once after the user agrees.

## Done

Return what changed, the project-test command, and the result.
