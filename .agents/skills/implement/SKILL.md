---
name: implement
description: Implement work from a spec or set of tickets. Use when the user wants a spec or tickets built, or asks to implement a piece of work. Composes `tdd`, `frontend-design` when building or reshaping UI, and `ui-testing` when the product has a user-visible UI. Does not review, document, or commit.
---

# Implement

Implement the work described by the user in the spec or tickets. Do not start the next stage. Do not review, update docs, or commit.

Use the `tdd` skill where possible, at pre-agreed seams.

After each change: typecheck as you go. Before calling the work done: run **all tests in the current project** (the runner and every test project this repo documents — not a single-file filter). A red test is a defect in this change — fix it and re-run. Do not stop while any project test is red.

If the work builds or reshapes a user-visible UI, use the `frontend-design` skill before writing that UI. A HIG review stays in `apple-design`.

If the product has a user-visible UI, use the `ui-testing` skill: write a UI integration test for the changed flow, then run the existing UI suite (included in the all-tests pass when UI cases live in that suite).

When the product has auth, tenancy, secrets, or private data, follow the Build section of `security-review`.

Return what changed, the test command, and the result.
