---
name: performance
description: >
  Read-only performance review for a named cost: time, memory, size, or queries.
  Use when a diff adds a query, a loop over growing input, a payload, or a render path,
  or when load time, Core Web Vitals, or a slow path is in question.
  Report measured regressions. Label static findings as hypotheses. Do not change code.
  Standards, spec, and excess stay in code-review.
license: MIT. Complete terms in LICENSE.txt
---

<!-- Consolidated from https://github.com/addyosmani/web-quality-skills/blob/main/skills/performance/SKILL.md (913a36c, MIT) and https://github.com/addyosmani/agent-skills/blob/main/skills/performance-optimization/SKILL.md (MIT). Host-specific browser-tool names dropped. Repo budget wins over starting guardrails. -->

# Performance

This catalog: this pass reports. `code-review` owns standards, spec, and excess. An accepted finding is applied later, then re-measured. Do not edit in this pass.

Measure before you call a change slow. A guess is not a regression.

## Review

1. Name one cost: time, memory, size, or queries. Use the repo's budget or SLA when it has one.
2. If the surface can run, record a baseline before judging. A later compare uses the same command, the same conditions, and a warm or cold cache on both sides. For a page, field p75 (CrUX or the repo's RUM) is pass/fail. A lab trace or query plan explains the cause. A Lighthouse score is not pass/fail.
3. If it cannot run, call static findings **hypotheses**. Name the command or trace that would verify each one.
4. Report a measured regression, or a hypothesis tied to a hunk in the diff. Do not report style. Do not edit.

## Starting guardrails

Use these only when the repo has no budget. They are not a universal fail. A typical content or commerce page is the reference for the web rows.

| Cost | Starting guardrail |
|---|---|
| Total page weight | < 1.5 MB |
| JavaScript, compressed | < 300 KB |
| CSS, compressed | < 100 KB |
| Above-fold images | < 500 KB |
| Fonts | < 100 KB |
| Third-party | < 200 KB |
| TTFB | < 800 ms |
| API time | p95 < 200 ms |

Field Core Web Vitals, p75:

| Metric | Good | Poor |
|---|---|---|
| LCP | ≤ 2.5 s | > 4.0 s |
| INP | ≤ 200 ms | > 500 ms |
| CLS | ≤ 0.1 | > 0.25 |

Between Good and Poor is needs improvement. Lab TBT is a proxy for INP, not field INP.

## What a finding can be

Web, when the diff touches the page:

- LCP image missing `fetchpriority="high"`, or an image missing width and height.
- Render-blocking CSS or a parser-blocking script on the first load.
- A preload that a trace does not show as late.
- A layout read and write inside one loop.
- A list that grows with no bound.
- A third-party script on the critical path.

Server, when the diff touches data access:

- One query per row.
- A list with no limit.
- An index with no plan before and after.
- A cache key that omits tenant, locale, or viewer.
- A cache of a value that must be fresh (balance, permission, checkout inventory).
- A larger pool while sessions wait for a connection.

## Report

- Cost.
- Baseline: source, conditions, and number. Or "no baseline".
- Kind: measured or hypothesis.
- Evidence: the number, the plan, or the hunk.
- The repo budget, when one exists.

## When a finding is accepted

Change that one bottleneck, then re-measure the same way. Keep a change only when it beats run-to-run noise and tests stay green. A neutral or worse result is a revert. Do not bundle several optimizations into one measurement. Record the attempt, including a revert, where the repo already keeps that note.
