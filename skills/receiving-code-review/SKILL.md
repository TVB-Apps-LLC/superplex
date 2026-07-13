---
name: receiving-code-review
description: Use when a pull request receives review comments, requested changes, reactions, or failed checks that must be evaluated before merge
---

# Receiving Pull Request Review

Use one persistent PR feedback subagent to own the review lifecycle from the first review round until the PR is merged or explicitly blocked. The subagent evaluates feedback, makes valid fixes, answers invalid feedback, resolves addressed threads, and monitors remote state.

**Core principle:** Technical correctness over performative agreement. Review feedback is input to verify against this codebase, linked spec, plan, tests, and current PR head.

## Response Pattern

For each new feedback item, follow this order before changing code:

1. **Read:** Capture the complete review, parent thread, diff context, and linked evidence.
2. **Understand:** Restate the technical requirement in the PR ledger or ask a clarifying question when its meaning is uncertain.
3. **Verify:** Check the relevant code, spec, implementation plan, test plan, contracts, and compatibility constraints.
4. **Evaluate:** Classify the item as valid, invalid, obsolete, or out of scope.
5. **Respond:** Make a factual acknowledgement or reasoned pushback on the relevant thread.
6. **Implement:** For valid feedback, make the smallest correct fix, test it, push it, and re-sweep the PR.

Do not answer with performative agreement such as “You’re absolutely right” or “Great point.” State the technical outcome, evidence, or clarifying question instead.

## Ambiguous Feedback

If any actionable item is unclear, stop before implementing that item. Read the whole thread, identify exactly what is ambiguous, and ask for clarification on the relevant GitHub thread or from the human partner when the ambiguity is architectural. Do not implement only the convenient subset of a coupled review request while guessing at the rest.

## Source-Aware Evaluation

| Source | Default handling |
| --- | --- |
| Human partner | Implement after understanding the requested outcome; ask when scope or intent is unclear. |
| External reviewer or bot | Verify against this repository before accepting. Check current behavior, compatibility, constraints, and whether the reviewer has full context. |
| Prior feedback on an old head | Mark obsolete only after comparing it to the current head; record why it no longer applies. |

When a request conflicts with an approved architectural decision, stop and surface the conflict rather than silently overriding either source.

## Scope and Pushback

Apply a YAGNI check to feedback that asks for a larger “proper” system, abstraction, metric suite, or feature: search the codebase and linked plan for a concrete consumer first. If there is no supported requirement or consumer, explain the out-of-scope decision with the relevant spec/plan reference.

Push back when the suggestion breaks existing behavior, conflicts with compatibility or security constraints, lacks context, adds unused scope, or is technically incorrect for this repository. Be specific: cite code, tests, the spec, or plan. If a prior pushback was wrong, correct it factually, state what changed in the evidence, then implement the valid fix.

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

For multiple valid findings, resolve them in risk order: blockers, security/data-loss/correctness issues, simple isolated fixes, then larger refactors. Run focused covering verification after each fix and the PR matrix before declaring the updated head ready. Warnings or noisy test output are findings when they undermine the claimed verification.

Reply to inline feedback in that inline thread, not as a top-level PR comment. Use the repository’s GitHub integration or the PR-comment reply endpoint; top-level comments are for top-level feedback only. Resolve a thread only after the fix or technical answer is visible on the current PR.

Use the repository's available GitHub integration or `gh` commands. Fourplex documents these operations as `gh pr checks <number> --watch`, `gh pr view <number> --comments`, `gh api .../pulls/<number>/comments`, review-state inspection, failed-run logs, and thread resolution through the GitHub API. If a required permission or API operation is unavailable, record the exact blocker and stop rather than claiming completion.

## Reaction and Merge Gates

- An eyes reaction on the PR description means a review is still in progress.
- A thumbs-up reaction is required for sign-off.
- Sign-off applies only to the current head SHA. Any new push invalidates prior sign-off and requires a fresh review signal.
- Merge is allowed only when the current head has thumbs-up sign-off, no eyes reaction, no pending review or `CHANGES_REQUESTED` state, no unresolved actionable threads, no new comments or reviews since the latest sweep, and all required checks pass.
- When all gates pass, squash-merge automatically without deleting the branch. Preserve the branch, worktree, and ledger until post-merge validation and the complete task/PR sequence are finished. Stop before merge if the PR requires new secrets or variables in Vercel, GitHub, Trigger.dev, or another cloud tool; resume the same ledger afterward.

## Common Mistakes

- Replying without resolving the addressed thread.
- Treating a stale thumbs-up as approval for new commits.
- Ignoring top-level comments because inline threads were checked.
- Merging while an eyes reaction or pending review remains.
- Calling feedback invalid without checking the spec, implementation plan, and code.
- Claiming a PR is complete without fresh check, review, reaction, and thread evidence.
- Implementing an unclear multi-part request piecemeal instead of clarifying the coupled requirement.
- Responding to an inline review as a top-level comment.
- Treating a reviewer’s stated rationale as proof rather than checking the code and linked plans.
