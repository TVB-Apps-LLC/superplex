---
name: writing-plans
description: Use when you have an approved spec or requirements for a multi-step task, before touching code
---

# Writing Plans

Write a decision-complete implementation plan. Organize work into dependency-ordered pull requests that are independently understandable, testable, reviewable, and mergeable.

**Announce at start:** "I'm using the writing-plans skill to create the implementation and test plans."

## Required Order

1. Read the approved spec completely. Extract requirements, user flows, affected boundaries, dependencies, constraints, and non-goals.
2. Explore the repository. Confirm real file paths, existing interfaces, test runners, CI checks, fixtures, and conventions.
3. Think through the work as a dependency graph. Identify foundational contracts, producers, consumers, integration points, operational work, and end-to-end flows.
4. Create a PR map before writing task details. Use sequential PRs by default; use stacked PRs only when the repository explicitly supports them.
5. Decide whether the change is non-trivial enough to require a separate test plan. When it is, write the companion test plan before finalizing implementation tasks.
6. Break each PR into bite-sized implementation tasks. Multiple tasks may belong to one PR. Tasks are execution units, not review gates.
7. Self-review the plan against the spec, dependency order, test plan, PR boundaries, and repository rules.

## PR Decomposition

For every candidate PR, ask:

- Can a reviewer understand the PR without reading unrelated future work?
- Does it leave the repository in a working, testable state?
- Does it establish or consume a stable interface rather than reaching across unfinished work?
- Is the review surface cohesive enough to receive one code-quality and spec-compliance verdict?
- Are its tests sufficient to prove its own behavior?
- What is deliberately excluded from this PR?

Prefer the smallest coherent slices that preserve working behavior. Common ordering is foundational contracts or pure logic → persistence and authorization → API/service behavior → UI or operational integration → E2E, documentation, and release validation. Adapt this to the actual dependency graph; never split solely by layer when that would create a broken intermediate state.

## Plan Documents

Save the implementation plan to:

```text
docs/superplex/plans/YYYY-MM-DD-<feature-name>.md
```

For non-trivial work, also save:

```text
docs/superplex/plans/YYYY-MM-DD-<feature-name>-test-plan.md
```

The implementation plan must link to the approved spec and companion test plan when present. The test plan must link back to the implementation plan.

## Implementation Plan Header

Every implementation plan starts with:

```markdown
# [Feature Name] Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superplex:subagent-driven-development (recommended) or superplex:executing-plans. Work is reviewed and merged by pull request, not by individual task.

**Goal:** [one sentence]

**Spec:** [exact path]

**Test plan:** [exact path, or `Not required — rationale: ...`]

**Architecture:** [2–3 sentences]

**Tech Stack:** [key technologies]

## Global Constraints

[Exact requirements that bind every PR]

## PR Map

[Dependency-ordered PR table or graph]
```

## PR Entry

Each PR entry must include all of the following:

```markdown
### PR 1: [Title]

**Purpose:** [user-visible or architectural outcome]
**Prerequisites:** None | PR N
**Target:** [base branch] ← [feature branch]
**Spec:** [exact path]
**Implementation plan:** [exact path]
**Test plan:** [exact path]
**Owned paths:** [files/modules]
**Produces:** [interfaces or contracts later PRs may consume]
**Verification:** [exact commands and expected results]
**Non-goals:** [explicit exclusions]
**Risks and rollback:** [migration, compatibility, deployment, and rollback notes]

#### Tasks

[Task 1, Task 2, ... using the task format below]
```

Every PR must be independently testable before it is opened. The PR body must repeat the spec, implementation-plan, and test-plan paths so later reviewers have durable context.

## Test Plan

Create a separate test plan when the feature has multiple user flows, meaningful integration risk, database or authorization behavior, deferred E2E work, or more than one PR. Keep simple one-PR changes inline only when the coverage reasoning is genuinely short.

The test plan must contain:

- Requirement-to-test traceability.
- Unit and component cases.
- Integration, API, contract, and database cases.
- Error, authorization, migration, rollback, and compatibility cases where relevant.
- E2E flows, fixtures, seed data, and environment requirements.
- Exact commands and expected verification artifacts.
- Known gaps, deferred cases, and the PR that owns each gap.
- A final matrix mapping every acceptance criterion and test case to its owning PR.

For Fourplex, use `tests/manifest/` as the test-case source of truth. Add cases there, validate with `pnpm test:cases:validate`, and regenerate `Docs/test-cases/` with the repository command; never hand-edit generated test-case Markdown. Earlier PRs should cover behavior with unit, integration, database, or contract tests. A final E2E PR may convert manifest cases from `not-yet-automated` to `automated`, add Playwright specs and Allure IDs, and regenerate the rendered documentation.

## Task Format

Tasks are implementation steps inside a PR. Each task must be independently understandable and testable:

```markdown
#### Task 1: [Component]

**Files:**
- Create: `exact/path`
- Modify: `exact/path:line`
- Test: `exact/path`

**Consumes:** [earlier PR contract or existing interface]
**Produces:** [interface used by later tasks or PRs]

- [ ] **Step 1: Write the failing test**
- [ ] **Step 2: Run it and verify the expected failure**
- [ ] **Step 3: Implement the minimum behavior**
- [ ] **Step 4: Run the focused and required PR verification**
- [ ] **Step 5: Commit with a clear message**
```

Do not use placeholders such as `TBD`, `TODO`, "add appropriate validation," or "write tests for the above." Include exact files, interfaces, commands, and expected outcomes.

## Self-Review

Before handing off the plan:

1. Map every spec requirement to a PR and verification artifact.
2. Confirm PR prerequisites form a valid dependency order with no cycles.
3. Confirm every PR is independently testable and has explicit non-goals.
4. Confirm every task belongs to exactly one PR.
5. Confirm all spec, implementation-plan, and test-plan paths are present.
6. Search for placeholders, contradictions, missing rollback notes, and unowned test cases.

Fix issues inline before presenting the plan.

## Execution Handoff

After saving the documents, offer:

**"Plans complete and saved to `<implementation-plan>` and `<test-plan-or-N/A>`. Use subagent-driven-development to execute the PRs sequentially, or executing-plans for inline execution. Which approach?"**

When execution begins, the controller must complete and merge the current PR review loop before starting a dependent PR.
