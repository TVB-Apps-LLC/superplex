---
name: finishing-a-development-branch
description: Use when a pull request has completed its feedback loop and you need to verify merge readiness, merge it, or preserve or discard the branch
---

# Finishing a Development Branch

Finish a PR-oriented branch only after fresh verification of the current remote head, checks, reviews, reactions, and threads.

**Announce at start:** "I'm using the finishing-a-development-branch skill to complete this PR."

**Core principle:** Verify the remote PR gate, preserve the workspace through the complete task lifecycle, and use explicit confirmation for an abort. The normal completion path is a PR merge, not a local merge menu.

## Establish the PR Context

Before opening or finishing a PR, determine the branch and target deliberately:

```bash
BRANCH=$(git branch --show-current)
BASE_SHA=$(git merge-base HEAD origin/main 2>/dev/null || git merge-base HEAD main 2>/dev/null)
GIT_DIR=$(cd "$(git rev-parse --git-dir)" 2>/dev/null && pwd -P)
GIT_COMMON=$(cd "$(git rev-parse --git-common-dir)" 2>/dev/null && pwd -P)
```

Use the implementation plan's target branch when it is explicit. Otherwise verify the candidate base with the repository default branch and the merge base before opening a PR. If `GIT_DIR` differs from `GIT_COMMON`, treat the checkout as a linked worktree; do not remove it merely because the PR merges. If HEAD is detached, create or identify a named head branch before opening the PR and record it in the ledger.

## Merge Gate

Before merging, verify all of the following for the current head SHA:

- The PR is open and mergeable.
- Required checks pass.
- The PR description has a current thumbs-up sign-off.
- No eyes reaction remains.
- No pending review or `CHANGES_REQUESTED` state remains.
- Every actionable thread is fixed or technically answered and resolved.
- No new comments or reviews appeared after the latest sweep.
- The PR body links to the approved spec, implementation plan, and test plan.

If any condition fails, return to `superplex:receiving-code-review`. Do not merge based on a local test run or a subagent report alone.

Required checks are authoritative for merge, but local test evidence is still part of the PR record. Before requesting or updating review, run the plan's focused and PR-level verification and record results; a green local run does not replace a fresh remote check sweep.

## Automatic Merge Policy

When the merge gate passes, squash-merge automatically:

```bash
gh pr merge <pr-number> --squash
```

Stop before merging if the PR requires new secrets or variables in Vercel, GitHub, Trigger.dev, or another cloud tool. Record the exact provisioning request and resume the same PR ledger after provisioning.

If the repository explicitly prohibits automatic merging, preserve that repository guardrail and report the PR as merge-ready instead.

After merging, verify the PR is closed and record the merge commit and timestamp in `.superplex/sdd/progress.md` and `.superplex/pr-review/<pr-number>.md`. Do not delete the remote branch or remove the local worktree here. The branch and worktree remain available for post-merge validation, dependent PRs, and any final E2E or release-validation PR.

## No PR Yet

If the branch has not been pushed or a PR has not been opened:

1. Verify the branch and target branch using the implementation plan and `git merge-base`.
2. Push the feature branch with upstream tracking.
3. Open a PR targeting the verified plan base branch.
4. Include the spec, implementation-plan, and test-plan paths in the PR body.
5. Start `superplex:requesting-code-review` and then `superplex:receiving-code-review`.

Do not delete the worktree while the PR feedback loop is active.

## Cleanup

Preserve the local and remote branch and its worktree through the complete task lifecycle. Cleanup is a separate finalization step after the PR is confirmed merged, post-merge validation passes, all dependent/final PRs are complete, and the controller records task completion in its progress and PR ledgers. Never pass `--delete-branch` as part of the merge command, and never remove a harness-owned worktree while the task or any related PR is active.

An explicitly user-authorized discard is an abort path, not normal cleanup. It must name the entire task/PR scope being abandoned before any branch or worktree is removed.

### Explicit Abort Confirmation

Before discarding an unmerged task, show the affected branch, commits, PR URL/state, and worktree path. Require the user to type `discard` exactly. Then verify whether the worktree is Superplex-owned or harness-owned; never remove a harness-owned worktree. A discard request does not authorize deleting unrelated branches, worktrees, or PRs.

## Post-Completion Cleanup

Only after the final PR, post-merge validation, and any dependent/final E2E or release PRs are complete may a separately authorized cleanup remove a Superplex-owned worktree or branch. From the main repository root, verify the worktree provenance before using `git worktree remove`, then run `git worktree prune` to clear stale registrations. Preserve host/harness-owned worktrees even after completion unless the owning platform performs the teardown.

## Quick Reference

| State | Action |
|---|---|
| Checks, reviews, or threads incomplete | Continue receiving-code-review |
| All gates pass | Squash-merge automatically; preserve branch/worktree |
| New cloud secret/variable required | Stop and request provisioning |
| Repository forbids auto-merge | Report merge-ready and stop |
| Merge complete | Verify closure, record state, preserve branch/worktree, continue post-merge validation |
| Explicit abort requested | Require exact `discard`, verify scope and worktree provenance, then perform only authorized cleanup |

## Common Mistakes

| Mistake | Prevention |
| --- | --- |
| Treating a local test run as merge authorization | Re-check remote checks, current head, reactions, reviews, and threads. |
| Targeting the wrong base branch | Use the plan target and verify it with the merge base before PR creation. |
| Removing a worktree from inside itself | Change to the main repository root before any authorized cleanup. |
| Cleaning up a harness-owned worktree | Determine provenance first; leave host-managed worktrees to their owner. |
| Treating PR merge as task completion | Retain branch/worktree until post-merge and dependent/final PR validation finish. |

## Red Flags

Never:

- Merge before fresh remote verification.
- Accept a stale thumbs-up after a new push.
- Merge while an eyes reaction or unresolved actionable thread remains.
- Pass `--delete-branch` during merge.
- Remove the branch or worktree when the PR is merged but the overall task, dependent PRs, or post-merge validation are incomplete.
- Force-push or discard work without explicit authorization.
- Open a PR without verifying the intended base branch.
- Remove a worktree from inside that same worktree.
