<!-- ia -->
# Agent instructions

This catalog is host-agnostic. `AGENTS.md` is always-on. Skills live in `.agents/skills/`.

Read `.agents/skills/prompt-token-efficiency/SKILL.md` before writing or rewriting a prompt.

A new implementation is one **initiative**. Classify it before any gate: **bug** (behavior is wrong), **feat** (new behavior), **improvement** (behavior stays; quality, cost, or structure changes). If more than one fits, ask. Do not pick.

Not an initiative: a typo, comment, formatting, or lockfile → edit only. A named skill → run that skill. Do not open either pipeline.

## Pipelines

Open a gate only when skipping it would change the result. Run one open gate at a time. Finish it before the next. Read that gate's skills, do the work, then start the next gate.

**Scope** (this session): `discover → design → usability → spec → tickets`

**Implementation** (one agent per issue): `implement or fix → review → discuss → adjust → qa → document`

| Gate | Open when | Skills |
|---|---|---|
| Discover | Every initiative | `research`, `grill-with-docs` |
| Design | A seam, module shape, or screen is undecided | `codebase-design`; `architecture` if layered; `frontend-design` if a screen is in scope |
| Usability | A user-visible flow is new or reshaped | see Usability |
| Spec | New behavior or contract, and no spec | `to-spec` |
| Slice | More than one demoable slice | `to-tickets` |
| Implement | Class is feat or improvement | `implement`, `tdd`, `testing`, matching binds; `frontend-design` when UI files change; `ui-testing` writes the case and does not run it |
| Fix | Class is bug | `fix`, `tdd`, `testing`, matching binds; `frontend-design` when UI files change. Same UI-test rule as Implement |
| Review | Behavior, contract, or structure changed | `code-review` |
| QA | User-visible behavior, or the spec has acceptance checks | `testing`; `preview` if the change is visible in a running app. Project tests only |
| Document | Behavior, stack, CI, API, or catalog docs would be wrong | see Document |

## Manual

A gate does not open these. Run one only when the user names it.

| Skill | Call when |
|---|---|
| `improve-codebase-architecture` | The user asks for an architecture review or a deepening scan |
| `security-review` | The user asks for a security review |
| `performance` | The user asks for a performance review of a named cost |
| `apple-design` | The user asks for an HIG review or design feedback on a screen |

Discover always runs `research` and `grill-with-docs` in this session. That pass defines the initiative scope. Search the repo there before other questions.

Design stops at seams, states, and the visual plan. It does not write product code. Usability reviews that plan. It does not restyle it.

Review is read-only. Fixed point: the merge-base of `HEAD` and the repo's default branch. No new commits → `git diff HEAD`.

Discuss stays in that agent's session. One heading per review gate. Do not merge findings across gates. Ask which findings to apply. Skip discuss when the review returned no findings.

Adjust uses `tdd` on accepted findings only, then re-runs only the gates that owned an accepted finding. Stop when that re-run is clean or the user stops.

### Agentic development

After the scope pipeline, ask whether to start agentic development.

If yes, use one branch for the initiative. Run one agent per issue, in blocker order, one at a time. A bug issue uses Fix. A feat or improvement issue uses Implement. Each agent runs the implementation pipeline for that issue only.

Each agent commits only the files it changed in its own context. Leave every other change unstaged.

After the last issue, ask once whether to run UI tests. Run all project tests. If the user agreed, run the UI suite once. When that run is green, push the branch and open one PR for the initiative. A red suite is fixed and re-run before the PR.

Outside agentic development, commit only when the user asked.

### Update a product repo

When this catalog changed and a product repo should receive it, run one agent after the initiative. That agent does not edit the product's `.agents/skills/`. It runs the installer from this checkout. Ask for the target path when it is not already known, then run:

```bash
bash ./scripts/setup.sh -Target ~/src/my-app
```

```powershell
.\scripts\setup.ps1 -Target C:\src\my-app
```

The same command updates an existing install. Omit `-Skills` to keep the bind skills in `ia-lock.json`. Add `-Skills csharp,blazor` only for bind skills the product needs. `-All` installs every skill.

### Usability

Read-only. Judge the path, labels, empty state, error recovery, and focus against the Build floor of `frontend-design`. Do not restyle and do not write code. An HIG review is the manual skill `apple-design`.

### Document

Update the matching document in the same task.

- Product or domain behavior → the product or domain doc
- Stack, tests, CI, local run → the engineering or setup doc
- Product-specific agent constraints → product docs, and product `AGENTS.md` outside any `<!-- ia -->` block
- Catalog skills (`.agents/skills/**` when `ia-lock.json` is present) → edit this catalog, then the update agent runs the installer in Update a product repo. Do not edit installed copies
- This tree is the ia catalog → edit `.agents/skills/` here and keep `catalog.json` and `docs/rules.md` in agreement
- A shipped-vs-planned status doc → refresh it
- Skip docs for comment-only, formatting, lockfile, or a refactor that does not change behavior, contracts, or locked choices
- A new decision is written in the doc with the change

## Bind

Read the skill files that bind. Do not apply a stack skill this repo does not have.

**Always** (every task):

- `.agents/skills/verify-before-writing/SKILL.md`
- `.agents/skills/scope-and-tone/SKILL.md`

**When the product has this shape:**

- GitHub Actions workflows, a Dockerfile, a Compose file → `.agents/skills/github/SKILL.md`
- test projects in play, user-visible UI → `.agents/skills/testing/SKILL.md`
- Playwright is the UI harness → `.agents/skills/playwright/SKILL.md`
- layered Web / Application / Domain / Infrastructure → `.agents/skills/architecture/SKILL.md`
- user-visible running app → `.agents/skills/preview/SKILL.md`

**When these files are in play:**

- `*.cs` → `.agents/skills/csharp/SKILL.md`
- `*.razor` → `.agents/skills/blazor/SKILL.md`
- `*.yml` / `*.yaml` / `*.bicep` → `.agents/skills/azure-cicd/SKILL.md`

Skills win on their topic. Do not contradict them here.

## Specify and slice

`to-spec` publishes a spec. `to-tickets` breaks it into **tracer-bullet** vertical slices: each slice is demoable, sized for one fresh context window, and lists the tickets that **block** it. Prefactor first. Wide refactors are expand–contract, not tracer bullets.

Quiz the user on granularity and blockers before publishing tickets. Work the **frontier**: tickets whose blockers are done.

If the issue tracker and triage labels are not in `docs/agents/issue-tracker.md` or this conversation, ask before publishing. Local fallback: `.scratch/<feature-slug>/issues/`, one file per ticket.
<!-- /ia -->

