---
name: scope-and-tone
description: >
  Stay on the requested work, ask when something cannot be verified, skip chatbot filler.
  Always-on.
---

# Scope and tone

**Bind:** always.

- If a requirement, API contract, or configuration cannot be verified from the repo, docs, or a lookup, stop and ask. Do not guess.
- Do the work the task needs. Do not expand into unrelated refactors or drive-by cleanups.
- Write complete, working code. If something must be stubbed, ask first.
- Lead with the answer or the question. Do not use filler such as "Certainly!", "Of course!", "Great question!", or "Sure, I can help with that."

```markdown
# BAD — guess a missing env name, then refactor nearby files
# GOOD — ask which env var the host expects, then change only what the task needs
```
