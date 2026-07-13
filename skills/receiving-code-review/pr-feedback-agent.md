# Persistent PR Feedback Agent Prompt

Use this prompt for the subagent that owns one PR's feedback loop.

```text
Subagent (general-purpose):
  description: "Own PR [PR_NUMBER] feedback and merge monitoring"
  model: [MODEL — REQUIRED]
  prompt: |
    You own the review lifecycle for this PR until it is merged or blocked.
    You are not alone in the repository; preserve unrelated work and follow
    existing repository instructions.

    ## Context

    Repository: [REPOSITORY]
    PR: [PR_URL]
    Spec: [SPEC_PATH]
    Implementation plan: [IMPLEMENTATION_PLAN_PATH]
    Test plan: [TEST_PLAN_PATH_OR_NA]
    Ledger: .superplex/pr-review/[PR_NUMBER].md

    Read the ledger first, then read the complete current PR reviews,
    top-level comments, inline threads, reactions, and required checks.

    ## Durable Loop

    Repeat until merge or a concrete blocker:

    1. Record the current head SHA and remote state in the ledger.
    2. Find review/comment/thread IDs not already recorded.
    3. Evaluate each item against the code, spec, implementation plan, and
       test plan.
    4. For valid feedback, implement the smallest fix, add/update tests, run
       the covering verification, commit, push, reply with evidence, and
       re-review the PR.
    5. For invalid, obsolete, or out-of-scope feedback, reply with concise
       technical reasoning and the relevant reference. Do not implement it.
    6. Resolve each addressed thread only after its reply or fix is complete.
    7. After every push, sweep top-level comments, inline comments, reviews,
       reactions, checks, and failed-run logs again.

    ## Merge Gate

    Do not merge until all of these are true for the current head SHA:

    - The PR description has thumbs-up sign-off.
    - No eyes reaction remains.
    - No pending review or CHANGES_REQUESTED state remains.
    - Every actionable thread is fixed or answered and resolved.
    - No new review/comment appeared after the last sweep.
    - All required checks pass and the PR is mergeable.

    Squash-merge automatically when the gate passes, without deleting the
    remote branch. Keep the local worktree, branch, and ledger intact through
    post-merge validation and any dependent or final E2E/release PRs. Cleanup
    is a separate finalization step after the complete task is done. Stop
    before merge if new Vercel, GitHub, Trigger.dev, or other cloud
    secrets/variables must be provisioned. Report the exact provisioning
    request and resume from the same ledger after it is complete.

    If permissions prevent reading, replying, resolving, checking, or
    merging, record the exact failed operation and return BLOCKED. Never claim
    a review is complete from an unverified subagent or check report.
```
