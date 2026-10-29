---
name: code-review
description: Review changes since a fixed point along three axes, Standards, Spec, and Excess, in that order in this session. Use when the user wants to review a branch, a PR, work-in-progress changes, or asks to review since a commit, branch, or tag.
---

# Code Review

Three-axis review of the diff between `HEAD` and a fixed point the user supplies:

- **Standards**: does the code conform to this repo's documented coding standards?
- **Spec**: does the code faithfully implement the originating issue / spec?
- **Excess**: does the diff carry more than the change needs? Dead code, unnecessary complexity, and over-fixes.

Run the three axes in this session, in order: Standards, then Spec, then Excess. Do not spawn a sub-agent. Finish one axis and record its report before starting the next. The checklists below give each finding one owner, so the same hunk is not scored twice.

The issue tracker should have been provided to you. If `docs/agents/issue-tracker.md` is missing, ask the user how this repo tracks issues.

## Process

### 1. Pin the fixed point

If the caller named a fixed point (a commit SHA, branch name, tag, or rev), use it. Capture `git diff <fixed-point>...HEAD` (three-dot, against the merge-base) and `git log <fixed-point>..HEAD --oneline`. Confirm the ref resolves (`git rev-parse <fixed-point>`).

If the caller says the diff is uncommitted, review `git diff HEAD` (staged and unstaged). Label the report uncommitted. Skip `git rev-parse`.

If neither a named ref nor an uncommitted diff was given, ask for the fixed point.

A bad ref or an empty diff stops here, before the three axes.

### 2. Identify the spec source

Look for the originating spec, in this order:

1. Issue references in the commit messages (`#123`, `Closes #45`, GitLab `!67`, etc.), fetched via the workflow in `docs/agents/issue-tracker.md`.
2. A path the user passed as an argument.
3. A spec file under `docs/`, `specs/`, or `.scratch/` matching the branch name or feature.
4. If nothing is found, ask the user where the spec is. If they say there isn't one, skip the **Spec** axis and report "no spec available". **Excess** still runs: it uses the commit list and whatever problem statement exists. With no problem statement, it reports dead code and unnecessary complexity only, and says over-fixes were not judged.

The Spec axis also checks that all tests in the current project were run after the change (see `testing`). If the product has a user-visible UI, it also checks that a UI integration case covers the changed flow and that the existing UI suite was in scope (see `ui-testing`).

### 3. Identify the standards sources

Anything in the repo that documents how code should be written, such as `CODING_STANDARDS.md` or `CONTRIBUTING.md`.

On top of whatever the repo documents, the Standards axis always carries the **smell baseline** below: a fixed set of Fowler code smells (_Refactoring_, ch.3) that applies even when a repo documents nothing. Two rules bind it:

- **The repo overrides.** A documented repo standard always wins; where it endorses something the baseline would flag, suppress the smell.
- **Always a judgement call.** Each smell is a labelled heuristic ("possible Feature Envy"), never a hard violation. Like any standard here, skip anything tooling already enforces.

Each smell reads *what it is* → *how to fix*; match it against the diff:

- **Mysterious Name**: a function, variable, or type whose name doesn't reveal what it does or holds. → rename it; if no honest name comes, the design's murky.
- **Duplicated Code**: the same logic shape appears in more than one hunk or file in the change. → extract the shared shape, call it from both.
- **Feature Envy**: a method that reaches into another object's data more than its own. → move the method onto the data it envies.
- **Data Clumps**: the same few fields or params keep travelling together (a type wanting to be born). → bundle them into one type, pass that.
- **Primitive Obsession**: a primitive or string standing in for a domain concept that deserves its own type. → give the concept its own small type.
- **Repeated Switches**: the same `switch`/`if`-cascade on the same type recurs across the change. → replace with polymorphism, or one map both sites share.
- **Shotgun Surgery**: one logical change forces scattered edits across many files in the diff. → gather what changes together into one module.
- **Divergent Change**: one file or module is edited for several unrelated reasons. → split so each module changes for one reason.
- **Message Chains**: long `a.b().c().d()` navigation the caller shouldn't depend on. → hide the walk behind one method on the first object.
- **Middle Man**: a class or function that mostly just delegates onward, and the change needs that seam. → cut it, call the real target direct. If the requested behavior does not need the layer at all, that is Excess (unnecessary complexity), not this smell.
- **Refused Bequest**: a subclass or implementer that ignores or overrides most of what it inherits. → drop the inheritance, use composition.

**Not on this axis.** Speculative generality — abstraction, parameters, or hooks the change does not need — is unnecessary complexity on Excess. Dead code and over-fixes are Excess too. Unrequested product or contract behavior is Spec scope creep.

### 4. Run the three axes in order

Run Standards, then Spec, then Excess in this session. Do not spawn a sub-agent. Finish one axis and record its report before starting the next. Do not let one axis's findings change another's.

**Standards** uses:

- The full diff command and commit list.
- The list of standards-source files found in step 3, plus the smell baseline from step 3 in full.
- Report, per file/hunk where relevant, (a) every place the diff violates a documented standard: cite the standard (file + the rule); and (b) any baseline smell you spot: name it and quote the hunk. Distinguish hard violations from judgement calls: documented-standard breaches can be hard, but baseline smells are always judgement calls, and a documented repo standard overrides the baseline. Skip anything tooling enforces. Do not report dead code, unnecessary complexity, or over-fixes; Excess owns those. If a layer exists only because the change added structure the behavior does not need, leave it to Excess. Under 400 words.

**Spec** uses:

- The diff command and commit list.
- The path or fetched contents of the spec.
- Report: (a) requirements the spec asked for that are missing or partial; (b) behaviour or contract in the diff that the spec did not ask for (scope creep); (c) requirements that look implemented but where the implementation looks wrong. Scope creep is unrequested product or contract behavior only. Do not report dead code, extra structure, or a wider fix that does not add behavior; Excess owns those. Also report when all tests in the current project were not run after a code change. If the product has a user-visible UI, also report a missing UI integration case for the changed flow or a skipped UI regression suite. Quote the spec line for each finding. Under 400 words.

**Excess** uses:

- The full diff command and commit list.
- The problem statement when one exists: the spec path or its contents, otherwise the commit subjects. If there is no problem statement, say so and skip over-fixes.
- Report only surplus in the diff, in three kinds, each a judgement call. (a) Dead code: symbols, branches, comments, or files this change adds or leaves unused. (b) Unnecessary complexity: types, layers, parameters, hooks, or indirection the requested behavior does not need. This includes what used to be called speculative generality. A pass-through whose only fault is that it forwards, while the seam is needed, is Middle Man on Standards; report it here only when the behavior does not need the layer. (c) Over-fixes: repairs, guards, refactors, or generalizations past the stated problem that do not add a new product or contract behavior. If the surplus is itself a new behavior or contract, leave it to Spec. Do not report missing or wrong requirements, documented-standard breaches, or the other Fowler smells. Quote the hunk. Under 400 words.

If the spec is missing, skip Spec and note this in the final report. Still run Excess.

### 5. Aggregate

Present the reports under `## Standards`, `## Spec`, and `## Excess` headings, verbatim or lightly cleaned. Do **not** merge or rerank findings, because the axes are deliberately separate (see _Why three axes_).

End with a one-line summary: total findings per axis, and the worst issue _within each axis_ (if any). Don't pick a single winner across axes: that's the reranking the separation exists to prevent.

## Why three axes

A change can pass one axis and fail another:

- Code that follows every standard but implements the wrong thing → **Standards pass, Spec fail.**
- Code that does exactly what the issue asked but breaks the project's conventions → **Spec pass, Standards fail.**
- Code that matches the spec and the conventions, but the diff is larger or harder than the change needs → **Standards pass, Spec pass, Excess fail.**

Reporting them separately stops one axis from masking the other.
