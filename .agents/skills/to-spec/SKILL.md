---
name: to-spec
description: Turn the current conversation into a spec and publish it to the project issue tracker. Use when the user wants a spec from what was already discussed. Do not grill; confirm test seams only when they are not already agreed.
---

# To Spec

This skill takes the current conversation context and codebase understanding and produces a spec. Do not grill the user. Synthesize what you already know. The only question in this skill is the seam confirmation in step 2, and only when those seams are not already agreed.

The issue tracker and triage label vocabulary should have been provided to you. If not, ask the user how this repo tracks issues and which labels to use.

## Process

1. Explore the repo to understand the current state of the codebase, if you haven't already. Use the project's domain glossary vocabulary throughout the spec, and respect any ADRs in the area you're touching.

2. Sketch the seams where the change will be tested. Prefer an existing seam to a new one. Use the highest seam that can see the behavior. Fewer seams are better; the ideal number is one.

If those seams are not already agreed in this conversation, ask the user to confirm them. If they are agreed, keep them.

3. Write the spec using the template below, then publish it to the project issue tracker. Apply the `ready-for-agent` triage label - no need for additional triage.

<spec-template>

## Problem Statement

The problem that the user is facing, from the user's perspective.

## Solution

The solution to the problem, from the user's perspective.

## User Stories

A LONG, numbered list of user stories. Each user story should be in the format of:

1. As an <actor>, I want a <feature>, so that <benefit>

<user-story-example>
1. As a mobile bank customer, I want to see balance on my accounts, so that I can make better informed decisions about my spending
</user-story-example>

This list of user stories should be extremely extensive and cover all aspects of the feature.

## Implementation Decisions

A list of implementation decisions that were made. This can include:

- The modules that will be built/modified
- The interfaces of those modules that will be modified
- Technical clarifications from the developer
- Architectural decisions
- Schema changes
- API contracts
- Specific interactions

Do NOT include specific file paths or code snippets. They may end up being outdated very quickly.

Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it within the relevant decision and note briefly that it came from a prototype. Trim to the decision-rich parts, not a working demo, just the important bits.

## Testing Decisions

A list of testing decisions that were made. Include:

- A description of what makes a good test (only test external behavior, not implementation details)
- Which modules will be tested
- Prior art for the tests (i.e. similar types of tests in the codebase)
- After a change, run all tests in the current project before calling the work done
- If the product has a user-visible UI, a UI integration case for the new flow and a regression pass of the existing UI suite

## Out of Scope

A description of the things that are out of scope for this spec.

## Further Notes

Any further notes about the feature.

</spec-template>
