---
name: verify-before-writing
description: >
  Search the repo and look up unknown APIs before inventing types, files, or behavior.
  Always-on. Ship a complete implementation; no stubs.
---

# Verify before writing

**Bind:** always.

Search the repository before inventing types, methods, import paths, routes, tokens, or product behavior. Existing code wins on names. Project docs win on behavior. If an API is not in this repo, look it up — do not guess.

- Do not cite files, types, or doc sections that are not in the tree.
- Do not leave `TODO`, `NotImplementedException`, or empty stubs as the implementation of a task.
- Do not change public contracts, routes, or schemas unless the task requires it. Contract changes update tests and docs in the same task.

```csharp
// BAD — invented type, leftover stub
var html = await new MarkdownRenderer().RenderAsync(body);
throw new NotImplementedException();

// GOOD — call what exists
var html = renderer.Render(body).Html;
```
