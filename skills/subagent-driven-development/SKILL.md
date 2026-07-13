---
name: subagent-driven-development
description: Use when executing a dependency-ordered implementation plan with independent tasks and pull requests in the current session
---

# Subagent-Driven Development

Execute an implementation plan one pull request at a time. Use focused implementation subagents for tasks, then review the complete PR for spec compliance, code quality, and test coverage. Task completion is not a review gate.

**Core principle:** fresh implementer context per task, one whole-PR review, persistent PR feedback handling, and a merge gate before dependent work.

## When to Use

Use when an approved implementation plan has a PR map and the work can be executed in the current session with subagent support. Use `executing-plans` when execution belongs in a separate session or subagents are unavailable.

## Process

```text
Load implementation plan and test plan
  → read PR map and create PR-level ledger
  → for each PR in dependency order:
       set up isolated branch/worktree
       dispatch one implementer subagent per task
       answer questions and record task progress
       run the PR test matrix
       open/update PR with spec/plan/test-plan links
       dispatch whole-PR reviewer
       start persistent PR feedback loop
       wait for checks, reactions, reviews, and resolved threads
       squash-merge without deleting branch/worktree unless cloud secret/variable provisioning is required
  → start the next dependent PR only after merge
  → run final E2E/release PR last when the test plan defers it
```

Before starting, inspect `.superplex/sdd/progress.md` and the PR review ledger. Completed PRs and tasks recorded there must not be re-dispatched.

## PR-Level Review Rule

Do not dispatch a code-quality or spec-compliance review after each task. Tasks are implementation units within a PR. After all tasks in the current PR are complete:

1. Generate the complete PR diff package.
2. Run the PR-specific test matrix and required verification.
3. Open or update the PR with these exact links in the body:
   - Approved spec.
   - Implementation plan.
   - Test plan, or the explicit reason there is none.
4. Dispatch the whole-PR reviewer using `../requesting-code-review/code-reviewer.md`.
5. Require separate spec-compliance, code-quality, and test-coverage verdicts.
6. Start `../receiving-code-review/pr-feedback-agent.md` for all remote feedback and merge monitoring.
7. Do not start a dependent PR until this PR's merge gate passes.

## Dispatching Implementers

Use `implementer-prompt.md`. Give each implementer a task brief file, the PR's scene-setting context, earlier PR contracts, exact constraints, and a report path. Each implementer must:

- Follow TDD: write a failing test, verify RED, implement minimally, verify GREEN, then refactor.
- Work only within the task and PR ownership boundaries.
- Commit its work and self-review before reporting.
- Report `DONE`, `DONE_WITH_CONCERNS`, `NEEDS_CONTEXT`, or `BLOCKED`.
- Re-run the tests covering any review fix and record the output in its report.

If an implementer is blocked, provide missing context, split the task, or re-dispatch with a more capable model. Do not silently fix its task in the controller context.

## Model Selection

Use the least powerful model that can handle the role, and always specify it explicitly.

| Work | Default model guidance |
|---|---|
| Mechanical implementation or simple research | Cheap tier: Claude Sonnet, Codex Luna, or Cursor Composer 2.5 |
| Multi-file integration, debugging, or judgment | Standard model |
| Complex implementation, architecture, security, concurrency, or final review | Frontier model |

The mapping is a preference, not a reason to use a weak model for high-risk work. Reviewer selection scales with PR size, complexity, and risk; a small mechanical PR does not require the frontier model.

## Reviewer Prompt Construction

When dispatching the whole-PR reviewer:

- Pass the spec, implementation-plan, and test-plan paths rather than relying on conversation history.
- Pass the PR entry, acceptance criteria, non-goals, verification results, base SHA, and head SHA.
- Do not write open-ended directives such as "check all uses" or "run race tests if useful" without a concrete named risk.
- Do not ask the reviewer to rerun tests already reported for unchanged code; request a focused test only for a specific doubt.
- Do not pre-judge findings or tell the reviewer what not to flag.
- Require file/line evidence for every finding and a clear verdict for all three review dimensions.

## PR Feedback Loop

The persistent feedback subagent owns valid and invalid PR comments. It must evaluate feedback against the code and linked documents, implement and test valid fixes, reply with evidence to invalid feedback, resolve addressed threads, and re-review after every push. It must monitor PR description reactions, review states, top-level comments, inline threads, required checks, and failed runs.

An eyes reaction means review is still in progress. A thumbs-up is required for the current head SHA. Merge requires no eyes reaction, no pending or `CHANGES_REQUESTED` review, no unresolved actionable threads, no new feedback after the latest sweep, and all required checks passing. Squash-merge automatically without deleting the branch or worktree unless new cloud secrets or variables must be provisioned; record that exception and resume from the PR ledger afterward. Preserve the branch, worktree, and ledgers through post-merge validation and all dependent/final PRs. Clean them up only in a separate finalization step after the complete task is done.

## Progress Ledger

Maintain `.superplex/sdd/progress.md` as ignored scratch state. Record one line per completed task and PR, including commit ranges, review verdicts, PR URL, current head SHA, and merge state. The ledger is the recovery map after compaction; trust it and `git log` over memory.

## Parallelism

Do not run multiple implementation subagents against the same PR in parallel. Use `dispatching-parallel-agents` only for genuinely independent research or work with disjoint ownership and no shared state. PRs with dependencies execute sequentially by default.

## Red Flags

Never:

- Start a dependent PR before its prerequisite PR merges.
- Treat a task review as a substitute for whole-PR review.
- Open a PR without spec, implementation-plan, and test-plan links.
- Proceed with Critical or Important PR findings, unresolved actionable threads, failed required checks, or pending reviews.
- Accept a stale thumbs-up after a new push.
- Resolve a thread before its fix or technical response is visible.
- Merge while an eyes reaction remains.
- Claim the PR is complete from an unverified subagent report.
- Delete a branch or worktree immediately after an individual PR merges.
