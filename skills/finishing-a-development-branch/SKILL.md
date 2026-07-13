---
name: finishing-a-development-branch
description: Use when a pull request has completed its feedback loop and you need to verify merge readiness, merge it, or preserve or discard the branch
---

# Finishing a Development Branch

Finish a PR-oriented branch only after fresh verification of the current remote head, checks, reviews, reactions, and threads.

**Announce at start:** "I'm using the finishing-a-development-branch skill to complete this PR."

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

## Automatic Merge Policy

When the merge gate passes, squash-merge automatically:

```bash
gh pr merge <pr-number> --squash --delete-branch
```

Stop before merging if the PR requires new secrets or variables in Vercel, GitHub, Trigger.dev, or another cloud tool. Record the exact provisioning request and resume the same PR ledger after provisioning.

If the repository explicitly prohibits automatic merging, preserve that repository guardrail and report the PR as merge-ready instead.

After merging, verify the PR is closed and record the merge commit and timestamp in `.superplex/sdd/progress.md` and `.superplex/pr-review/<pr-number>.md`.

## No PR Yet

If the branch has not been pushed or a PR has not been opened:

1. Push the feature branch.
2. Open a PR targeting the plan's base branch.
3. Include the spec, implementation-plan, and test-plan paths in the PR body.
4. Start `superplex:requesting-code-review` and then `superplex:receiving-code-review`.

Do not delete the worktree while the PR feedback loop is active.

## Cleanup

Clean up only after a successful merge or an explicitly confirmed discard. Preserve any worktree or branch needed to address PR feedback. Never remove a harness-owned worktree.

## Quick Reference

| State | Action |
|---|---|
| Checks, reviews, or threads incomplete | Continue receiving-code-review |
| All gates pass | Squash-merge automatically |
| New cloud secret/variable required | Stop and request provisioning |
| Repository forbids auto-merge | Report merge-ready and stop |
| Merge complete | Verify closure, record state, then clean up |

## Red Flags

Never:

- Merge before fresh remote verification.
- Accept a stale thumbs-up after a new push.
- Merge while an eyes reaction or unresolved actionable thread remains.
- Clean up the branch before the PR is merged.
- Force-push or discard work without explicit authorization.
