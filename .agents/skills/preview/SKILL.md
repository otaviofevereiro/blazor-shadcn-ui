---
name: preview
description: >
  After a visible change set, run the documented local preview and exercise the flow.
  Use when the change can be seen in a running app.
---

# Local preview

**Bind:** always, when the change can be seen in a running app.

A change that can be seen in the running app is incomplete until the project's documented local preview is running and the affected URL is open. Do this once at the end, after edits, tests, and docs — not after every file.

Skip for docs-only, catalog-only, tests-only, git-only, or when the user said not to run.

## How to run

Use the host, port, and command the repo documents (README, `compose.yaml`, or the local-run doc). Do not invent a second way to start the app, and do not take a port from an old terminal or `launchSettings.json` when the docs name a different origin.

1. Check terminals. Do not start a second copy of the same app against the same database.
2. Start or rebuild the documented preview and wait until the documented origin responds.
3. Open the affected route on that origin. Exercise the changed flow in the browser before calling UI work done.

```markdown
# BAD — guess `dotnet run` and a launchSettings port when the repo says Compose
# GOOD — the documented command → wait until the documented origin responds → open that origin + path
```
