<!-- ia -->
# Agent instructions

This catalog is host-agnostic. `AGENTS.md` is always-on. Skills live in `.agents/skills/`.

Run every open gate in this session, in order. Do not spawn a sub-agent. Classify the request, open only the gates below, read that gate's skills, do the gate's work, then start the next open gate. A review gate is read-only.

Read `.agents/skills/prompt-token-efficiency/SKILL.md` before writing or rewriting a prompt.

## Gates

Open a gate only when skipping it would change the result. Run the first match:

1. Typo, comment, formatting, or lockfile → edit only.
2. The user named a skill → run that skill in this session. Also open Review, Security, Performance, QA, and Document when their gates match. Do not open earlier gates.
3. Otherwise walk the pipeline. Skip every closed gate.

| Gate | Open when | Role | Skills |
|---|---|---|---|
| Discover | An API, doc, or current behavior is unknown | discover | `research` |
| Design | A seam, module shape, or screen is undecided | design | `codebase-design`; `architecture` if layered; `frontend-design` if a screen is in scope |
| Usability | A user-visible flow is new or reshaped | usability | see Usability |
| Spec | New behavior or contract, and no spec | spec | `to-spec`; `grill-with-docs` while a decision is unsettled |
| Slice | More than one demoable slice | tickets | `to-tickets` |
| Implement | A spec or stated result needs code | implement | `implement`, `tdd`, `testing`, matching binds; `frontend-design` when UI files change; `ui-testing` if the product has UI; Build section of `security-review` if auth, tenancy, secrets, or private data |
| Fix | Expected behavior of a defect is already stated | fix | `tdd`, `testing`, matching binds; `frontend-design` when UI files change. Replaces Discover through Implement |
| Review | Behavior, contract, or structure changed | review | `code-review` |
| Security | Diff touches auth, input, secrets, tenancy, public endpoints, deserialization, or crypto | security | `security-review` |
| Performance | A cost is named, or the change adds a query, a loop over growing input, a payload, or a render path | performance | `performance` |
| QA | User-visible behavior, or the spec has acceptance checks | qa | `testing`; `ui-testing` if the product has UI; `preview` if the change is visible in a running app |
| Document | Behavior, stack, CI, API, or catalog docs would be wrong | docs | none — see Document |

Classify when no stage was named: **bug** (behavior is wrong), **feat** (new behavior), **improvement** (behavior stays; quality, cost, or structure changes). If more than one fits, ask. Do not pick. `improve-codebase-architecture` runs only when the user asked for an architecture review.

Ask effort once when they did not name stages or a time box. A named stage set runs those gates plus any open gate they left out; ask once before dropping an open gate. A time box skips closed gates only.

## Pipeline

`discover → design → usability → spec → tickets → implement or fix → review → security → performance → discuss → adjust → qa → document`

Run one gate at a time. Finish it before the next. Review, security, and performance stay read-only and run in that order. QA runs after adjust. Document runs after QA.

The design gate stops at seams, states, and the visual plan. It does not write product code. The usability gate reviews that plan and does not restyle it.

The review gate names the fixed point: the merge-base of `HEAD` and the repo's default branch. Resolve that branch from the repo. If the work has no new commits, the diff is uncommitted (`git diff HEAD`).

Discuss stays in this session. Present each review gate's findings under its own heading. Do not merge or rerank across gates. Ask which findings to apply. Skip discuss when every open review gate returned no findings.

Adjust uses `tdd`. Its scope is accepted findings only. Then re-run only the gates that owned an accepted finding. Stop when that re-run is clean or the user stops.

Done: every open gate ran and passed, closed gates are named with the reason, project tests are green when code changed, and docs match when the document gate was open.

Search the repo before any question other than classification or effort. Do that search in the discover gate when it is open.

Commit only when the user asked, after the loop is clean.

### Usability

Read-only. Judge the path, labels, empty state, error recovery, and focus against the Build floor of `frontend-design`. Do not restyle and do not write code. Read `apple-design` only when the user asked for an HIG review or the UI is an Apple platform.

### Performance

Read-only. Follow `performance`. Do not change code.

### Document

The document gate updates the matching document in the same task. Do not defer it.

- Product or domain behavior → the product or domain doc
- Stack, tests, CI, local run → the engineering or setup doc
- Product-specific agent constraints → product docs, and product `AGENTS.md` outside any `<!-- ia -->` block
- Catalog skills (`.agents/skills/**` when `ia-lock.json` is present) → edit the ia repository, then reinstall with `scripts/setup`. Do not edit those installed copies in a product repo
- If this tree is the ia catalog, edit `.agents/skills/` here and keep `catalog.json` and `docs/rules.md` in agreement
- A shipped-vs-planned status doc → refresh it
- Skip docs for comment-only, formatting, lockfile, or a pure refactor that does not change behavior, contracts, or locked choices
- A new decision is written in the doc with the change

## Bind

Read the skill files that bind. Do not apply a stack skill this repo does not have.

**Always** (every task):

- `.agents/skills/verify-before-writing/SKILL.md`
- `.agents/skills/scope-and-tone/SKILL.md`

**When the product has this shape:**

- GitHub Actions workflows, a Dockerfile, a Compose file -> `.agents/skills/github/SKILL.md`
- test projects in play, user-visible UI -> `.agents/skills/testing/SKILL.md`
- Playwright is the UI harness -> `.agents/skills/playwright/SKILL.md`
- layered Web / Application / Domain / Infrastructure -> `.agents/skills/architecture/SKILL.md`
- user-visible running app -> `.agents/skills/preview/SKILL.md`

**When these files are in play:**

- `*.cs` -> `.agents/skills/csharp/SKILL.md`
- `*.razor` -> `.agents/skills/blazor/SKILL.md`
- `*.yml` / `*.yaml` / `*.bicep` -> `.agents/skills/azure-cicd/SKILL.md`

Skills win on their topic. Do not contradict them here.

## Specify and slice

`to-spec` publishes a spec. `to-tickets` breaks it into **tracer-bullet** vertical slices: each slice is demoable, sized for one fresh context window, and lists the tickets that **block** it. Prefactor first. Wide refactors are expand–contract, not tracer bullets.

Quiz the user on granularity and blockers before publishing tickets. Work the **frontier**: tickets whose blockers are done.

If the issue tracker and triage labels are not in `docs/agents/issue-tracker.md` or this conversation, ask before publishing. Local fallback: `.scratch/<feature-slug>/issues/`, one file per ticket.
<!-- /ia -->

