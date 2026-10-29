---
name: grill-with-docs
description: Relentless interview to sharpen a plan or design while writing ADRs and glossary terms. Use when the user wants a plan grilled, a design sharpened, or docs created as decisions are made.
---

# Grill With Docs

Interview until the plan is sharp enough to specify. Write the docs as decisions land. Do not wait until the end.

## Grill

Walk the decision tree with the user: constraints, dependencies, the shape of the module, what sits behind the seam, what tests survive. One question at a time when a fork is load-bearing. Do not invent a second design in parallel unless the user asks, or `codebase-design` is in play.

Stop grilling when remaining questions would not change the spec. Then stop. `to-spec` is a later stage.

## Domain model

Keep the domain language current in the same turn a term is named or sharpened:

- A module named after a concept not in `CONTEXT.md` → add the term. Create the file if it does not exist.
- A fuzzy term gets a precise meaning → update `CONTEXT.md` there.
- The user rejects a candidate with a reason a future explorer would need → offer an ADR. Skip ephemeral reasons ("not now") and self-evident ones.

ADRs go where the repo already keeps them (`docs/adr/` if that exists). Glossary terms go in `CONTEXT.md`.
