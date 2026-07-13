---
name: receiving-code-review
description: Use when a pull request receives review comments, requested changes, reactions, or failed checks that must be evaluated before merge
---

# Receiving Pull Request Review

Use one persistent PR feedback subagent to own the review lifecycle from the first review round until the PR is merged or explicitly blocked. The subagent evaluates feedback, makes valid fixes, answers invalid feedback, resolves addressed threads, and monitors remote state.

## Start a PR Ledger

Create or resume this non-committed file:

```text
.superplex/pr-review/<pr-number>.md
```

Record the repository, PR URL, spec path, implementation-plan path, test-plan path, current head SHA, processed review/comment/thread IDs, check status, eyes/thumbs-up state, open feedback, dispositions, last poll, and next action. Never process an ID already recorded as handled.

Dispatch the persistent subagent using `pr-feedback-agent.md`. It may use a lower-cost model for mechanical comment triage, but use a stronger model when feedback requires architecture, security, concurrency, or cross-PR judgment.

## Feedback Loop

For every new review or comment:

1. Read the complete review context and the relevant spec/plan sections.
2. Decide whether the feedback is valid, invalid, obsolete, or out of scope.
3. For valid feedback, implement the smallest correct fix, add or update tests, run the covering verification, commit, push, and reply with evidence.
4. For invalid or out-of-scope feedback, reply in the relevant thread with concise technical reasoning and a spec/plan reference where useful. Do not change code solely to satisfy invalid feedback.
5. Resolve the thread only after the fix or technical answer is visible and verified.
6. After every push, re-review the updated PR, sweep all top-level and inline comments, and restart the merge gate.
7. Never move to a dependent PR while the current PR has unresolved Critical or Important findings, unresolved actionable threads, failed required checks, or a pending review.

Use the repository's available GitHub integration or `gh` commands. Fourplex documents these operations as `gh pr checks <number> --watch`, `gh pr view <number> --comments`, `gh api .../pulls/<number>/comments`, review-state inspection, failed-run logs, and thread resolution through the GitHub API. If a required permission or API operation is unavailable, record the exact blocker and stop rather than claiming completion.

## Reaction and Merge Gates

- An eyes reaction on the PR description means a review is still in progress.
- A thumbs-up reaction is required for sign-off.
- Sign-off applies only to the current head SHA. Any new push invalidates prior sign-off and requires a fresh review signal.
- Merge is allowed only when the current head has thumbs-up sign-off, no eyes reaction, no pending review or `CHANGES_REQUESTED` state, no unresolved actionable threads, no new comments or reviews since the latest sweep, and all required checks pass.
- When all gates pass, squash-merge automatically unless the PR requires new secrets or variables in Vercel, GitHub, Trigger.dev, or another cloud tool. Stop before merge for that provisioning exception and resume the same ledger afterward.

## Common Mistakes

- Replying without resolving the addressed thread.
- Treating a stale thumbs-up as approval for new commits.
- Ignoring top-level comments because inline threads were checked.
- Merging while an eyes reaction or pending review remains.
- Calling feedback invalid without checking the spec, implementation plan, and code.
- Claiming a PR is complete without fresh check, review, reaction, and thread evidence.
