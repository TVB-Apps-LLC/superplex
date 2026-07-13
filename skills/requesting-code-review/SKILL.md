---
name: requesting-code-review
description: Use when a pull request is ready for review, after completing a PR-sized change, or before merging
---

# Requesting Pull Request Review

Request one whole-PR review covering spec compliance, code quality, and test coverage. A PR may contain multiple implementation tasks; task completion is not a review gate.

**Core principle:** Review the cohesive PR before feedback cascades. Give the reviewer a complete, self-contained evidence package rather than controller conversation history.

## When to Request Review

Request review:

- After all tasks assigned to the current PR are complete.
- After the PR-specific verification matrix passes.
- After opening or updating the PR with durable links to the spec, implementation plan, and test plan.
- Again after any fix push, using the receiving-code-review loop.

Do not request a merge-readiness verdict for an unfinished task or a partial PR.

Optional focused reviews remain valuable when stuck, before a risky refactor, or after a complex bug fix. They diagnose a named concern only; they do not replace the required full-PR review and verdict.

## Required Reviewer Context

Dispatch a reviewer subagent with:

- Repository and PR URL/number.
- Base and head revisions.
- Approved spec path.
- Implementation-plan path.
- Test-plan path, or the documented reason there is none.
- The current PR entry, acceptance criteria, non-goals, and prerequisites.
- Verification commands and their fresh results.

Use the template at `code-reviewer.md`. Select the least expensive model capable of the review; use a frontier model for subtle architecture, security, concurrency, or broad integration risks. Always specify the model explicitly.

Obtain and record revisions explicitly:

```bash
BASE_SHA=<verified PR base SHA>
HEAD_SHA=$(git rev-parse HEAD)
git diff --stat "$BASE_SHA".."$HEAD_SHA"
```

Use the PR base SHA, not `HEAD~1`: PRs can contain many commits.

## Required Verdicts

The reviewer must return separate verdicts for:

1. Spec compliance.
2. Code quality and maintainability.
3. Test coverage and verification evidence.

Each finding is Critical, Important, or Minor and includes file/line evidence, impact, and a suggested fix where useful. The reviewer must state either **Ready for PR feedback loop** or **Not ready**.

## After Review

- Critical and Important findings block the PR feedback loop until addressed.
- Minor findings are recorded in the PR ledger and carried into the next review if they remain relevant.
- Dispatch or continue the persistent PR feedback subagent from `../receiving-code-review/pr-feedback-agent.md` to process GitHub comments, implement valid fixes, answer invalid feedback, resolve addressed threads, and monitor the PR until the merge gate is satisfied.
- Do not claim the PR is ready based solely on the reviewer report; verify the remote PR state, checks, reactions, reviews, and threads.

## Finding Discipline

- Fix Critical findings immediately and re-run their covering verification.
- Fix Important findings before the PR can proceed through the feedback loop.
- Record Minor findings in the PR ledger with their disposition; do not silently lose them.
- If a reviewer is wrong, push back with code, tests, or spec/plan evidence. If the point cannot be resolved from available evidence, ask a precise clarifying question.

## Workflow Integration

| Workflow | Review behavior |
| --- | --- |
| `subagent-driven-development` | One whole-PR review after the PR's tasks and verification matrix complete. |
| `executing-plans` | One whole-PR review at each PR boundary. |
| Ad-hoc development | Request a full review before opening/merging a PR-sized change. |

## Red Flags

Never skip the PR review because a change seems simple, ignore Critical findings, proceed with unresolved Important findings, or ask a reviewer to ignore a possible issue before it evaluates the evidence.
