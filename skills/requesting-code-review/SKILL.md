---
name: requesting-code-review
description: Use when a pull request is ready for review, after completing a PR-sized change, or before merging
---

# Requesting Pull Request Review

Request one whole-PR review covering spec compliance, code quality, and test coverage. A PR may contain multiple implementation tasks; task completion is not a review gate.

## When to Request Review

Request review:

- After all tasks assigned to the current PR are complete.
- After the PR-specific verification matrix passes.
- After opening or updating the PR with durable links to the spec, implementation plan, and test plan.
- Again after any fix push, using the receiving-code-review loop.

Do not request a merge-readiness verdict for an unfinished task or a partial PR.

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
